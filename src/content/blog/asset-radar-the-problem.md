---
title: "The problem Asset Radar is solving"
description: "Why I'm building a portfolio dashboard from scratch, and what existing tools get wrong."
date: "2025-02-08"
tags: ["asset-radar", "data-analysis"]
---

Every portfolio tracker I've used falls into one of two categories: too simple to be useful, or bloated with features nobody asked for. The simple ones give you a pie chart and call it a day. The complex ones bury relevant data under twelve tabs and a premium paywall.

What I actually want is straightforward. Pull data from multiple sources, normalize it, and show me where things stand — allocation breakdown, drift over time, and anything that looks off. That's it. No social feeds, no gamification, no AI stock picks.

So I'm building it.

## The scope

Asset Radar is a portfolio intelligence dashboard. The core loop is: ingest financial data → normalize into a common schema → compute allocation and drift metrics → render interactive visualizations. Nothing revolutionary on paper, but the execution has to be tight. Financial data is messy, formats vary wildly between sources, and rounding errors compound fast if you're not careful.

The stack I'm leaning toward:

- **Python** for the backend — pandas for transformation, FastAPI for the service layer
- **PostgreSQL** for persistence
- **React + D3.js** for the frontend
- Modular ingestion layer so adding new data sources doesn't require rewriting the pipeline

## Why not just use an existing tool

Partly because nothing fits what I want. But mostly because this is exactly the kind of project where the process teaches more than the output. Data normalization, aggregation pipelines, interactive charting — each of these has depth that I won't fully understand by reading documentation alone.

More on the architecture decisions in the next post.
