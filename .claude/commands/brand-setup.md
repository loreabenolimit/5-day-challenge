# Brand Setup — Day 0 of the 5 Day Customer Challenge

Day 0 of the challenge. Two jobs, in this order:

1. **Orient them in Claude Code** — get their workspace set up correctly so every later day just works.
2. **Build their brand book** — from whatever they give you: their store URL, an existing brand book they upload, extra files (logos, product photos, packaging), or just a conversation if they're starting fresh. Produce a real **brand-book.pdf** plus a **brand-book.md** that every later skill (insert, landing page, emails) reads from.

The brand book is the foundation of the whole challenge. Days 1–5 should never have to ask "what are your colors / fonts / voice" again — they read `brand-book.md`. This is also their first win: they finish Day 0 holding a professional brand document they can use anywhere.

**Tone throughout:** warm, encouraging, plain-English. Most of these people have never used Claude Code and aren't designers. Never make them feel behind. Short, punchy, formatted, never a wall of text.

> ⛔ **HARD RULE — NO DASHES IN ANY OUTPUT.** Never use an em dash (—) or en dash (–) in anything the user sees or keeps: not in `brand-book.md`, not in `brand-book.html`, not in the PDF, not in chat copy, not in titles, not in number ranges. Use a comma, a colon, a period, parentheses, or the word "to" for ranges (e.g. "300 to 700", not "300–700"). This applies even though THIS instruction file contains dashes; the rule governs what you produce, not what you read. Step 7 runs a mechanical check that will catch any that slip through, but write clean from the start.

---

## Flow

### Step 0 — Two opening questions + environment check

Before anything else, ask two questions. These shape how the entire Day 0 session runs.

**Question 1: Have you used Claude Code before?**

Ask warmly:

> "Quick question before we start: have you used Claude Code before, or is this your first time?"

- **No / first time:** run the full orientation in Step 1. Explain everything from scratch, go slower, reassure freely.
- **Yes, I've used it:** skip the basics in Step 1. Jump straight to confirming their folder and model, then get moving. Don't over-explain things they already know.

Note their answer — it affects tone and pacing for the whole session.

**Question 2: Where do you want your challenge folder?**

Ask:

> "Where would you like to keep everything you build this week? I'll create a folder there and set it all up. A good default is something like `Documents/[brand-name]-challenge` — or tell me wherever makes sense for you."

- Take whatever they say (or suggest the default if they're unsure), create the folder with `mkdir -p`, and `cd` into it. Do this for them.
- Inside that folder, immediately create `.claude/commands/` — this is where the skill files for each day of the challenge will live:
  ```bash
  mkdir -p .claude/commands
  ```
- **Save this skill file to the project commands folder now.** The user dragged `brand-setup.md` into this session — write its contents to `.claude/commands/brand-setup.md` so future sessions in this folder can run `/brand-setup` without needing to drag the file again:
  ```bash
  # Claude writes the skill content to .claude/commands/brand-setup.md
  ```
  Tell them: *"I've saved the Day 0 skill to your project folder. From now on, any skill file you drag into Claude at the start of a day gets saved here automatically — that's what makes `/brand-setup`, `/insert-creator`, etc. work."*

This establishes the pattern for every day of the challenge: open Claude Code in this folder, paste the day's prompt, drag the day's skill file in, and Claude installs it and runs it. They never touch the folder manually.

**Environment check (silent):**
- **bash + Python 3** — required. If unavailable, stop and tell them this challenge runs in Claude Code (desktop app or CLI) and they need to open it there.
- **Chrome extension (`mcp__Claude_in_Chrome__*`)** — note whether it's connected. Not a hard blocker (every step has a fallback), but Step 1b gets it connected if it isn't.
- **A browser for PDF export** — needed only at Step 8. Don't check now.

**Model:** ask the user to check which model is active — they can see it at the bottom of their Claude Code window. If it's already **claude-sonnet-4-6**, **claude-opus-4-7**, or **claude-opus-4-8**, move on silently.

Otherwise note it — Step 1 handles the model nudge.

Then go to Step 1.

### Step 1 — Welcome + orientation (the "what do I do now" moment)

**If they're new to Claude Code:** run the full version below. Settle them in before doing anything else. Keep it to a tight, friendly briefing — not a manual.

**If they've used Claude Code before:** skip down to the model note and three habits. Don't re-explain what they already know.

---

Say (for new users):

> "Welcome to Day 0. 👋 You're in Claude Code — think of it as Claude with hands. It can read and write files right here on your computer, build pages, make PDFs, the works. Everything you make this week gets saved as real files in your challenge folder, which we just set up."

Then give them a quick **model** note:

> "One quick setup note: I recommend running the challenge on **Claude Sonnet 4.6** — it's fast, handles everything we'll build, and gives you the most room to iterate without running low on usage. If you'd rather use **Opus 4.7 or 4.8**, that's fine too. Switch any time with `/model`."
>
> "Also worth flagging now: the challenge runs 6 sessions over the week, and some of them are heavy (Day 0 alone sets up 4 tools plus your brand book). **The Pro plan ($20/month) is the minimum to run Claude Code**, but if you hit a usage limit mid-session you'll have to wait a few hours before continuing. The **Max plan ($100/month)** gives you 5x the usage and is what I'd recommend for the challenge — it means no interruptions on the heavy days. You can start on Pro and upgrade if you hit a wall, or start on Max to run it clean."

If they're already on Sonnet 4.6, Opus 4.7, or Opus 4.8, confirm it in one line and move on.

Then give them the **three habits** that make Claude Code work (this is the mental model — keep it to these three, short):

> "Three habits that'll make this week click:
>
> - **Treat me like a sharp new creative director.** I'm fast and I follow direction, but I'm only as good as the brief. The more you tell me about your product and customer, the better everything comes out.
> - **Never settle for the first version.** If a color, a headline, or a layout isn't right, just say so. 'Make it warmer,' 'shorter,' 'try three other options.' Iterating is the whole point — it costs you nothing.
> - **Give me raw material.** Drop in your logo, product photos, listing screenshots, anything you've got. I build from what you give me. We'll start that right now."

Don't dump all the common beginner mistakes on them as a list — weave the fixes into the habits above. Then move into Step 1b to get them connected.

### Step 1b — Get connected (one-time setup for the whole week)

Day 0 is where we wire up everything the challenge needs, so that every later day is just "run the skill, build the thing." Walk through these connections now, warmly and one at a time. Keep it light, this is plumbing, not homework. If something doesn't connect, never block, note it and move on (every later day has a fallback), but do try to get them set up here.

**Start with a quick Claude connections check.** Before anything else, run `/mcp` in this session to see what's currently connected. Show them the output in plain English — most people have never seen it.

Say something like:

> "First, let me check what's already wired into Claude on your end."

Then explain what they're looking at:

> "Claude Code connects to outside tools through something called **MCP servers** — think of them as plugins that give me hands in your browser, your files, and external services. For this challenge, here's what I need connected:
>
> | Connection | What it does | When it's needed |
> |---|---|---|
> | **Claude for Chrome** | Lets me see and control your browser — read your site, deploy pages to Shopify, preview your funnel | Days 0, 2, 3 (required) |
>
> That's the one that matters most. Everything else in the challenge (Klaviyo, Shopify) connects through the browser or your files — no extra MCP setup needed.
>
> Let me check if Chrome is already connected."

Then route based on the `/mcp` output:
- **Claude in Chrome shows as connected:** confirm it and move to item 2 below.
- **Not listed or showing as disconnected:** walk them through it (item 1 below).
- **Other MCP servers are connected (Slack, Notion, email, etc.):** acknowledge them briefly — *"I can see you've got [X] connected too, great — that won't affect the challenge."* Then continue.

**1. The Chrome extension (Claude in Chrome).** This is what lets Claude see your live website, pull your real colors and fonts, deploy pages to Shopify, and preview what you build. Check if it's already connected from the `/mcp` check above.

- **If connected:** confirm in one friendly line: *"Your Chrome extension is connected — that's what I'll use to read your site, deploy your pages, and preview everything you build."*
- **If not connected:** walk them through it:
  > "One quick install and you're set: the **Claude for Chrome extension**. Here's how:
  >
  > 1. Open Chrome and go to the Chrome Web Store — search **'Claude for Chrome'** or go to claude.ai and find the extension link there.
  > 2. Click **Add to Chrome** and pin it to your toolbar.
  > 3. Open the extension and sign in with your Claude account.
  > 4. Come back here and let me know — I'll verify it's working.
  >
  > This is the one connection that makes the whole week smooth. If you'd rather skip it, I have fallbacks for most things, but Day 2's Shopify deploy works best with it connected."
  - After they say it's connected, verify with a quick `mcp__Claude_in_Chrome__navigate` to `about:blank`. If it works, confirm. If it still errors, reassure them you'll use WebFetch fallbacks and continue.

After the Chrome check, tell them plainly:

> "That's the only Claude-side connection needed for this challenge. Everything else — Shopify, Klaviyo — we'll set up as accounts and credentials, not Claude plugins. Let's get those sorted now."

**2. Their Shopify store.** Day 2 deploys the opt-in landing page directly to Shopify. Without a store, there is nowhere to publish it.

Ask:

> "Do you have a **Shopify store** set up for this brand? That's where we'll publish your landing page on Day 2 — it takes about 2 minutes to deploy once you're logged in.
>
> - **Yes, I have a store:** great, just share the store URL (e.g. `yourstore.myshopify.com`) and make sure you can log into the Shopify admin. I'll note it and we're set.
> - **No store yet:** no problem — Shopify has a free trial and it's the easiest path for this challenge. I can open the signup in your browser right now and walk you through it. It's the go-to platform for Amazon sellers moving to DTC."

Route based on their answer:

**If they have a store:** extract the store handle from the URL (the subdomain before `.myshopify.com`, e.g. `yourstore`). Record it in `brand-book.md` under `shopify_store_handle`. Confirm in one line: *"Got it, I'll use `yourstore` for the Day 2 deploy."*

**If they don't have a store:** navigate to `https://www.shopify.com/free-trial` using the Chrome extension and walk them through signing up. Key points to communicate:
- Free 3-day trial, then $1/month for the first 3 months — no risk to get started
- They don't need to set up a full store before Day 2; just having an account with an admin panel is enough
- Once they've created the account, grab the store handle from the URL bar (`https://admin.shopify.com/store/[handle]`) and record it

If they want to skip it for now, that is fine — but flag it clearly: *"You'll need this before Day 2. When you're ready, just say 'let's set up Shopify' and I'll open it up and handle it."* Record `shopify_store_handle: "none yet"` in `brand-book.md`.

**3. Your email platform.** Day 2 wires the opt-in form to a real list. Day 4 builds the automated sequence. Without this in place, everyone who scans your QR code goes through the funnel but their email is never saved — the whole point of the challenge is lost.

**You do not need to set this up right now to finish Day 0.** But it must be done before you start Day 2. Flag it clearly so they don't forget.

**The recommendation is Klaviyo.** It is free up to 250 contacts with flows and automations included, no credit card needed. It is purpose-built for eCommerce and is what the rest of the challenge is written around. If they already use something else, that works too.

Say:

> "One thing you'll need before Day 2: an email platform. This is where every subscriber who scans your insert QR code goes — without it, the form captures nothing. You can set it up now or come back to it before Day 2, but it's a must.
>
> **My recommendation: Klaviyo.** It's free to start (up to 250 contacts, no credit card), built specifically for eCommerce, and it's what Days 2 and 4 are built around. If you already use something else — Mailchimp, Omnisend, MailerLite, ActiveCampaign — that works too, just tell me which.
>
> Want to set it up now? I can open Klaviyo in your browser and walk through it with you — I'll handle most of the clicking."

**Default: always offer to do it for them.** Never just give step-by-step instructions and wait. Open Klaviyo in their browser using the Chrome extension (`mcp__Claude_in_Chrome__navigate`) and drive the setup yourself. Ask questions along the way; don't front-load them. The seller should feel like they're watching it get done, not doing it themselves.

**If they want to do it now (or already have Klaviyo):**

Navigate to `https://www.klaviyo.com` and check what state their account is in. Route from there:

**A) No account yet:**
Navigate to `https://www.klaviyo.com/register`. Fill in their name and email if you know it from the brand book or conversation. Tell them: *"I've opened the signup page — you'll need to type your password and click 'Create account' yourself, then come back and let me know when you're in."* Once they confirm they're logged in, proceed to the account setup steps below.

**B) Account exists, setup not complete (they land on an onboarding page like `/account-setup/*`):**
Drive the setup steps directly. Use `mcp__Claude_in_Chrome__read_page` (filter: interactive) on each screen to find the form fields, then `mcp__Claude_in_Chrome__form_input` to fill them. Proceed screen by screen:

- **Mailing address screen** (`/account-setup/mailing-address`): Required by anti-spam law — this address appears in every email footer. Before filling it in, search the user's files for a business address:
  1. Search `~/Downloads`, `~/Desktop`, and `~/Documents` for files with "incorporation", "certificate", "registered", or the company name.
  2. Check `brand-book.md` for any address field.
  3. If found, show it to the user: *"I found this address in your files: [address]. Is this the right one to use, or would you like to use a different one?"*
  4. If not found, ask: *"What business address should go in your Klaviyo account? This shows in the footer of every email — a registered business address, P.O. box, or office address all work."*
  5. Once confirmed, fill all fields (address line 1, line 2, city, country, state, ZIP) using `form_input`, then ask: *"Ready to submit — want me to hit Continue?"* and click the submit button only after they confirm.

- **Sender information screen** (`/account-setup/sender-info`): Read the pre-filled Sender Name and Sender Email. Show them to the user: *"Next screen is your sender info — this is what subscribers see in their inbox. It's pre-filled as: [Name] / [Email]. Does that look right, or want to change either one?"* Update if needed, then click Continue.

- **Plan selection screen** (appears as a modal or `/onboarding/...` page titled "Plan recommendations" or "Compare plans"): Say: *"You're on the Free plan — 250 contacts, 500 emails/month, full automations, no credit card. That's everything you need for this challenge. Want me to keep the free plan?"* Once they confirm, click **"Keep my current free plan"**. If they're not yet on the free plan, find and click the Free plan's "Select Plan" button. Never suggest upgrading — the free plan covers the whole challenge.

- **Subsequent onboarding screens**: read each screen with `read_page`, fill what you can, ask what you need, and always offer to click through rather than asking them to do it.

**C) Account exists and setup is complete (they land on the dashboard):**
Skip onboarding. Go straight to creating the list and getting credentials (steps below).

**What you're setting up and why (say a plain-language version to the seller, so it isn't just credentials with no context).** Three pieces, each with a job:
- **The List** — the bucket every opt-in lands in. The insert form subscribes people here, and *joining the list is what triggers the welcome/guide email* (built Day 4). No list, nowhere for leads to go.
- **The Public API key (site ID)** — a short, safe-to-expose key that lets the opt-in form's JavaScript add a subscriber straight from the page. It is meant to live in public page code.
- **The Private API key** — full-access and secret. Used only for server-side setup (like creating the list here). It goes in `.env` and *never* in a page.
Grab all three now so Days 2 to 4 are plug-and-play instead of stopping to hunt for credentials.

**Creating the list (do this yourself via the API once you have credentials, or via the browser).** Name it for what it is, a list of opt-in leads. Do **NOT** name it "Buyers" (free subscribers have not bought anything, and that name pollutes your real buyer segmentation later) and do **NOT** name it "Newsletter" (people who opted in for a free guide did not sign up for a newsletter — that framing drives unsubscribes).

If you already have a Private API Key:
```bash
curl -s -X POST https://a.klaviyo.com/api/lists/ \
  -H "Authorization: Klaviyo-API-Key $KLAVIYO_API_KEY" \
  -H "revision: 2024-10-15" \
  -H "Content-Type: application/json" \
  -d '{"data":{"type":"list","attributes":{"name":"[Product Nickname] Subscribers"}}}'
```
Parse the `id` from the response, that is the List ID.

If you do not yet have an API key, create it in the browser: navigate to `https://www.klaviyo.com/lists` → "Create List" → name it `[Product Nickname] Subscribers` → save → open the list settings and read the List ID from the page (`javascript_tool` / `read_page`).

**Set the list to SINGLE opt-in (this directly controls whether the guide email sends).** A lead-magnet list must be single opt-in so the welcome/guide email fires the instant someone joins. Double opt-in inserts a "confirm your subscription" email first, which most freebie-seekers never click, so the guide never arrives. Navigate to the list → **Settings → Consent → choose "Single opt-in" → Save**. Confirm in one line: *"Set your list to single opt-in so the guide sends instantly, with no extra confirmation step."*

**Getting BOTH keys (do this yourself via the browser).** Navigate to `https://www.klaviyo.com/settings/account/api-keys`:
- **Public API Key / site ID** — copy the short value shown at the top (`javascript_tool` / `read_page`) and record it in `brand-book.md` as `klaviyo_public_key`. This is the one the opt-in form uses; it is safe in page code. (This is a different key from the private one, and the part later days were missing if you skip it.)
- **Private API Key** — click "Create Private API Key", name it "Challenge", scope Full Access, Create, then read the `pk_...` value immediately (it is shown once). Save it to `.env` as `KLAVIYO_API_KEY=pk_...` and add `.env` to `.gitignore`. Never put this in a page.

Once you have the List ID, public key, and private key, record them in `brand-book.md`:
```yaml
email_platform: klaviyo
klaviyo_list_id: "XXXXXX"
klaviyo_public_key: "XXXXXX"   # safe to expose; used by the opt-in form JS
# private key lives in .env, never in brand-book.md or any page
```
Tell them: *"All done. Your list, public key (for the form), and private key (saved privately) are wired up. Days 2 to 4 just use these."*

**If they want to come back to it:** fine. Record `email_platform: klaviyo` and `klaviyo_list_id: "none yet"` with a comment, and restate: *"Make sure this is in place before Day 2, the form captures nothing without it. Say 'let's connect Klaviyo' when you're ready and I'll handle it."*

**If they use a different platform (Mailchimp, Omnisend, ConvertKit, MailerLite, ActiveCampaign).** The same three jobs exist everywhere (a list, a public capture key/endpoint, and a signup-triggered automation that delivers the email); only the wiring differs. Record in `brand-book.md`: `email_platform: <name>`, the **audience/list ID**, and the **public capture method** (a public form-action URL or a public/site key), and note where the delivery automation lives. Per-platform notes:
- **Mailchimp** — capture posts to the audience's embedded form-action URL (`https://<dc>.list-manage.com/subscribe/post?u=...&id=...`). GOTCHA: that endpoint is NOT CORS-readable from JavaScript, so the form has to submit fire-and-forget through a **hidden iframe** (or a tiny serverless function), and you confirm a signup by checking the audience grew, not a fetch response. Delivery = a **Customer Journey** triggered on "joins audience" / tag added. Record the audience ID and the `u` + `id` form params.
- **Omnisend / ConvertKit / ActiveCampaign / MailerLite** — each has a CORS-friendly form or API endpoint plus signup-triggered automations; record the list/form ID and the public key or endpoint.
In every case the delivery email is just an automation triggered by the signup, carrying the same hosted PDF link (set up Day 3). Same rule: must be in place before Day 2.

**4. Plan + model** are already handled in Step 1 — just make sure they're squared away (Pro plan or higher (Max recommended), Sonnet 4.6 or Opus 4.7/4.8). No need to repeat if you already covered it.

**5. Auto-accept mode (so the rest of the week is smooth).** Every later day writes files and runs small commands (starting a preview server, making a PDF). In the default mode they'd get a permission prompt on each one, which is tedious. Set them up so it just flows:

- Tell them: *"One last thing that makes every day smoother: turn on auto-accept mode so you're not clicking 'approve' on every file I save. Press **Shift+Tab** to cycle the mode (look at the bottom of your Claude Code window), and stop on **'auto-accept edits'**. That lets me write your files without asking each time. You can press Shift+Tab back to normal anytime."*
- **Also pre-approve the handful of commands the skills use,** so bash steps don't prompt either. Write `.claude/settings.json` in the challenge folder (create `.claude/` if needed). If a `.claude/settings.json` already exists, merge into it rather than overwriting:
  ```json
  {
    "defaultMode": "acceptEdits",
    "permissions": {
      "allow": [
        "Bash(python3 -m http.server:*)",
        "Bash(python3 -m pip install:*)",
        "Bash(python3 dashboard.py:*)",
        "Bash(npx puppeteer:*)",
        "Bash(grep:*)",
        "Bash(curl:*)",
        "Bash(mkdir:*)",
        "Bash(lsof:*)",
        "Bash(pkill:*)"
      ]
    }
  }
  ```
  This sets the folder to auto-accept edits by default and whitelists the exact commands the challenge runs (local preview servers, dependency installs, the dash-sweep grep, logo downloads, the Day 5 dashboard). Nothing destructive is whitelisted, anything outside this list still asks. Tell them: *"I also pre-approved the few safe commands this challenge uses, so you won't be interrupted, and anything new still asks first."*
- Note: a newer **"Auto"** mode (full auto-approve with safety checks) may appear in the Shift+Tab cycle on some versions. If they have it and prefer it, it's fine, but **auto-accept edits + the pre-approved commands above is the recommended, stable setup** for the challenge.

Capture what got connected (Chrome extension yes/no, Shopify store handle, email platform + list ID + public key + list set to single opt-in, auto-accept set) so Step 5 can write it into `brand-book.md` and Step 9 can confirm it. Then move into Step 1c.

### Step 1c — Pick your first-run product (ASIN + nickname)

The challenge builds a funnel for **one product at a time**. Have them choose the **single product they'll run first** now, so every later day references the same thing without re-asking. Keep it to three quick answers:

> "Last setup question: which **one product** do you want to build this funnel around first? Pick the one that matters most, your best seller, a new launch, or whatever you most want more reviews and repeat buyers for. (You can run the challenge again for other products later.) I just need two things:
>
> 1. **The ASIN** of that product, the 10-character code in your Amazon listing URL, right after `/dp/`. Or paste the listing URL and I'll pull it.
> 2. **A short nickname** you want to call it throughout, e.g. 'Alpha Charge' or 'the dog brush.' This is just so we both refer to it the same easy way all week (and after)."

- **Extract the ASIN** from a pasted `/dp/<ASIN>` URL if they give a link. If they don't know it, that's the one thing to find later, note it.
- The **nickname** is their plain-language handle for the product, used in chat and as a label in later outputs so nothing feels generic.
- Record both for Step 5 (`asin`, `product_nickname` in the YAML). If they sell across multiple marketplaces, the ASIN can be the primary-marketplace one; note the others only if it comes up.

This is also the natural place to surface the **one-link-per-product** principle they'll meet on Day 1: since they're focusing on one product, its insert gets its own link/QR, and the opt-in alone will tell them it's this product. Then move into Step 2.

### Step 2 — Gather their material (intake)

This is where they hand you the raw material. The website is the easiest starting point, but it is **not** the only thing they can give you. Open it up and invite anything.

Ask, warmly:

> "Now let's pull your brand together. Every seller has at least one of these — drop whichever fits:
>
> - **Your website** (Shopify store, brand site, or brand landing page) — easiest starting point, I'll pull your colors, fonts, and logo automatically
> - **Your Amazon listing URL** — works great if your website doesn't reflect your brand well or isn't live yet
> - **Any other landing page or sales page** you're actively using
>
> And anything else you've got, send it my way. The more you give me, the more it'll feel like *you*:
> - **Logo files** (PNG, SVG) or **product photos**
> - **Packaging, labels, or inserts** you already use
> - **An existing brand book or brand guidelines** (PDF, Word, Canva export, even a screenshot) if you have one
> - **Notes** on your voice, your story, your customer, or brands you admire"

Then route based on what they give you. These paths are **not exclusive** — combine whatever applies:

- **Path A — website, listing, or page URL:** go to Step 3 (extract from site). If they give you their Amazon listing, navigate to it and extract what you can: product name, category, images, key phrases that reveal voice and audience. Colors from a listing are less reliable than a brand site — pull them but treat as a starting point.
- **Path B — existing brand book / guidelines:** go to Step 2b (parse it first), then fill only what's missing.
- **Any extra files they drop** (logos, photos, screenshots, packaging): read them with the Read tool, save useful images into `assets/`, and feed them into the right brand-book section (logo, imagery, colors). Always acknowledge what they gave you so they feel heard.

Whatever they send, treat it as input — never tell them something they offered isn't useful. If you're unsure where a piece fits, ask a quick clarifying question, then use it.

### Step 2b — They already have a brand book (parse, then gap-check)

If they upload an existing brand book or brand guidelines, **do not rebuild it from scratch and do not make them re-answer things it already covers.** Your job is to read it, reuse what's there, and find what's missing for this challenge.

1. **Read it.** PDF → use the Read tool with the `pages` parameter. Word doc → use the docx skill. Images/screenshots → read them visually. Pull out everything it already defines.
2. **Map it to the 8 sections** this challenge needs (see Step 5): brand at a glance, logo, colors, typography, imagery, voice & tone, audience, application notes.
3. **Show them a quick gap report** — what's covered and what's thin or missing, in plain language. For example:
   > "Nice, this is a solid start. You've already got your **logo, colors, and fonts** locked in. For the challenge I'll also want a clear **voice** and a sharp **audience** definition, since those drive the insert and email copy on Days 1 and 4."

4. **Let them choose how to proceed.** Offer both options plainly and follow their lead:

   > "Two ways we can go from here:
   >
   > **A) Keep your brand book as-is.** I'll leave your document exactly as it is and just create the small `brand-book.md` file the rest of the challenge reads from, pulling the key details out of yours. Nothing about your design changes.
   >
   > **B) Generate a fresh one.** I'll build you a new, polished brand book in the challenge's format, combining what's in your current book with anything I pull from your website and other assets. You'll still keep your original. Which sounds better?"

   - **If A (keep theirs):** do not produce a styled PDF. Just write `brand-book.md` (the YAML profile block + the 8 sections, filled from their document and any gaps), and point later days at it. Leave their original file untouched.
   - **If B (regenerate):** build the full `brand-book.md` **and** the styled `brand-book.html` → `brand-book.pdf` as normal (Steps 5 through 8), enriched with their site and assets. Tell them their original is untouched and they now have a fresh version too.

5. **Fill only the gaps** — pull missing visual tokens from their site (Step 3) if they gave a URL, and ask only the interview questions (Step 4) that their brand book didn't already answer. Never re-ask what's already in the doc.

Either way, the deliverable that powers the rest of the challenge is `brand-book.md`. The styled PDF is produced only on path B (or path C / fresh builds). Then continue to Step 3 (only for any missing visual tokens) and/or Step 4 (only for unanswered questions).

### Step 3 — Extract the brand from their site

Pull the visual identity automatically. Don't make them tell you colors and fonts — read it off their site, then let them correct it later in the preview. (Skip any token their uploaded brand book already defined.)

**Fonts — preferred method (Chrome extension):**
Navigate to the brand URL with `mcp__Claude_in_Chrome__navigate`, then read computed styles:
```js
const h1 = document.querySelector('h1');
[
  'h1: ' + (h1 ? window.getComputedStyle(h1).fontFamily : 'none'),
  'body: ' + window.getComputedStyle(document.body).fontFamily
]
```
This returns the real font families (e.g. `Satoshi`, `Assistant`). Use these directly.

**Fonts — fallback (no extension):** Fetch the page HTML with WebFetch and look, in order:
1. `<link>` tags pointing to Google Fonts — extract the family from the URL
2. `@import` rules pointing to fonts.googleapis.com
3. CSS `font-family` on `:root`, `body`, or custom properties — including Shopify theme variables (`--font-heading-family`, `--font-body-family`)
4. `@font-face` declarations

If the font is on Google Fonts, plan to load it via `<link href="https://fonts.googleapis.com/css2?family=...">`. If it's on Fontshare (e.g. Satoshi, Cabinet Grotesk), use `<link href="https://api.fontshare.com/v2/css?f[]=[name]@900,700,500,400&display=swap">`. Default to **Inter** if nothing is found.

**Colors — look for in this order:**
1. CSS custom properties (`:root` variables) — `--bg`, `--accent`, `--white`, `--primary`, `--color`, etc.
2. `background` and `color` on `body`
3. Button background colors (`.btn`, `.cta`, `button`)

Capture at least: **background**, **accent/brand**, **text**. If you can find secondary/supporting colors, grab those too. If extraction fails, use safe defaults: white bg (#ffffff), near-black text (#111111), a blue accent (#1696D2), Inter font — and tell them you used starting defaults they can change.

**Logo:** find the logo image on the site (header `img`, or any image with "logo" in the filename/alt). Download it into the working folder as `assets/logo.png` (create `assets/` if needed) so it can be embedded in the brand book. If you can't find one, note it and move on — don't block.

**White/reversed logo (needed for the Day 5 dashboard's dark mode).** Also produce `assets/logo-white.png`, a version that reads on a dark background, and set `logo_white` in the brand book. Easiest reliable methods, in order: (a) if the site offers a white/reversed logo variant, download that; (b) if the logo is a simple 1-color mark on a transparent PNG or SVG, recolor its marks to white (e.g. with Python/Pillow: load the PNG, and for every non-transparent pixel set RGB to white, keeping the alpha) and save as `assets/logo-white.png`; (c) if the logo is multi-color, illustrated, or photographic, the pixel-recolor approach will destroy it — set `logo_white: "none"` and note it clearly: *"Your logo has multiple colors so a white version would need to be made by a designer. I've set logo_white to none — the Day 5 dashboard handles this gracefully."* A white-on-dark logo is what keeps the dashboard's dark theme from showing an invisible or clashing mark.

**Product imagery (for the imagery section):**
1. Search their site's product page for gallery images (`document.querySelectorAll('img')` via the extension, sorted by natural width), OR
2. Search their Amazon listing (`site:amazon.com [brand] [product]`), open the ASIN page, grab the main gallery image.
Save 1–3 representative shots to `assets/` if available. Optional — skip silently if nothing's found.

Don't confirm each value with the user as you go — just extract and proceed. They'll see everything in the preview and can fix it there.

### Step 4 — Fill the gaps (brand fundamentals interview)

The site gives you the look. Now get the substance — the things a site can't tell you. Ask these conversationally, **one or two at a time**, not as a form. Keep it light.

**Only ask what you don't already know.** Skip anything their website, uploaded brand book, or extra material already answered. If they gave a URL, use what you saw to make smart guesses and just ask them to confirm ("Looks like you sell ___ — is that right?"). The goal is the fewest questions needed to fill real gaps, not a full interview every time.

Cover (whatever is still missing):
1. **What you sell, in one line** — the product and who it's for.
2. **The customer** — who buys this? What problem are they trying to solve? What does life look like after your product fixes it?
3. **Voice** — *"If your brand were a person, how would it talk?"* Offer quick anchors to pick from or riff on: warm & friendly / bold & punchy / premium & calm / expert & no-nonsense / playful. Land on **3–5 adjectives**.
4. **Words you love / words you'd never use** — optional but gold for the voice section.
5. **Logo confirmation** — if you found one, show it; if not, ask if they have a file to drop in (and proceed without if not).
6. **Compliance category** — ask this if the product type isn't already clear from their site or materials: *"Quick one: is your product a supplement, health/wellness product, financial product, or anything else that comes with claims you need to be careful about? Or is it a general physical good with no special requirements?"* Map their answer to: `supplement`, `health`, `finance`, `beauty`, `other-regulated`, or `none`. This controls whether later days auto-add required disclaimers (e.g. the FDA statement for supplements) to downloadable guides and email copy. If obvious from the product (e.g. a protein powder, a sleep supplement), infer it and confirm in one line rather than asking.

For Branch B (no site), also ask their **color vibe** (e.g. "earthy and natural," "clean and clinical," "bold and energetic") and pick a tasteful starter palette from that — they can refine in preview.

Keep it tight. You're aiming for enough to write a real brand book, not a branding agency intake.

### Step 5 — Assemble `brand-book.md` (the source of truth)

Write `brand-book.md` to the working folder. This is the file every later day reads, so it must be clean and parseable. Start with a compact machine-readable block, then the human-readable guide.

**The fenced block below is illustrative scaffolding showing the file's contents. Do NOT write the outer ` ```markdown ` / closing ` ``` ` wrapper into the actual file.** The real `brand-book.md` must begin with the `#` heading and contain exactly one clean **top-level ` ```yaml ` block** (the dashboard and every later day parse that block by matching a ` ```yaml ` fence, so a stray wrapper fence around it will break parsing).

```markdown
# [Brand Name] Brand Book

<!-- BRAND PROFILE: machine-readable. Later challenge days read this block. -->
```yaml
brand_name: [name]
website: [url or "none yet"]
one_liner: [what they sell, one line]
colors:
  background: "#ffffff"
  text: "#111111"
  accent: "#1696D2"
  secondary: ["#...", "#..."]   # optional
fonts:
  heading: "[Heading font]"
  body: "[Body font]"
logo: "assets/logo.png"          # full-color logo, for light backgrounds. or "none"
logo_white: "assets/logo-white.png"   # white/reversed logo for DARK backgrounds (the Day 5 dashboard's dark mode needs this). or "none"
voice: ["adjective1", "adjective2", "adjective3"]
audience: "[one line — who buys and the problem solved]"
compliance_category: "[none | supplement | health | finance | beauty | other-regulated]"
shopify_store_handle: "[subdomain from myshopify.com URL, e.g. yourstore — or 'none yet']"
email_platform: "[klaviyo | mailchimp | omnisend | mailerlite | other — from Step 1b]"
klaviyo_list_id: "[List ID the opt-in form subscribes people to, e.g. ABC123 — or 'none' if different platform]"
klaviyo_public_key: "[Public API Key / site ID, safe to expose in page JS, e.g. ABC123 — used by the opt-in form to capture emails; 'none' if different platform]"
product_nickname: "[short handle for the first-run product from Step 1c, e.g. Alpha Charge]"
asin: "[the first-run product's Amazon ASIN from Step 1c, e.g. B08VW2NDKX, else 'none']"
```

Note: the Klaviyo **public key (site ID)** above is safe to expose and is what the opt-in form's JavaScript uses to capture emails (Day 2). The **private API key** is NOT stored in this file, it lives in `.env` as `KLAVIYO_API_KEY=pk_...` (gitignored) and is used only for server-side setup. Set `compliance_category` from the product type: if it is a supplement, health, finance, or other regulated product, later days add the required disclaimer to any downloadable guide and to ad/email copy automatically.

Fill `product_nickname` and `asin` from Step 1c (the first-run product). If Step 1c didn't land the ASIN, leave it `none`, it's the one thing to fill in before Day 4's review link. Later days use `product_nickname` to refer to the product in chat and to label files/outputs, and `asin` to build the Amazon review link.

## 1. Brand at a glance
[Name, what they sell, the one-line positioning, the customer in a sentence.]

## 2. Logo
[Embed the logo. Clear-space rule (keep padding around it equal to or greater than the height of one letter). Minimum size. 2 to 3 quick don'ts: don't stretch, don't recolor, don't add effects.]

## 3. Colors
[Each color as a labeled swatch with its HEX and its ROLE: background / text / accent / secondary. Note where each is used — accent for buttons, CTAs, highlights; text for body; background for surfaces.]

## 4. Typography
[Heading font + body font, with weights. A short note: headings = [font] bold; body = [font] regular. Where they came from / how to load them.]

## 5. Imagery
[Direction for product and lifestyle shots — the feel, not a stock library. Include the saved product shots if any. Note the kind of imagery that fits the voice.]

## 6. Voice and tone
[The 3 to 5 adjectives. 2 to 3 "we sound like this" example sentences written in their voice. A short do/don't word list if they gave one. One line on tone shifts by context if relevant — e.g. punchy in ads, warmer in emails.]

## 7. Audience
[Who buys, the problem they have, the transformation after your product. This is what makes every later day's copy land — keep it concrete.]

## 8. Using this brand book in the challenge
[One short paragraph: this file is your single source of truth. Days 1 to 5 (insert, landing page, post-opt-in, emails) all build from these colors, fonts, voice, and audience. You can edit this file any time as your brand evolves — and reuse it anywhere outside the challenge too.]
```

Fill every section with THEIR specifics — never leave a template placeholder in the final file. Keep the machine-readable YAML block accurate; that's what later days parse.

### Step 6 — Build the styled brand book (`brand-book.html`)

> **Skip Steps 6 to 8 if they chose path A in Step 2b** (keep their existing brand book). In that case `brand-book.md` is the only deliverable — confirm it's written, then jump to Step 9 to wrap up. Build the styled HTML/PDF for everyone else (fresh builds, path C, and path B "generate a fresh one").

Turn the markdown into a clean, on-brand HTML document — this is what becomes the PDF. It should look like a real brand guide, using their own colors and fonts so it feels like *theirs*.

Design requirements:
- **Letter-size pages** (8.5in x 11in), multi-page, with generous margins. This is a document, not a one-pager.
- A **cover page**: brand name large, logo, one-liner, "Brand Book" + the date.
- One clean section per the 8 headings above. Use their heading font for titles, body font for text.
- **Color swatches** rendered as actual colored blocks with the HEX printed on/under each.
- **Type specimens**: show the heading font and body font with a sample line each ("The quick brown fox…").
- Embed the logo and any product images (base64 or referenced from `assets/` — base64 is safest for a portable PDF).
- Subtle, premium, lots of whitespace. The accent color used sparingly for rules/section numbers. No clutter.
- **No em dashes** anywhere in the copy.

Include the print CSS so headless print-to-PDF comes out at exact Letter size:
```html
<style>
  @page { size: Letter; margin: 0.6in; }
  @media print {
    .page-break { page-break-before: always; }
    body { background: #fff; }
  }
  /* On-screen preview gets a soft gray backdrop; print stays white. */
  body { background: #f2f2f2; }
</style>
```
Load their fonts via the `<link>` you identified in Step 3 (Google Fonts or Fontshare). Default to Inter if none.

**After generating the HTML, go to Step 7 (preview). Do not export the PDF until they approve.**

### Step 7 — Preview BEFORE export (mandatory)

**Do NOT export the PDF yet.**

0. **Dash sweep (mandatory, before anything else, on BOTH paths).** `brand-book.md` (and `brand-book.html` if you built it) must contain zero em dashes and zero en dashes. Run:
   ```bash
   grep -n "—\|–\|&mdash;\|&ndash;" brand-book.md brand-book.html
   ```
   If it prints anything, rewrite each line to remove the dash (comma, colon, period, parentheses, or "to" for a range) and re-run until the command returns nothing.

1. Save the HTML to `brand-book.html` in the **current working directory** (never `/tmp`).
2. Start a local server: `python3 -m http.server 7770 --directory "$(pwd)" &` (use port 7770).
3. **Primary preview = headless screenshot (no extension needed).** Render page(s) to PNG at Letter dimensions using the system-Chrome ladder from Step 8, and show them inline with the Read tool.
4. **Optional, only if the Chrome extension is available:** also open the live page so they can scroll it:
   ```
   navigate to: http://localhost:7770/brand-book.html
   ```
5. **Reassure + suggest.** Open with: *"This is your first draft, nothing's locked. Colors, fonts, the cover, any wording, all changeable in seconds. Just say the word."* Then offer 2 to 4 concrete, tailored suggestions for THIS brand, and the export exit ("Or if you love it, I'll turn it into your PDF.").

Loop here until they're happy. When they approve, go to Step 8.

### Step 8 — Export the PDF

The deliverable is **always a real PDF saved to their folder** — never "download the HTML." If an export tool fails, install/retry rather than handing over HTML.

Serve the file first (if not already): `python3 -m http.server 7770 --directory "$(pwd)" &`

**System-Chrome export (preferred — no npm install):**
```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
[ -x "$CHROME" ] || CHROME="/Applications/Chromium.app/Contents/MacOS/Chromium"
[ -x "$CHROME" ] || CHROME="/Applications/Brave Browser.app/Contents/MacOS/Brave Browser"
[ -x "$CHROME" ] || CHROME="/Applications/Microsoft Edge.app/Contents/MacOS/Microsoft Edge"

URL="http://localhost:7770/brand-book.html"
"$CHROME" --headless=new --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="./brand-book.pdf" "$URL"
```

**Puppeteer fallback (if no system Chrome found):**
```js
const puppeteer = require('puppeteer');
const browser = await puppeteer.launch({args: ['--no-sandbox']});
const page = await browser.newPage();
await page.goto('http://localhost:7770/brand-book.html', {waitUntil: 'networkidle0'});
await new Promise(r => setTimeout(r, 1200));
await page.pdf({path: './brand-book.pdf', format: 'Letter', printBackground: true, margin: {top:'0.6in',bottom:'0.6in',left:'0.6in',right:'0.6in'}});
await browser.close();
```
Claude runs this itself and saves the file — the user does nothing.

### Step 9 — Wrap up (set up Day 1)

Confirm what they now have, in their folder:
- brand-book.md — the working source of truth the rest of the challenge reads (always produced)
- brand-book.pdf — their polished brand book, use it anywhere (only if a fresh one was generated)
- assets/ — their logo and product shots (if any were saved)

And confirm they're **fully connected for the week** (from Step 1b), so every later day is just "run the skill":

**Claude connections (MCP):**
- Claude for Chrome connected and verified (or noted as skipped, with WebFetch fallback in place)

**Accounts and credentials:**
- Shopify store handle recorded in brand-book.md (or flagged as must-do before Day 2)
- Klaviyo (or other platform): list created (named for opt-in leads, not "Buyers", and set to single opt-in), List ID + public key in brand-book.md, private API key in .env

**Claude Code setup:**
- Claude Code + Pro plan + model squared away (Sonnet 4.6 or Opus 4.7/4.8)
- Auto-accept mode on + safe commands pre-approved in .claude/settings.json

**Challenge:**
- First-run product locked: [product_nickname] (ASIN recorded, or flagged as the one thing to add)

**Two hard requirements before Day 2.** Flag either one clearly if it wasn't completed:

- **Shopify store:** Day 2 deploys directly to Shopify admin. Without a store handle in brand-book.md, the deploy can't run.
- **Klaviyo:** If the List ID and API key are not in place, the Day 2 landing page form will capture nothing.

Then close warm and point forward.

**End-of-day handoff (always output this at the very end).** After the warm close above, output a clearly marked copy-paste block the user can take into a fresh session for Day 1. Use `pwd` to get the actual folder path, and fill in the product nickname and ASIN from `brand-book.md`.

**Note for Claude reading this in a future session:** when the user drags in a skill file and says to install it, read the file content, write it to `.claude/commands/[filename].md` in the current working directory, confirm it's saved, then execute the skill.

---

## Design principles (carry across the whole challenge)

- **Extract first, ask second** — pull colors/fonts/logo from their site before asking design questions.
- **Never generic** — every output is specific to their product and brand.
- **3 options when it's a creative choice** — colors, voice directions, cover layouts.
- **Preview before export** — always show it in the browser/inline before making the PDF.
- **Reassure non-designers** — nothing is final, everything changes in seconds.
- **No em dashes** — not in copy, not in titles, not anywhere.
- **The deliverable is a saved file they can open** — PDF + md, never raw HTML.
- **Model check at start** — Sonnet 4.6 recommended; Opus 4.7/4.8 also great. Only nudge if on something older.
