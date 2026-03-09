---
title: "The problem Locktrace is solving"
description: "Why I'm building a spatial dashboard from scratch for Toronto theft data, and what existing open data portals get wrong."
date: "2026-02-21"
tags: ["locktrace", "data-analysis"]
---

Every open data portal I've used for crime stats falls into one of two categories: a massive raw CSV that crashes your browser, or a clunky GIS dashboard that takes ten seconds to render a basic choropleth map.

What I actually want is straightforward. Pull the Toronto Police Service (TPS) data for vehicle and bike thefts, normalize it, and show me where things stand: geographic hotspots, temporal spikes, and anomalies. No bloated widgets, no premium paywalls.

So I'm building it.

## The scope

Locktrace is a spatial intelligence dashboard focused exclusively on vehicle and bicycle thefts in Toronto. The core loop is: ingest police data → normalize into a PostGIS schema → compute spatial and temporal metrics → render interactive visualizations. Nothing revolutionary on paper, but the execution has to be tight. Police data is messy, location coordinates vary wildly, and errors compound fast if you're not careful.

The stack I'm leaning toward:

- **Python** for the backend: pandas for transformation, FastAPI for the service layer
- **PostgreSQL + PostGIS** for spatial persistence
- **React + D3.js** for the frontend
- Modular ingestion layer so adding new years or formats doesn't require rewriting the pipeline

## Why not just use an existing tool

Partly because nothing fits what I want. But mostly because this is exactly the kind of project where the process teaches more than the output. Spatial normalization, geospatial pipelines, interactive hexbin charting. Each of these has depth that I won't fully understand by reading documentation alone.

More on the architecture decisions in the next post.