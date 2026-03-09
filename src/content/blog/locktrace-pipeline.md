---
title: "Data pipelines are 80% edge cases"
description: "What building the Locktrace ingestion layer actually looks like day to day."
date: "2026-02-19"
tags: ["locktrace", "data-engineering"]
---

The ingestion layer is working. Took about a week of focused effort, and most of that time was spent on problems I didn't anticipate.

Here's what I mean. The "happy path" — read a CSV, parse the columns, insert into PostGIS — took maybe two hours. The remaining five days were edge cases.

Dates in three different formats depending on the year of the police report. Street names misspelled or formatted inconsistently ("Yonge St" vs "Yonge Street" vs "YONGE"). Missing coordinates. Thefts where the time is silently defaulted to 00:00 because the victim didn't know exactly when their bike was taken.

None of this is hard to fix individually. A regex here, a type cast there. But the accumulation of it is where complexity lives. Each edge case is a conditional branch. Each branch is a potential failure point.

## What helped

Two things made this manageable:

**Validation layer between ingestion and storage.** Every record passes through a schema check before hitting the database. If a coordinate is null, if a time value is outside expected bounds, if a date doesn't parse — it gets flagged and quarantined. I don't try to fix bad data automatically. I just make sure it doesn't pollute the clean dataset.

**Deterministic test fixtures.** I wrote a small set of intentionally messy sample files — mixed formats, missing fields, encoding issues — and run every ingestion script against them before committing. Not a full test suite yet, but it catches the regressions that matter.

The pipeline isn't elegant. It's a series of pragmatic decisions stacked on top of each other. But it works, and the spatial data coming out the other end is consistent. That's what matters.