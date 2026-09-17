---
name: prototyping
description: Use when you have a written INTENT document and need to build the fastest possible throwaway version to find out whether an approach works, not a polished implementation.
---

# Prototyping

Take an INTENT document and build the fastest throwaway version that answers one question: does this approach work? Not a polished implementation.

## Rules

- Optimize for "do we know yet" over code quality. Rough and disposable beats correct and durable.
- Skip tests, error handling, edge cases, polish, abstractions, and config — unless skipping one would prevent answering the question.
- No prescribed toolchain. "Quick" means something different per domain (script, UI mockup, API call chain, data pipeline, ...) — pick whatever gets to a working demo fastest, decided now, per-project, not fixed in advance.
- Stop as soon as the core question is answered. Don't keep building.
- Tell the user explicitly that it's throwaway: what was skipped, and what would need rework before this could become real.

## Process

1. Read the INTENT doc. Identify the one thing being tested — the specific uncertainty the prototype needs to resolve.
2. Pick the cheapest technology/approach that can demonstrate it.
3. Build only enough to demonstrate that one thing.
4. Report: does it work, what was skipped, what's the fastest path from here to real.

Keep this file itself minimal — expand only when real use exposes a gap, not upfront.
