# Draft Follow-Up Email: a Claude skill

Say **"draft follow-up email"** after a meeting. Claude reads your Wispr Flow transcript and saves a follow-up to your Gmail Drafts, ready for you to check and send. It never sends anything itself.

**[⬇️ Download draft-follow-up-email.zip](https://github.com/shannonmcgivney/draft-follow-up-email/releases/latest/download/draft-follow-up-email.zip)**

## What goes in the email

| Part | What it says |
|---|---|
| **Thanks** | One sentence thanking them, mentioning something specific from the meeting |
| **Summary** | What you discussed and decided, in two sentences at most |
| **Next steps** | Action items grouped "On my end" and "On your end", with any deadlines |
| **What you promised** | Anything you said you'd send or share. If the link or file isn't in your notes, Claude adds a placeholder like `[ATTACH: course package]` instead of making one up |

It reads the full transcript rather than just the summary, so promises you made out loud are less likely to get missed. It also keeps private things out of the email: anything personal you shared, your own notes about the person, and side conversations about other people.

## What you need

- The **Wispr Flow** connector (with Wispr Flow recording your meetings)
- The **Gmail** connector

Connect both in Claude's connector settings.

## Install

**Claude.ai or the Claude desktop app:** download `draft-follow-up-email.zip` (don't unzip it), then go to **Customize → Skills → + Add → Upload skill** and pick the zip.

**Claude Code:**

```bash
git clone https://github.com/shannonmcgivney/draft-follow-up-email.git
cp -R draft-follow-up-email/skills/draft-follow-up-email ~/.claude/skills/
```

## Try it

- "Draft follow-up email." (uses your most recent meeting)
- "Draft a follow-up for my meeting with Sam."
- "Write the follow-up for yesterday's partner call."

If a follow-up draft for that meeting already exists, Claude points you to it instead of making a second one.

## Make it yours

Open `SKILL.md` to change the email's structure, the sign-off, or the list of things it never includes.

By McGivney Media.
