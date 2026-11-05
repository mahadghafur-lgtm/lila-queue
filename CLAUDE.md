# LILA Monday Queue: project instructions

## What we are building
A small internal support tool for LILA (a smart kitchen composter) that a support person opens on Monday morning to run a queue of 12 tickets. It is a take-home exercise, so it must open on the first try and be easy to explain and edit live.

## Hard technical constraints
- ONE file: `index.html` with inline CSS and plain JavaScript. No frameworks, no build step, no npm, no backend, no API keys.
- It must work by double-clicking `index.html` (file://) AND on GitHub Pages.
- Do NOT use `fetch()` to load `tickets.csv`. Browsers block fetch on file://. Instead, embed the CSV text inside `index.html` as a string and parse it. Keep `tickets.csv` in the repo as the source copy.
- The CSV has quoted fields that contain commas and apostrophes. Use a real CSV parser that handles quotes, not `split(',')`.
- Save user changes in `localStorage`, wrapped in try/catch. The page must still work if storage is blocked.
- Keep code readable: clear function names and short comments. The owner must be able to make a small change live on a call.

## The date
- "Today" defaults to **Monday 2026-10-05** (the scenario date), NOT the real clock.
- Add a small date picker in the header so the user can change "today" and watch priorities update.
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
Each ticket gets one or more **reasons**. Each reason has a tier. The ticket's tier is its most urgent reason. Every reason is shown on the ticket in plain English and names the rule, e.g. "Rule 3: carrier claim window closes tomorrow (Oct 6)".

**Tier 1: Deadline about to expire**
- Carrier claim not yet filed and the Rule 3 deadline is within 7 days (or passed).
- Customer asked for a refund inside the Rule 2 window, the refund or return is not started, and the window ends within 7 days.
- Sort by days left, fewest first.

**Tier 2: Customer let down**
- Rule 1 reply is overdue (show how many business days late).
- A promised date in the issue text has passed (e.g. "promised a replacement by Sep 26").
- Sort by business days late, most first.

**Tier 3: Blocked on someone else**
- Status is "waiting on warehouse", "waiting on carrier" or "waiting on engineering", or the notes say an internal step is not done (e.g. "not allocated yet"). Name who it is blocked on.
- Sort by how long it has been blocked.

**Tier 4: Reply due today**
- Rule 1 reply is due today.

**Tier 5: On track**
- Nothing due.

**Resolved** tickets go to the bottom, greyed out.

**Tie-breaks inside a tier:** repeat customers first, then oldest last customer message, then ticket ID.

**Cases (linked tickets):** tickets from the same customer about the same problem are one case. Show them linked and place them next to each other in the queue. The case is ranked by its most urgent ticket; the other tickets in the case sit directly below it.

**Extra flags (shown as small tags; they do not change the tier):**
- **Repeat customer:** same customer name on more than one ticket, or notes mention earlier tickets. Link the tickets together.
- **Needs manager decision:** customer wants a refund but delivery was more than 30 days before the request (Rule 2). The tool must never offer a refund here.
- **Possible firmware issue:** only when firmware is 2.4.1 AND the issue text uses stalling language (stops, pauses, stalls, or "since the update"). Do NOT flag on firmware 2.4.1 alone, and do NOT flag on an amber light alone.

## Features
**Must-haves**
1. Load the 12 tickets.
2. Priority queue sorted by the logic above, with the reasons visible on every ticket.
3. Change status (dropdown) and add notes (timestamped list). Both survive a page refresh. Include a "Mark replied" button that sets `last_reply_from` = us and `last_reply_date` = today. Include a "Reset to original data" button.

**Status options:** open, waiting on us, waiting on customer, waiting on warehouse, waiting on carrier, waiting on engineering, escalated to manager, resolved.

**Extra feature (the main one): Pattern panel**
- Groups tickets that share a likely cause (e.g. firmware 2.4.1 stalls) with a count and ticket IDs.
- Lists repeat customers.
- Lists deadlines expiring in the next 7 days.

**Small extras (after the core works)**
- **Reply template drafter:** a draft reply per ticket built from simple templates. It follows the house rules (it never promises a refund outside Rule 2 or before Rule 4). Copy button. No AI API.
- **CSV export:** downloads the current queue including the user's changes.

## Design (inspired by the LILA brand, no logos or copied content)
- Background warm cream `#FFF8EE`, alternate panels `#F4F3EF`, white cards.
- Brand maroon `#8B2323` for header and primary buttons. Text `#2A2A2A`, secondary text `#6B6B6B`.
- Font: Raleway from Google Fonts. Light weight for headings, regular or medium for ticket text. Fall back to system sans-serif if offline.
- "Eyebrow" labels: tiny uppercase text with a short dash, e.g. "— REPLY OVERDUE".
- Rounded pill buttons and status chips.
- Urgency colors must be distinct from the brand maroon: Tier 1 strong red, Tier 2 orange, Tier 3 purple or blue, Tier 4 amber, Tier 5 green. Always pair color with a text label.
- A summary bar at the top with counts: replies overdue, deadlines this week, blocked, due today.
- Must look good at laptop width and still be usable on a phone.

## Working style
- Build in small slices and stop after each slice so it can be tested.
- Do not invent rules or company policy beyond the four house rules.
- When unsure, ask instead of guessing.
