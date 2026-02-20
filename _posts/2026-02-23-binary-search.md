---
layout: post
title: "Binary Search: Invariants and Boundaries"
date: 2026-02-23
categories: [dsa, binary-search]
tags: [binary-search, invariants]
---

## Summary
- Binary search works by maintaining a strict invariant about where the answer can be.
- Variants (first true / last true) are more common than “find exact”.

## Invariant examples
- “Answer is in [l, r]”
- “l is always false, r is always true” (first-true pattern)



![Binary Search Diagram]({{ "/assets/images/dsa/binary-search.png" | relative_url }})

## Pitfalls
- Infinite loops from wrong mid update
- Using `mid = (l+r)/2` without thinking about rounding direction
- Returning `mid` instead of boundary pointer

## Mini-quiz
1. What’s the difference between “find exact” and “first true”?
2. When do you use `mid = (l+r)//2` vs `mid = (l+r+1)//2`?
3. What does the invariant look like for “minimum x such that f(x) is true”?
