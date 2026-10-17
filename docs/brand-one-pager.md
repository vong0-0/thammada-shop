# Brand one-pager: THAMMADA (ທຳມະດາ)

Status: draft v1, to be finalised in Sprint D1.

## Name

| | |
|---|---|
| Lao | ທຳມະດາ |
| Romanised | THAMMADA |
| Meaning | "ordinary", "everyday" |
| Tagline (EN) | Ordinary, done well. |

The Lao spelling was confirmed by the project owner. The idea is borrowed from the "no-brand" calm of MUJI: the best clothes are the ones you can wear every day without thinking about them.

Before the name is used anywhere public, search Google and Facebook for existing Lao businesses with the same name.

## Positioning

**Clean smart-casual basics.** Plain, well-fitting clothing that is tidy enough for work and comfortable enough for a day off.

Foundation: the clarity of Uniqlo and MUJI. Differentiator: styling help in the style of BEAMS and URBAN RESEARCH, for example a "Shop the look" feature that suggests outfits as sets.

## People

**Mind, the customer.** 22, lives in Vientiane, shops on her phone, usually pays with a bank QR, and does not want a long sign-up process.

**Boonmee, the store owner (admin).** Needs to update stock quickly, see which orders are paid, and know what to ship.

## Visual direction

Calm, spacious, picture-led. Plenty of white space, little text, one accent colour used sparingly.

### Colour (starting values, estimated from reference art)

| Token (semantic) | Hex | Use |
|---|---|---|
| `surface` | `#F6EEDC` | Page background (warm cream) |
| `surface-raised` | `#FBF7EC` | Cards, inputs |
| `text-primary` | `#24424F` | Body text, lines, icons (deep blue-green) |
| `accent` | `#8A2B24` | Primary buttons, sale prices, highlights (brick red) |

Contrast ratios (WCAG formula, calculated):

| Foreground on background | Ratio |
|---|---|
| `text-primary` on `surface` | 9.2 : 1 |
| `accent` on `surface` | 7.4 : 1 |
| `text-primary` on `surface-raised` | 10.0 : 1 |
| `surface-raised` on `accent` (button text) | 8.0 : 1 |

All pass WCAG AA for normal text. Re-check in Figma once the final palette is chosen.

Always use semantic token names in design and code, never raw colour names.

### Typography

- Latin: one clean sans-serif, to be chosen and tested in Sprint D1.
- Lao: Noto Sans Lao (verify availability and weights on Google Fonts).
- Test both together at body and heading sizes. Lao glyphs are thin, so avoid very light weights.

## Photography and illustration

The two layers have different jobs.

- **Product photos:** plain background, even light, true colour and fabric texture. Customers must be able to judge the product.
- **Illustration:** the brand's personality layer. Used for the logo, hero banners, empty states (empty cart, no results), 404, order success, and icons.

Illustration rules:

- Flat style with one consistent line weight and no more than 4 colours per piece, taken from the palette.
- Prefer objects and everyday scenes over detailed characters.
- Text inside illustrations is English only. Lao text is placed as HTML so real fonts render properly.
- MVP scope: about 6 to 8 pieces (logo, 1 to 2 hero banners, 3 empty states, 404).
- Original work only. Reference art is for studying style, never for copying characters or compositions.

## Tone of voice

Polite, short and direct. Describe the clothes plainly (material, fit, care). No shouting promotions, no hype.

## Rules

1. Footer on every page: "Demo project, not a real store."
2. No logos, photos or copy from reference brands.
3. Product photos come from owned shots, mockups or stock with a licence that allows this use.
4. Dark mode applies to the admin panel only (Could have). The customer site is light only.
