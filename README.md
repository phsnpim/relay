# Relay · UI handoff skill

A Claude skill that builds developer handoff boards **in Figma**, next to the designs. It documents what Dev Mode can't show: when a button is disabled, what a field accepts, which message appears, what a status means, how the flow branches, and what changes at each screen size.

It works in any project. On first use it reads the platform, screen sizes, fonts and colours from your design file and design system, and asks only for what it can't find.

## Install

**Claude Code (as a plugin, gets updates):**

```
/plugin marketplace add phsnpim/relay
/plugin install relay@relay
```

Update later with `/plugin marketplace update relay`.

**Claude Code (skill folder only):** copy `skills/ui-handoff` into `~/.claude/skills/`.

**Claude app:** zip the `skills/ui-handoff` folder and upload it under Settings → Capabilities → Skills.

You also need the **Figma connector** set up, so Claude can read and write your Figma file.

## First run

Ask Claude for a handoff (for example "make a dev handoff for the new checkout flow"). It will ask you, in one message:

- **The feature:** what it is, who uses it, where it starts and ends, what's new or changed. Claude works out the screens and boards from this and shows you the scope to confirm.
- **Four locations:** where the handoff boards go, where the handoff kit lives, which library is your design system (read only), and where the screen components are.
- **Where the rules come from:** PRD, tickets, API spec, or notes. Claude never invents rules; anything missing becomes `TBD` with an open question.
- **The note language.**

The answers are saved in the file as a `Handoff profile`, so later runs don't ask again.

## What you get

Five lines of boards, read top to bottom: **Overview** (contents and user flow) → **Scope** (what's new, changed or reused) → **Details** (one column per screen or component) → **Sizes** (each screen at every breakpoint) → **Log** (changelog).
