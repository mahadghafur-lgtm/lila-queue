# LILA Monday Queue

A support tool for LILA, the smart kitchen composter, that turns the Monday ticket export into a prioritised queue. It explains each ticket's priority and helps draft replies.

## How to open it

Double-click `index.html`. No install, server or internet needed. Works in Chrome and Safari.

## Features

- **Priority queue:** each ticket sits in a tier, with plain-English reasons naming the rule.
- **Date picker:** "today" starts at Mon Oct 5, 2026; change it and the queue re-sorts.
- **Summary bar:** replies overdue, deadlines this week, blocked, due today.
- **Patterns panel:** likely firmware issue, repeat customers, setup help, upcoming deadlines.
- **Editing:** status dropdown, timestamped notes, "Mark replied" and "Reset to original data".
- **Draft reply:** Send stays disabled until every [placeholder] is filled. Send is a demo button. A real version would connect to an email service.
- **Export CSV:** downloads all 12 tickets with your changes, in the same 11 columns as `tickets.csv`.
- **Collapsible tiers:** click a tier heading, or press Enter or Space, to fold it; this browser remembers.

## How the tiers work

1. **Time bound:** a Rule 2 refund window or Rule 3 carrier claim closes within 7 days, or a reply is overdue or due today (Rule 1).
2. **Product/Customer Service Issue:** a missed promise, repeated contact, or a firmware or setup fault.
3. **External Blockages:** waiting on warehouse, carrier, engineering, manager or an unnamed owner.
4. **On track.**

A ticket goes in the tier of its most urgent reason; "reply due today" never outranks tiers 2 or 3. Ties go to repeat customers, then the oldest unanswered message. A customer's tickets form one case. Resolved tickets sit at the bottom.

## Assumptions

- Today is Mon Oct 5, 2026, not the real date.
- Business days are Monday to Friday; weekends are skipped.
- Maya Okafor's refund request date is her earliest ticket in the data (Sep 28). Her two earlier tickets aren't in the data, so check their dates.
- T-110 shows "owner not stated in the data"; no owner is invented.
- Drafts mentioning the software update or a downgrade start with [confirm with engineering before sending] and can't be sent until engineering confirms.

## Limitation

Edits are saved in this browser only (localStorage). They don't sync to other devices, and clearing site data removes them. Use Export CSV to keep a copy.
