---
name: draft-follow-up-email
description: Drafts (never sends) a Gmail follow-up email after your most recent Wispr Flow meeting - thanks the attendees, sums up the meeting in two sentences, lists next steps and action items by owner, and includes what you promised to send. Use when the user says "draft follow-up email", "draft a follow-up", "follow up on my last meeting", or "write the follow-up for my meeting with <name>".
---

# Draft follow-up email

After a meeting, write a ready-to-review follow-up email and save it to the user's Gmail Drafts. **Only ever create a draft. Never send, schedule or reply-send anything.** If a tool would send, don't call it.

Wispr Flow times are UTC. Show them to the user in their computer's local time zone (check with `date` if you can). In the email itself, refer to dates and times the way the other person would, or leave times out.

## Tools
- Wispr Flow connector: `search_meetings`, `get_meeting`, `get_meeting_participants_enriched`
- Gmail connector: `list_drafts`, `search_threads`, `get_thread`, `create_draft`

Load them with ToolSearch if they're deferred. If either connector needs authentication, stop and tell the user to connect or reconnect it in their connector settings. Don't write the email somewhere else instead.

## 1. Pick the meeting
- **Default ("draft follow-up email"):** the most recent meeting that has ended. Call `search_meetings` with no query (newest first) and take the first one where `end` is in the past, `shared` isn't true, and `finalized` is true.
- **Named ("...for my meeting with Sam"):** search by name or title and pick the most recent match.
- Wispr Flow sometimes has two entries for one meeting with the same title and start time: the user's prep notes (`has_transcript: false`) and the recording. Use the one with the transcript.
- If the latest meeting isn't finalized yet, say so. Offer the one before it, or ask the user to try again in a few minutes.
- If the meeting has no other participants with an email address (a solo or internal note), say so and stop. Don't guess an address.

## 2. Gather what was said
- `get_meeting` with `view_transcript: {}`, and keep paging the transcript until it's finished. The transcript is the source of truth. The summary and notes help but can drop details.
- `get_meeting_participants_enriched` for the attendees' emails. Leave out the user.
- Collect:
  - **The gist:** what the meeting was for and where it landed.
  - **Next steps by owner:** the user's, theirs, and shared ones.
  - **The user's promises:** anything they said they'd send, share, intro, check or follow up on ("I'll send you...", "I can get you...", "let me connect you with..."). These go in the email.
- Optional: search Gmail (`search_threads` with `from:` or `to:` their address) for an existing thread with this person. If there's a recent one, pass its latest message id as `replyToMessageId` so the draft stays in that thread. Otherwise start a new email.

## 3. Write the email
Write it the way a person talks: warm, plain, direct, and short enough to read on a phone. Avoid AI tells: no em dashes, no "delve", "I hope this email finds you well", "testament to", "not just X but Y", padding in threes, or "Let me know if you have any questions!" sign-offs. If a writing-style skill is installed (for example `human`), follow its rules.

Structure:
1. **Greeting:** "Hi <first name>," (for a group: "Hi all," or the first names).
2. **Thanks:** one sentence thanking them for their time, mentioning something specific from the meeting.
3. **Summary:** two sentences at most on what was discussed and decided.
4. **Next steps:** a short list grouped as "On my end:" and "On your end:" (plus "Together:" if needed). Use their wording for their commitments and keep it friendly, never like assigning homework. Include dates or deadlines that were mentioned.
5. **What the user promised:** for each item, include it or mark where it goes:
   - If the link or detail is stated in the transcript or notes, include it.
   - Otherwise put a clear placeholder the user will see before sending, like `[ATTACH: course package]` or `[LINK: program flyer]`. Never invent a URL, number, price or date.
6. **Close:** one line pointing to the next touchpoint if one came up, then "Best," and the user's first name.

Subject: short and specific, such as "Great connecting today: next steps on the pilot". Skip it if replying in a thread.

**Never put the user's personal life in the email:** where they live or travel, their safety, identity or health, or anything else personal they shared. Even if they talked about it openly in the meeting, it doesn't go in writing. The same goes for the other person's personal or family details.

Also don't put in the email: the user's private notes about the person (phone numbers, impressions, strategy notes), side remarks about third parties, internal pricing they didn't share with them, or anything the transcript shows as said in confidence. If you're unsure, leave it out and mention it in the report.

## 4. Create the draft
- Before creating it, check `list_drafts` (query `to:<their email> newer_than:3d`, `DRAFT_VIEW_FULL`) for a follow-up draft for this meeting. If one exists, don't make a second. Report the existing one, unless the user explicitly asked for a new version.
- `create_draft` with `to` set to the attendee emails, a plain-text `body`, and an `htmlBody` with the same text as simple `<p>` paragraphs and `<ul><li>` lists. Set `includeSignature: false` unless the user wants their Gmail signature, so the name isn't signed twice.
- If you can write files, append one line to `~/.claude/follow-up-drafts.log`: `<meeting_id>\t<meeting title>\t<draft id>\t<ISO time>`. Create the file if it doesn't exist. This keeps a record and lets an automation skip meetings that already have a draft.

## 5. Report back
Keep it short:
- the meeting (title, local time) and who the draft is addressed to
- the Gmail draft link (`viewUrl`)
- every `[ATTACH: ...]` or `[LINK: ...]` placeholder the user needs to fill
- anything you left out on purpose, and anything you were unsure of (for example a name spelling or a missing email)
- a reminder that it's saved as a draft and nothing was sent
