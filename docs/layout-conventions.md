# Layout conventions and Lao market notes (D1-03)

## Decision

We do **not** collect screenshot benchmarks. Online clothing shops share a standard structure that customers already know, so THAMMADA keeps the structure and differentiates through brand, illustration and a "Shop the look" feature.

The conventions below come from general knowledge of online fashion shops, not from a fresh audit of any specific site. Treat them as a starting point to be tested with real people in Sprint D3.

## Login decision

Login is required at checkout. Browsing products and filling the cart work without an account. Reasons: a simpler data model (every order belongs to a user), no "view order by link" page to secure, and the shop owner only sees orders from known users. Guest checkout is a Could have for later. Risk: some customers may drop out at the sign-up step, so keep sign-up short and ask for it only at checkout.

## Standard layout (mobile-first)

| Screen | Convention |
|---|---|
| Home | Illustrated banner, categories, new arrivals, "Shop the look", footer with "Demo project, not a real store" |
| Product list | 2 columns on mobile, filters in a bottom sheet, card with image, name, price and colour dots |
| Product detail | Swipeable images, size buttons (sold-out sizes shown dimmed), colour dots, "Add to cart" bar fixed at the bottom, collapsible sections for material, size table and care |
| Cart | Separate page (simpler than a slide-over panel for the MVP) |
| Checkout | One page. **Login is required here**; browsing and the cart need no account. Province / district / village address, shipping choice, payment choice |
| After payment | "Confirming payment" then success or failure, plus a "My orders" page (list and order detail) |

## Lao market notes

Source: the project owner's own knowledge. Not verified research.

How most small Lao clothing sellers work today:

1. They sell through social media (Facebook, WhatsApp) instead of their own website.
2. To take payment, the seller sends a QR code in the chat.
3. The customer pays and sends a screenshot of the transfer back to the seller.
4. The seller checks the screenshot by hand to confirm payment.
5. Then the seller ships the order.

### What this means for the design

| Observation | Design response |
|---|---|
| Payment confirmation is manual (checking screenshots). It is slow, easy to get wrong and open to fake slips. | Confirm payment automatically from the payment gateway's webhook. The order page shows the status without any human step. This is the main story of the project. |
| Customers are used to paying by scanning a QR code. | Keep QR payment (Phajay Payment Link) as the primary method. Nothing new to learn. |
| Customers are used to asking questions in chat before buying (size, stock, colour). | Show stock and sizes clearly, include a size table, and add a contact button to chat. Contact channels to be decided (WhatsApp, Facebook Messenger). Priority: Should have. |
| Products are shared by sending links in chats. | Give each product a clean, shareable URL with a good preview image and title (Open Graph tags). Inference from social-selling habits. Priority: Should have. |
| Orders and stock are tracked by hand in chat history. | Stock per size and colour lives in the database and the admin panel (already in scope). |
| Shipping is arranged by the seller. | MVP: a fixed shipping fee table by province. No carrier API. |

### Cash on delivery

Earlier planning assumed cash on delivery is common in Laos. That assumption has no support yet, and the flow described above is pay-first. COD is therefore an **optional** payment method (Could have) and is used only as a technical stepping stone in Sprint S3 before Phajay is connected.

## Still unknown (check before finalising)

- Which shipping companies sellers use, and typical fees between provinces
- Whether customers expect to pay before shipping for every order
- Whether Lao should be the default language, with English second (current plan: Lao and English)
- Which chat channel customers prefer for contacting sellers

## Portfolio story

> Small Lao sellers confirm payments by reading transfer screenshots in chat. THAMMADA replaces that step with automatic confirmation through a payment gateway, and keeps the QR payment habit customers already have.

## Definition of Done for D1-03

- Layout conventions agreed (this document)
- Lao market notes recorded
- Findings reflected in the Sprint D1 wireframe task (D1-09) and the product backlog
