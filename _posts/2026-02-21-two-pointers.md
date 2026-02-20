---
layout: post
title: "Two Pointers Pattern"
date: 2026-02-21
categories: [dsa, arrays]
tags: [two-pointers, patterns]
---

## Summary
- Use two indices to scan from left/right (or slow/fast) to avoid nested loops.
- Best when the array/string is sorted or when you can maintain an invariant.

## Intuition
Move pointers based on a condition so you never “re-check” work.



![Two Pointers Diagram]({{ "/assets/images/dsa/two-pointers.png" | relative_url }})

## Steps
1. Initialize `l = 0`, `r = n-1` (or slow/fast).
2. While `l < r`, evaluate condition.
3. Move one pointer in a way that preserves the invariant.

## Complexity
- Time: O(n)
- Space: O(1)

## Pitfalls
- Off-by-one (`l <= r` vs `l < r`)
- Moving the wrong pointer breaks the invariant

## Mini-quiz
1. When do you prefer two pointers over hash set?
2. What invariant does “pair sum in sorted array” maintain?
3. What changes for “slow/fast” vs “left/right”?
