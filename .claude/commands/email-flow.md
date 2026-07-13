# Email Flow Creator

Build the 7-email post-opt-in sequence that runs the relationship on autopilot. This is Day 4 of the funnel: the customer scanned the insert (Day 1), landed and gave their email (Day 2), saw the post-opt-in offer (Day 3). Now the emails take over. The sequence welcomes them, gets them using the product, builds connection, asks for a review at the right moment, educates, proves the product with other customers, and finally makes the next offer. Same brand voice and audience as everything else this week. The seller picks plain-text or designed HTML; the skill writes every email and gives paste-ready setup steps for their email platform.

> ⛔ **HARD RULE — NO DASHES IN ANY OUTPUT.** Never use an em dash (—) or en dash (–) in anything the user sees or keeps: not in subject lines, not in preview text, not in body copy, not in the HTML, not in chat, not in titles, not in number ranges. Use a comma, colon, period, parentheses, or "to" for ranges. This applies even though THIS file contains dashes; the rule governs what you produce, not what you read. Step 8 runs a mechanical sweep to catch any that slip through.

---

## Step 0 — Install this skill

The user dragged this file into Claude Code to start the day. Before anything else:

1. Run `mkdir -p .claude/commands` to ensure the folder exists
2. Use the Write tool to save this skill's full content to `.claude/commands/email-flow.md` in the current working directory — so future sessions in this folder can run `/email-flow` without needing to drag the file again

Confirm in one line: *"Day 4 skill installed. Let's build your email sequence."* Then continue to Step 1.

---

## Flow

### Step 0 — Model + environment check

**Model:** check which model is active. If it's already one of **claude-sonnet-4-6**, **claude-opus-4-7**, or **claude-opus-4-8**, skip this silently — they all work great here.

Otherwise, say:

> "For best results (sequence strategy, copywriting, brand matching), I recommend **Claude Sonnet 4.6**, or if you prefer **Opus 4.7 / 4.8**, those work great too. You can change it with `/model` or from the model selector. Want to switch before we start?"

**Environment:** silently confirm the tools this skill needs:
- **bash + Python 3** — required only if the seller picks designed HTML (local preview server). If unavailable, you can still deliver plain-text emails and a copy-paste doc. Don't block.
- **Chrome extension (`mcp__Claude_in_Chrome__*`)** — used for HTML email preview screenshots. OPTIONAL. If a `mcp__Claude_in_Chrome__*` call errors or the tool is absent, treat it as unavailable for the whole session and screenshot via the Puppeteer fallback or just show the rendered file. Never block on it.

**Auto-accept (smoother run):** if the bottom of your Claude Code window doesn't show **auto-accept edits**, press **Shift+Tab** to turn it on so you're not approving every file save. Day 0 sets this up for the challenge folder; this is just a nudge if you're not already on it.

Then go to Step 1.

---

### Step 1 — Read the context (silent, no questions)

The emails are the last link in a chain the seller has already built this week. Read ALL of these files before asking anything. What you extract here drives every email in the sequence, not just #1.

1. **`brand-book.md` (Day 0 output)** — read the full `BRAND PROFILE` YAML block: `brand_name`, `one_liner`, `colors`, `fonts`, `logo`, `voice`, `audience`, `email_platform`, `klaviyo_list_id`, `klaviyo_public_key`, `product_nickname`, `asin`. The **voice** and **audience** drive every word you write. If `email_platform` is set, use it in Step 5 instead of re-asking. Load the logo from `logo:` if set (used in HTML headers). Confirm in one line: *"Found your brand book, writing the whole sequence in your brand voice for your audience."*

2. **The Day 1 insert** (`*-insert.html` or similar) — extract: the exact hook/headline that got them to scan the QR code. Email #1 must pay off that specific promise, not a generic welcome.

3. **The Day 2 landing page** (`*-warranty.html`, `*-video.html`, `*-download.html`, or similar) — extract:
   - **What hook they opted in for** (warranty activation, video, lead magnet/guide, tips). Email #1 delivers this specific thing — warranty confirmation, the guide link, the tips, the video link — never a generic "welcome."
   - **The exact wording of the opt-in promise** (e.g. "Get your free Restore Guide" or "Activate your 2-year warranty"). This is what the subscriber expects to receive first.

4. **The Day 3 post-opt-in page** (`*-welcome.html`, `*-video.html` with offer block, or similar) — this is critical. Extract:
   - **Did the seller build an OTO?** If yes: what was the offer (discount, bundle, free product), what was the exact price/discount shown, what was the CTA URL, was there a countdown timer?
   - **Did they build a VIP club?** If yes: what is the club name, what platform (WhatsApp, Facebook, Discord, etc.), what is the join URL?
   - **Was it both (stacked OTO + club)?**
   - **Does the page fire a Klaviyo `took_offer` tag?** (Look for the `onOfferClaim` function built in Day 3.) If yes, the `took_offer: true` profile property is what determines which subscribers go through Flow 2.

   **This context shapes the entire sequence:**
   - **Email #1** (Flow 1): delivers the Day 2 promise. Does NOT mention the Day 3 offer (they may not have taken it yet when Email #1 fires — they go to the Day 3 page AFTER opting in but Email #1 fires at the same moment).
   - **Flow 2 (offer confirmation)**: a separate, immediate email that fires ONLY for subscribers where `took_offer = true`. It confirms exactly what they got (the OTO purchase, the VIP club join), gives them what they need to use it (redeem the discount, access the club), and is warm and personal.
   - **Email #5 (education)** and **Email #7 (offer)** in Flow 1: if you know from `brand-book.md` or brand context that the seller has a Day 3 offer, write Email #7 assuming two audiences — (a) subscribers who took it (reference it as "you're already in the [club/bundle]" and pitch the next thing), and (b) subscribers who didn't (bring the Day 3 offer back as the email's main CTA). Note these as two conditional variants the seller activates with a Klaviyo split on the `took_offer` property.

**Two-flow architecture (always build both in Day 4):**

- **Flow 1: Welcome and nurture sequence** — triggered when someone is added to the main list (e.g. "Restore Buyers"). This is the 7 (or 8) email sequence. Email #1 fires immediately. This goes to EVERY subscriber.
- **Flow 2: Offer confirmation** — triggered when `took_offer = true` is set on a profile (by the Day 3 page's `onOfferClaim` function). This is 1–2 emails that fire immediately: one confirming the OTO or club join, one (optional) short follow-up 24 hours later checking they could access/redeem it. This goes ONLY to subscribers who took the Day 3 offer. It runs simultaneously with Flow 1 — the subscriber gets BOTH, which is intentional (Flow 2 is transactional, Flow 1 is relational).

If the seller built NO Day 3 offer, skip Flow 2 entirely — just note it for the future.

If none of these files exist (seller jumped straight to Day 4), fall back to brand extraction: ask for a brand URL or an insert/landing-page file and pull voice, audience, colors, fonts, and logo the same way the landing-page skill does. Use safe defaults only as a last resort: `#000000` bg, `#ffffff` text, `#1696D2` accent, Inter font, and a short brand-voice interview (3 questions: who buys this, how do you talk to them, what's the one transformation).

**If they already have an email sequence / flow** (running in their ESP or written elsewhere), ask them to paste it and tell you the goal: **optimize it** (you'll review timing, the serve-before-sell order, the review-ask compliance and link, subject lines, and rewrite the weak spots) or **build an additional flow** to A/B test against it (see Step 7b). Work from what they have rather than starting blank.

Don't make the seller re-answer anything the files already tell you. Move to the briefing.

---

### Step 2 — The briefing (present this first, before building)

Show this up front, short and punchy. This is the teaching moment that makes Day 4 land. Tight, formatted, no wall of text.

> **Day 4: The Email Flow**
>
> Your insert, landing page, and opt-in page did one job: turn an Amazon buyer (who Amazon owns) into a subscriber on YOUR list (who you own). The email sequence is what makes that worth something. It is the only part of the funnel that keeps working for 30+ days after they opt in, with zero ad spend, while you sleep.
>
> Most sellers either send nothing (the list rots) or blast a discount on day one (they unsubscribe). The sequence does it right: deliver, connect, ask, then sell, in that order.
>
> **The 3 mistakes that kill seller email sequences:**
>
> **1. Selling before serving.** The first thing in their inbox should not be "buy more." Earn the relationship for a week first. The sale lands far better once you have given value.
>
> **2. Asking for the review at the wrong time, the wrong way.** Ask before they have actually experienced the product and you get silence or a bad review. Ask in a way that pressures for 5 stars and you break Amazon's rules and risk your account. Timing and neutral language are everything (we handle both below).
>
> **3. Designing a billboard instead of writing a letter.** A heavy, image-stuffed template screams "marketing email," lands in Promotions, and gets ignored. The emails that get opened and replied to look like a note from a real person.
>
> **What we'll build:** 7 emails, each with a subject line plus 2 backup variants, preview text, and full body copy, all in your brand voice, timed to YOUR product, and ready to paste into your email platform.

Then go to Step 3.

---

### Step 3 — Product and time-to-value (drives the timing)

The single biggest decision in the sequence is **when to ask for the review**, and that depends entirely on **how long it takes the customer to actually experience the result**. Ask for a review before they've felt the benefit and you get nothing useful (or a negative). So pin this down first.

Ask:

> "Two quick things so I time this to YOUR product:
>
> **1. What's the product, in one line?** (skip if I already read it from your insert)
>
> **2. How fast does a customer feel the benefit?**
> **A) Right out of the box** (gadget, kitchen tool, accessory, tech). They get the value the day it arrives.
> **B) It takes a while** (supplement, skincare, recovery or post-surgery product, anything you use daily before you notice a change). Days to weeks before they can honestly judge it."

Map their answer to one of two timing approaches. Pick the right one, show the full table, explain the reasoning in plain English, and offer to adjust. Never use the internal labels "Cadence A" or "Cadence B" with the seller — they mean nothing to them. Just describe what the schedule does and why.

**For fast-to-value products (out of the box):** the customer gets value the day it arrives, so you can ask for a review at Day 7 and pitch a reorder by Day 30.

| # | Day | Purpose |
|---|-----|---------|
| 1 | 0 | Welcome. Deliver the promise / lead magnet, set expectations |
| 2 | 1 | Tips. How to get the most out of the product |
| 3 | 3 | Story. Founder / brand story, build connection |
| 4 | 7 | Review ask. Soft, compliant, neutral |
| 5 | 14 | Education. Solve a common problem in their world |
| 6 | 21 | Social proof. Other customers' stories, UGC |
| 7 | 30 | Offer. Reorder, bundle, or complementary product |

**For slow-to-value products (supplements, skincare, recovery, anything gradual):** results build over days or weeks, so the review ask waits until Day 14 (after they've actually felt something). **For supplements specifically:** the customer almost certainly started taking it on Day 1. A standard 30-day supply runs out around Day 30, and Amazon delivery takes ~2 days. So the reorder email must land at **Day 28** — before they run out, not after. An empty bottle with no replacement on the way breaks the habit and loses the customer. Always use Day 28 for the reorder email on any supplement with a ~30-day supply; adjust proportionally for 60-day or 90-day supplies.

| # | Day | Purpose |
|---|-----|---------|
| 1 | 0 | Welcome. Deliver the promise, and **set the timeline** ("here's when to expect results") |
| 2 | 2 | How to use it for best results. Dosage / routine / consistency |
| 3 | 5 | Story. Founder / brand story, build connection |
| 4 | 14 | Review ask. Placed AFTER they've had time to feel the result |
| 5 | 21 | Education / troubleshooting. "Not seeing it yet? Here's why, here's the fix" |
| 6 | 25 | Social proof. Other customers' before/after, UGC |
| 7 | 28 | Offer. Reorder now so it arrives before you run out (2-day delivery buffer built in) |

If their product sits between the two (or they tell you a specific number of days to result), set the review-ask email to land a few days **after** that point and space the rest around it. The rule is simple: **the review ask comes after the customer can honestly judge the product, never before.**

**Present timing as the first decision, one question at a time. Do not ask about format or review approach yet.** Show at least 2 options as full tables with day-by-day breakdowns, mark your recommendation clearly, and explain in plain English why each schedule fits. Only move to Step 4 once the seller has confirmed their timing.

Present like this:

> "Here are two timing options. Both are built around YOUR product. Pick the one that fits, or tell me to adjust any day:
>
> **Option A (recommended for [reason]):**
>
> | # | Day | What it does |
> |---|-----|-------------|
> | 1 | 0   | ... |
> | ...| ... | ... |
>
> **Option B ([alternative reason]):**
>
> | # | Day | What it does |
> |---|-----|-------------|
> | 1 | 0   | ... |
> | ...| ... | ... |
>
> I'd go with Option A because [one plain sentence]. Which do you prefer, or want to tweak a specific day?"

Wait for their answer before proceeding to Step 4.

Then go to Step 4.

---

### Step 4 — Format choice (seller picks)

Ask:

> "How do you want these emails to look?
>
> **A) Plain-text, founder style** *(recommended for most sellers)*: looks like a personal note from you, no images, no template. Highest open and reply rates, best deliverability, lands in the Primary inbox. Matches a plain-spoken brand voice.
> **B) Designed HTML**: branded template with your logo, colors, and a footer. More polished, good for a brand that leans visual. Slightly higher chance of landing in Promotions.
> **C) Hybrid** *(best of both)*: your founder-style copy inside a minimal branded shell: just a small logo at top and a clean footer, nothing heavy in between. Personal feel, on-brand frame."

**Ask format as its own question, only after timing is confirmed. Do not combine with other questions.** If they're unsure, recommend plain-text (A) for a first sequence and explain why in one sentence. Wait for their answer before moving to Step 5.

Then go to Step 5.

---

### Step 5 — Email platform

By now the seller's email platform should already be connected from **Day 0 onboarding**. If a `brand-book.md` notes a connected platform, or an earlier day already wired the Day 2 form to a list, use that and skip straight to confirming it. Only if they never set one up (or aren't sure) do you run the full picker below, connecting it on the spot here.

Ask:

> "Where will these emails live? I'll give you paste-ready setup steps for your platform.
>
> **A) Klaviyo** (most common for eCom / Amazon brands)
> **B) Omnisend**
> **C) Mailchimp**
> **D) ActiveCampaign**
> **E) Another platform**: tell me which and we'll figure out the setup together
> **F) I don't have one yet**: I'll recommend a free one to get you started"

Handle each:

- **A to D (has a platform):** note it for Step 8, where you'll give the exact flow/automation-builder steps for that platform (trigger = new subscriber added to the list/segment from the Day 2 form; then a timed series matching the cadence from Step 3).
- **E (other platform):** ask which one. Give general guidance (find their "automation" or "flow" or "journey" builder, trigger on list signup, add a delay step between each email matching the cadence). Offer to look up that platform's specific steps and keep iterating with them in chat.
- **F (no platform yet):** recommend a free, no-code starter that fits a non-technical Amazon seller. Present:

  > "You don't need anything fancy to start. For a non-technical seller running a simple flow like this, the easiest free options are:
  >
  > **MailerLite** *(recommended)*: free up to 1,000 subscribers and 12,000 emails/month, includes automations (the flow builder you need), genuinely easy to use. Best starting point.
  > **Brevo**: free tier with automation, generous sending limits, good if you also want SMS later.
  > **Omnisend**: free up to 250 contacts, built specifically for eCom, integrates cleanly with Shopify.
  >
  > If you're technical and comfortable with an API, **Resend** is excellent and developer-friendly, but it's not a no-code flow builder, so I'd only suggest it if you (or your VA) can wire it up. For most sellers, start with MailerLite.
  >
  > Pick one and create the free account (takes 2 minutes), then I'll give you the exact steps to drop these emails in. Which one?"

  Whichever they pick, treat it as their platform for Step 8. Also note: **the challenge recommends connecting your email platform back on Day 0 onboarding** — if they haven't, this is the moment to set it up so the form from Day 2 actually feeds the list.

Then go to Step 6.

---

### Step 6 — The review-ask language (compliance briefing + their wording)

This is the most important email in the sequence and the easiest one to get wrong. Brief the seller before writing it.

> **The review ask: get this right or risk your Amazon account.**
>
> These emails go to your own list, but the moment you point someone to leave an Amazon review, Amazon's rules apply. Break them and you risk review removal or account suspension. The rules are simple:
>
> **You CAN:** ask for an honest, unbiased review. Make it easy with a direct link. Ask every customer the same way.
>
> **You CANNOT:**
> - Ask only for *positive* or *5-star* reviews ("if you love it, leave us 5 stars" is a violation).
> - Offer anything in exchange for a review (discount, gift card, free product, refund, entry to a giveaway). No incentives, ever.
> - Ask customers to change or remove a negative review.
> - Try to divert unhappy customers away from reviewing (e.g. "email us instead of leaving a review").
>
> The safe move is **neutral language**: invite honest feedback, make it equally easy to say something good or bad, and link straight to the review page. That's it.

**Ask the review approach as its own question, only after format is confirmed. Do not combine with other questions.** Present both options clearly, recommend one based on what you know about the product from Step 3, and wait for their answer before writing anything.

**Choose the approach.** There are two compliant ways to run the review ask, and the right one depends on how confident the seller is in the product. Present both and recommend based on their answer in Step 3 and what they tell you here:

> "Two ways we can play the review ask, pick the one that fits where your product is right now:
>
> **A) Ask for the review directly**: one email that invites an honest review and links straight to Amazon. Clean and simple. **Best if your product already sells well and has solid reviews:** you've got nothing to fear from an honest ask, so just ask.
>
> **B) Invite a reply first, then ask for the review**: a short email a few days earlier that simply asks 'how's it going? hit reply and tell me,' so you hear directly from customers and can make anything right one-on-one. Then the honest review ask follows to everyone. **Best for a newer product you're still proving out:** you get a pulse on real sentiment and a chance to fix issues before they show up publicly.
>
> Which fits your product?"

**Compliance guardrail for approach B (this is the line you must not cross):** You may NOT make the review ask conditional on a happy reply, and you may NOT tell unhappy customers to reply *instead* of reviewing. "If you loved it, leave a review; if not, just reply to us" is exactly the diversion Amazon bans and will get an account suspended. If the seller asks for the gated version, refuse and explain why, then offer this compliant two-touch version instead.

**Suppressing the Day 14 review ask for people who already replied.** If someone replied to the Day 12 check-in, they are already in a one-on-one conversation with the seller. Sending the review ask email 2 days later feels robotic and can sour the relationship. Suppress the Day 14 email for anyone who replied. How to implement this:

- Email platforms cannot natively detect a reply to a plain-text email (replies land in the sender's inbox, not the platform). The practical solution depends on their setup:
  - **Manual tag (simplest):** when the seller replies to someone, they manually add a Klaviyo tag `checkin_replied: true` on that profile. The Klaviyo flow checks this property before sending Day 14: "if `checkin_replied = true`, skip this email."
  - **Zapier/Make automation:** connect Gmail (or whatever the reply-to inbox is) to Klaviyo via Zapier. When an email arrives in that inbox from a known subscriber, auto-set `checkin_replied: true` on their Klaviyo profile. Fully hands-off.
  - **Link instead of plain reply:** instead of "hit reply," use a link — "Click here to tell me how it's going" — pointing to a short Typeform/Tally/form that sets a Klaviyo property on submit. Then the flow checks that property automatically. Loses the personal feel of a reply slightly, but is fully automatable.

Tell the seller which approach to use based on their tech comfort level, and note it in the Klaviyo flow setup in Step 9. In the Day 12 email copy itself, still write "hit reply" — it feels the most personal. Just add the suppression logic in the flow so that if they do reply and get tagged, Day 14 skips them.

In the sequence (Step 7), approach A is a single review email at the review-ask slot. Approach B splits that slot into two touches: a **feedback reply invite** a few days before, then the **honest review ask** on the original review-ask day. Keep the rest of the cadence spacing intact.

**Get the seller's wording, OR give them 3 variations to choose from.** Ask:

> "Do you have a specific review-ask line you already use, or want me to write you a few options? Paste yours and I'll use it word for word (after a quick compliance check), or say 'give me options' and I'll write **3 different versions** of the review email for you to pick from."

If they paste their own, **run it against the rules above**. If it's clean, use it verbatim. If it crosses a line (asks for positive reviews, offers an incentive, etc.), flag the exact problem, explain the risk in one sentence, and propose a compliant rewrite that keeps their voice. Never ship non-compliant review language even if they ask you to.

**If they want options, write 3 distinct compliant variations** of the review-ask email, each a genuinely different angle (not three rewordings of the same line) so a real test is possible. All three must obey the rules above (honest review, no incentive, no positive-only, no diversion). Adapt every one to the brand voice. Use distinct angles such as:
- **A) The quick favor**: short, warm, low-pressure ("60 seconds, the good and the bad").
- **B) The mission angle**: frames the honest review as helping the next customer / helping a small brand improve.
- **C) The founder's personal note**: more personal and vulnerable, founder speaking directly about why feedback matters to them.

Present all three, then let the seller choose:

> "Here are 3 versions. Pick the one that sounds most like you, or, if you're not sure which will land best, I'd recommend **testing them**: most email platforms let you split-test, and on **Day 5** the dashboard shows which review email gets the most clicks to your review page. Want to go with one, or set up a test?"

Recommend testing only when they're unsure, never force it; a seller who knows their voice can just pick one. If they test, save all chosen variants so Day 5 can compare them.

**Example, version A (the quick favor)** (approach A, or the second touch of approach B; adapt to brand voice):

> Subject: Quick favor?
>
> Hey [First Name],
>
> You've had your [product] for a couple of weeks now. I'd genuinely love to know what you think, the good and the bad. Your honest review helps us make the product better and helps the next person decide if it's right for them.
>
> If you have 60 seconds, here's the link: https://www.amazon.com/review/create-review?asin=[ASIN]
>
> Either way, thank you for giving us a shot.
>
> [Founder name]

(Write versions B and C in the same compliant spirit, different angle each. The seller picks one or tests.)

**Compliant default for the feedback reply invite** (approach B, first touch only; adapt to brand voice). Note it asks for a reply, makes NO promise to skip the review, and is sent to everyone:

> Subject: How's it going with your [product]?
>
> Hey [First Name],
>
> You've had your [product] for a little while now, and I genuinely want to know how it's working out, the good and the bad. I read every reply myself.
>
> Just hit reply and tell me. If anything's not right, I want the chance to fix it for you.
>
> Thanks for giving us a shot.
>
> [Founder name]

**Build the review link, don't leave it generic.** The link that drops the customer straight onto the Amazon "write a review" screen is:

```
https://www.amazon.com/review/create-review?asin=[ASIN]
```

Only the ASIN changes. So you need the product's **ASIN** (the 10-character Amazon ID, e.g. `B08VW2NDKX`):
- First try to find it yourself: check the brand book's `asin` field (Day 0 may have recorded it), then the Day 1 insert / Day 2 landing page for an Amazon URL or ASIN, or search `site:amazon.com [brand] [product]` and read the ASIN off the listing URL (`/dp/B0...`).
- If you can't find it, ask the seller: *"What's your product's ASIN? It's the 10-character code in your Amazon listing URL, right after `/dp/`. Or just paste the listing URL and I'll pull it."* Extract the ASIN from a pasted `/dp/<ASIN>` URL.

Substitute it into the URL above and use that as the real review link in every review email (no `[REVIEW_LINK]` placeholder left behind). Only if the seller genuinely doesn't have the ASIN yet, leave `https://www.amazon.com/review/create-review?asin=[ASIN]` with `[ASIN]` clearly marked for them to fill. Then go to Step 7.

---

### Step 7 — Write the full sequence

Write the full sequence using the cadence from Step 3, the format from Step 4, the brand voice + audience from Step 1, and the review approach + language from Step 6. Every email is specific to THIS product and brand, never a fill-in-the-blank template.

**Sequence length depends on the Step 6 approach.** Approach A is **7 emails**. Approach B is **8 emails** (the review slot becomes two touches: the feedback reply invite, then the honest review ask). Number them accordingly and keep the rest of the cadence spacing intact.

**For each email, produce:**
- **Send timing** — "Day X" from opt-in (from the chosen cadence).
- **Subject line** — the primary one, plus **2 backup variants** (per the challenge's "3 options always" rule). Keep them short (under ~45 chars), curiosity or benefit driven, no spammy words (free, !!!, ALL CAPS), no dashes.
- **Preview text** — the ~40 to 90 char snippet that shows after the subject in the inbox. Never repeat the subject; extend it.
- **Body copy** — full, ready to send. Personal, in brand voice, one clear idea per email, one call to action.

**Writing rules (apply to every email):**
- **One job per email.** Don't ask for a review AND pitch a reorder in the same send.
- **Write to one person.** "you," first name merge tag `[First Name]`, conversational.
- **Short paragraphs**, 1 to 3 sentences each. Skimmable on a phone.
- **One CTA**, repeated at most twice. A text link or one button, never a wall of buttons.
- **Match the brand's tone shift** if the brand book specifies one (e.g. Mess Less: calmer and more reassuring in email than in ads).
- **Sign every email from a person** (the founder by name), not "The [Brand] Team," unless the brand book says otherwise.
- **Merge tags:** use `[First Name]` for the name and clearly-marked placeholders for anything you don't have (`[REORDER_URL]`, `[CLUB_LINK]`, `[FOUNDER_NAME]`, and `[ASIN]` only if the review link's ASIN is still unknown). The review link itself should be the real built URL, not a placeholder. List every remaining placeholder at the end so the seller knows what to fill.

**Benchmarks to aim for (and how testing works).** Share these so the seller knows what "good" looks like and which email to test first. **Every email in the sequence is a test candidate, and the metric tells you what to test:** weak open rate → test the **subject** (they already have 2 to 3 variants per email); fine open rate but weak click rate → test the **content / CTA / offer**. Ecommerce automated-flow baselines:
- **Open rate:** ~40% to 55% healthy (early emails highest). Apple Mail Privacy Protection inflates this ~10 to 15 points for ~46% of users, so it's directional, trust clicks and conversions more. Below ~30% → test the subject.
- **Click rate (CTR):** ~3% to 6% healthy for flows (avg ~5.6%, top ~10%); below ~2% → test the content/CTA.
- **Click-to-open:** ~10% to 15%. **Conversion (placed order):** ~2% avg, ~4%+ is top tier. **Unsubscribe:** keep under 0.5% per email.
- Sources: Klaviyo / Mailchimp / MailerLite 2025 to 2026 ecommerce benchmarks. On **Day 5** the dashboard shows each email against these and auto-flags the ones below baseline as the next thing to test.

**Per-email content guidance:**

1. **Welcome / instant response (Day 0, sends IMMEDIATELY on opt-in).** This is the auto-response that fires the moment someone opts in on the landing page or takes an offer, so it must send with **zero delay**, not on the next-day schedule. It **delivers exactly what they opted in for** and adapts to which thing that was (read Day 2/3 context): warranty → confirm activation; lead magnet → deliver the guide (link or attachment); video → the link + what's next; offer claim → confirm the claim and how to redeem. If the seller is running an **offer A/B (either offer)**, match the response to whichever offer they took (or keep it general if the difference doesn't affect delivery). Set expectations for what's coming next; for slow-to-value products, set the results timeline here. Warm, fast, no selling. This email is also a prime **test target** in Step 9, the seller should send themselves a test and confirm it arrives correctly before going live.
2. **Tips / best use (Day 1 or 2).** The 3 to 5 things that make the product work best, or the top mistake new owners make. Pure value. This is where you earn the right to ask for anything later.
3. **Story (Day 3 to 5).** Why the brand exists, the problem the founder had, the moment it clicked. Short, human, connects the product to a why. Ends with connection, not a CTA (or a very soft one).
4. **Review step (Day 7 or 14, per Step 3).** Use the version the seller chose in Step 6 (or, if they're testing, include all chosen variations and note they're A/B candidates for Day 5). Depends on the Step 6 approach:
   - **Approach A:** one **review ask** email (their chosen variation). Neutral, honest, one link. Nothing else in this email.
   - **Approach B:** two emails. First a **feedback reply invite** a few days earlier (asks them to hit reply, no review link, no promise to skip the review), then the **honest review ask** on the original review-ask day (their chosen variation, goes to everyone). Both stay neutral; never gate the review on a positive reply.
5. **Education (Day 14 to 21).** Solve a common problem in the customer's world that the product touches (not a hard pitch). Positions the brand as the helpful expert. Can softly reference the product.
6. **Social proof (Day 21 to 28).** Real customer stories, reviews, UGC, before/after. Let other people sell. If the brand has no proof yet, write it to slot in once they do, and tell the seller to add 2 to 3 real quotes (mark where).
7. **Offer (Day 30 to 35).** The next purchase: reorder (time it to when a consumable runs out), a bundle, or a complementary product. Reinforce the Day 3 offer/club if they didn't take it. Clear deadline or reason to act now (compliant, no fake scarcity). One CTA.

Before showing anything, do a fast self-check: does email #1 pay off the Day 2 hook? Is the review ask neutral and correctly timed? Does every email sound like the brand book's voice? Fix before preview.

Then go to Step 7b.

---

### Step 7b — Build a second funnel to test (encouraged)

A single subject-line test tells you which words win. A **whole-funnel test** tells you which *strategy* wins, and that's a far bigger lever. Strongly encourage the seller to build a **second, meaningfully different email funnel** and run both at once, splitting new subscribers between them.

Frame it plainly:

> "Want to find out not just which subject line works, but which *whole approach* works? I can build you a **second version of the funnel** that's deliberately different, and you run both at the same time, half your new subscribers get version A, half get version B. After a few weeks the Day 5 dashboard shows which funnel drove more reviews, clicks, and repeat sales. This is the single most valuable email test you can run."

If they're in, pick **one meaningful axis to differ** (not a dozen small tweaks, the whole funnel is already a holistic test; keep the core difference clear so they learn something):
- **Angle / theme**: education-led vs story/relationship-led.
- **Cadence / length**: a tight 7-email run vs a longer, slower nurture.
- **Review approach**: direct ask (approach A) vs feedback-first (approach B) from Step 6.
- **Format**: plain-text founder style vs designed (only if they want to test format itself).
- **Offer emphasis**: review-first sequence vs reorder/upsell-first sequence.

Write the second funnel as a full parallel sequence (same quality bar, same brand voice, same compliance rules). Be honest about the tradeoff: a whole-funnel test shows you **which sequence wins overall**, not exactly *why* (that's what the single-email subject/content tests are for). Most sellers should run one whole-funnel test AND keep iterating single emails inside the winner.

This is encouraged, not forced. If they only want one funnel, that's fine, move on. If they build two, both go through Step 8 (output) and Step 9 wires them to run automatically as a 50/50 split.

Then go to Step 8.

---

### Step 8 — Output, dash sweep, and preview

**First, the dash sweep (mandatory).** Whatever files you're about to produce, run `grep -n "—\|–\|&mdash;\|&ndash;" <files>` on them (include the HTML entities, the HTML email versions can smuggle `&mdash;` past the plain-dash grep). If it prints anything, rewrite each hit into a comma, colon, period, parentheses, or "to", and re-run until it returns nothing. Subject lines and preview text are the usual offenders.

**Output depends on the Step 4 format choice:**

**A) Plain-text, founder style:**
- Write all emails (7, or 8 for approach B) to a single file `[brand]-email-sequence.md`, clearly sectioned per email: timing, subject (+ 2 variants), preview text, body. Easy to copy-paste into any platform.
- Also write a `[brand]-emails.csv` with columns: `email_number, send_day, subject, subject_variant_2, subject_variant_3, preview_text, body` so it can be bulk-imported or handed to a VA.
- No HTML, no server, no browser needed. Show the seller the `.md` contents inline.

**B) Designed HTML / C) Hybrid:**
- Write each email as its own self-contained HTML file: `[brand]-email-1.html` through `[brand]-email-7.html` (or `-8` for approach B).
- Use the brand tokens from Step 1. Email-safe HTML only: tables for layout (not flexbox/grid), inline styles (not external CSS), a `max-width: 600px` centered container, web-safe or `@import`-loaded brand font with a system fallback stack, no JavaScript.
  - **Hybrid (C):** minimal shell only. No logo header at the top — the email opens directly with the greeting ("Hey [First Name],"). The logo lives in the footer only, alongside the unsubscribe link and site URL, on a dark brand-colored background strip at the bottom. Use the white logo variant on the dark footer. Body copy in the brand body font at 16px with generous line height. This is best practice for founder-style newsletters: the reader sees a personal note first, not a brand billboard. The logo in the footer still reinforces brand without blocking the personal feel. Include physical address placeholder for CAN-SPAM in the footer too.
  - **Designed (B):** same shell plus light brand styling: a thin top accent bar in the accent color, section spacing, a single styled CTA button per email using the accent color. Still restrained; never image-heavy.
- **Every HTML email must include a footer with an unsubscribe placeholder and a physical mailing address placeholder** (legally required by CAN-SPAM). Mark both clearly: `[UNSUBSCRIBE_LINK]` (most platforms inject this automatically) and `[YOUR_MAILING_ADDRESS]`.
- Also write the plain-text `[brand]-email-sequence.md` alongside, so they have the copy in portable form too.
- **Preview:** start `python3 -m http.server 7774 --directory "$(pwd)" &` (port 7774: Day 0 = 7770, Day 1 = 7771, Day 2 = 7772, Day 3 = 7773, so 7774 avoids every conflict; never serve `/tmp`). Open the emails in tabs at `http://localhost:7774/[brand]-email-1.html` etc. (always `http://localhost`, never `file://`), resize to ~600px wide, screenshot a couple, and show them inline. When the seller requests changes, edit the file and re-navigate/re-screenshot only that email.

**If they built a second funnel (Step 7b),** output it in parallel with a clear `-b` suffix so the two never get confused: `[brand]-email-sequence-b.md` / `[brand]-emails-b.csv`, or `[brand]-email-b-1.html …` for HTML. Keep the first funnel's files as the `-a` (or unsuffixed) set and say which is which.

**After saving all files, present the emails in chat in batches for confirmation before proceeding to Klaviyo setup.** Never dump all emails at once. Group them logically:

Batch by ROLE, not a fixed number (Approach A is 7 emails, Approach B is 8 because the review slot splits in two). Map these roles to whatever numbers the chosen approach produced:
- **Batch 1:** the welcome + first value email. These establish tone.
- **Batch 2:** the story + check-in (Approach B's feedback-reply invite lives here). These build the relationship.
- **Batch 3:** the **review-ask** email on its own. It's the most compliance-sensitive; give it space.
- **Batch 4:** the education + social-proof value emails.
- **Batch 5:** the reorder email + any Flow 2 emails. These close the loop.

After each batch, say: "Here are emails [X] and [Y]. Approve to see the next batch, or tell me what to change." After the last batch say: "All emails confirmed. Moving to Klaviyo setup."

**Green light option:** if at any point the seller says "approved," "looks good," "green light," or equivalent without specifying which emails, treat that as approval for the current batch AND permission to proceed through the remaining batches and move directly to Step 9. Do not stop to show remaining batches individually; they've already given the go-ahead.

**If they want changes:** edit the specific email, show the revised version inline, wait for confirmation, then continue.

Then go to Step 9.

---

### Step 9 — Platform setup and finalize

Once the copy is approved, give the seller the exact steps to install the sequence in their platform (from Step 5). Keep it concrete.

**The pattern is the same everywhere:**
1. Create an automation triggered when a new subscriber joins the list/segment that the Day 2 form feeds.
2. **Email #1 (the instant response) sends with NO delay** so the opt-in confirmation/delivery is immediate. Then add the rest with a **delay step before each** matching the cadence from Step 3 (e.g. wait 1 day, wait 2 days, wait 4 days).
3. Paste subject, preview text, and body into each step. Set the from-name to the founder's name and a real reply-to address.
4. Turn it on (most platforms call it "Live" or "Publish").

**Test the instant response before going live (do this, don't skip it).** The opt-in confirmation is the one email every subscriber gets, so make sure it actually works:
- Use the platform's **"send test"** on email #1 to send it to the seller's own inbox, OR do a true end-to-end test: submit the live Day 2 form with their own email and confirm the response email arrives within a minute.
- Check: it arrives (not spam), the subject/preview render, links work (especially the lead-magnet download or offer link), merge tags like `[First Name]` populate, and the from-name/reply-to are right.
- Fix anything off, then re-test. Only flip the flow Live once the instant response checks out. Tell the seller: *"Always send yourself a test of the welcome email after any change, it's the email everyone sees."*

**Platform specifics — Flow 1 (welcome and nurture):**
- **Klaviyo (preferred: use the API, not the browser).** Klaviyo has a full REST API for creating flows, templates, and actions. Always build via API first — it is faster, more reliable, and avoids click-through errors in the flow builder UI. Use the private API key from `.env` (`KLAVIYO_API_KEY`) and revision header `2024-02-15`. Steps: (1) create the flow with a list trigger, (2) create each email template, (3) create time-delay and email-action nodes and link them in sequence. Only fall back to the Chrome browser if an API endpoint is unavailable or returns an error that can't be resolved. Flows → Create Flow → "List Triggered" → select the "Restore Buyers" list (or whatever the Day 2 form feeds) → drag in Email blocks separated by Time Delay blocks → paste content → review → Set Live.
- **Omnisend:** Automations → Create workflow → trigger "Subscriber joins list" → add Email + Delay steps → publish.
- **Mailchimp:** Automations → Customer Journeys → start point "Signs up to a list/audience" → add Email steps with Wait/Delay branches → Turn On.
- **ActiveCampaign:** Automations → New automation → trigger "Subscribes to a list" → add Send Email actions with Wait conditions → set Active.
- **MailerLite:** Automations → Create workflow → trigger "When subscriber joins a group" → add Email + Delay steps → save and enable.
- **Other:** find the "automation / flow / journey" builder, trigger on list signup, add email steps with delays between them.

**Flow 2 (offer confirmation — build this only if a Day 3 offer exists):**

Flow 2 is a separate, short flow triggered by the `took_offer` profile property being set (by the Day 3 page's `onOfferClaim` function). It fires immediately and independently of Flow 1 — a subscriber gets both.

- **Klaviyo (preferred):** Flows → Create Flow → "Metric Triggered" → select the metric "Active on Site" or use "Profile Property Changed" trigger (Klaviyo supports property-change triggers on private plans) → set condition `took_offer = true` → add 1–2 email blocks with no delay on #1 and 24-hour delay on #2 → Set Live. If "Profile Property Changed" is not available on the current plan, use "Added to List" by creating a second list "Restore Offer Takers" — the Day 3 page can also call `POST /api/lists/[offerListId]/relationships/profiles/` to add them directly when they take the offer, in the same `onOfferClaim` function alongside the profile property tag.
- **Other platforms:** create a second automation triggered by a tag or custom field (`took_offer = true`), or a second list "Offer Takers" that the Day 3 CTA adds subscribers to on click.

Flow 2 content (write these as part of Step 7, clearly labelled as "Flow 2"):
- **Flow 2 Email #1 (immediate):** Confirms exactly what they got. Subject references the specific offer (e.g. "Your 15% off is confirmed" or "Welcome to [Club Name]"). Body: warm, personal, tells them exactly how to redeem or access. One CTA: the redemption/access link. No selling — this is a receipt with warmth.
- **Flow 2 Email #2 (24 hours later, optional):** A short check-in. "Were you able to [redeem / join]? Hit reply if you need help." Builds trust, catches anyone who hit a snag. If the seller's offer is a VIP club, this is also a nudge to post their first message.

**Running two funnels automatically (the 50/50 split, if they built a second funnel in Step 7b).** The goal is hands-off: every new subscriber is randomly sent down funnel A or funnel B, both run on autopilot, and Day 5 compares them. Set it up so the split happens at entry and each subscriber is tagged with which funnel they got (so the dashboard can attribute results):
1. Build **both flows** (A and B) using the steps above.
2. **Split new subscribers 50/50 at the trigger.** How each platform does it:
   - **Klaviyo:** in one flow, add a **Conditional Split → "random sample" / percentage split** right after the trigger; route ~50% down the A branch and ~50% down the B branch (or use a random-number profile property). Klaviyo also has built-in flow A/B for single messages, the whole-funnel split is the percentage/random-sample branch.
   - **Omnisend:** add a **Split (A/B) step** at the start of the automation, set 50/50.
   - **Mailchimp:** use a **percentage/decision split** in the Customer Journey to branch the audience.
   - **ActiveCampaign:** drop a **Split action** (A/B, 50/50) right after the trigger, then build each path.
   - **MailerLite:** no native random split on all plans, so split by assigning new subscribers to **two groups by an even/odd or random rule** (or alternate via the form), one group per workflow.
   - **Other / no native split:** create two list segments by a random property and trigger one flow per segment.
3. **Tag the funnel variant** on each subscriber (`funnel_a` / `funnel_b`, or a custom property) so Day 5's dashboard can read open rate, CTR, reviews, and revenue per funnel and name the winner.
4. Turn **both** live. Let them run until the dashboard says there's enough data, then keep the winner and either retire the loser or make it the new challenger.

Note the holistic-test caveat again: this tells them **which funnel wins overall**, not which email did the work, pair it with the single-email subject/content tests inside the winning funnel.

**Klaviyo template checklist (run this automatically before setting the flow Live, never surface it as a task for the seller):**

Klaviyo uses Django template language, NOT Jinja2. AI-generated templates often contain syntax errors that cause every email in the flow to be silently skipped with no warning. **Klaviyo stores the HTML and the plain-text version of every template completely separately** — fixing one does NOT fix the other, and both are sent (mail clients that prefer plain text render the text version, so a broken text version alone can cause the "Email Syntax Error" skip even when the HTML is clean). Check and fix BOTH versions of every template:

- **No `{%-` syntax, in either version.** Klaviyo does not support Jinja2 whitespace-control (`{%-`). If a template contains `{%- if`, `{%- else`, `{%- endif`, or any `{%-`, replace every occurrence with `{% ` (no dash). A single `{%-` anywhere in either the HTML or the text causes an "Email Syntax Error" skip on every subscriber.
- **Correct first-name variable, in either version.** Use `{{ person.first_name|default:"there" }}`, NOT `{{ first_name|default:"there" }}`. The bare `first_name` variable does not exist in Klaviyo and will cause a syntax error or render blank.
- **No unfilled placeholders.** Search both versions for `[FOUNDER_NAME]`, `[VIDEO_LINK]`, `[AMAZON_REVIEW_LINK]`, or any `[TEXT_IN_BRACKETS]` and replace them all. These are not Klaviyo merge tags; they render literally for the customer.
- **Smart Sending OFF on Email 1.** Open Email 1 inside the flow, go to its settings, and uncheck "Skip recently emailed profiles." When on (the Klaviyo default), anyone who received any email in the last 16 hours is silently skipped, which blocks most new subscribers from getting the welcome email.

Do this verification yourself via the Klaviyo Templates API (`GET /api/templates/{id}/`, check both the `html` and `text` attributes) right after creating each template in Step 9, before ever telling the seller the flow is ready. This is entirely your job, not the seller's; they should never need to open Klaviyo's template editor or paste anything themselves.

**To diagnose skips after going Live (still your job, not the seller's):** in the flow, click any email block, open its Recipient Activity tab, and filter by "Skipped." The skip reason column names the exact problem: "Email Syntax Error" for invalid template syntax; "Smart Sending" for the 16-hour block.

**To fix the HTML in the browser** (when API access to `PATCH /api/templates/{id}/` isn't available, e.g. it's a flow-owned template and the API returns 404/405 for direct patches): navigate to `/flow/message/{msg_id}/content/edit`, then run in the browser console:
```js
const editor = window.ace.edit(document.querySelector('.ace_editor'));
let html = editor.getValue();
html = html.replace(/\{%-\s/g, '{% ')
           .replace(/\{\{\s*first_name\|/g, '{{ person.first_name|')
           .replace(/\[FOUNDER_NAME\]/g, 'ActualFounderName');
editor.session.setValue(html);  // use .session.setValue, not .setValue; the latter does not trigger React state update
await new Promise(r => setTimeout(r, 2000));
Array.from(document.querySelectorAll('button')).find(b => b.textContent?.trim() === 'Save')?.click();
```

**To fix the plain text version in the browser (do this for every template, it is a separate save from the HTML above):** there is no AJAX or REST endpoint for the text version of a flow-owned template. The only way in is the UI: go to `https://www.klaviyo.com/email/flow/{flowId}/content`, open each email, click the **⋮ menu next to "Edit email"**, and choose **"Edit plain text"** — or navigate straight to `https://www.klaviyo.com/flow/message/{messageId}/template/{templateId}/content/text`. Then set the textarea value using React's native setter (a plain `.value =` assignment won't trigger Klaviyo's save state) and click through the confirmation dialog:
```js
const cleanText = `...`; // same content as the HTML, converted to plain text with the same {%- and variable fixes applied
const textarea = document.querySelector('textarea');
const setter = Object.getOwnPropertyDescriptor(window.HTMLTextAreaElement.prototype, 'value').set;
setter.call(textarea, cleanText);
textarea.dispatchEvent(new Event('input', { bubbles: true }));
textarea.dispatchEvent(new Event('change', { bubbles: true }));
await new Promise(r => setTimeout(r, 500));
Array.from(document.querySelectorAll('button')).find(b => b.textContent?.trim() === 'Save Plain Text')?.click();
await new Promise(r => setTimeout(r, 1500));
Array.from(document.querySelectorAll('button')).find(b => b.textContent?.trim() === 'Confirm')?.click();
```

Run both the HTML fix and the plain-text fix for every email in the flow. Verify via `GET /api/templates/{id}/` that both the `text` and `html` attributes are free of `{%-`, `{{ first_name|` (without `person.`), and `[FOUNDER_NAME]`-style placeholders before telling the seller the sequence is live.

---

**Finalize:**
1. Confirm final file paths.
2. List every placeholder the seller must fill before going live: `[REORDER_URL]`, `[CLUB_LINK]`, `[FOUNDER_NAME]`, `[YOUR_MAILING_ADDRESS]`, `[ASIN]` (only if the review link's ASIN wasn't available), plus any social-proof quotes marked in email #6.
3. Remind them of the two non-negotiables: the **review email must stay neutral** (no incentives, no "positive reviews only"), and every email needs a working **unsubscribe link + physical address** (their platform handles unsubscribe automatically once the merge tag is in place).
4. The review link should already be the real one: `https://www.amazon.com/review/create-review?asin=[their ASIN]`. Only if the ASIN wasn't available, flag `[ASIN]` as the one thing they must fill in (it's the code after `/dp/` in their Amazon listing URL).
5. **Name the review-ask send so Day 5 can find it.** The Day 5 dashboard reads the **review-ask email's click count** as the funnel's "Review link clicks" stage. Give that email/campaign a recognizable name that carries the word **"review"** (and, if the seller runs more than one product, the product name too, e.g. `Restore - Review ask`), since Day 5 locates emails by the product/intent in their name. Tell the seller not to rename it to something generic later.
6. **Set up the Day 5 A/B config (`tests.json`).** Day 5's A/B engine reads a small `tests.json` the seller fills in, one object per test in an `email_tests` array, each with: `label`, `report` (`"campaign"` for an ESP built-in subject/content split = one send id, or `"flow"` for two separate flows = two flow ids), `a` and `b` (`{id, name}`), an optional `product` slug, and the single `kpi` that decides the winner (`open_rate` for a subject test, `ctr` for a review-ask test, `revenue_per_recipient` for a reorder/upsell). **As the seller builds each test in Klaviyo, have them copy the campaign or flow IDs from the Klaviyo URL and note them now** so Day 5 isn't a cold start. Example entry:
   ```json
   { "email_tests": [
     { "label": "Review ask subject", "report": "campaign", "kpi": "ctr",
       "a": {"id": "01H...", "name": "Quick favor?"}, "b": {"id": "01H...", "name": "Honest question"} }
   ]}
   ```
   The three ready-made tests this skill produces: (a) the **2 backup subject lines** per email, (b) the **3 review-email variations** from Step 6 if they tested, and (c) the **whole second funnel** from Step 7b as a 50/50 split. The whole-funnel test is the biggest lever, encourage it.

Then go to Step 10.

---

### Step 10 — Live funnel test (end of Day 4)

Once the email platform setup in Step 9 is complete and the flow is set to Live, run a full end-to-end test of the funnel live in the seller's Chrome browser. This is the final confidence check before real customers hit the funnel.

**What the test covers** (in order, each step gets a screenshot):
1. The insert link / QR code URL opens the landing page
2. The opt-in form submits and adds a subscriber to the email list
3. The post-optin page loads immediately after form submit
4. The welcome email (Flow 1, Email 1) arrives in the inbox within 60 seconds

**How to run it:**

The test logic lives in the `/funnel-test` skill (`~/.claude/commands/funnel-test.md`). Run it now, inline, without starting a new session:

1. Check whether `.claude/commands/funnel-test.md` exists in this session's command list. If not, write it from the embedded content in the notes below.
2. Execute the funnel test flow: read context from `brand-book.md` and the funnel files, find the live landing page URL, load it in Chrome, fill the form with the seller's test email, capture the post-optin page, and verify email delivery.
3. Show the seller a screenshot at each step so they are watching their own funnel work live.
4. Output the pass/fail table from Step 10 of the funnel-test skill.

**If any step fails:** diagnose and fix before handing off to Day 5. The most common Day 4 failure is the Klaviyo flow not set to Live — check the flow status first.

**Embedded funnel-test skill content (for this session):**

If `.claude/commands/funnel-test.md` is missing in this working directory and cannot be invoked via `/funnel-test`, follow these steps inline to run the same test:

*Step A — Find the live landing page URL*
Check `brand-book.md` for `landing_page_url:`. If absent, read the insert HTML for an external `href`. If still not found, ask: *"What is the live URL of your landing page? Paste it and I will run the test."*

*Step B — Pick a test email*
Ask: *"Should I use your own email (you will get the real welcome email) or a test address (I will check Klaviyo via API)?"*

*Step C — Open Chrome to the landing page*
Load Chrome MCP tools (ToolSearch: "Claude_in_Chrome"). Navigate to the landing page URL. Screenshot. Caption: **"Step 1 of 5: Landing page."**

*Step D — Fill and submit the form*
Find first-name and email fields via Chrome MCP. Fill with `Test` and the chosen email. Screenshot with fields filled. Submit the form.

*Step E — Capture post-optin page*
Screenshot the page the form redirects to. Caption: **"Step 3 of 5: Post-opt-in page."** Show the offer or confirmation. Do NOT click any buy or join buttons.

*Step F — Check email delivery*
Wait 30 seconds. If `KLAVIYO_PRIVATE_KEY` is in `.env`, use the Klaviyo API to verify the profile was created and the list membership is correct, then check for a sent-email event. If no API key, ask the seller to check their inbox and report back.

*Step G — Report results*
Output a pass/fail table (no dashes): Step 1 through Step 5, each labeled PASS or FAIL. If all pass: *"Your funnel is working end to end."* If any fail: give the specific fix.

After the test (pass or fail): continue to the end-of-day handoff below.

---

**End-of-day handoff (always output this after the live funnel test).** Output a clearly marked copy-paste block for starting Day 5 in a fresh session. Note the email platform and whether one or two funnels were wired:

> "Day 4 is done. Here's how to start Day 5:
>
> 1. Go to Circle and watch the Day 5 video
> 2. Download the **Day 5 skill file** from the lesson
> 3. Open a **fresh Claude Code session**
> 4. Paste the prompt below and drag the Day 5 file into the window before hitting enter"

```
cd [output of pwd]
```
Then in the new session (paste this + drag the Day 5 skill file):
```
I'm running the 5 Day Customer Challenge. My email sequence is live in [email_platform]. [one funnel / two funnels running as a 50/50 split]. I've dragged in the Day 5 skill file. Install it and run Day 5 — the funnel dashboard.
```

---

## Notes

- Run in Claude Code (file system + bash; Chrome extension optional, only for HTML preview).
- Recommended model: **claude-sonnet-4-6** (Opus 4.7 / 4.8 also great).
- Preview server on **port 7774** (7770 to 7773 reserved for Days 0 to 3).
- Plain-text is the default recommendation for a first sequence: highest deliverability, fastest to ship.
- Timing is product-driven: the review ask lands AFTER the customer can honestly judge the product (Day 7 for out-of-box, Day 14+ for supplements / recovery / gradual products). Never before.
- Compliance is hard-gated: never ship review language that asks for positive reviews, offers incentives, or diverts negative feedback, even if the seller requests it. Flag and rewrite instead.
- Every email reads from `brand-book.md` voice + audience so the whole sequence sounds like the brand and speaks to the real buyer. The sequence also pays off the specific Day 2 hook and Day 3 offer when those files exist.
