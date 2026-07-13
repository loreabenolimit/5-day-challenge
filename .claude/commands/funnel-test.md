# Funnel End-to-End Test

Walk the complete funnel live in the seller's Chrome browser, from the insert link through the opt-in form and post-optin offer, to the welcome email arriving in the inbox. Run this at the end of Day 4 to confirm every connection in the chain is working before real customers see it.

> ⛔ **HARD RULE — NO DASHES IN ANY OUTPUT.** Never use an em dash (—) or en dash (–) in anything the user sees or keeps: not in test results, not in captions, not in chat, not in titles. Use a comma, colon, period, parentheses, or "to" for ranges. This applies even though THIS file contains dashes; the rule governs what you produce, not what you read.

---

## What this test checks

It is not enough that pages load, the test must confirm the **attribution handoff** the Day 5 dashboard depends on, since a funnel can look fine and still report nothing:

1. The insert link/QR opens the landing page **AND the URL carries `utm_campaign` (product) + `utm_content` (insert-a/b/c)**. Always start the test from the FULL tagged insert URL, not a bare landing URL.
2. The opt-in form submits and the subscriber lands in Klaviyo **with the three properties set: `source=Amazon`, `product`, and `insert_variant`** (matching the URL's UTM tags). List membership alone is not enough.
3. The post-optin offer page loads, and the **offer CTA reaches the coupon page** (`[brand]-coupon.html`), the page whose Shopify views are the dashboard's "offer clicks".
4. The coupon page reveals the code and **tags the profile `took_offer` + `insert_variant`** in Klaviyo.
5. The **welcome email AND the review-ask email** exist in the live flow (the review-ask is the whole point of a Review Funnel, confirm it is present and its link points to the Amazon review page).
6. Page views are visible in **Shopify Analytics** (the page is on the store), and the seller is reminded Shopify stats lag a few minutes. (No GA4 anywhere.)

Each step gets a screenshot. The seller watches it live in their Chrome window.

---

## Step 0 — Install this skill

The user dragged this file into Claude Code. Before anything else:

1. Run `mkdir -p .claude/commands`
2. Save this file to `.claude/commands/funnel-test.md`

Confirm in one line: *"Funnel test skill installed. Let's walk through your live funnel."* Then continue.

---

## Flow

### Step 1 — Read context (silent, no questions yet)

Read all of these before asking anything:

1. **`brand-book.md`** — extract: `brand_name`, `email_platform`, `klaviyo_list_id`, `klaviyo_list_name`, `landing_page_url` (if set by Day 2). Also note `klaviyo_public_key` if present.
2. **The insert file** (`*-insert.html`) — scan all `href` and `data-*` attributes for an external URL (not localhost). That is the QR code destination and the funnel's entry point.
3. **The landing page file** (`*-download.html`, `*-warranty.html`, `*-video.html`, or similar) — note what form fields it has (typically: first name, email) and where the form submits (Klaviyo embed script, or a POST action URL).
4. **The post-optin page** (`*-welcome.html`, `*-offer.html`, or offer block appended to video page) — note what appears after form submit (confirmation header, OTO, VIP club).
5. **`.env`** — look for `KLAVIYO_PRIVATE_KEY` or `KLAVIYO_API_KEY`. This lets you verify email delivery via the Klaviyo API without waiting for the inbox.

### Step 2 — Find the live landing page URL

The test needs the **publicly accessible URL the QR encodes, with its UTM tags intact** (so attribution is actually exercised). Prefer the insert's own tagged URL. Check in this order:

1. External `href` in the insert HTML (the URL the QR encodes) — this should already include `?utm_campaign=...&utm_content=insert-a`. Use it verbatim, tags and all.
2. `landing_page_url:` in `brand-book.md` — if it has no UTM tags, append `?utm_campaign=<product>&utm_content=insert-a` before testing.
3. Vercel or jointopdog.com URL referenced in `brand-book.md` or the landing HTML comments.

**If the resolved URL has no `utm_campaign`/`utm_content`, that is a FAIL to report immediately** (Day 5 will attribute nothing), do not silently test a bare URL.

If none of those yield a URL, ask the seller:

> "What is the live URL of your landing page? It is the URL printed on your insert or encoded in the QR code. Paste it here and I will run the full funnel test from there."

### Step 3 — Pick a test email address

Ask the seller:

> "What email should I use for the funnel test?
>
> **A) Your own email (recommended)** — you will get the real welcome email in your inbox, which proves the whole chain works end to end.
> **B) A disposable test address** — I will use something like `test+funnelcheck@yourdomain.com` and verify delivery via the Klaviyo API instead.
>
> Which do you prefer?"

If they pick A: confirm their email address.
If they pick B: confirm their domain or use the one from `brand-book.md` website field.

Note: if the Klaviyo flow has a rate-limit or deduplication rule that blocks the same email from entering twice, mention that Option B is safer for repeat tests.

### Step 4 — Load Chrome MCP tools

Load the Chrome extension tools via ToolSearch if not yet available:

```
ToolSearch: query "Claude_in_Chrome", max_results 20
```

If the Chrome extension is not connected, stop and tell the seller:

> "Your Chrome extension is not connected. To run the live funnel test:
> 1. Open Chrome
> 2. Make sure the Claude Code Chrome extension is enabled
> 3. Come back and run this test again"

Do not fall back to computer-use pixel clicks for this flow: the form fill needs DOM access.

### Step 5 — Open the landing page in Chrome

1. Navigate a new Chrome tab to the live landing page URL.
2. Wait 3 seconds for the page to fully render.
3. Screenshot the page.
4. Show the screenshot inline with caption: **"Step 1 of 5: Landing page."**

Verify the page loaded correctly:
- Not a 404 or error screen
- The brand logo and headline are visible
- The opt-in form is present

If the page fails to load: report the failure with the screenshot and the HTTP status. Common causes:
- Page not yet published (still draft on Vercel or GHL)
- Wrong URL (mismatch between what is on the insert and what was deployed)
- Domain not propagated yet

Do not continue to Step 6 until the landing page loads cleanly.

### Step 6 — Fill out the opt-in form

Use the Chrome MCP `find` or `read_page` tool to locate:
- First name input field
- Email input field
- Submit / CTA button

Fill in:
- First name: `Test`
- Email: the address chosen in Step 3

Screenshot after filling the fields (but before submitting), so the seller can see the data entered.

Then click the submit button.

If the form uses a Klaviyo embed script (not a standard POST form), the submit triggers the Klaviyo JS SDK. The page will either redirect or show a success state in place. Either is correct.

### Step 7 — Capture the post-optin page

After submit, the browser redirects to the post-optin page (or the video page scrolls to reveal the offer block). Screenshot immediately. Show inline with caption: **"Step 3 of 5: Post-opt-in page."**

Verify:
- Page loaded (not blank, not 404)
- Confirmation header or offer block is visible
- Personalization: if the page uses `[First Name]` merge from a URL parameter, verify it populated or note that it will once Klaviyo sends real subscribers (the test form submit may not pass the name via query string)
- OTO or VIP club section is visible

If there is an OTO buy button or club join button: screenshot it to show the seller it is there, but do NOT click it during the test. Tell the seller: *"The offer button is present. I am skipping the click to avoid triggering a real transaction."*

### Step 8 — Wait and check email delivery

Wait 30 seconds after form submit, then check for the welcome email.

**Path A: Klaviyo API (if private key is in `.env`)**

Run the following Python check:

```python
import requests, json, os, time
from dotenv import load_dotenv
load_dotenv()

api_key = os.getenv("KLAVIYO_PRIVATE_KEY") or os.getenv("KLAVIYO_API_KEY")
test_email = "[EMAIL_FROM_STEP_3]"
headers = {
    "Authorization": f"Klaviyo-API-Key {api_key}",
    "revision": "2024-10-15",
    "Content-Type": "application/json"
}

# 1. Profile created, WITH the attribution properties (the Day 5 handoff)
profiles_resp = requests.get(
    "https://a.klaviyo.com/api/profiles/",
    headers=headers,
    params={"filter": f'equals(email,"{test_email}")'}   # custom properties come back in attributes by default
)
profiles = profiles_resp.json().get("data", [])
if not profiles:
    print("FAIL: profile not found in Klaviyo, the form submit did not reach Klaviyo")
else:
    profile_id = profiles[0]["id"]
    props = (profiles[0].get("attributes", {}) or {}).get("properties", {}) or {}
    print(f"PASS: profile created, id={profile_id}")

    # 2. Attribution properties: source=Amazon, product, insert_variant (Day 5 reads these)
    for key, want in [("source", "Amazon"), ("product", None), ("insert_variant", None)]:
        val = props.get(key)
        if not val:
            print(f"FAIL: profile is missing the '{key}' property, Day 5 cannot attribute this opt-in")
        elif want and val != want:
            print(f"WARN: '{key}' is '{val}', expected '{want}'")
        else:
            print(f"PASS: {key} = {val}")

    # 3. List membership
    list_resp = requests.get(
        f"https://a.klaviyo.com/api/profiles/{profile_id}/relationships/lists/",
        headers=headers
    )
    lists = [l["id"] for l in list_resp.json().get("data", [])]
    list_id = "[KLAVIYO_LIST_ID_FROM_BRAND_BOOK]"
    if list_id in lists:
        print(f"PASS: profile is on the correct list ({list_id})")
    else:
        print(f"FAIL: profile is NOT on list {list_id}, check the form list assignment")

    # 4. Welcome email sent (best-effort; the flow may add a delay before email 1)
    for attempt in range(6):
        events_resp = requests.get(
            "https://a.klaviyo.com/api/events/",
            headers=headers,
            params={"filter": f'equals(profile_id,"{profile_id}")', "sort": "-datetime"}
        )
        events = events_resp.json().get("data", [])
        email_events = [e for e in events
                        if "email" in str((e.get("attributes") or {})).lower()]
        if email_events:
            print("PASS: a welcome email event has fired for this profile")
            break
        if attempt < 5:
            time.sleep(15)
    else:
        print("WARN: no email event yet after 90s, the flow may have a delay or is not set to Live")
```

Fill in `test_email` and `list_id` from context. Show the output to the seller.

**Path B: No API key or non-Klaviyo platform**

Tell the seller:

> "Check your inbox now for the welcome email. It should arrive within 60 seconds of hitting submit. Paste the subject line here when it arrives, or let me know if it does not show up after 2 minutes."

Wait for their reply. If the email arrives: ask them to screenshot it. If it does not arrive after 2 minutes, move to Step 9 with a FAIL on that row and the debug checklist.

### Step 9 — Screenshot the inbox (if using seller's own email)

If Option A was chosen in Step 3, ask the seller to open their inbox now. Use the Chrome MCP to:
1. Navigate to their email client (Gmail, Outlook, or similar) in Chrome
2. Screenshot the inbox showing the welcome email in the list
3. Click into the email and screenshot the opened email

Show both screenshots inline with captions: **"Step 5 of 5: Welcome email in inbox."**

### Step 10 — Report results

Output a clean pass/fail summary. No dashes in the lines.

```
Funnel Test: [Brand Name]

  Step 1: Landing page loaded          [ PASS ]
  Step 2: Form submitted               [ PASS ]
  Step 3: Post-optin page loaded       [ PASS ]
  Step 4: Added to email list          [ PASS ]
  Step 5: Welcome email received       [ PASS ]
```

If all pass: *"Your funnel is working end to end. Real customers will experience exactly what you just saw."*

If any step fails, diagnose and give the fix inline:

| Failure | Most likely cause | Fix |
|---------|-------------------|-----|
| Landing page 404 | Not published yet or wrong URL | Redeploy or correct the insert URL |
| Form submits but no redirect | Wrong redirect URL in form embed | Update form `redirect_url` in Klaviyo embed settings |
| Post-optin page blank | Redirect URL points to wrong file | Check and correct the file path or Vercel URL |
| Profile not in Klaviyo | Wrong public API key or list ID in form | Verify the Klaviyo embed snippet matches the correct list |
| Email did not arrive | Flow not set to Live, or Email 1 has a delay, or emails are being silently skipped | Set flow to Live; confirm Email 1 trigger is "immediately"; check Recipient Activity for skip reasons (see below) |
| Email landed in spam | Sender domain not verified in ESP | Add SPF and DKIM records for the sending domain |
| Email skipped: "Email Syntax Error" | Template contains `{%-` Jinja2 whitespace-control syntax, which Klaviyo's Django engine rejects entirely. **Check BOTH the HTML and the plain text version** — Klaviyo stores them separately, so fixing one does not fix the other, and a broken plain text alone causes this same skip. | Fix and re-save both versions yourself (never ask the seller to touch Klaviyo). HTML: `/flow/message/{msg_id}/content/edit`, replace every `{%- ` with `{% ` (no dash) via the browser console `editor.session.setValue()` pattern. Plain text: open the ⋮ menu next to "Edit email" on the message content page → "Edit plain text" (or navigate to `/flow/message/{msg_id}/template/{template_id}/content/text`), set the textarea via React's native setter, click "Save Plain Text" then "Confirm." |
| Email skipped: "Smart Sending" | "Skip recently emailed profiles" is ON for Email 1 (16-hour window blocks most new subscribers) | Open Email 1 in the flow, go to settings, uncheck "Skip recently emailed profiles," save |
| Email skipped: variable error | Template uses `{{ first_name }}` instead of `{{ person.first_name }}`, again possibly only in the plain text version | Update the template to use `{{ person.first_name|default:"there" }}` throughout, in both the HTML and the plain text |

**How to find skip reasons in Klaviyo:** in the flow, click the email block you want to investigate, then open the "Recipient Activity" tab and filter by "Skipped." The reason column shows exactly why each subscriber was skipped. Always check this tab first when an email fails to arrive.

**Always verify via the Templates API, not just the eye test:** `GET /api/templates/{id}/` and confirm neither the `html` nor the `text` attribute contains `{%-`, `{{ first_name|` (without `person.`), or an unfilled `[PLACEHOLDER]`. This is the only reliable proof a fix actually landed, since the flow editor UI can visually look fine while the underlying saved template is still broken.

**This whole diagnose-and-fix loop is your job, end to end.** The seller should never be asked to open Klaviyo's editor, paste a script into the console, or manually fix template text. If an email is skipping, fix it yourself via the API/browser methods above and re-run the test until it passes.

After reporting, if invoked from Day 4 (email-flow skill): return and continue to the end-of-day handoff.

If run standalone: ask *"Want to fix any of these issues now?"* and address them in the same session.

---

## Notes

- Requires Chrome extension (`mcp__Claude_in_Chrome__*`) connected and the Chrome window visible.
- Klaviyo API check requires `KLAVIYO_PRIVATE_KEY` or `KLAVIYO_API_KEY` in `.env`.
- Do not click OTO buy buttons or VIP join buttons during the test (avoids triggering real transactions).
- If the flow deduplicates by email and the same address was used in a prior test, use a new email variant (`test+funnel2@...`) to get a fresh subscriber.
- Chrome tier note: Chrome is tier "read" for computer-use tools. Use the Claude-in-Chrome MCP extension tools (`mcp__Claude_in_Chrome__*`), not `mcp__computer-use__*`, for form input and navigation.
