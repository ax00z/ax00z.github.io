---
title: "D3 is not a charting library"
description: "Building Locktrace's dashboard, and why D3.js was the right call even when it wasn't easy."
date: "2025-02-18"
tags: ["locktrace", "d3", "frontend"]
---

D3 doesn't give you charts. It gives you primitives for bindings between data and the DOM. The distinction matters because it means you're not configuring a map — you're building one from SVG elements, scales, and transitions. More work upfront, significantly more flexibility.

For Locktrace, I needed three main views: a spatial hexbin map, a time-series line chart for temporal spikes, and a table with sortable columns for individual incidents. The hexbin map is where D3 justified itself. No plug-and-play mapping library I tested handled the geographic grouping I needed — thefts binned by geographic radius, dynamically sizing based on zoom level. D3's `d3-hexbin` gave me exactly the structure I needed.

## The React + D3 tension

These two frameworks both want to own the DOM, which creates friction. I went with the "D3 for math, React for rendering" pattern. D3 computes the layouts, scales, and spatial transformations. React renders the SVG elements. This keeps React's reconciliation happy and avoids the bugs that come from D3 directly manipulating nodes that React thinks it controls.

Concretely: I use `useMemo` to run D3's layout computations, then map the output to JSX `<path>` and `<circle>` elements. Transitions are CSS-based rather than D3's built-in `.transition()`. Slightly less smooth, but far easier to reason about.

## What the dashboard looks like now

The main view is the hexbin map — it gives an immediate visual of where thefts are concentrated across Toronto's wards. Click into a ward and it expands to show temporal data. The spike chart sits below it, showing how theft rates have shifted over time against historical baselines. Red zones indicate where incidents exceed expected thresholds.

It's functional. Not polished yet. But the data flows from PostGIS through the API to the charts without anything breaking, and that felt like a milestone worth noting.