# Phajay Payment Link: integration notes (spike D1-02)

Status: sandbox spike finished. Production mode has **not** been tested.

How to read this document. Every statement is tagged:

- **[Observed]** we saw it happen in our own sandbox tests.
- **[Docs]** the official documentation says so, but we did not verify it.
- **[Unknown]** not tested or not answered yet. Do not build on these without checking.

Official docs: https://payment-doc.lailaolab.com/v1 (parts of the docs disagree with real behaviour, see "Docs vs reality").

## 1. One-time portal setup (Test Environment)

- **[Observed]** The webhook URL must be set on the **Sandbox Webhook** tab. With only the "Payment Webhook" tab filled in, sandbox payments produced no webhook. After setting the Sandbox Webhook tab, webhooks arrived.
- **[Observed]** The portal says the webhook URL must use HTTPS and accept POST only.
- **[Docs]** Success URL and Cancel URL are set under Settings > Callback URL Setting.
- **[Docs]** The Test key is on the Key Management page. Never commit it. Keep it in an environment variable on the server only.
- During local development a public HTTPS tunnel (we used ngrok) is needed so Phajay can reach the machine.

## 2. Create a payment link

```
POST https://payment-gateway.phajay.co/v1/api/test/payment/get-payment-link
Content-Type: application/json
Authorization: Basic base64(TEST_KEY)
```

Body:

| Field | Type | Required? | Notes |
|---|---|---|---|
| `amount` | number | **Yes** [Observed: 400 BAD_REQUEST when missing] | LAK. 0 is rejected. |
| `description` | string | **Yes** [Observed: 400 BAD_REQUEST when missing] | Use plain ASCII. [Docs] BCEL does not support Lao/Thai text in the description. |
| `orderNo` | string | **Not enforced** [Observed: link created without it] | [Docs] says it must be unique. Phajay does **not** generate one: when it is omitted, the response contains no `orderNo` at all. We always send our own. |
| `tag1`, `tag2` | string | No | [Observed] returned in the webhook unchanged. Docs mention tag1 to tag6 in the webhook. |

Example request body:

```json
{
  "orderNo": "THM-000123",
  "amount": 10000,
  "description": "THAMMADA order THM-000123",
  "tag1": "thammada",
  "tag2": "optional extra reference"
}
```

Success response, **[Observed]** HTTP 200 with three fields:

```json
{
  "message": "SUCCESSFULLY",
  "redirectURL": "https://payment-link.sandbox.phajay.co?amount=10000&linkCode=XXXXXXXXXXXX",
  "orderNo": "THM-000123"
}
```

- `orderNo` in the response echoes the value we sent.
- `linkCode` is 12 upper-case letters/digits and must be read from the query string of `redirectURL`. Store it against the order immediately.
- The docs show `message: "SUCCESS"` and a `netlify.app` host. Real behaviour: `SUCCESSFULLY` and `payment-link.sandbox.phajay.co`.
- `redirectURL` also carries `amount` in its query string (for example `amount=20000`). **[Unknown]** whether the payment page charges the amount Phajay stored for that `linkCode` or the value in the URL. Our webhook check (amount equals the order total) protects us either way.

### Error behaviour [Observed, sandbox]

| Test | Result |
|---|---|
| Missing `amount`, missing `description`, `amount = 0`, empty `{}` | HTTP 400 `{"message":"BAD_REQUEST","detail":"amount and description are required"}`. `amount = 0` gets the same "required" message, so 0 is treated as missing |
| Invalid JSON body | HTTP 400 with a **different shape**: `{"error":{"message":"<JSON parser message>","detail":"not found endpoint","timestamp":"..."}}`. No top-level `message`, and the text leaks the parser error |
| Wrong key | HTTP 401 `Invalid publicKey` (the API says "publicKey" although the docs call it the Secret/Test key; no functional impact) |
| No Authorization header | HTTP 401 `Missing or invalid authorization header` |
| Missing `orderNo` | HTTP 200 (link created) |
| **Duplicate `orderNo`**, same amount or different amount | **HTTP 200, and a brand-new `linkCode` every time.** Three requests with the same `orderNo` returned three different `linkCode`s, and the third carried the different amount |
| `amount` negative (-1000) | **HTTP 200** |
| `amount` as string `"10000"`, decimal `1000.5`, `1`, `100000000` | HTTP 200 |
| `description` in Lao script | HTTP 200 (creation accepted; behaviour at payment time not tested) |
| `orderNo` of 200 characters | HTTP 200 |

Takeaways: the sandbox is very lenient. Phajay did **not** protect us against duplicate order numbers or nonsensical amounts, so our server must. Error response shapes are not consistent (three different shapes were seen), so treat anything other than HTTP 200 with a `redirectURL` as a failure and log the raw body. Production may be stricter, so repeat these tests when production access exists.

## 3. Customer pays

- **[Observed]** The customer is sent to `redirectURL`, a hosted Phajay page. A mock bank list is shown, then a QR code.
- **[Observed]** The QR must be scanned with the **PhaJay App** (the sandbox test app), not a real banking app. The docs page "PhaJay App" has the download link.
- **[Observed]** After paying, Phajay shows its own success screen with a countdown (52 seconds was seen shortly after the page loaded) and a button. Then the customer is redirected to our Success URL, automatically when the countdown ends or immediately via the button. The docs say 5 seconds; real behaviour is longer.
- **[Observed]** A customer may never return to our site (closing the tab), so the order status must never depend on the success page.
- The hosted page shows a 30-minute payment window (reported by the tester). **[Unknown]** what happens after it ends, and whether a webhook is sent.
- **[Observed]** The sandbox payment page has no cancel button. **[Unknown]** whether `/cancel` is ever called, and whether production differs.

## 4. Webhook (Phajay calls our server)

```
POST https://<our-domain>/<webhook-path>
Content-Type: application/json
x-phajay-signature: <344 characters>
```

**[Observed]** Body fields (names and types as received):

| Field | Type | Notes |
|---|---|---|
| `status` | string | `PAYMENT_COMPLETED` |
| `orderNo` | string | echo of what we sent |
| `linkCode` | string | 12 characters |
| `transactionId` | string | **Equal to `linkCode` in our sandbox payments.** Do not assume this in code. |
| `amount` | number | the amount |
| `txnAmount` | number | **[Unknown]** how it differs from `amount`. Check both against the order. |
| `paymentMethod` | string | e.g. `BCEL` |
| `paymentTime` | number | a number, not an ISO string as the docs say |
| `description`, `tag1` to `tag6` | string | tag1 and tag2 echoed; the others empty |
| `successURL` | string | the success URL Phajay will redirect to, see section 5 |
| `message`, `userId`, `paymentId`, `closedBy`, `paymentData` | string | present; meaning not investigated |

Other facts:

- **[Observed]** The webhook arrived before the customer clicked through Phajay's success screen. The real delay between payment and webhook was **not measured**.
- **[Observed]** The header `x-phajay-signature` is present and 344 characters long. That length does not match a typical HMAC (64 hex or 44 base64 characters) and is consistent with a base64-encoded 256-byte value such as an RSA-2048 signature. This is a guess from the length only.
- **[Unknown]** How to verify the signature (algorithm, key, which bytes are signed). The docs say to contact Phajay. **Do not implement verification by guessing.** Keep the raw request body so it can be verified later.
- **[Unknown]** What response the endpoint must return, whether Phajay retries, and whether duplicates are sent.
- Fields that the sandbox docs list but we did **not** receive: `origin`, `_id`, `user`, `currency`, `createdAt`, `updateAt`, `qrCode`, `isMerchantIdPayment`, `isAffiliatePayment`. The docs warn that sandbox and production payloads may differ.

## 5. Redirect to our Success URL

- **[Observed]** The webhook's `successURL` field was of the form `https://<our-domain>/success?linkCode=...&amount=...&description=...&orderNo=...` (note: the description is not URL-encoded).
- **[Observed, inconsistent]** In our very first test the request to `/success` carried only `linkCode`. Later runs include more parameters. We do not know why. Code must therefore rely only on `linkCode`.
- Query values come through the customer's browser and can be edited by anyone. **Never** use them to decide that an order is paid. Never render them into HTML.

## 6. Check transaction status

```
GET https://payment-gateway.phajay.co/v1/api/payment/check-transaction/status/{id}
Authorization: Basic base64(KEY)
```

- **[Observed]** Returned HTTP 404 `PAYMENT_NOT_FOUND` for a sandbox payment that had already produced a `PAYMENT_COMPLETED` webhook (tried with the `linkCode`, which equals the `transactionId`).
- **[Observed]** The Test Environment "Payment History" page in the portal was empty after our test payments.
- Likely explanation (inference, not confirmed): sandbox payments are not stored in the main system, so this endpoint cannot see them. **In the sandbox the webhook is the only way to learn that a payment completed.**
- **[Docs]** In production this endpoint is meant to confirm payment, with `isPaid: true`. **[Unknown]** until production is tested.

## 7. Docs vs reality

| Docs say | Reality in our sandbox |
|---|---|
| Create-link response has `message` and `redirectURL` | Also contains `orderNo` |
| `message` is `SUCCESS` | `SUCCESSFULLY` |
| Payment page host is `payment-link-sandbox.netlify.app` | `payment-link.sandbox.phajay.co` |
| Sandbox success screen shows 5 seconds | Longer countdown (52 s seen) |
| Sandbox webhook has `origin`, `_id`, `createdAt`, ... | Has `paymentMethod`, `txnAmount`, `paymentId`, `successURL`, ... |
| `paymentTime` is an ISO date string | A number |
| Webhook settings: one place | Separate "Sandbox Webhook" tab, not mentioned in the docs |
| `orderNo` must be unique | Duplicates accepted |

## 8. Design decisions that follow

1. **Create our own order first** (status `PENDING`), with our own unique order number (database unique constraint), then call Phajay. The amount is always calculated on the server from the database, an integer greater than 0.
2. **Store `linkCode` and `redirectURL` against the order** as soon as the link is created.
3. **Do not create a second link for an order that already has an unexpired `PENDING` link.** Reuse the stored `redirectURL`. Phajay returns a new link with a new `linkCode` every time, even for the same `orderNo` [Observed], and each is probably payable (not tested), which risks a double payment.
4. **Only a verified webhook marks an order `PAID`.** Match on `orderNo` and `linkCode`, require `status = PAYMENT_COMPLETED`, and require both `amount` and `txnAmount` to equal the order total. Use a database transaction, make the handler idempotent (a repeat for a `PAID` order does nothing), and respond quickly.
5. If a webhook arrives for an order that is already `PAID` or expired, store it and flag it for manual review (possible double payment). Refunds are handled by hand.
6. **The success page only reads our database** by `linkCode`: show "confirming" while `PENDING` and re-check every few seconds. Provide an "My orders" page because customers may never come back to the success page.
7. Treat anything other than HTTP 200 with a `redirectURL` as a failed link creation. Log the raw response. Never show raw Phajay messages to customers.
8. Signature verification: write a `verifyPhajaySignature()` function now, behind a config switch that is **off** while the method is unknown. Document that clearly in the README. It must be turned on before any real money is accepted.
9. Expire `PENDING` orders somewhat later than the payment window (for example 35 to 40 minutes if the window really is 30), so stock is not released while a link can still be paid.
10. Parse webhook bodies defensively: missing fields or changed types must not crash the handler. Use the field names in section 4, not the docs.
11. Repeat the tests in sections 2 to 6 when production mode becomes available.

## 9. Open questions for Phajay support

1. How to verify `x-phajay-signature` (algorithm, which key and where to get it, exactly which data is signed, encoding).
2. What response the webhook endpoint must return, retry policy, and whether duplicates can be sent.
3. How to check the status of a sandbox transaction (check-status returns 404 for a completed sandbox payment).
4. Link expiry time, and what happens at expiry (any webhook, any redirect).
5. What is the difference between `amount` and `txnAmount` in the webhook.
6. Is there a cancel flow in production, and is `/cancel` ever called?
7. Why did the success redirect carry only `linkCode` in one test and more parameters later?
8. Is a duplicate `orderNo` allowed in production, and how are minimum and maximum amounts enforced?
9. Does the payment page use the `amount` in the `redirectURL` query or the amount stored for the `linkCode`? Can editing the URL change what is charged?

## 10. Not tested

- QR Payment API (`generate-{bank}-qr`)
- Production mode (needs KYC)
- Payment after link expiry, cancellation, failed payment
- Whether two links created for the same `orderNo` can both be paid (the `linkCode`s do differ)
- Refunds

## 11. How the spike was run

A throwaway Node.js project (not part of the shop repository) with four small scripts: a webhook receiver that logs the raw request, a script that creates one payment link, a script that checks status, and a script that sends 18 error-case requests. The test key lived in a `.env` file that is not committed. Logs contained no keys; personal data was kept out of anything shared.
