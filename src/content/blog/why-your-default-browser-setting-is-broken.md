---
title: "Why Your Default Browser Setting is Broken (And How to Fix It)"
date: 2026-01-07
description: "macOS has a setting for your default browser. Here's what it doesn't do — and how to make links actually follow your context."
---

You're deep in work. Chrome tabs open, three docs in progress, a YouTube tutorial playing in the background. A colleague sends you a link in Slack. You click it.

Safari opens.

Wrong Google account. Your carefully arranged tabs? Gone from view. Now you're copying the URL, switching back to Chrome, pasting — all to see a link you clicked two seconds ago.

This isn't a one-time annoyance. If you use multiple browsers, this happens 20, 30, maybe 50 times a day. Each time, a tiny interruption. A small tax on your focus. Death by a thousand paper cuts.

It doesn't have to be this way.

## The Problem macOS Refuses to Fix

macOS has a setting for your default browser. You set it once — years ago — and forgot about it.

Here's what that setting doesn't do: it doesn't know what you're doing *right now*. It doesn't care that you've been working in Chrome for the past two hours. It doesn't notice you switched to Firefox for testing. It just sends every link to whatever browser you picked in 2019.

In a world where people used one browser, this made sense.

In 2026? When developers test across browsers, remote workers separate work from personal, and privacy-conscious users run different contexts? It's broken.

## Why You're Using Multiple Browsers

You're not doing anything weird. These workflows are everywhere:

**Work vs. Personal.** Chrome for work (company Google account, Slack, internal tools). Safari for personal (your actual email, your actual life). Clean separation without managing Chrome profiles.

**Development and testing.** DevTools in Chrome, but you need to verify in Safari and Firefox. Different browsers, different rendering, different bugs.

**Privacy contexts.** Firefox with strict tracking protection for general browsing. Chrome for sites that break with privacy settings enabled.

**Arc + Safari.** Arc's workspaces for deep work, Safari's battery efficiency for everything else.

None of these are edge cases. But macOS treats them that way.

## The Workarounds (And Why They All Suck)

People have tried to solve this. Nothing works well.

**The manual method:** Click link → wrong browser opens → copy URL → switch to right browser → paste → finally see the page. Do this 30 times a day and watch your productivity drain away.

**Rule-based apps (Choosy, OpenIn, $10):** Create routing rules — "Slack links go to Chrome, Mail links go to Safari." Powerful if you want to program your link behavior. Most people don't.

**Picker apps (Browserosaurus):** Every link triggers a popup. Choose the browser. Click. Repeat for every single link. The friction adds up fast.

All of these require you to *think* about browser routing. You're solving the problem manually, over and over.

What if links just opened where you were already working?

## The Fix: Links Follow Your Focus

The solution is obvious once you see it: track which browser you're using, and send links there.

Working in Chrome? Links open in Chrome.
Switch to Safari? Links follow.
No rules. No popups. No thinking.

This is what BrowserRouter does. It's a small menu bar app that watches which browser has your focus. Click a link anywhere — Mail, Slack, Notion, anywhere — and it opens in your current browser.

Install it. Set it as your default browser. Forget it exists.

Two days later, you'll realize something: you haven't copy-pasted a URL in 48 hours. Links just... work. They go where you expect them to go, without you doing anything.

That's the whole point.

## Privacy, Because It Matters

BrowserRouter stores exactly one thing: the order of your recently used browsers.

No URLs logged. No browsing history. No analytics. No network requests. The app doesn't even have internet access — there's nothing to phone home to.

It's also open source. Don't trust me? [Read the code yourself](https://github.com/mares29/browserrouter).

## When This Isn't Right for You

BrowserRouter isn't for everyone.

If you need hard rules — "Slack links *always* go to Chrome, no matter what" — you want Choosy. It's $10 and built for complex routing logic.

But if you just want links to follow your context, BrowserRouter is simpler, free, and requires zero setup.

## Try It (30 Seconds)

BrowserRouter is free, open source, and takes 30 seconds:

1. [Download the latest release](https://github.com/mares29/browserrouter/releases)
2. Drag to Applications
3. Open it, set as default browser in System Settings
4. Done. Links now follow your focus.

No account to create. No settings to configure. No subscription.

Your links will start going where they should. No more Safari hijacking your Chrome workflow.
