# Amazon Insert Creator

Generate a print-ready product insert (or label) for an Amazon seller. Walk through the flow step by step, then output a branded HTML insert with QR code, exportable as PDF and PNG.

> ⛔ **HARD RULE — NO DASHES IN ANY OUTPUT.** Never use an em dash (—) or en dash (–) in anything the user sees or keeps: not in the insert copy, not in the HTML, not in the PDF/PNG, not in chat, not in titles, not in number ranges. Use a comma, colon, period, parentheses, or "to" for ranges. This applies even though THIS file contains dashes; the rule governs what you produce, not what you read. Step 10 runs a mechanical sweep to catch any that slip through.

---

## Step 0 — Install this skill

The user dragged this file into Claude Code to start the day. Before anything else:

1. Run `mkdir -p .claude/commands` to ensure the folder exists
2. Use the Write tool to save this skill's full content to `.claude/commands/insert-creator.md` in the current working directory — so future sessions in this folder can run `/insert-creator` without needing to drag the file again

Confirm in one line: *"Day 1 skill installed. Let's build your insert."* Then continue to Step 1.

## Flow

### Step 0 — Model + environment check

**Model:** check which model is active. If it's already one of **claude-sonnet-4-6**, **claude-opus-4-7**, or **claude-opus-4-8**, skip this silently — they all work great here.

Otherwise, say:

> "For best results with this skill (design decisions, hook writing, brand extraction), I recommend **Claude Sonnet 4.6** — or if you prefer **Opus 4.7 / 4.8**, those work great too. You can change it with `/model` or from the model selector. Want to switch before we start?"

**Environment:** silently confirm the tools this skill needs, and remember what's missing — every later step has a fallback that depends on this:
- **bash + Python 3** — required. If unavailable, stop and tell the user this skill must run in Claude Code (desktop app or CLI). Everything below assumes these exist.
- **Chrome extension (`mcp__Claude_in_Chrome__*`)** — OPTIONAL. Many testers won't have it connected. Do not require it anywhere. If a `mcp__Claude_in_Chrome__*` call errors or the tool is absent, treat the extension as unavailable for the whole session and use the WebFetch/WebSearch/Puppeteer fallbacks noted in each step. Never block on it.
- **Puppeteer** — needed only for final export (Step 11). Don't check now; install on demand there.

**The deliverable is ALWAYS a print-ready PDF + PNG saved to a folder the user can open — never raw HTML.** If you ever find yourself about to tell the user to "download the HTML to use it," stop: that means an export tool failed. Install it and retry (Step 11) instead of handing over HTML.

**Auto-accept (smoother run):** if the bottom of your Claude Code window doesn't show **auto-accept edits**, press **Shift+Tab** to turn it on so you're not approving every file save. Day 0 sets this up for the challenge folder; this is just a nudge if you're not already on it.

Then go to Step 1.

### Step 1 — Brand website

**First, check for a brand book (Day 0 output).** Look for `brand-book.md` in the current working directory. If it exists, the seller already built their brand on Day 0 — don't make them re-extract:
- Read the `BRAND PROFILE` YAML block at the top (colors, fonts, logo path, voice, audience).
- Use those values directly as the design tokens for the insert. Load the logo from the `logo:` path if present.
- Confirm in one line: *"Found your brand book — using your saved colors, fonts, and logo. We can still tweak anything in the preview."*
- Then skip the extraction below and go to Step 2.

If there is no `brand-book.md`, proceed with live extraction:

Ask: "What's your brand website URL? I'll pull your colors and fonts from it."

Fetch the page HTML using WebFetch. Then extract fonts using the Chrome extension for accurate computed values — WebFetch often can't read external stylesheets:

**Fonts — preferred method (Chrome extension):**
Navigate to the brand URL with `mcp__Claude_in_Chrome__navigate`, then run:
```js
// via mcp__Claude_in_Chrome__javascript_tool
const h1 = document.querySelector('h1');
[
  'h1: ' + (h1 ? window.getComputedStyle(h1).fontFamily : 'none'),
  'body: ' + window.getComputedStyle(document.body).fontFamily
].join('\n')
```
This returns the actual computed font families (e.g. `Satoshi`, `Assistant`). Use these directly.

**Fonts — fallback (if Chrome extension unavailable):**
1. `<link>` tags pointing to Google Fonts — extract the family name from the URL
2. `@import` rules in `<style>` tags pointing to fonts.googleapis.com
3. CSS `font-family` on `:root`, `body`, or CSS custom properties
4. Any `@font-face` declarations

If the font is on Google Fonts, load via `<link href="https://fonts.googleapis.com/css2?family=...">`. If it's on Fontshare (e.g. Satoshi, Cabinet Grotesk), use `<link href="https://api.fontshare.com/v2/css?f[]=[name]@900,700,500,400&display=swap">`. Default to **Inter** if nothing is found.

**Colors — look for in this order:**
1. CSS custom properties (`:root` variables) — look for `--bg`, `--accent`, `--white`, `--primary`, `--color`, etc.
2. `background` and `color` on `body`
3. Button background colors (`.btn`, `.cta`, `button`)
4. Any hex codes or `rgb()` values in `<style>` tags

Extract:
- Background color (used as insert background)
- Accent / brand color (used for QR code, highlights, key word in hook)
- Text color (used for body text)
- Font family name + weights

If extraction fails, use safe defaults: white bg (#ffffff), near-black text (#111111), blue accent (#1696D2), Inter font.

No need to confirm with the user — just proceed. They'll see the colors and font in the preview and can request changes then.

### Step 2 — Product

**First, check if they already have an insert.** Many sellers arrive with one already made (their own, or from a previous tool). Ask: *"Do you already have a product insert? If so, drop it in (the file or a photo) and tell me what you want to do: (a) **optimize it**, I'll critique the hook, offer, CTA, and QR against what works and rebuild a stronger version, or (b) **keep it and build a complementary/additional one** (e.g. a second variant to A/B test, or an insert for another product). If you're starting fresh, just say so and we'll build from scratch."*
- **Optimize:** read their insert, point out concrete weaknesses (weak hook, multiple CTAs, no value offer, low-contrast QR, TOS risks), then rebuild it stronger using the steps below. Keep what already works.
- **Build more:** treat their existing insert as the reference for brand/voice/style and build the new one to match.
- **From scratch:** continue normally.
Either way, the goal is to improve or extend what they have, not ignore it.

**Check the brand book first.** If `brand-book.md` has a `product_nickname` (and often an `asin`) from Day 0's first-run pick, use it: *"We're building this for your [product_nickname], right? Give me a one-line description of what it does so I nail the hook."* Only ask which product from scratch if there's no nickname recorded. Carry the nickname through as the label for this run's outputs.

Otherwise ask: "What product is this insert going into? Give me a one-line description of what it does."

### Step 3 — Insert size
Ask: "What size is your insert or label?"
- A) **Label / tiny** — supplement bottle, small packaging (fits 3"×1.5" or smaller)
- B) 4" × 4"
- C) 4" × 6" *(most common Amazon insert)*
- D) 5" × 7"
- E) Custom

Map to px at 150dpi: label=450×225, 4×4=600×600, 4×6=600×900, 5×7=750×1050

**If A (label/tiny):** skip Steps 4–7 entirely. Go straight to Step 3b, then hook research (Step 5).

### Step 3b — Existing label/packaging (label flow only)

**For supplement bottles and small packaging, the QR always goes on the BACK label — never the front.** The front is reserved for the brand, product name, and required claims. The back typically has some open space near the bottom (below the "about" copy and above or around the social handles / website URL).

**Step 1 — Try to find their back label automatically:**
Before asking the user to upload anything, attempt to locate the product's back label yourself:
1. Search their brand website product page for gallery images (JavaScript `document.querySelectorAll('img')` via the Chrome extension, sorted by natural width)
2. Search their Amazon listing (search `site:amazon.com [brand] [product]`, open the ASIN page, extract gallery image URLs via JS, download full-res versions)
3. Download candidate images and read them visually — identify which one shows the **back panel** (look for: supplement facts table, ingredient list, "Distributed by", social handles, website URL, legal disclaimer)
4. If a clear back label is found, confirm with the user: *"I found your back label — I'll place the QR in the lower section. Want to use this, or upload a higher-res flat artwork instead?"*

**Step 2 — If not found, ask the user:**
> "I couldn't locate your back label automatically. Please drop in your label artwork — even a photo of the bottle back works. I'll place the hook + QR in the lower section and avoid covering the supplement facts or required claims."

**Overlay rules (applies whether found automatically or uploaded):**
- Always target the **back label**, lower section — below the brand description copy, above or replacing the existing social/website CTA
- Never cover: Supplement Facts panel, ingredient list, allergen statements, net weight, "Distributed by" block, barcode, or any FDA-required text
- Safe zones: bottom-center of back panel (over social handles / existing website URL), or lower-right corner if the back panel has a clear column
- Use the uploaded/found image as the full background of the HTML canvas at its native aspect ratio
- Overlay a clean QR block in the identified safe zone:
  - White pill/rectangle, opacity 0.93–0.96, border-radius 8–10px
  - Inside (top to bottom): hook text (bold, fits zone width) → thin divider → QR code centered → URL below
  - Match type weight and padding to the existing label's visual style
  - The overlay should feel *intentionally placed*, not dropped on — size it so it fits the zone without crowding
- Dimensions: match the label image's pixel dimensions (or scale to print resolution)
- Note to user: *"This is a placement preview. For final print, apply this overlay to your flat label artwork/dieline at full resolution."*

**If user has no existing label and no packaging yet:**
- Generate the minimal standalone label: hook + QR + URL on a brand-colored background (as before)

### Step 4 — Offer type (skip for labels)
Ask: "What should your insert offer? Or let me decide based on your product."
- A) **Let AI decide** — research what works best for this product category
- B) Warranty
- C) How-to video
- D) Tips & tricks
- E) Recipe / companion content
- F) Custom

If A: use WebSearch to find high-view YouTube content and popular articles for the product category. Recommend one offer type with a one-line rationale. Confirm before proceeding.

**Scan rate expectations by hook type (always factor this into the "Let AI decide" recommendation).** The expected percentage of customers who actually scan the insert varies enormously by hook type, sometimes 3 to 5x, and that multiplier compounds through the whole funnel. Factor it in before recommending:

- **"How to install / set up / use" video hook** — the single highest-scan hook type for products with any setup, assembly, calibration, or learning curve (fitness equipment, electronics, tools, appliances, kitchen gadgets, anything that ships in pieces or requires a technique). The customer just opened the box and is actively curious about how to use it right. That motivation is as strong as it gets. Expected scan rate: 15 to 25%+ for products with real setup complexity. The more uncertain a customer might feel without help, the higher the rate. This is the default recommendation for any product in this tier.
- **Warranty activation hook** — strong for high-ticket products ($100+) where the customer has a financial stake. Expected: 10 to 18% for high-ticket, 5 to 10% mid-range. Drops toward 3 to 5% for low-cost items where customers do not feel the warranty is worth the registration step.
- **Tips and tricks / secret guide hook** — solid across most categories, especially where there is a clear "right way" vs "wrong way" to get results. Expected: 7 to 12%.
- **Recipe / companion content hook** — high for consumables, food, and kitchen products where the experience IS the product. Expected: 8 to 15% for food and drink categories.
- **Discount / coupon hook** — moderate to high depending on perceived value. A meaningful discount (15%+) on a repeat-purchase product can pull 10 to 20%. Small discounts on one-time purchases land closer to 5%.
- **Generic "scan for more info" or "visit our website"** — lowest of all. Without a specific compelling reason, most customers do not bother. Expected: 2 to 5%. Never recommend this.

**When running "Let AI decide," factor in BOTH the product category benchmark AND the hook type scan lift.** The goal is total opt-ins, which equals scan rate times opt-in rate times units sold. A video hook at 20% scan rate and 35% opt-in rate (7% total) beats a warranty hook at 10% scans and 50% opt-in rate (5% total). Run this math explicitly when it is a close call.

Routing by product type:
- **Products with any setup, assembly, or technique involved** (fitness equipment, electronics, tools, appliances, kitchen gadgets, power tools, exercise machines, instruments): recommend the "how to use" video hook. It is the most natural reason to scan, it delivers immediate value, and it feeds the funnel at the highest possible rate. Lead with this recommendation strongly.
- **High-ticket durable goods with no meaningful setup** (luggage, fashion accessories, home decor, jewelry): warranty hook is the natural fit. The customer has a financial stake but no setup anxiety.
- **Consumables and food products** where the experience is what matters (supplements, coffee, snacks, cooking ingredients, protein powder): recipe or tips hook. These customers want ideas, not instructions.
- **Mid-ticket products in any category**: if the product has any video tutorial potential at all, the "how to use" video hook will almost always lift scan rate from the 5 to 8% range into 12 to 18%. Default to video unless there is a clear reason not to.

When presenting the recommendation to the seller, include the expected scan rate: *"For a fitness accessory like yours, a setup video hook typically drives 15 to 20% scan rates versus 7 to 12% for a warranty hook, because customers who just bought it are actively looking for how to use it well. That gap adds up across every print run."*

### Step 4b — Hero visual (skip for labels)

Ask the seller:

> "Would you like a **hero image or icon** as the central visual on your insert — like a product illustration, a lifestyle icon, or a branded graphic?
>
> **Why this matters:** If I design the graphic myself, I'll build it from SVG shapes. It'll look clean and on-brand, but probably won't be as specific or polished as an icon made for your exact product.
>
> A good example: a sleeping bag insert works much better with a sleeping bag icon than with generic shapes.
>
> **Best source: [Flaticon](https://www.flaticon.com)** — search your product (e.g. "sleeping bag", "protein shaker", "yoga mat", "air fryer"), pick a flat or outline style, download as **PNG with transparent background**, and drag it into this conversation.
>
> Other good sources: [Noun Project](https://thenounproject.com), [Icons8](https://icons8.com).
>
> Note: not every product has a good icon match — a supplement pill may, a custom gadget may not. Use your judgment.
>
> - **A) I'll drop in an image or icon now**
> - **B) Let Claude design the graphic** — I'll build SVG icons in your brand colors"

**If A (user provides an image):**
- If they drag/paste it into the conversation, it's already available as a vision input — read it and embed as base64.
- If they give a file path, use the Read tool to load the file as base64.
- Embed it in the HTML as `<img src="data:image/[type];base64,...">`.
- Use it as the **central hero visual** — place it prominently in the middle section (centered alone, or right side with feature labels stacked on the left, whichever fits the image's aspect ratio).
- Size it to 150–220px tall and keep it centered or right-aligned depending on layout.
- Do NOT apply CSS color filters — use the image as-is.

**If B (let Claude design):**
- Continue with SVG icon cards as normal. Tell the user: "I'll design SVG icons — they'll be clean and on-brand, but more generic than a custom icon."

### Step 5 — Hook research

Run all three searches in parallel, then synthesize into 4 hooks.

#### A) YouTube view counts (Chrome extension preferred, WebSearch fallback)

**If the Chrome extension is available:** navigate to each URL below using the extension (`mcp__Claude_in_Chrome__navigate` + `mcp__Claude_in_Chrome__get_page_text`) — it returns real on-page view counts, which WebSearch does not.

**If the Chrome extension is NOT available (most desktop-app testers):** do NOT skip hook research. Fall back to WebSearch + WebFetch:
- Search `[product keywords] youtube most viewed`, `best [product keywords] video`, `[product keywords] how to tips`.
- Use the returned video titles to ground hooks. When a view count isn't visible, cite the title + "top-ranked result" instead of a fabricated number — never invent a view count.
- Supplement with the Article-headline search in section B, which already uses WebSearch, so you always have grounded hooks even with no extension at all.

Search 1 — general videos:
`https://www.youtube.com/results?search_query=[product+keywords]`

Search 2 — Shorts only (highest-view short-form):
`https://www.youtube.com/results?search_query=[product+keywords]&sp=CAASAhAB`

Search 3 — usage/how-to angle:
`https://www.youtube.com/results?search_query=[product+keywords]+tips+setup+how+to`

From `get_page_text` output, extract every video title + view count visible on the page. Look for patterns like "9.9M views", "1.4M views", "662K views". Record the top 6–8 by view count with their exact titles.

#### B) Article headlines (WebSearch)

Run these searches and record the article titles returned:
- `"[product category]" tips guide most popular article 2024 2025`
- `"[product keywords]" viral article headline site:wirecutter.com OR site:theverge.com OR site:packhacker.com OR site:buzzfeed.com`
- `best "[product keywords]" reddit upvoted`

For any promising article titles, fetch the page with WebFetch to confirm the headline and check if it has social share counts or strong SEO ranking signals.

#### C) Synthesize into 4 hooks

Map the top-performing titles into insert hooks. For each hook, cite the source (video title + view count, or article title + site).

**Hook rules:**
- Hooks must make the customer want to scan/visit — they already bought the product. No selling, only curiosity.
- Each hook must be rooted in a real piece of content with a verified view count or strong traffic signal. No invented hooks.
- For labels: hook = 3–6 words max. It's the only copy on the label. Make it impossible to ignore.
- Examples of strong label hooks: "The secret most users miss." / "You're only getting half of it." / "One thing changes everything."

Present the 4 hooks in this format:
> **A)** *"Hook text here"*
> ↳ Rooted in: "Exact YouTube title" — X.XM views / or "Article title" — [site]

Wait for user to pick one.

### Step 6 — Time / duration input (skip for labels)
- If video offer: "How long is the video?"
- If warranty: "How many years is the warranty?"
- If tips: "How many tips are in the guide?"

### Step 7 — Support line (skip for labels)
Ask: "Would you like to add a support contact on the insert? (optional)"
- Email only
- Phone only
- Both
- Skip

### Step 8 — URL slug + tracking tags (the QR encodes ALL of this)

The QR code must encode a URL with two tracking tags baked in, because Day 5's dashboard reads them straight from Shopify Analytics: `utm_campaign` tells it **which product**, `utm_content` tells it **which insert variant**. Build the full URL automatically; the seller never has to understand UTMs.

1. **Base slug:** `[brand-domain]/[product-slug]-[offer-type]` (e.g. `getmessless.com/alpha-charge-setup`). Let the user confirm or change the slug.
2. **Append the tags automatically:** `?utm_campaign=<product-slug>&utm_content=insert-a`
   - `utm_campaign` = the product slug (so each product gets its own; this is the "one link per product" rule).
   - `utm_content` = the insert variant, **always lowercase, exactly `insert-a` / `insert-b` / `insert-c`**. The FIRST insert is `insert-a` by default (never leave it untagged), additional variants get `insert-b`, `insert-c`.

So the **exact URL the QR encodes** for a first insert looks like:
`https://getmessless.com/alpha-charge-setup?utm_campaign=alpha-charge&utm_content=insert-a`

Tell the seller in one plain line: *"I've added a hidden tracking tag to your link so the Day 5 dashboard can automatically tell you which product and which insert drove each scan and sign-up. You don't have to do anything with it."* Set `url` to this full tagged string before generating the QR in Step 9.

**⚠️ Do not send the insert to print until the whole funnel is live at this exact URL.** The link and QR are locked the moment the insert prints, there is no changing them for that print run, so this slug is a promise the rest of the week has to keep. The Day 2 landing page (and ideally the full funnel through Day 4) must be deployed and tested at this exact URL **before** anything goes to the printer: decide the slug here, build and deploy to it, confirm it resolves, then print. If the seller has already printed, the deployed page must match what was printed (Day 2 handles that with a redirect). This gate is restated at export (Step 11).

**One link / QR code per product (always prefer this).** The strong default is a **unique link and QR per product**, even when you reuse the exact same insert hook across several products. The payoff is huge and free: when a customer opts in, you already know **which product they bought** from the link alone, no need to ask them. That clean attribution powers the Day 5 dashboard (per-product performance) and lets every later step (offer, emails) be product-aware. Make this recommendation explicitly, and give each product its own `utm_campaign=<product-slug>` (built into the URL in Step 8, always present, never optional).

**When the seller reuses ONE link/QR across multiple products or marketplaces.** Some sellers print one shared insert. If they do, whether you need to ask the customer anything depends entirely on **what they're offering next**:
- **If the next step doesn't care who/what** (e.g. a pure warranty activation, or a generic newsletter signup), don't add any friction, just capture name + email as normal.
- **If the next step IS product- or region-specific** (a product-specific warranty, a complementary-product upsell, region-specific shipping or offers), then the landing page needs to learn the missing piece. Flag this now and carry it to Day 2: the capture form may add a small **"Which product did you buy?"** dropdown, or a **country/marketplace** selector, so the seller can segment. Keep it to the single field that the next step actually needs (every extra field costs conversion).
- **Multiple marketplaces / countries (same inserts):** same logic. If they sell the same product across Amazon US / UK / DE etc. on one insert and the downstream offer differs by region, they may want to validate marketplace at opt-in (a country field, or separate links per marketplace). If the offer is identical everywhere (e.g. warranty only), they don't need it.

Note which case applies and pass it forward, Day 2 builds the form, Day 3 builds the offer, both need to know whether product/marketplace must be captured. **But always restate the cleaner path first:** one link/QR per product (and per marketplace where it matters) removes the question entirely.

**Build to test (forward-reference to Day 5) — this is the most important place in the whole challenge to make more than one version.** The insert is the slowest thing to change (it's printed and shipped, so a weak one is locked in for a whole print run), which makes it the highest-leverage thing to test. Strongly encourage making **2 or even 3 insert variants** now, each with the same destination but a different `utm_content` (always lowercase: `insert-a` for the first, then `insert-b` / `insert-c`), so they can be split across the next print run or across SKUs. The lowercase `insert-a/b/c` form is load-bearing, the Day 5 dashboard matches on it exactly. Two ways to set the test up:
- **Same landing page, different hooks** — tests which insert *message* makes more people scan and opt in.
- **Different landing pages** — each insert points to its own page (tests the insert + page as a pair).

On **Day 5** the dashboard compares scan-to-opt-in per insert and names the winner. Frame it as: "everything else this week you can tweak in minutes, but the insert takes a print run to change, so this is the one to test 2 to 3 versions of from the start." Encourage strongly, but never hard-block, if they only want one, that's their call.

### Step 9 — Generate insert

#### Standard insert layout (4×4, 4×6, 5×7)

**Every insert must feel unique. Do NOT default to 3 cards every time.** Choose a middle section layout based on the product, offer type, and number of genuine points to make. If only 2 things matter, show 2. If 4 are equally strong, show 4. If one idea is so powerful it deserves the whole space, go full-width. Variety across inserts is intentional — no two should look the same.

**Middle section layout options — pick the best fit:**

- **2 cards** — when there are exactly 2 distinct steps or benefits. Cards are wider, icons larger, more breathing room. Best for simple/clean brands.
- **3 cards** — balanced, works for 3 clear steps or tips. Most common but not the default.
- **4 cards** (2×2 grid) — when 4 ideas are equally important. Compact icons, short labels. Good for feature-rich products.
- **1 large hero block** — one bold insight, massive icon centered, single line of text. Hits hardest when the hook and the single point reinforce each other.
- **Numbered steps** (vertical list, 2–4 items) — icon + number + short label on one row each. Best for sequential processes ("Step 1 / Step 2 / Step 3").
- **Split layout** — hero image/icon on one side, stacked feature labels on the other. **Use this when the seller provided a custom icon (Step 4b option A)** — the icon goes right, labels go left (or vice versa). The icon should be 150–220px tall. Good for products where one strong visual carries the message.

Choose based on content fit, not habit. The structure is:
1. **Top group** (flex-shrink: 0): Logo + hook (large, bold). No insert title.
2. **Spacer** (flex: 1): absorbs space between hook and middle section
3. **Middle section** (flex: 0 0 auto): chosen layout above
4. **Bottom group** (flex-shrink: 0): divider + CTA

**Watch CTA block — brand-specific, never generic:**

Do NOT use a standard pill badge ("▶ WATCH — 2 MINUTES"). Instead, design a watch CTA that reflects the brand's visual style. Pull cues from the brand website (shape, color application, typography weight).

The CTA must be a full-width block with 3 distinct zones:
```
[ PLAY ZONE | LABEL + SUBTITLE | TIME ]
```
- **Play zone** (left, ~70px wide): filled with brand accent color, centered play triangle in bg color
- **Label zone** (center, flex): "Watch Now" in accent color (Satoshi/brand font, uppercase, tracked) + subtitle = offer title in muted white (Assistant/body font, 14px)
- **Time zone** (right, ~60px): bold large number stacked over the unit word — "90" over "SECONDS", "2" over "MINUTES". Never abbreviated (no "90s" or "2min") — stacked two-line is always clearer.

Border: 1.5px solid brand accent. Background: accent at 5–8% opacity. Border-radius: 12–16px.

Adapt visual style to brand aesthetic:
- Minimal/dark brand (e.g. Velomix): sharp rectangle, solid accent fill on play zone, white time
- Colorful/playful brand: softer corners, gradient border, colored time
- Premium/luxury brand: thin border, subtle glow, serif or light-weight time text

Then QR code centered below (160×160px min), URL below QR, support email below URL.

The QR code is ALWAYS centered. Never side-by-side with text.

#### Label layout (tiny / supplement)

**Structure (single centered column, full height):**
```
[LOGO]          ← small, top center
[HOOK]          ← 3–6 words, very large, bold, fills width
[QR CODE]       ← centered, as large as space allows
[brand.com/slug] ← centered, small
```
No feature cards. No subline. No support line. No extra copy. The hook IS the message — it must be strong enough to make someone scan on its own.

**Design rules (all formats):**
- Background: brand bg color extracted from site (default #000)
- Accent: brand accent color extracted from site (default #1696D2)
- Font: brand font extracted from site — load via Google Fonts `<link>` tag with correct weights (default Inter 400/600/700/900)
- If the brand uses a self-hosted or non-Google font that can't be loaded via CDN, fall back to the closest Google Fonts match
- Top accent line: 3px gradient across top edge
- Insert title: 14px, muted (45% opacity), plain weight — NOT the hook
- Hook: large (52px+ for standard, fills width for label), 900 weight, accent color on key word
- **Minimum font size for ALL text: 14px. No exceptions. Support email minimum 12px.**
- QR code: generated via Python `qrcode` library, brand accent color on white background, always centered
- URL: always centered directly below QR code, 14px minimum
- Support line: 12px, very muted (20% opacity), never more than one line

**Card/feature block rules:**
- Use **inline SVG icons**, NOT system emojis. Emojis render differently across devices and look inconsistent in print. SVGs are crisp, brand-colored, and reliable.
- Each icon: 44×44px inline SVG, drawn in **brand accent color only** (1–2 colors max: accent + accent at low opacity for fills). No gradients, no shadows.
- Icon style should match brand aesthetic — geometric for minimal brands, rounded for playful, thin-stroke for premium.
- Card label: 2–3 words max, 14px minimum, bold. No descriptors — the icon IS the message.
- Fewer words = stronger insert

**SVG icon pattern (use for every card):**
```html
<svg width="44" height="44" viewBox="0 0 44 44" fill="none" xmlns="http://www.w3.org/2000/svg">
  <!-- strokes and fills use brand accent color, e.g. stroke="#0ABFBC" fill="#0ABFBC" fill-opacity="0.15" -->
</svg>
```
Design the icon paths to clearly represent the concept: a water drop for liquid, circular arrows for mixing, a timer/clock for timing, a shield for warranty, a lightbulb for tips, etc.

**After generating the HTML, go directly to Step 10 (preview). Do not export PDF or PNG until the user approves the design.**

**Python QR generation:**
```python
import qrcode, base64
from io import BytesIO
# url MUST be the full tagged URL from Step 8, e.g.
# "https://getmessless.com/alpha-charge-setup?utm_campaign=alpha-charge&utm_content=insert-a"
qr = qrcode.QRCode(version=2, error_correction=qrcode.constants.ERROR_CORRECT_H, box_size=12, border=3)
qr.add_data(url)
qr.make(fit=True)  # auto-bumps version so the longer tagged URL still fits
img = qr.make_image(fill_color=accent_color, back_color='white')
buf = BytesIO()
img.save(buf, format='PNG')
qr_b64 = base64.b64encode(buf.getvalue()).decode()
```

**Puppeteer export:**
```js
const puppeteer = require('puppeteer');
const browser = await puppeteer.launch({args: ['--no-sandbox']});
const page = await browser.newPage();
await page.setViewport({width: W, height: H, deviceScaleFactor: 2});
await page.goto('http://localhost:PORT', {waitUntil: 'networkidle0'});
await new Promise(r => setTimeout(r, 1200));
await page.screenshot({path: pngPath, clip: {x:0, y:0, width: W, height: H}});
await page.pdf({path: pdfPath, width: `${W_IN}in`, height: `${H_IN}in`, printBackground: true, margin: {top:0,bottom:0,left:0,right:0}});
await browser.close();
```

**System-Chrome export (no npm install — use when Puppeteer isn't installed).**
Find an installed Chromium browser and render headlessly. Requires the `@page` print CSS above for an exact-size PDF.
```bash
# Pick the first browser that exists:
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
[ -x "$CHROME" ] || CHROME="/Applications/Chromium.app/Contents/MacOS/Chromium"
[ -x "$CHROME" ] || CHROME="/Applications/Brave Browser.app/Contents/MacOS/Brave Browser"
[ -x "$CHROME" ] || CHROME="/Applications/Microsoft Edge.app/Contents/MacOS/Microsoft Edge"

URL="http://localhost:7771/[brand]-insert.html"
# PDF — exact size comes from the @page rule in the HTML:
"$CHROME" --headless=new --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="./[brand]-insert.pdf" "$URL"
# PNG — 2× retina at exact insert pixels:
"$CHROME" --headless=new --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=2 --window-size=W,H \
  --screenshot="./[brand]-insert.png" "$URL"
```
Claude runs this itself and saves both files — the user does nothing.

**Preview page wrapper (always include this in the HTML file):**
The HTML file served for preview must wrap the insert in a scaling container so it fits any screen without scrolling. The insert itself stays at exact print dimensions — only the outer wrapper scales it down for the browser preview.

```html
<!-- OUTSIDE the insert div, wrapping it: -->
<style>
  html, body {
    margin: 0; padding: 0;
    width: 100%; height: 100vh;
    background: #2a2a2a;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
  }
  .preview-scaler {
    transform-origin: center center;
    /* JS sets the scale dynamically to fit viewport */
  }

  /* Print CSS — makes browser/headless Print-to-PDF come out at EXACT insert size, no margins.
     Substitute the real inch dimensions for the chosen size (e.g. 4in 6in). */
  @page { size: [W_IN]in [H_IN]in; margin: 0; }
  @media print {
    html, body { background: #fff; width: auto; height: auto; overflow: visible; display: block; }
    .preview-scaler { transform: none !important; }  /* print true size, not the scaled-down preview */
  }
</style>
<div class="preview-scaler">
  <div class="insert"> ... </div>
</div>
<script>
  function fitToScreen() {
    const el = document.querySelector('.preview-scaler');
    const insert = document.querySelector('.insert');
    const scaleX = (window.innerWidth - 40) / insert.offsetWidth;
    const scaleY = (window.innerHeight - 40) / insert.offsetHeight;
    const scale = Math.min(scaleX, scaleY, 1);
    el.style.transform = 'scale(' + scale + ')';
  }
  fitToScreen();
  window.addEventListener('resize', fitToScreen);
</script>
```

This ensures:
1. The insert is always centered in the browser window
2. The full insert is always visible — scaled down to fit if the screen is smaller
3. The actual insert dimensions are unchanged for the PDF/PNG export

Serve the HTML via `python3 -m http.server 7771 --directory "$(pwd)" &` before launching Puppeteer. **IMPORTANT: Always serve from the current working directory (`$(pwd)`), never from `/tmp` — serving `/tmp` on all network interfaces is blocked by the security policy.**

### Step 10 — Preview BEFORE export (mandatory)

**Do NOT export PDF or PNG yet.**

0. **Dash sweep (mandatory, first).** The insert HTML must contain zero em dashes (—) and zero en dashes (–). Run `grep -n "—\|–\|&mdash;\|&ndash;" [brand]-insert*.html` (include the HTML entities and any `-b` variant files). If it prints anything, rewrite each into a comma, colon, period, parentheses, or "to", and re-run until it returns nothing. Then continue.
1. Save the HTML to `[brand]-insert.html` in the **current working directory** (NOT `/tmp`)
2. Start a local server: `python3 -m http.server 7771 --directory "$(pwd)" &` (use port 7771; serving `/tmp` on all interfaces is blocked by security policy)
3. **Primary preview = headless screenshot (no Chrome extension needed).** Take a PNG screenshot at exact insert dimensions into the current working directory using the same tooling ladder as Step 11 — **system Chrome headless `--screenshot` if Puppeteer isn't installed**, so you don't have to wait on an npm install just to preview. Show it inline with the Read tool. This is the preview every user sees — it works in the desktop app with no extension and no install.
4. **Optional, only if the Chrome extension is available:** also open the live page so the user can interact with it:
```js
// mcp__Claude_in_Chrome__tabs_context_mcp — get or create a tab
// mcp__Claude_in_Chrome__navigate — ALWAYS use http://localhost:...
// NEVER use file:// URLs — the Chrome navigate tool auto-prefixes https:// and will break them
navigate to: http://localhost:7771/[brand]-insert.html
```
   If the extension is unavailable, skip this entirely — the Puppeteer screenshot already showed the design. Do NOT tell the user to open the HTML themselves.
5. **Reassure + suggest — don't just ask a flat "any tweaks?"** Most sellers aren't designers and won't know what they're allowed to ask for, so two things:

   **a) Make it clear nothing is final.** Open with a line like: *"This is a first draft, not the final — nothing's locked. Anything here can change in seconds: colors, layout, the icon, the hook, the background. Just say the word."*

   **b) Offer 2–4 concrete, tailored directions** for THIS specific insert, then the export exit. Rules for the suggestions:
   - **Tailored, never a generic menu.** Reference what's actually on the insert you just made (e.g. "right now it's 3 SVG cards on a flat background") and suggest the highest-value upgrades for it. Pick the 2–4 most relevant — do NOT list all of them every time.
   - **Only suggest things this skill can actually do.** Draw from the idea bank below.
   - Keep each one to a single actionable line. End with: *"…or if you love it as-is, say the word and I'll export the PDF + PNG."*

   **Idea bank (pick the 2–4 that best fit this insert):**
   - **Hero icon** — "Grab an icon for [product] from [flaticon.com](https://www.flaticon.com) and drop it in — it'll read sharper than the SVG I drew." (Use this when the insert relies on a generic SVG and a real product icon would land harder.)
   - **Lifestyle image** — "Upload a photo of the product in use and I'll integrate it — as a side panel, a background band, or behind the hook."
   - **Background texture** — "Right now the background is flat; I can add [wavy lines / topo contours / dot grid / botanical outlines] to fit the brand."
   - **Invert the brand colors** — "Keep your exact brand palette but reverse it for the insert — e.g. flip the dark-on-light to light-on-dark (or vice versa) so it pops differently than the website." (Same hex codes, swapped roles — bg ↔ accent/text.)
   - **Middle layout** — "Try a different layout — one bold hero block instead of the cards, or a split layout with the icon on one side."
   - **Alternate hook** — offer one of the other hooks already researched in Step 5, by name.
   - **Watch CTA / accent emphasis** — a different CTA shape or a stronger accent treatment.

When the user requests changes:
- Update the HTML file
- Re-take the Puppeteer screenshot and show the updated PNG inline (and re-navigate the Chrome tab too, if the extension is in use)
- Repeat until approved.

### Step 11 — Final export (only after approval)

Once the user approves the design, generate both files **in the current working directory** (NOT `/tmp` — testers can't find `/tmp`):
1. PNG at `./[brand]-insert.png` (2× deviceScaleFactor)
2. PDF at `./[brand]-insert.pdf` (exact print dimensions, no margins)

**Export is mandatory and automatic — Claude saves the files; HTML is never the deliverable.** Work down this ladder, stopping at the first rung that succeeds. Every rung except the last saves the files for the user with no manual step:

1. **Puppeteer already installed** → use the Puppeteer export above. Exact, fastest path.
2. **System Chrome headless** (no download) → use the System-Chrome export above. A desktop-app user almost always has Google Chrome (or Brave/Edge/Chromium) installed, so this works with zero install. Relies on the `@page` print CSS for exact PDF size.
3. **Install Puppeteer, then retry rung 1** → `cd /tmp && npm install puppeteer` (downloads Chromium, ~1 min — tell the user it's installing, don't give up). If `launch()` fails, retry with `{args: ['--no-sandbox','--disable-setuid-sandbox']}`.
4. **Manual Print-to-PDF (true last resort, only if 1–3 all fail)** → the page is already on the local server, so tell the user: *"Open http://localhost:7771/[brand]-insert.html → Cmd/Ctrl+P → Save as PDF → Margins: None."* Thanks to the `@page` rule it prints at exact insert size. **Never** substitute raw HTML for the PDF.

After export, confirm the two file paths, then **push for a second variant before anything else.**

Say:

> "Your first insert is done. Before we move on - this is the best moment to build a second variant, and it only takes a few minutes.
>
> Here's why it matters: once this goes to print, you're locked in for the whole run. If the hook isn't pulling, you won't know for months, and you can't fix it until your next order. A second variant now means you're testing from day one instead of guessing.
>
> Two ways to go:
>
> **A) Same offer, different hook** - same destination URL with a different `utm_content` tag (e.g. `?utm_content=insert-b`). Tests which message makes more people scan. Easiest to set up - I can generate it now.
>
> **B) Different offer entirely** - a new hook pointing to a completely different landing page and offer (e.g. one insert offers a tips guide, the other offers a warranty). Tests whether the offer itself changes behaviour. More to build, but if you're not sure which offer resonates, this tells you fast.
>
> Which direction, or want me to recommend based on your product?"

- **If A:** generate the second insert now using one of the other hooks from Step 5 research. Save **all three formats** so the dashboard and printer both have what they need: `[brand]-insert-b.html`, `[brand]-insert-b.pdf`, `[brand]-insert-b.png`. Its QR uses the same destination with `utm_content=insert-b`. (The first insert is already `insert-a` from Step 8, never leave it untagged.) Confirm the files.
- **If B:** run through the offer type and hook questions again (Steps 4 and 5) for the new variant. Use a new slug (e.g. `brand.com/product-warranty` vs `brand.com/product-tips`), still with its own `utm_campaign` and `utm_content=insert-b`. Generate, export, and save as `[brand]-insert-b.html` / `.pdf` / `.png`.
- **If they skip:** respect it, but note clearly: *"When you're ready to test, just say 'build insert B' in this session or a new one - all the research is already done."*

Then **gate the printing on the funnel being live.** Do not tell them to print yet. Say: *"One important thing before you print: the QR and link get locked the moment this goes to the printer, there is no changing them for that run. So do not send it to print until your funnel is actually live at the exact URL on this insert. Finish Day 2 (landing page deployed and tested at that URL), ideally the whole funnel through Day 4, and confirm the link resolves. Once it is live and verified, send the **PDF** to your print shop / Amazon insert printer (exact print size, 40px safe margin). The PNG is for quick previews and digital use."*

**End-of-day handoff (always output this after the print gate).** Output a clearly marked copy-paste block for starting Day 2 in a fresh session:

> "Day 1 is done. Here's how to start Day 2:
>
> 1. Go to Circle and watch the Day 2 video
> 2. Download the **Day 2 skill file** from the lesson
> 3. Open a **fresh Claude Code session**
> 4. Paste the prompt below and drag the Day 2 file into the window before hitting enter"

```
cd [output of pwd]
```
Then in the new session (paste this + drag the Day 2 skill file):
```
I'm running the 5 Day Customer Challenge. My insert is done for [product_nickname]. The URL on the insert is [brand.com/slug]. I've dragged in the Day 2 skill file. Install it and run Day 2 — the landing page.
```

## Design principles (always apply)

**Print safe zone — mandatory:**
- Minimum **40px padding on all four sides** of the insert. No text, QR code, logo, or design elements within this margin. Print shops cut on or near the edge — anything in the last 40px risks being trimmed off.
- The bottom edge is especially vulnerable. Keep the support email and any bottom content at least 50px from the bottom edge.
- Decorative background elements (topo lines, patterns) may extend to the edges — they're non-critical. Only content elements must respect the 40px safe zone.

**Background texture — always add one:**
Never leave the background as a single flat color. Every insert must have a subtle background texture or pattern that fits the brand. Choose based on brand personality and product category:
- **Outdoor / adventure / camping** → topographic contour lines (wavy horizontal paths at low opacity, with occasional enclosed "island" ovals — like a topo map)
- **Tech / electronics / gadgets** → subtle dot grid or circuit-trace lines (thin straight lines with small junction dots)
- **Food / beverage / supplements** → soft radial or diagonal wave lines (organic, flowing)
- **Beauty / skincare / wellness** → delicate botanical outlines (leaf or petal silhouettes at very low opacity)
- **Sports / fitness** → diagonal speed lines or a subtle crosshatch grid
- **Premium / luxury / minimal** → very fine grain (tiny dot scatter at 3–5% opacity) or a barely-visible diagonal line pattern
- **Default (unknown brand)** → gentle horizontal wave lines at low opacity

Implementation: add an absolutely-positioned SVG as the first child inside the insert div (`position:absolute; top:0; left:0; width:100%; height:100%; pointer-events:none`). Draw the texture in a color slightly lighter or darker than the background — never in the accent color. Opacity of individual strokes: 0.05–0.15. The texture must be subtle enough that it's invisible at a glance but adds depth up close.

Also add a radial gradient vignette to the background CSS:
```css
background:
  radial-gradient(ellipse 75% 55% at 50% 32%, rgba(255,255,255,0.04) 0%, transparent 100%),
  radial-gradient(ellipse 100% 100% at 50% 100%, rgba(0,0,0,0.25) 0%, transparent 60%),
  [brand-bg-color];
```

- Insert title = what the insert is about ("Alpha Charge Setup Guide") — never "Thank you for your purchase"
- Hook = curiosity gap, not a sales pitch — they already bought
- Time expectation always visible on standard inserts ("2-min video", "Register in 20 sec", "2-year warranty")
- Support line is optional, visually minimal, and must NEVER feel like a prominent feature. It should look like fine print — small, muted, almost an afterthought. If it draws the eye at all, it's too big. The insert is about curiosity and value, not customer service. Default font size: 11px, opacity: 20%. If the user skips it, don't add it.
- **QR code is always centered and is the primary visual focus of the CTA section**
- **URL is always centered directly below the QR code**
- QR code + URL always together — one is for phones, one is for laptops
- Logo from brand website, not text — fetch and embed as base64. If unreachable, ask user to upload. Never improvise a logo.
- Logo `<img>` must use `align-self:flex-start` and never `max-width:100%` — flex containers stretch images unless explicitly told not to
- Labels = hook + QR + URL only. Every extra word dilutes the hook.
- Minimum 14px for all text (12px for support email). If text can't fit at that size, cut the text — not the size.
- Cards use inline SVG icons in brand accent color — never system emojis (inconsistent across devices/print).
- Fewer words always wins. If in doubt, remove it.

## Notes
- Run in Claude Code (requires file system, bash, Chrome extension)
- Recommended model: **claude-sonnet-4-6** (checked at Step 0)
- Install dependencies if missing: `pip3 install "qrcode[pil]" Pillow` and `npm install puppeteer` in /tmp
- If brand logo not found at site root, try /logo.png, /logo-white.png, /assets/logo.png. If still not found (SSL errors, 404s, inaccessible site), **ask the user to upload it** — never invent or recreate the logo as SVG: *"I couldn't find your logo automatically. Can you drop the logo file into the chat or tell me the file path?"*
- Logo images must always render at natural aspect ratio. When embedding in HTML, always use `align-self:flex-start` (or `display:inline-block` wrapping) to prevent flex container stretching. Never use `max-width:100%` on a logo `<img>` tag — this allows the container to stretch the image horizontally while height stays fixed, distorting proportions. Safe pattern: `style="height:Xpx; width:auto; display:block; align-self:flex-start;"`
