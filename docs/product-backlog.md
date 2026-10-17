# Product backlog and roadmap

## Product Goal

Within about six development sprints, a customer can place an order and pay with Phajay (sandbox), and the owner can manage products and orders in the admin panel.

## Scrum setup (solo)

| Role | Responsibility |
|---|---|
| Product Owner | Orders the backlog and decides what is in the MVP |
| Scrum Master | Looks after the process and removes blockers |
| Developer | Designs, builds and tests |

Rule: the scope of a sprint does not change mid-sprint. New ideas go to the backlog.

Sprints are one week long with about 28 hours of plannable capacity. See [sprint-d1-backlog.md](sprint-d1-backlog.md) for how capacity is calculated.

## Epics

| ID | Epic | Priority |
|---|---|---|
| E0 | Design: brand, UI, design system, assets | MVP |
| E1 | Foundation and DevOps (repo, database, CI, deploy) | MVP |
| E2 | Product catalogue (list, search and filter, detail, variants) | MVP |
| E3 | Cart | MVP |
| E4 | Authentication and account | MVP |
| E5 | Checkout and orders (address, shipping fee) | MVP |
| E6 | Payment (Phajay and cash on delivery) | MVP |
| E7 | Admin panel (products, stock, orders) | MVP |
| E8 | Internationalisation (Lao and English) and UI polish | Should have |
| E9 | Email notifications, coupons, reviews | Could have |
| E10 | Admin dark mode (customer pages stay light only) | Could have |

Condition for pulling E10 into a sprint: Sprint S6 is finished and there is spare velocity.

## Roadmap (about 10 weeks, to be adjusted after D1 and D2)

| Sprint | Sprint Goal |
|---|---|
| D1 | Brand, flows and wireframes ready, and the Phajay sandbox called once |
| D2 | Customer-facing high-fidelity UI and design system done |
| D3 | Admin UI, tested prototype, assets and handoff done |
| S0 | Project deployable, database ready, design tokens and base components in code |
| S1 | Customer can browse products and choose size and colour |
| S2 | Cart, sign-up and login |
| S3 | Customer can create an order (address, shipping fee) with cash on delivery first |
| S4 | Customer can pay with Phajay end to end, including callback handling |
| S5 | Owner can manage products, stock and orders in admin |
| S6 | Lao/English, tests, README, demo video, final deploy |

The riskiest item, the Phajay integration, is investigated early in a spike (Sprint D1) so that screen design does not depend on guesses.

## Definition of Ready (front-end stories)

A story can enter a sprint only when:

- the Figma frame for that screen is linked,
- every state is designed (default, loading, error, empty),
- the assets it needs are exported.

## Definition of Done

- Passes lint and type-check
- Tests exist for important logic, especially pricing, stock and payment
- Meets the acceptance criteria
- Merged to `main` through a pull request (even when reviewing alone)
- Deployed to staging and verified there
- README and docs updated where affected

For design sprints, also: tested with at least 3 real people, and layers and components named consistently.

## Example user story

**US-601.** As a customer, I want to pay with a bank QR so that I can complete my order from my phone.

- Given I have an order with status `PENDING`, when I choose QR payment, then the system shows a QR with the correct amount.
- Given I paid successfully, when Phajay reports the result, then the order becomes `PAID` and stock is deducted once only.
- Given 15 minutes pass without payment, then the order expires and stock is released.

## Payment flow principles

1. The server calculates prices from the database. The client never sends prices.
2. The server creates the payment with Phajay and stores the returned reference against the order.
3. Only the server-verified result moves an order to `PAID`. Follow the verification method in the Phajay documentation.
4. Payment handling is idempotent: repeated callbacks must not deduct stock or send email twice.
5. A scheduled job expires stale `PENDING` orders and releases stock.
