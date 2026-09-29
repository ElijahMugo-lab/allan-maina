# TASTE.md

Design rules for the Allan Maina site. This is the contract any change has to
pass before it ships. The whole site is one file, [index.html](index.html), with
no build step, so these rules live here rather than in a config.

---

## 1. What this site is

A lead generation page for one person: a private chauffeur in Nairobi who also
does minor car repairs and TV mounting. Every decision serves one action, which
is starting a WhatsApp conversation.

- **Audience:** Nairobi clients booking a driver. Executives, families, people
  who need a JKIA run.
- **Tone:** sober, competent, local. Not luxury fantasy, not startup energy.
- **Dials:** `DESIGN_VARIANCE 7`, `MOTION_INTENSITY 4`, `VISUAL_DENSITY 3`.

---

## 2. Photography rules

**No people in any image.** This is the strictest rule on the page. Cars,
interiors, roads, engine bays, rooms, the Nairobi skyline. No faces, no hands,
no figures, no silhouettes of people.

Why it matters here: stock photos of people are always someone else's staff in
someone else's country, and on a one-man service site that reads as a lie. A
clean car photographed well says more than a model in a suit.

Other photography rules:

- **Alt text describes the actual photo.** Not what you wish the photo showed.
  If the file is a Bentley front end, the alt text says a car, not "Allan".
  This has already gone wrong once.
- **Every image is verified before it ships.** Open the URL, look at it. Do not
  trust a filename, an ID, or a search result caption.
- All photos are placeholders until Allan supplies his own. The hero and the
  three service cards are the priority swaps, and each is marked with a comment
  in the markup.
- Images sit on a `--surface-2` container so a failed load degrades to a solid
  tile instead of a white gap.
- Everything below the fold is `loading="lazy"`. The hero is `fetchpriority="high"`.

---

## 3. The hero

**The hero image always fills the entire hero section, and the copy rides in one
minimal card beside it.** Never a split column, never a text-only hero.

```
+--------------------------------------------------+
|  [image fills the whole section, edge to edge]   |
|                                                  |
|   +---------------------+                        |
|   | headline (2 lines)  |                        |
|   | one line of copy    |                        |
|   | [CTA]  [secondary]  |                        |
|   +---------------------+                        |
+--------------------------------------------------+
```

Rules for the card:

- Maximum four things inside: headline, one line of copy, one primary CTA, one
  secondary CTA. Nothing else. No badges, no stats, no trust strip, no tagline
  under the buttons.
- Headline caps at 2 lines. Because the card is narrow, the display size is
  capped lower than a full width hero would use.
- **The card is frosted glass.** Layered `backdrop-filter`, a dark tint, a 1px
  outer border, inset specular hairlines top and bottom, and a sheen along the
  top edge. Apple documents Liquid Glass for Apple platforms only and ships no
  web package, so this is an approximation and the code says so.
- **The glass tint stays dark and the sheen stays off the copy.** This is the
  part that breaks if you nudge it. White veils read as luminance, and luminance
  over grey text destroys contrast: at 15 percent white the body copy drops to
  roughly 2:1. So the tint sits near 74 percent, the sheen is capped at 7 percent
  and confined to the top padding band, and the hero copy runs brighter than
  `--muted`. Check contrast against the lightest part of the photo underneath,
  not the average.
- A `prefers-reduced-transparency` fallback swaps the blur for a solid fill and
  drops the sheen.
- Section is `min-height:100dvh`, never `100vh`.
- On mobile the image still fills the section. The card moves to the bottom and
  the scrim rotates to vertical.

---

## 4. Tokens

Defined once in `:root`. Do not introduce a colour, radius, or font outside this
set.

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0e0f10` | page background |
| `--surface` | `#16181a` | cards |
| `--surface-2` | `#1d2023` | media wells, image fallback |
| `--ink` | `#e9e7e3` | primary text |
| `--muted` | `#9a9ea4` | secondary text, 7.3:1 on bg |
| `--accent` | `#d2694a` | the only accent, 5.4:1 on bg |
| `--accent-hi` | `#e07d5e` | accent hover, focus ring |

**One accent, everywhere.** Rust. It is the only saturated colour on the page.
No second accent for a badge, a status, or a footer link. Saturation stays under
80 percent.

**Theme is locked to dark.** No section inverts to a light background.

**Radius is a two tier system.** Media and cards are `14px`. Interactive
controls are full pill. Nothing else.

**Type:** Archivo for everything, Geist Mono for the phone number and quote
attributions. Display sizes use `font-stretch:104%` and `-0.028em` tracking.
No serif on this page.

---

## 5. Banned

These are the patterns that make a page look machine generated. They were all
removed once already, so do not reintroduce them.

- Em-dash and en-dash. Anywhere. Use a hyphen, a comma, or two sentences.
- The middle dot as a separator in body copy, headings, or hero text. The one
  exception is the footer credit line, which is a conventional place for it and
  which reads better with the dots than without.
- Eyebrows, the small uppercase tracked label above a heading. The page runs
  zero. The headline alone is enough.
- Section numbering, `01 / 02 / 03` above card titles.
- Scroll cues. The user knows what scrolling is.
- Three equal cards in a row. The services grid is a 3 cell bento with one lead
  tile, and it stays that way.
- Two sections sharing a layout family. Seven sections, seven layouts.
- Invented statistics. The old page claimed 500 trips and a 100 percent clean
  record. If a number is not real, it does not go on the page.
- Coloured glow. No `box-shadow: 0 0 Npx <colour>`. Shadows are tinted to the
  background, and always carry a vertical offset.
- Emoji. Icon libraries only, and currently the page needs none.
- More than one label for one action. Every booking control on the page reads
  **Book on WhatsApp**. Nav, hero, contact, floating button.

---

## 6. Motion

Reveals on scroll, hover lift on interactive things, nav background on scroll
past the sentinel. That is the whole budget.

- **No `window.addEventListener('scroll')`.** Use `IntersectionObserver`. Both
  the reveal system and the nav state already do.
- Animate `transform` and `opacity` only.
- Every animation must answer "what does this communicate". Reveals give
  hierarchy on entry, hover gives feedback, nav state signals a change. Anything
  that cannot answer gets cut.
- `prefers-reduced-motion` collapses all of it. Non negotiable.

---

## 7. Accessibility floor

- Body text at 4.5:1 minimum, checked against the actual background it sits on.
- Visible `:focus-visible` ring on everything focusable.
- Skip link first in the DOM.
- The mobile menu sets `aria-expanded`, closes on Escape, returns focus.
- Alt text on every image. Decorative backgrounds get `alt=""` plus
  `aria-hidden`.
- Phone numbers are `tel:` links.

---

## 8. Using OriginKit

[OriginKit](https://originkit.dev) is a free animated component library. Its MCP
server is wired up in [.mcp.json](.mcp.json) so components can be pulled in
directly rather than hand written.

**Setup:** run `/mcp` in Claude Code and sign in with OriginKit. The server
supports OAuth, so authentication is held by the connector and no key is stored
in this repo. There is no `.env`, no key in `.mcp.json`, and no key in any file
here. If a key ever ends up in the working tree, rotate it.

**The adoption gate.** OriginKit ships React and Tailwind components with their
own tokens and their own motion defaults. This page is one file of vanilla CSS
at `MOTION_INTENSITY 4`. Dropping a component in as shipped would break both the
palette and the motion budget. So anything pulled from OriginKit passes three
checks before it lands:

1. **Retokenize.** Every colour, radius, and font in the component is swapped for
   the tokens in section 4. If it arrives with a purple gradient and a 24px
   radius, it leaves with `--accent` and `14px`. A component that cannot survive
   this is the wrong component.
2. **Motion budget.** Strip anything infinite, anything parallax, anything that
   listens to raw scroll. Keep entry and hover. If the component is only
   interesting because it never stops moving, it does not belong here.
3. **Port to vanilla.** There is no build step and no React. The component gets
   translated to plain HTML and CSS in `index.html`, with the JavaScript reduced
   to an `IntersectionObserver` or dropped entirely.

Use it for what it is genuinely good at, which is motion patterns worth copying.
Do not use it as a source of layout, because the layout rules in sections 3 and
5 win.

---

## 9. Before you ship

- [ ] Zero em-dashes and en-dashes in visible copy
- [ ] Every image opened and confirmed to match its alt text
- [ ] No people in any image
- [ ] Hero image fills the section, card holds four things or fewer
- [ ] Hero card copy still passes AA against the brightest part of the photo
- [ ] Checked with reduced transparency on
- [ ] One accent colour across every section
- [ ] Every booking control says "Book on WhatsApp"
- [ ] Checked at 375px, 768px, and 1440px
- [ ] Checked with reduced motion on
- [ ] Tab through the whole page, focus ring visible the entire way
- [ ] No numbers on the page that nobody can back up
