---
title: "New project: Threadline"
description: "Starting something new: investigative analysis for law enforcement communication data."
date: "2025-02-23"
tags: ["threadline", "announcement"]
---

While Locktrace is still in active development, I've started scoping out a second project that's been in my head for a while.

The premise: when law enforcement builds a case against an individual or a criminal organization, they often end up with enormous communication datasets. We're talking tens of thousands of messages across WhatsApp, Telegram, SMS, extracted from seized devices using forensic tools. The output is typically a massive spreadsheet. And someone has to read it.

That process takes weeks. Sometimes longer. A detective sits with a printout or a CSV file and manually pieces together who's talking to whom, what locations are being referenced, when communication spikes occur, and which numbers are central to the network. It's tedious, error-prone, and doesn't scale.

Threadline automates the analysis layer. The detective uploads the extracted dataset, and the system maps the communication network, identifies hub entities, flags temporal anomalies, and extracts named entities: addresses, financial references, recurring terms from unstructured text.

The output is a visual network graph and a structured summary. Instead of reading fifty thousand messages, the investigator sees: "This number is the central node. Communication spiked 400% in the 48 hours before the incident. These 14 locations were referenced."

I've always been drawn to this kind of problem: taking chaotic, unstructured information and making it legible. The investigative angle is what makes it compelling to me personally. More on the technical architecture soon.