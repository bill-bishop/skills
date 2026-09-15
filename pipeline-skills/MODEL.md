# Software-Change Workflow — Model

The big picture, before the sequence-diagram detail in `index.html` / `dataflow-diagram.html`.

A single loop shape repeats through the whole pipeline: a process produces an artifact, the **Evidence Cycle** (Evidence Extraction → Evidence Review) turns it into reviewed, addressable evidence, and the next process reads that evidence. Everything downstream of Reference Intake is really that one cycle, run five times against five different artifacts, plus a failure path (Fault Localization) that can reopen any earlier step. Orchestration today is manual — the user invokes each step in order — but the dataflow doesn't require that; a single `software-change` skill could later drive the same sequence.

| Step | Layer | What happens | Next |
| ---- | ----- | ------------- | ---- |
| 1 | Reference Intake | User submits a change request plus whatever references they have. Reference Intake assembles the Reference Set, and if no Current Model exists yet, invokes **Model Dataflow Spec** against the codebase to derive one. | 2 |
| 2 | Evidence Cycle | Evidence Extraction runs against some user-supplied or Agent-derived Reference Set (after step 1, this will be the change request, Current Model, and other gathered references) into individually addressable evidence; Evidence Review walks the user through unresolved items one at a time until none remain pending. This is the same reusable cycle every time it appears below, just pointed at whatever artifact the prior step just produced. | 3 |
| 3 | Target Model Proposal | Reads the Current Model plus reviewed evidence, proposes the target system design, and iterates with the user until they approve it — the one point where the user signs off on the overall design, distinct from the per-item adjudication each Evidence Cycle already does. | 4 | <-- User feedback: seems misstated, isnt every evidence cycle an interactive approval gate? 
| 4 | Evidence Cycle | Reused (step 2), now run against the approved Target Model. | 5 |
| 5 | File Surface Discovery | Reads the evidence to identify which implementation surfaces participate in the change. | 6 |
| 6 | Evidence Cycle | Reused, now run against the discovered File Impact. | 7 |
| 7 | Change Planning | Reads the accumulated evidence to determine the concrete implementation changes needed. | 8 |
| 8 | Evidence Cycle | Reused, now run against the Change Plan. | 9 |
| 9 | Implementation | Executes the approved plan against the codebase, producing a Candidate Implementation. | 10 |
| 10 | Validation | Tests the candidate against the evidenced requirement, Target Model, and Change Plan. | if pass → done, else → 11 |
| 11 | Fault Localization | Traces failures back up the chain (Implementation → Change Plan → File Impact → Target Model → Evidence Set), non-destructively marking suspects. User confirms or rejects each, one at a time, via the same review pattern as the Evidence Cycle. | re-invoke whichever step (3, 5, or 7) produced the confirmed-wrong artifact; every step between that point and Implementation re-runs in sequence → 9 |
