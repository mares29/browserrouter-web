---
title: "Notes on Going Open Source"
date: 2026-01-07
description: "Reflections on releasing a personal project to the public."
---

BrowserRouter started as a script I wrote to solve my own problem. Somewhere along the way, it became an app with a landing page and a GitHub repository. Here's what I learned.

## Building for one

The best motivation for building software is solving your own problem. When you're the user, you know exactly what matters. No user research required—you have direct access to someone who will use this tool every day.

BrowserRouter exists because I got frustrated clicking links in Slack and watching them open in Safari while I was deep in a Chrome work session. That frustration was enough to write the first version in an afternoon.

## The moment of polish

There's a point in every project where "it works for me" isn't enough anymore. You start thinking about edge cases. You add error handling. You write documentation.

This is the moment where most personal projects die. The interesting problem is solved. What remains is the work of making it useful for others.

Pushing through that resistance is how side projects become real software.

## Open source as accountability

Making the code public changes your relationship with it. Suddenly there's a theoretical audience, even if no one's actually reading your code yet. You write better comments. You clean up that hacky workaround. You think about maintainability.

Open source isn't just about sharing—it's a forcing function for quality.

## What I'd tell myself

If I could go back to the beginning of this project, here's what I'd say:

**Ship earlier than you're comfortable with.** The feedback you get from having something real in the world is worth more than the polish you're adding in isolation.

**Documentation is marketing.** A good README does more for adoption than any feature.

**Solve one problem well.** Scope creep is the enemy of utility software. BrowserRouter does one thing. That focus is a feature.

The code is on GitHub. Maybe it'll be useful to someone else who's tired of fighting their operating system's defaults.
