# Brand one-pager: THAMMADA (ທຳມະດາ)

Status: draft v3. Colour, typography and wordmark decided; illustration style guide still to finish in Sprint D1.

## Name

| | |
|---|---|
| Lao | ທຳມະດາ |
| Romanised | THAMMADA |
| Meaning | "ordinary", "everyday" |
| Tagline (EN) | Ordinary, done well. |

The Lao spelling was confirmed by the project owner. The idea is borrowed from the "no-brand" calm of MUJI: the best clothes are the ones you can wear every day without thinking about them.

Before the name is used anywhere public, search Google and Facebook for existing Lao businesses with the same name.

## Wordmark

Decision: option B, the horizontal lockup.

- Latin: THAMMADA in Noto Sans SemiBold with +8% letter-spacing, colour `text-primary`.
- Divider: a thin vertical line in `text-muted`.
- Lao: ທຳມະດາ in Noto Sans Lao SemiBold, colour `accent`.
- Monogram: the Lao letter ທ (first letter of ທຳມະດາ) in white on a brick-red circle or rounded square. Used for the favicon and social avatar.

Rules (details and examples in the Figma file, section "Wordmark", board "Wordmark spec v1"):

| Rule | Value |
|---|---|
| Clear space | x on every side, where x is about the cap height of the T |
| Minimum size | 140 px wide for the full lockup. Below that, use Latin only (96 px) or the monogram. The monogram reads as a letter from about 32 px and is only a shape at 16 px. These minimums are estimates and must be checked on a real phone. |
| Colour versions | Primary on cream, reversed (cream on brick), mono navy, mono cream on navy. Figma components: `Wordmark/Primary`, `Wordmark/Reversed`, `Wordmark/Mono navy`, `Wordmark/Mono cream`. |
| Don't | Stretch, rotate, add shadows, recolour outside the four versions, or place on a busy photo without a solid panel. |

The wordmark is typeset from the Noto fonts (SIL Open Font License), not custom-drawn letterforms. Outline the text before exporting files.

Still to do: search Facebook and Google for existing Lao businesses named ທຳມະດາ or Thammada.

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

Decision: option A, the Noto family (Noto Sans for Latin, Noto Sans Lao for Lao). The two were designed to match each other, which lowers the risk of mismatched size and weight.

| Role | Latin | Lao |
|---|---|---|
| Heading | Noto Sans 600 | Noto Sans Lao 600 (loopless) |
| Body | Noto Sans 400 | Noto Sans Lao Looped 400 (planned) |
| Number (prices, sizes, order numbers) | Noto Sans 500, tabular figures | Western digits 0-9 from the Latin font |

Notes:

- Noto Sans Lao covers only the Lao block, so digits 0-9 and the kip sign come from the Latin font. The kip sign is not in the Noto Lao fonts either, so write prices as `250,000 ກີບ` or `250,000 LAK`.
- The Lao body font is Noto Sans Lao Looped because Google describes it as suitable for all text, while loopless Noto Sans Lao is aimed at headlines and advertising. **Figma does not offer Noto Sans Lao Looped.** Until it is installed locally in the Figma desktop app, the `Lao/Body` style uses Noto Sans Lao Regular as a stand-in. The decision between looped and loopless body text is open and should be confirmed with real Lao copy in Sprint D2.
- Figma text styles hold one font each, so styles come in pairs: `Latin/*` and `Lao/*`. In code, the CSS font stack lists the Latin font first, then the Lao font.
- Figma cannot set tabular figures in a text style. Use `font-variant-numeric: tabular-nums` in code.

Type scale (mobile, size / line-height). Lao line-height is about 4px larger so stacked vowel and tone marks do not collide.

| Style | Latin | Lao | Weight |
|---|---|---|---|
| Display | 32 / 40 | 32 / 44 | 600 |
| H1 | 28 / 36 | 28 / 40 | 600 |
| H2 | 22 / 30 | 22 / 34 | 600 |
| H3 | 18 / 26 | 18 / 30 | 600 |
| Body | 16 / 26 | 16 / 30 | 400 |
| Small | 14 / 22 | 14 / 26 | 400 |
| Price | 18 / 26 | 18 / 30 | 500 |
| Button | 16 / 24 | 16 / 28 | 600 |

Desktop sizes (Display 44, H1 36, H2 28, H3 20) are added in Sprint D2. Lao rules: no letter-spacing, no italics, no weights lighter than 400, body not below 16px on mobile.

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