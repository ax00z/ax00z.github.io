---
title: "Where Locktrace goes from here"
description: "The current state of the project and what v2 needs to look like."
date: "2026-02-27"
tags: ["locktrace", "roadmap"]
---

Locktrace works. The core loop — ingest, normalize, compute, visualize — is solid. The spatial map renders, the temporal spike chart tracks accurately, and the data pipeline handles the edge cases I've thrown at it.

But "works" and "good" aren't the same thing.

## What v2 needs

**Anomaly detection.** Right now the dashboard shows you what's happening. It doesn't tell you what's unusual. I want to add a statistical layer that flags deviations — a sudden spike in Honda CRV thefts in a specific ward, or a bike theft hotspot that's drifted past two standard deviations from its historical mean. This is less about predictive modeling and more about surfacing things that deserve attention.

**Weather and transit normalization.** The current pipeline assumes thefts happen in a vacuum. I want to correlate incident rates with external variables like precipitation or proximity to major transit hubs. This means integrating new datasets and applying correlations at the snapshot level, which touches the schema, the ingestion layer, and the metrics computation. Not trivial.

**Custom alert rules.** Let users define thresholds — "notify me if vehicle thefts in Ward 10 exceed 15 in a week" or "flag any high-end bike theft near the downtown core." This requires a lightweight rules engine on the backend and a simple UI for creating/managing rules. 

**Automated TPS API connectors.** Currently, Toronto Police Service open data is imported manually. The next version needs scheduled pulls from their API. Each connector is its own ingestion module, so the architecture supports this — it's just implementation work per source.

## Timeline

I'm not putting hard dates on any of this. But the anomaly detection layer is the priority because it transforms the dashboard from a reporting tool into something that actually thinks about the data. 

More updates as things ship.