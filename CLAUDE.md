# LILA Monday Queue: project instructions

## What we are building
A small internal support tool for LILA (a smart kitchen composter) that a support person opens on Monday morning to run a queue of 12 tickets. It is a take-home exercise, so it must open on the first try and be easy to explain and edit live.

## Hard technical constraints
- ONE file: `index.html` with inline CSS and plain JavaScript. No frameworks, no build step, no npm, no backend, no API keys.
- It must work by double-clicking `index.html` (file://) AND on GitHub Pages.
- Do NOT use `fetch()` to load `tickets.csv`. Browsers block fetch on file://. Instead, embed the CSV text inside `index.html` as a string and parse it. Keep `tickets.csv` in the repo as the source copy.
- The CSV has quoted fields that contain apostrophes (the sample has no commas inside quoted fields, but real exports may). Use a real quote-aware CSV parser anyway, not `split(',')`.
- Parser tests: an empty field (e.g. T-102's notes) must show blank, never "undefined"; T-101's note must keep its apostrophes (`'restart the unit'`).
- Save user changes in `localStorage`, wrapped in try/catch. The page must still work if storage is blocked.
- Keep code readable: clear function names and short comments. The owner must be able to make a small change live on a call.

## The date
- "Today" defaults to **Monday 2026-10-05** (the scenario date), NOT the real clock.
- Add a small date picker in the header so the user can change "today" and watch priorities update.
- **Future tickets:** tickets whose `opened` date is after the date-picker date are hidden from the queue and counted in a small note (e.g. "1 ticket not yet opened"). A `last_reply_date` after the picker date is ignored.
- Parse all dates as plain calendar dates (no time zones). Avoid `new Date("2026-10-05")`, which is read as UTC and can shift a day. Build dates from year, month and day parts.

## House rules (cannot be changed by the tool)
1. Every customer gets a reply within 1 business day.
2. Refunds are available for 30 days after delivery. After that, the warranty covers repair or replacement, not a refund.
3. A carrier damage claim must be filed within 14 days of delivery.
4. A refund is paid only after the warehouse confirms the returned unit has arrived.
Only the manager can make an exception.

## Definitions
- **Business days:** Monday to Friday. Ignore holidays.
- **Reply due (Rule 1):** applies only when `last_reply_from` = customer. Due date = 1 business day after `last_reply_date`. A message on Fri, Sat or Sun is due Monday.
- **Business days late:** number of business days after the due date, up to and including today.
- **Refund window end (Rule 2):** delivered + 30 calendar days.
- **Refund eligibility (Rule 2):** judged by the date the customer asked for the refund, NOT today's date. A refund requested on or before the window end stays eligible even if the window has since ended.
- **Carrier claim deadline (Rule 3):** delivered + 14 calendar days.

## Priority logic
Each ticket gets one or more **reasons**. Each reason has a tier. The ticket's tier is its most urgent reason. Every reason is shown on the ticket in plain English and names the rule (or, for product and customer-service reasons, what the problem is), e.g. "Rule 3: carrier claim window closes tomorrow (Oct 6)".

The tiers (number, name, one-line definition, colour) live in ONE place: the `TIERS` config at the top of the script in `index.html`. Everything else reads from it.

**Tier 1: Time bound**
- Carrier claim not yet filed and the Rule 3 deadline is within 7 days (or passed).
- Customer asked for a refund inside the Rule 2 window, the refund or return is not started, and the window ends within 7 days.
- Rule 1 reply is overdue (show how many business days late).
- Rule 1 reply is due today, but only when the ticket has no Tier 2 or Tier 3 reason (a plain request). "Due today" never outranks a product, customer-service or blocked reason.
- Sort: deadlines first (fewest days left), then overdue replies (most business days late), then replies due today.

**Tier 2: Product/Customer Service Issue**
- A promised date in the issue text has passed (e.g. "promised a replacement by Sep 26").
- Repeated contact: the same customer has more than one ticket, or the notes mention earlier tickets.
- Possible firmware issue (firmware 2.4.1 AND stalling words, as in the flag below).
- Setup problem: the issue text mentions setup, connect or Wi-Fi (whole words). An onboarding-call request on its own is not a problem.
- Sort by business days late on a missed promise, most first; the rest by the tie-breaks.

**Tier 3: External Blockages**
- Status is "waiting on warehouse", "waiting on carrier", "waiting on engineering" or "escalated to manager", or the notes say an internal step is not done (e.g. "not allocated yet").
- Name an owner ONLY when the data states one (e.g. the status says "waiting on warehouse"). Never invent an owner. In the sample data only T-105 is blocked on the warehouse.
- When no owner is stated, say so, e.g. T-110: "Blocked: replacement not allocated (owner not stated in the data)".
- Sort by how long it has been blocked.

**Tier 4: On track**
- Nothing due and nothing blocked.

**Resolved** tickets go to the bottom, greyed out.

**Tie-breaks inside a tier:** repeat customers first, then oldest last customer message, then ticket ID.

**Cases (linked tickets):** tickets from the same customer about the same problem are one case. Show them linked and place them next to each other in the queue. The case is ranked by its most urgent ticket; the other tickets in the case sit directly below it.

**Extra flags (shown as small tags; they do not change the tier):**
- **Repeat customer:** same customer name on more than one ticket, or notes mention earlier tickets. Link the tickets together.
- **Needs manager decision:** customer wants a refund but delivery was more than 30 days before the request (Rule 2). The tool must never offer a refund here.
  - On every case with this flag, show an **Assumption** line, e.g. "Refund request date taken from earliest ticket in the data (Sep 28). Two earlier tickets are not in the data; check their dates." Build the date (earliest `opened` in the case) and the earlier-ticket count (from the notes) from the data for each case; do not hard-code them.
- **Possible firmware issue:** only when firmware is 2.4.1 AND the issue text uses stalling language (stops, pauses, stalls, or "since the update"). Do NOT flag on firmware 2.4.1 alone, and do NOT flag on an amber light alone.

## Test dates for the priority logic
Set the date picker to each date and check:
- **Sat Oct 3:** T-101 is not overdue (weekend replies are due Monday). T-111 (opened Oct 4) is hidden and the note says "1 ticket not yet opened".
- **Mon Oct 5:** order is T-104, T-108, T-109/T-101, T-102, T-112, T-107, T-111 (Tier 1), then T-110, T-103, T-106 (Tier 2), then T-105 (Tier 3).
- **Tue Oct 6:** T-104 says the carrier claim closes TODAY.
- **Wed Oct 7:** T-104 says the carrier claim deadline PASSED yesterday.

## Features
**Must-haves**
1. Load the 12 tickets.
2. Priority queue sorted by the logic above, with the reasons visible on every ticket.
3. Change status (dropdown) and add notes (timestamped list). Both survive a page refresh. Include a "Mark replied" button that sets `last_reply_from` = us and `last_reply_date` = today. Include a "Reset to original data" button.

**Status options:** open, waiting on us, waiting on customer, waiting on warehouse, waiting on carrier, waiting on engineering, escalated to manager, resolved.

**Extra feature (the main one): Pattern panel**
- Groups tickets that share a likely cause (e.g. firmware 2.4.1 stalls) with a count and ticket IDs.
- Second cluster, **"New-customer setup help"**: tickets whose issue text mentions setup, connect, Wi-Fi or onboarding (whole-word matching). Show the count, the ticket IDs, and the days since delivery for each. In the sample data: T-106 (Wi-Fi during setup) and T-107 (onboarding call).
- Lists repeat customers.
- Lists deadlines expiring in the next 7 days.

**Small extras (after the core works)**
- **Reply template drafter:** a draft reply per ticket built from simple templates. It follows the house rules (it never promises a refund outside Rule 2 or before Rule 4). Send button: disabled while any [placeholder] is unfilled; when clicked it sends nothing, shows "Sent to <customer> (demo: no email is actually sent)", sets last_reply_from = us and last_reply_date = today, adds a "Reply sent" note, saves like other edits and re-sorts (status unchanged). For a linked case it applies to every ticket in the case. No AI API.
  - Send is a demo button. A real version would connect to an email service.
- **CSV export:** downloads the current queue including the user's changes.

## Design (inspired by the LILA brand, no logos or copied content)
- Background warm cream `#FFF8EE`, alternate panels `#F4F3EF`, white cards.
- Brand maroon `#8B2323` for header and primary buttons. Text `#2A2A2A`, secondary text `#6B6B6B`.
- Font: Raleway from Google Fonts. Light weight for headings, regular or medium for ticket text. Fall back to system sans-serif if offline.
- "Eyebrow" labels: tiny uppercase text with a short dash, e.g. "— REPLY OVERDUE".
- Rounded pill buttons and status chips.
- Urgency colors must be distinct from the brand maroon: Tier 1 strong red, Tier 2 orange, Tier 3 blue, Tier 4 green, Resolved grey (set in the `TIERS` config). Always pair color with a text label.
- A summary bar at the top with counts: replies overdue, deadlines this week, blocked, due today.
- Must look good at laptop width and still be usable on a phone.

## Working style
- Build in small slices and stop after each slice so it can be tested.
- Do not invent rules or company policy beyond the four house rules.
- When unsure, ask instead of guessing.
