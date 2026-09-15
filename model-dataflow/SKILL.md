---
name: model-dataflow
description: Model how data moves from the user, through each application layer, down to the system of record and back. Use when asked to trace, diagram, or document a data flow, request lifecycle, or system interaction sequence.
---

# Data Flow Trace

## Purpose

Model how data moves from the user, through each application layer, down to the system of record (SOR) — and back, if there's a response leg.

The output is a light, readable sequence of steps which faithfully captures the dataflow surface of the user request. It is not a formal specification, and it leaves implementation details unspecified, clarifying instead data transformations, which owners-of-data are in play, and the flow of control from a systemic perspective.

## Output shape

The output is a single ordered table:

| Step | Layer | What happens | Next |
| ---- | ----- | ------------ | ---- |

- **Layer** — where this step occurs (e.g. UI, API, service, queue, SOR).
- **What happens** — one or two sentences. What's read, what's decided, what's written.
- **Next** — which step follows. Branches and loops are expressed here directly as conditionals, not as separate diagrams or prose:
  - `3`
  - `if approved → 5, else → 2`
  - `if retries < 3 → 2, else → error`

The Next column is the only control-flow mechanism. It should fully capture branching and looping without needing anything else.

Treat the first draft as something to iterate on with the user, not a final deliverable.

Capture your draft and modifications on disk, not in chat

## Principles

- **Light, not dense.** A short table someone can scan in a minute beats an exhaustive specification.
- **Sequence over structure.** Show the order steps happen in and how control moves between them. Don't catalogue every artifact's provenance, schema, or lifecycle in prose.
- **No visualizations.** Don't generate Mermaid, Graphviz, PlantUML, ASCII, or similar — lay out the relevant sequences and branching procedurally, after all, this is software.
- **Don't prescribe implementation.** Name layers and what crosses between them. Don't invent filenames, schemas, storage engines, or APIs unless the user has already specified them.
- **Propose, then iterate.** Show the intended output shape first. Revise the table based on feedback rather than trying to get it complete in one pass.

## Procedure

1. Confirm the workflow to trace and its starting point — the user action or event that kicks it off.
2. Propose the table shape (columns, granularity) to the user if it isn't already obvious from the request.
3. Draft the step sequence from that starting point through to the system of record, and back to the user if applicable, covering any flows in scope for the user's request.
4. Represent every loop or branch through the Next column only.
5. Share the draft and iterate with the user until the sequence matches their understanding of the flow.
