---
title: "Where Asset Radar goes from here"
description: "The current state of the project and what v2 needs to look like."
date: "2025-02-21"
tags: ["asset-radar", "roadmap"]
---

Asset Radar works. The core loop — ingest, normalize, compute, visualize — is solid. The treemap renders, the drift chart tracks accurately, and the data pipeline handles the edge cases I've thrown at it.

But "works" and "good" aren't the same thing.

## What v2 needs

**Anomaly detection.** Right now the dashboard shows you what's happening. It doesn't tell you what's unusual. I want to add a statistical layer that flags deviations — a sudden shift in allocation that wasn't triggered by a manual rebalance, or a holding that's drifted past two standard deviations from its historical mean. This is less about predictive modeling and more about surfacing things that deserve attention.

**Multi-currency normalization.** The current pipeline assumes everything is in a single currency. That's fine for a prototype but falls apart with international holdings. This means integrating FX rate data and applying conversions at the snapshot level, which touches the schema, the ingestion layer, and the metrics computation. Not trivial.

**Custom alert rules.** Let users define thresholds — "notify me if equity allocation drops below 60%" or "flag any single holding above 15% of total." This requires a lightweight rules engine on the backend and a simple UI for creating/managing rules. I'm looking at a JSON-based rule schema that the API evaluates on each snapshot update.

**Real-time data connectors.** Currently, data is imported manually. The next version needs scheduled pulls from brokerage APIs. Each connector is its own ingestion module, so the architecture supports this — it's just implementation work per source.

## Timeline

I'm not putting hard dates on any of this. But the anomaly detection layer is the priority because it transforms the dashboard from a reporting tool into something that actually thinks about the data. That's the feature that makes Asset Radar worth using over a spreadsheet.

More updates as things ship.
