---
title: "Data pipelines are 80% edge cases"
description: "What building the Asset Radar ingestion layer actually looks like day to day."
date: "2025-02-14"
tags: ["asset-radar", "data-engineering"]
---

The ingestion layer is working. Took about a week of focused effort, and most of that time was spent on problems I didn't anticipate.

Here's what I mean. The "happy path" — read a CSV, parse the columns, insert into Postgres — took maybe two hours. The remaining five days were edge cases.

Dates in three different formats across two sources. Currency symbols embedded in numeric fields. Duplicate entries that only differed by a trailing whitespace. One source that silently rounds fractional shares to zero. Another that uses commas as decimal separators.

None of this is hard to fix individually. A regex here, a type cast there. But the accumulation of it is where complexity lives. Each edge case is a conditional branch. Each branch is a potential failure point. And financial data has zero tolerance for silent corruption — if a rounding error compounds across six months of snapshots, the drift metrics become meaningless.

## What helped

Two things made this manageable:

**Validation layer between ingestion and storage.** Every record passes through a schema check before hitting the database. If a field is null when it shouldn't be, if a numeric value is outside expected bounds, if a date doesn't parse — it gets flagged and quarantined. I don't try to fix bad data automatically. I just make sure it doesn't pollute the clean dataset.

**Deterministic test fixtures.** I wrote a small set of intentionally messy sample files — mixed formats, missing fields, encoding issues — and run every ingestion script against them before committing. Not a full test suite yet, but it catches the regressions that matter.

The pipeline isn't elegant. It's a series of pragmatic decisions stacked on top of each other. But it works, and the data coming out the other end is consistent. That's what matters.
