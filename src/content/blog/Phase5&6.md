---
title: "Phases 5&6"
description: "Rust is so good!"
date: "2026-03-13"
tags: ["locktrace", "rust", "data-security"]
---

Just finished a massive sprint on Threadline. If you haven't seen it yet, I’m building this to stop investigators from going blind while staring at giant WhatsApp and Telegram spreadsheets.

Scrumming through phases 5 and 6 alone was a grind, but keeping a tight board helped me stay on track. I finally got the core of the evidence handling and analysis done. Here is what I actually shipped.

The biggest addition is the chain of custody system. When you're handling sensitive data, you have to prove it hasn't been messed with. I built a system that stamps every single message with a SHA-256 hash the second it hits the app. I even wrote a standalone Rust tool just to verify those hashes. If someone alters even one character in a text, the whole chain breaks and the tool catches it.

I also finished the anomaly detection features. Threadline now flags weird patterns automatically, like late-night messaging bursts or specific keywords popping up together. You can see all of this on a visual timeline and then export the exact evidence you need into a clean report.

It feels good to finally cross these phases off my list. You can pull the latest version from the repo and try it out.