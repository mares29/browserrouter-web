---
title: "Why Native Still Matters"
date: 2026-01-21
description: "In a world of Electron apps, there's still a case for building native software."
---

Every few months, someone publishes a thinkpiece about how native development is dead. Cross-platform frameworks have won. Why write Swift and Kotlin when you can write JavaScript once?

Having built BrowserRouter as a native Swift app, I have thoughts.

## The numbers that matter

BrowserRouter uses about 10MB of RAM. A comparable Electron app would use 150-300MB just to exist. That's not a theoretical concern—it's the difference between software that fades into the background and software that fights for resources.

The app is 2MB on disk. It launches instantly. It doesn't spin up a Chromium instance to display a menu bar icon.

## Native means invisible

The best menu bar apps are the ones you forget about. They sit there, doing their job, never demanding attention. Native apps make this possible in a way that frameworks struggle with.

System integration matters too. BrowserRouter needs to observe which app has focus, intercept URL handling, and communicate with the system's browser registration. These aren't web platform features. Fighting against the platform to access them is a losing battle.

## When Electron makes sense

Cross-platform frameworks aren't wrong—they're tradeoffs. If you need to ship on Windows, Mac, and Linux with a small team, Electron might be the right choice. If your app is essentially a web app that happens to need desktop presence, it makes sense.

But for a utility that lives in your menu bar and does one thing well? Native is still the answer.

## Craft as a statement

There's something else, harder to quantify: building native feels like craftsmanship. You're working with the materials the platform provides, not around them.

In a world of increasingly bloated software, small and fast is a feature worth pursuing.
