---
title: "BrowserRouter vs. Choosy vs. Browserosaurus: Which Link Router Should You Use?"
date: 2026-01-28
description: "Three solid options for fixing macOS link routing. They work completely differently — here's how to pick."
---

You use multiple browsers. Links keep opening in the wrong one. You've decided to fix this.

Good news: there are three solid options. Bad news: they work completely differently, and choosing the wrong one means more friction, not less.

I built BrowserRouter, so I'm biased. I'll be honest about that — and about when the other options are better choices.

Here's how to pick.

## The Core Difference

All three apps solve the same problem (links opening in the wrong browser), but their philosophies are completely different:

**Choosy** says: "Tell me your rules, and I'll follow them forever."
**Browserosaurus** says: "I'll ask you every time. You decide."
**BrowserRouter** says: "I'll watch what you're doing and guess."

Which approach fits you depends on how you think about browser routing.

## Choosy: Rules-Based Routing

**Price:** $10 (one-time)
**Website:** [choosy.app](https://choosy.app)

Choosy lets you define routing rules. Examples:
- "Links from Slack → Chrome"
- "Links containing 'github.com' → Firefox"
- "Links from Mail → Safari, unless it's a Google Doc, then Chrome"

You program your link behavior once. Choosy handles it forever.

**The experience:** You spend 15-30 minutes thinking through your rules and setting them up. Then links route automatically based on those rules. When your workflow changes, you update the rules.

**When Choosy shines:**

*You're a developer who always wants GitHub in Firefox and staging links in Chrome.* Choosy handles this perfectly — URL patterns and source app rules make this automatic.

*Your company uses specific apps that should always open in your work browser.* Rule: "Links from Jira, Confluence, and company Slack → Chrome." Done.

*You have strong opinions about specific link types.* Google Docs in Chrome. News sites in Safari. Documentation in Firefox. Choosy lets you encode these preferences.

**When Choosy frustrates:**

*You don't have predictable rules.* If your browser choice depends on what you're doing in the moment (not where the link came from), rules can't capture that.

*You hate configuration.* Choosy requires upfront thinking. If you want something that "just works," this isn't it.

*$10 feels like a lot for a utility.* It's a fair price for what you get, but free alternatives exist.

**Verdict:** Choosy is for people who think in systems. If you can articulate rules for your browser routing, it'll execute them perfectly.

---

## Browserosaurus: Manual Selection

**Price:** Free, open source
**Website:** [browserosaurus.com](https://browserosaurus.com)

Browserosaurus shows a popup for every link. You see all your browsers. You click the one you want. The link opens there.

No rules. No guessing. Pure manual control.

**The experience:** Click a link → popup appears → click a browser icon → page opens. Every time, for every link.

**When Browserosaurus shines:**

*You're paranoid about links opening in the wrong place.* Some people really want to consciously choose every time. Browserosaurus guarantees that.

*You click fewer than 10 links per day.* If you're not clicking dozens of links, the popup isn't that annoying.

*You don't trust automatic systems.* If the idea of an app guessing which browser you want makes you uncomfortable, manual selection removes that uncertainty.

**When Browserosaurus frustrates:**

*You click a lot of links.* The popup adds friction to every single one. Click 30 links a day, and you're making 30 extra decisions.

*You want flow, not interruption.* Even a fast popup breaks your momentum. You clicked a link because you wanted to see a page, not because you wanted to think about browsers.

*Your choice is usually obvious.* If 90% of the time you'd just pick "whatever browser I'm in," Browserosaurus makes you do that manually.

**Verdict:** Browserosaurus is for people who want certainty over convenience. You'll never be surprised by which browser opens — but you'll always be prompted.

---

## BrowserRouter: Automatic Context-Following

**Price:** Free, open source
**Website:** [browserrouter.app](https://browserrouter.app)

BrowserRouter tracks which browser you're currently using. When you click a link, it opens in your most recently focused browser.

No rules. No popups. Just context-aware routing.

**The experience:** You're working in Chrome. Click a link. Chrome opens it. Switch to Safari. Click a link. Safari opens it. You don't think about it — links follow your focus.

**When BrowserRouter shines:**

*Your browser choice depends on context, not rules.* "I want links to open wherever I'm working" is the whole point of BrowserRouter.

*You hate configuration.* Install, set as default browser, done. No rules to think through, no decisions to make.

*You value privacy.* No URL logging, no analytics, no network requests. Open source, so you can verify.

*You want free and simple.* It costs nothing, and setup takes 30 seconds.

**When BrowserRouter frustrates:**

*You need hard rules.* "Slack links must always go to Chrome" isn't possible — BrowserRouter follows your focus, not app-specific rules.

*You frequently switch contexts mid-task.* If you're in Safari but click a link you want in Chrome, BrowserRouter sends it to Safari. You'd need to switch browsers first, or copy-paste.

*You want manual control.* BrowserRouter guesses. It's right most of the time, but it doesn't ask.

**Verdict:** BrowserRouter is for people who want zero friction. If "open links where I'm working" describes your need, it handles this automatically.

---

## Decision Flowchart

**Do you have specific rules in mind?**
("Slack links always Chrome, GitHub always Firefox")
→ **Yes:** Get Choosy. It's built for this.
→ **No:** Keep reading.

**Do you want to choose every link manually?**
→ **Yes:** Get Browserosaurus. Complete control, no guessing.
→ **No:** Keep reading.

**Do you just want links to follow your current browser?**
→ **Yes:** Get BrowserRouter. Zero config, automatic routing.

---

## What About OpenIn?

OpenIn ($10) is like Choosy but handles more than browsers — file types, email clients, URL schemes. If you need to route `.pdf` files to specific apps or control `mailto:` links, OpenIn is more comprehensive.

For browser routing alone? Overkill.

---

## My Honest Recommendation

Start with BrowserRouter.

Not because I built it — because it's free and takes 30 seconds. If automatic context-following solves your problem, you're done. No money spent, no configuration required.

If you find yourself wanting rules ("this should *always* go there"), switch to Choosy. It's $10 and worth it for rule-based routing.

If you realize you want manual control over every link, switch to Browserosaurus.

The beauty of all three being cheap or free: you can try them without risk. Start simple, upgrade to complex only if you need it.

---

**Quick links:**
- [BrowserRouter](https://browserrouter.app) — Free, automatic, privacy-first
- [Choosy](https://choosy.app) — $10, powerful rules
- [Browserosaurus](https://browserosaurus.com) — Free, manual selection
