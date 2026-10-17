# THAMMADA (ທຳມະດາ)

> Ordinary, done well.

A clean smart-casual basics e-commerce website for the Lao market, with online payment through the [Phajay](https://www.phajay.co/en) payment gateway.

**Demo project, not a real store.** The brand, products and checkout exist only as a portfolio and learning exercise. No real orders are fulfilled and no real payments are taken.

## Vision

Customers browse everyday clothing, pick a size and colour, and pay by bank QR or card in a few steps. The store owner manages products, stock and orders from an admin panel.

## Product Goal

Within about six development sprints, a customer can place an order and pay with Phajay (sandbox), and the owner can manage products and orders in the admin panel.

## Status

| Phase                                       | State       |
| ------------------------------------------- | ----------- |
| Discovery and brand direction (Sprint D1)   | In progress |
| UI design and design system (Sprint D2)     | Not started |
| Admin UI, prototype and handoff (Sprint D3) | Not started |
| Development (Sprints S0-S6)                 | Not started |

Design file: [THAMMADA Design on Figma](https://www.figma.com/design/FD8geftHt0rLKMlPpeusWv)

## How this project is run

Solo Scrum with one-week sprints. One person plays Product Owner, Scrum Master and Developer, and the process is adapted accordingly.

- Product backlog and sprint board: GitHub Projects
- Planned capacity per sprint: about 28 hours
- Every sprint has a Sprint Goal, a Review (recorded as a short demo video) and a Retrospective
- Details: [docs/product-backlog.md](docs/product-backlog.md)

## Planned tech stack

These choices are planned and may change after the design sprints.

- Next.js (App Router) with TypeScript
- PostgreSQL with Prisma
- Auth.js
- Tailwind CSS and shadcn/ui
- Payments: Phajay REST API (sandbox first), plus cash on delivery
- Images: Cloudinary or Cloudflare R2
- Deployment: Vercel and a managed Postgres provider

## Key design decisions

- Prices are stored as integer LAK (the kip has no decimal subunit in everyday use).
- The server computes all prices and totals. The client is never trusted for payment state.
- Payment providers sit behind a common interface, so Phajay and cash on delivery are interchangeable.
- Colours are semantic design tokens from the start, so an admin dark mode can be added without rework. The customer-facing site is light only.
- Illustrations use English text only. Lao text lives in the HTML so that real Lao fonts render correctly.

## Repository layout

```
docs/
  brand-one-pager.md      Brand direction, palette, tone, illustration rules
  product-backlog.md      Epics, roadmap, Definition of Ready / Done
  sprint-d1-backlog.md    Sprint D1 goal, tasks and hours
```

## Credits and references

Brand direction is inspired by the calm, functional approach of Uniqlo, MUJI, GU, Global Work, BEAMS and URBAN RESEARCH. No logos, photos, text or other assets from those brands are used.
