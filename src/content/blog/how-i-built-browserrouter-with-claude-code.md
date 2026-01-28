---
title: "How I Built BrowserRouter with Claude Code in One Weekend"
date: 2026-01-14
description: "I don't know Swift. I'd never built a macOS menu bar app. But I had Claude Code — and that changed everything."
---

Saturday morning. Coffee. An idea that had been nagging me for months: a simple app to fix macOS link routing. By Sunday night, it was live on GitHub.

I don't know Swift. I'd never built a macOS menu bar app. I hadn't touched Xcode in years.

But I had Claude Code. And that changed everything.

## The Itch I Couldn't Scratch

Every day, the same friction: click a link in Slack while working in Chrome, watch Safari open instead. Copy URL, switch browsers, paste. A tiny annoyance, repeated dozens of times daily.

The fix seemed obvious — an app that tracks which browser you're using and routes links there. No rules, no popups. Just automatic context-following.

The problem: I'm not a Swift developer. The learning curve for macOS development, SwiftUI, menu bar apps, URL handlers — it felt like weeks of work for a side project that might not even pan out.

So I never built it. For years.

Then I tried something different.

## The Experiment

I opened Claude Code and typed:

> I want to build a macOS menu bar app that tracks which browser is focused and routes clicked links to that browser. It should register as a URL handler and forward URLs to the most recently used browser.

Within minutes, I had a Swift project structure. Real code. A Package.swift file, proper directories, the skeleton of an app.

I thought: "Okay, but it won't compile."

It didn't. But I pasted the errors back. Claude Code fixed them. New errors. Fixed those too. After maybe fifteen rounds of this — maybe two hours total — I ran the app.

A globe icon appeared in my menu bar.

## Day One: Making It Work

The first working version did three things:

1. Watched for browser focus changes using `NSWorkspace` notifications
2. Registered itself as a URL handler for `http://` and `https://`
3. Forwarded URLs to whatever browser was most recently active

That's the whole concept. But making it *good* took the rest of the day.

I wanted the menu bar to show which browser was active. Claude Code built the SwiftUI view. I wanted users to choose which browsers to track. Claude Code added a preferences submenu. I wanted launch-at-login. Claude Code wired up `SMAppService`.

The pattern was consistent: I described behavior in plain English. Claude Code translated it to Swift. I tested it, found problems, described them. Claude Code fixed them.

I wasn't fighting syntax errors or reading Apple documentation. I was making product decisions.

## Day Two: The Edge Cases

Sunday was about reality.

What happens if the most recent browser isn't running anymore? What if the user hasn't launched any tracked browsers yet? What about browsers I hadn't considered?

Each edge case was a conversation:

*"What if Chrome was the last focused browser, but it's not running when a link is clicked?"*

Claude Code implemented fallback logic — try the most recent browser, then work backwards through the history, finally fall back to a configured default.

*"How do I detect which browsers are installed on the user's system?"*

It wrote a function to scan `/Applications` for known browser bundles.

*"This crashes if someone runs it without setting a fallback."*

Fixed.

By Sunday afternoon, I had a working app that handled every case I could think of.

## The Moment It Clicked

Packaging was where I expected to fail. Building a proper `.app` bundle, getting the Info.plist right for URL handling, figuring out distribution without paying Apple's $99/year developer fee.

Claude Code wrote a build script. One command: `./scripts/bundle.sh`. Out comes a working BrowserRouter.app.

I dragged it to Applications. Set it as my default browser. Opened Slack, clicked a link.

Chrome opened.

I switched to Safari. Clicked another link.

Safari opened.

No configuration. No popups. It just... worked. The thing I'd wanted for years, built in a weekend.

## What This Taught Me

**AI amplifies understanding — it doesn't replace it.** I couldn't have written this Swift code from scratch. But I understood what I wanted the app to do. When Claude Code produced something wrong, I could describe the problem because I knew what "right" looked like. The AI handled the syntax; I handled the product.

**The barrier to building is lower now.** Not zero — you still need to think clearly about what you're building. But the gap between "I know what I want" and "I can make it" has shrunk. Weekend projects that would have taken weeks are now achievable in a weekend.

**Shipping beats learning (sometimes).** I could have spent weeks properly learning Swift and macOS development. I would have gained valuable skills. But I also would have lost momentum, and BrowserRouter might never have shipped. For scratching your own itch, speed matters.

## The Code Is Open

BrowserRouter is MIT licensed. You can see exactly what Claude Code produced and what I edited afterward:

**GitHub:** [github.com/mares29/browserrouter](https://github.com/mares29/browserrouter)

The architecture is simple: single menu bar app, about 500 lines of Swift. Tracks browser focus, intercepts URLs, routes them. No analytics, no network requests, no URL logging.

## Should You Build With AI?

For a project like this — well-defined scope, unfamiliar stack, weekend timeline — absolutely.

The pattern works when:
- You know exactly what you want to build
- Learning the technology isn't the goal
- Shipping something that works matters more than doing it "the right way"

For my day job? I still write code the traditional way — understanding deeply matters when you're maintaining systems for years. For side projects? Claude Code is now my default starting point.

If you've been sitting on an idea because the tech stack felt unfamiliar, give AI-assisted development a try. The app you've been wanting might be a weekend away.
