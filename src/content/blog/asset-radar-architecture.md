---
title: "Picking the right architecture (and the wrong one first)"
description: "Initial architecture decisions for Asset Radar, and why I scrapped my first approach."
date: "2025-02-11"
tags: ["asset-radar", "architecture"]
---

Started with a monolith. Single Flask app, SQLite, everything in one process. Figured I'd keep it simple and refactor later.

That lasted about three days.

The problem wasn't performance — it was the data ingestion layer fighting with the API layer for structure. Every new data source I added meant touching code that had nothing to do with ingestion. Classic coupling issue that I should've anticipated.

## What I switched to

Separated the system into three clear boundaries:

**Ingestion service** — standalone Python scripts per data source. Each one reads raw data, normalizes it into a common schema, and writes to Postgres. These run independently. If one source is down or changes its format, nothing else breaks.

**API layer** — FastAPI serving computed metrics. Allocation percentages, drift from target, historical snapshots. Stateless, reads from the database, does the math, returns JSON.

**Frontend** — React app consuming the API. D3.js for the charts because I need more control than Recharts or Chart.js give me. The allocation treemap in particular requires custom layout logic.

## The schema decision

This took longer than I expected. Financial data comes in different shapes depending on the source — some give you individual transactions, some give you daily balances, some give you holdings snapshots. I needed a unified model.

Landed on a normalized structure: `accounts`, `holdings`, `snapshots`, and `transactions`. Every ingestion script maps its source format into these four tables. Redundant in some cases, but it means the API layer never has to think about where the data came from.

Not glamorous work. But this is where most data projects either hold together or fall apart.
