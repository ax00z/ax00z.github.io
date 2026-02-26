---
title: "D3 is not a charting library"
description: "Building Asset Radar's dashboard, and why D3.js was the right call even when it wasn't easy."
date: "2025-02-18"
tags: ["asset-radar", "d3", "frontend"]
---

D3 doesn't give you charts. It gives you primitives for bindings between data and the DOM. The distinction matters because it means you're not configuring a bar chart — you're building one from SVG elements, scales, and transitions. More work upfront, significantly more flexibility.

For Asset Radar, I needed three main views: an allocation treemap, a time-series line chart for historical drift, and a table with sortable columns for individual holdings. The treemap is where D3 justified itself. No charting library I tested handled the nested grouping I needed — assets grouped by class, then by account, with proportional sizing and drill-down interaction. D3's `treemap()` layout with `d3.hierarchy()` gave me exactly the structure I needed.

## The React + D3 tension

These two frameworks both want to own the DOM, which creates friction. I went with the "D3 for math, React for rendering" pattern. D3 computes the layouts, scales, and data transformations. React renders the SVG elements. This keeps React's reconciliation happy and avoids the bugs that come from D3 directly manipulating nodes that React thinks it controls.

Concretely: I use `useMemo` to run D3's layout computations, then map the output to JSX `<rect>` and `<text>` elements. Transitions are CSS-based rather than D3's built-in `.transition()`. Slightly less smooth, but far easier to reason about.

## What the dashboard looks like now

The main view is the treemap — it gives an immediate visual of where your money sits. Click into an asset class and it expands to show individual holdings. The drift chart sits below it, showing how allocation has shifted over time against your target weights. Red zones where drift exceeds a configurable threshold.

It's functional. Not polished yet. But the data flows from Postgres through the API to the charts without anything breaking, and that felt like a milestone worth noting.
