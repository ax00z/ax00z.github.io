---
title: "Picking the right architecture (and the wrong one first)"
description: "Initial architecture decisions for Locktrace, and why I scrapped my first approach."
date: "2026-02-04"
tags: ["locktrace", "architecture"]
---

Started with a monolith. Single Flask app, SQLite, everything in one process. Figured I'd keep it simple and refactor later.

That lasted about three days.

The problem wasn't performance — it was the data ingestion layer fighting with the API layer for structure. Every new police dataset I added meant touching code that had nothing to do with ingestion. Classic coupling issue that I should've anticipated.

## What I switched to

Separated the system into three clear boundaries:

**Ingestion service** — standalone Python scripts per data source. Each one reads raw TPS open data, normalizes it into a common schema, and writes to PostGIS. These run independently. If a source format changes, nothing else breaks.

**API layer** — FastAPI serving computed metrics. Geographic bounding boxes, temporal spikes, historical snapshots. Stateless, reads from the database, does the math, returns JSON.

**Frontend** — React app consuming the API. D3.js for the spatial and temporal charts because I need more control than Recharts or Leaflet give me out of the box. The hexbin map in particular requires custom layout logic.

## The schema decision

This took longer than I expected. Open data comes in different shapes depending on the year — some files give you exact coordinates, some give you ward-level aggregates, some separate bikes from vehicles entirely. I needed a unified model.

Landed on a normalized structure: `neighborhoods`, `incidents`, `vehicle_types`, and `temporal_snapshots`. Every ingestion script maps its source format into these four tables. Redundant in some cases, but it means the API layer never has to think about where the data came from.

Not glamorous work. But this is where most data projects either hold together or fall apart.