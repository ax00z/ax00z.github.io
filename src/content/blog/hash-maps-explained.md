---
title: "Hash maps are everywhere and here's why"
description: "A practical look at hash tables — how they work under the hood, when to use them, and the gotchas that come up in interviews."
date: "2025-02-20"
tags: ["cs-fundamentals", "data-structures"]
---

If there's one data structure that keeps showing up — in interviews, in production code, in basically every language's standard library — it's the hash map. And yet a lot of CS graduates (myself included, until recently) treat it as just "the dictionary thing" without really understanding what's happening underneath.

So here's the breakdown I wish I had earlier.

## The core idea

A hash map stores key-value pairs. You give it a key, it gives you back the value. The magic is that it does this in O(1) average time — constant time lookup regardless of how many items you've stored.

How? It uses a **hash function** to convert your key into an index in an underlying array. Instead of searching through every element to find what you need, you compute where it should be and go straight there.

```
key → hash function → index → value
```

Think of it like a filing cabinet where every folder has a specific slot number. You don't flip through every folder looking for "client contracts." You compute which slot that label maps to and pull it directly.

## What actually happens in memory

Under the hood, a hash map is backed by an array. When you do something like:

```python
cache = {}
cache["user_123"] = {"name": "Amir", "role": "engineer"}
```

The hash map takes the key `"user_123"`, runs it through a hash function to get a number, then mods that number by the array size to get an index. The value gets stored at that index.

The hash function's job is to distribute keys evenly across the array. A good hash function means fewer collisions; a bad one means everything clusters in the same few slots and performance tanks.

## Collisions and how they're handled

Two different keys can hash to the same index. That's a collision, and it's unavoidable once you have more keys than array slots. There are two main strategies:

**Chaining** — each slot in the array holds a linked list (or another structure). When two keys collide, both get appended to the list at that index. To look something up, you hash to the right slot, then walk the list. If the hash function is good and the table isn't overloaded, these lists stay short.

**Open addressing** — instead of chaining, you look for the next available slot in the array. Linear probing checks the next slot, quadratic probing jumps further, and double hashing uses a second hash function to determine the step size. This keeps everything in one contiguous array, which can be better for cache performance.

Python dicts use open addressing. Java's HashMap uses chaining.

## The time complexity catch

Everyone says hash maps are O(1). That's the *average* case with a decent hash function and a reasonable load factor. Worst case — if every key collides — you're looking at O(n) because you're basically searching a linked list or probing through the entire array.

In practice, modern implementations keep this from happening by:
- Resizing the array when the load factor gets too high (usually around 0.75)
- Using well-designed hash functions that minimize clustering

For interviews, know both the average and worst case, and be ready to explain why the worst case rarely happens in practice.

## When to reach for a hash map

Any time you need fast lookups by key. Common patterns:

- **Counting frequencies** — count characters in a string, word occurrences, etc.
- **Caching / memoization** — store computed results to avoid recalculating
- **Detecting duplicates** — check if you've seen something before in O(1)
- **Grouping** — map items to categories
- **Two-sum type problems** — store complements as you iterate

If you're doing a coding interview and you're stuck, ask yourself: "Would a hash map let me trade space for time here?" The answer is yes more often than you'd think.

## The gotcha that trips people up

Hash maps don't preserve insertion order in every language. In Python 3.7+, dicts maintain insertion order as an implementation detail (and it's guaranteed in 3.8+). In Java, `HashMap` doesn't guarantee order — you'd need `LinkedHashMap` for that. In JavaScript, object key order is... complicated.

If order matters, know which variant to use in whatever language you're interviewing in.

## Summary

Hash maps are the workhorse of modern programming. Fast lookups, flexible keys, and they show up in almost every real-world system. Understanding how they work under the hood — hashing, collisions, resizing — will help you both in interviews and in writing better code.

Next up, I'll probably write about trees or graphs. Something I'm less comfortable with, which is exactly why I should write about it.
