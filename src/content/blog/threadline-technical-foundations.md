---
title: "Threadline: four technical problems worth solving"
description: "Cryptographic chain of custody, deterministic graph theory, air-gapped NLP, and O(1) memory streaming — the foundations of Threadline."
date: "2026-02-19"
tags: ["threadline", "architecture", "NLP", "graph-theory"]
---

Threadline deals with law enforcement data. That changes the engineering constraints significantly compared to a typical web app. The tool needs to process large communication datasets — often extracted from devices during criminal investigations — and produce analysis that's reliable enough to support casework. Not "probably correct." Correct.

Here are the four technical foundations I'm building on.

## Cryptographic chain of custody

Evidence integrity is non-negotiable. If the analysis output can't prove it was derived from an unmodified source, it has no value in an investigation.

Every stage of the pipeline produces a cryptographic hash. The raw input file gets SHA-256'd on upload. Each transformation step — parsing, deduplication, entity extraction — hashes its output and chains it to the previous hash. The final analysis report references the entire chain back to the original file.

This means you can verify, at any point, that the analysis corresponds to the exact dataset that was ingested. No modifications, no insertions, no omissions. If a single byte changes anywhere in the pipeline, the chain breaks and the system flags it.

The implementation borrows from Merkle tree verification. Each processing node maintains an append-only log of input hash → transformation → output hash. Lightweight, tamper-evident, and auditable.

## Deterministic graph theory

The communication network is a directed graph. Phone numbers are nodes, messages are edges. The analytical value comes from graph metrics: degree centrality (who communicates the most), betweenness centrality (who connects disparate groups), and PageRank-derived influence scoring (who is structurally central to the network).

Determinism matters here. Given the same input, the system must produce the same graph and the same metrics every time. No randomized algorithms, no probabilistic shortcuts. If an investigator reruns the analysis on the same dataset, the output has to be identical. Otherwise, findings become difficult to defend.

I'm implementing this with NetworkX for the graph computation and a fixed-seed configuration for any algorithm that involves iteration ordering. The graph layout for visualization uses a force-directed algorithm with deterministic initialization — same positions, same output, every time.

Temporal analysis adds another layer. The graph isn't static. Communication patterns shift over time, and the interesting signals are often in the shifts — a cluster of numbers that suddenly start communicating at unusual hours, or a node that goes from peripheral to central in a short window. I'm partitioning the graph into time slices and computing differential metrics across them.

## Air-gapped NLP for PII

The messages contain personal information. Names, addresses, phone numbers, financial references. Processing this data through cloud-hosted NLP services isn't acceptable — it would mean transmitting sensitive evidence to third-party servers.

All NLP runs locally. Named Entity Recognition uses a fine-tuned spaCy model running on the same machine as the rest of the pipeline. No API calls, no external dependencies, no data leaving the system. The tradeoff is reduced model capability compared to cloud services, but for entity extraction in conversational text, spaCy's transformer-based models perform well enough.

The NER pipeline extracts: physical addresses, phone numbers (cross-referenced against the communication graph), monetary references, and high-frequency terms that may indicate coded language. These get tagged and indexed alongside the graph data, so an investigator can query "show me all messages mentioning [address]" and get results with full graph context — who sent the message, who received it, and where those entities sit in the network.

## O(1) memory streaming

Some of these datasets are large. Fifty thousand messages is manageable. But the system needs to handle cases where that number is significantly higher without running out of memory or taking prohibitively long.

The ingestion pipeline is stream-based. Messages are read, parsed, and processed one at a time — the entire dataset never needs to sit in memory simultaneously. The graph is built incrementally: each message adds or updates nodes and edges as it's processed. Entity extraction runs per-message. Hash computation is streaming.

The constant-memory constraint forces some design decisions. Deduplication uses a Bloom filter rather than a hash set (probabilistic, but bounded memory). Temporal partitioning happens during ingestion rather than as a post-processing step, so each time slice is written to disk as it's completed.

The result is a pipeline that scales with disk I/O rather than RAM. Processing time increases linearly with dataset size, but memory usage stays flat.

---

These four constraints — integrity, determinism, data isolation, and constant memory — define the architecture. Every implementation decision has to respect all four simultaneously, which makes the engineering harder but the system more trustworthy. For a tool that's meant to support criminal investigations, that's the only acceptable standard.

More updates as the implementation progresses.
