---
layout: post
title: "Sliding Window Basics"
date: 2026-02-22
categories: [dsa, arrays]
tags: [sliding-window, patterns]
---

## Summary
- Maintain a window `[l..r]` and expand/shrink to satisfy a condition.
- Great for subarray/substring problems.

## Core idea
Keep a running state (sum, counts, max freq) so updates are O(1) per move.



[//]: # ![Sliding Window Diagram]({{ "/assets/images/dsa/sliding-window.png" | relative_url }})

## Steps
1. Expand `r` to include new element.
2. While invalid, shrink from `l`.
3. Update best answer.

## Common pitfalls
- Forgetting to update counts when moving `l`
- Mixing “fixed window size” vs “variable window condition”

## Mini-quiz
1. How do you know when to shrink?
2. Name one state variable you’d track for “longest substring without repeats”.
3. Why is sliding window typically O(n)?
