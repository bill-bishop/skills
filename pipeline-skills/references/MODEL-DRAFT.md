Right. I was describing the **information flow**, but you're asking for the **operational dataflow**: what concrete process the user invokes, what it consumes, what process follows, and what artifact crosses each boundary.

The entry point itself has to be part of the model.

I'd describe it like this, while still staying implementation-agnostic:

| Step                              | Invocation / User Action                                                                                     | Skill does                                                                                                                                                                                                                                                                 | Reads                                   | Writes / Produces                                                                      | Next                                                 |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **1. Reference Intake**           | User invokes **Reference Intake**, supplying their change request and whatever references they already have. | Establishes the reference context for this specific change. Interactively requests missing material. Ensures a user-approved Current Model is among the references, creating/alignment-reviewing one with the user if necessary.                                           | User invocation + supplied references   | **Reference Set** containing requirement, Current Model, and other gathered references | User invokes Evidence Extraction                     |
| **2. Evidence Extraction**        | User invokes **Evidence Extraction** for the prepared Reference Set.                                         | Reads every indexed reference and decomposes it into individually addressable evidence without judging relevance or correctness.                                                                                                                                           | Reference Set                           | **Evidence Set**, with every item linked to its source reference                       | User invokes Evidence Review                         |
| **3. Evidence Review**            | User invokes **Evidence Review**.                                                                            | Examines the Evidence Set for conflicts, outdatedness, invalidity, ambiguity, or anything else requiring adjudication. Interactively walks the user through unresolved items.                                                                                              | Evidence Set                            | **Evidence Review Decisions** associated with the affected evidence                    | Continue until no review decisions remain unresolved |
| **4. Target Model Proposal**      | User invokes **Target Model Proposal**.                                                                      | Reads the intact Current Model plus reviewed evidence and proposes the desired holistic system change at the same abstraction/granularity as Current Model. Interactively revises it with the user. Every changed/additional assertion identifies its supporting evidence. | Current Model + Reviewed Evidence       | User-approved **Target Model**                                                         | User invokes Evidence Extraction on Target Model     |
| **5. Target Evidence Extraction** | User invokes **Evidence Extraction** on the approved Target Model.                                           | Decomposes the derived model into individually addressable evidence while preserving links to the model and its supporting evidence premises.                                                                                                                              | Target Model                            | **Target Model Evidence** added to the available Evidence Set                          | User invokes File Surface Discovery                  |
| **6. File Surface Discovery**     | User invokes **File Surface Discovery** for the change.                                                      | Uses the available evidence to investigate the codebase and determine which implementation surfaces participate in the evidenced Current→Target change.                                                                                                                    | Evidence Set + codebase                 | **File Impact** artifact, with scope conclusions linked to evidence                    | User invokes File Evidence Extraction                |
| **7. File Evidence Extraction**   | User invokes **File Evidence Extraction** on the discovered implementation surface.                          | Examines those implementation surfaces and extracts concrete implementation facts without yet deciding how they should change.                                                                                                                                             | File Impact + identified code surfaces  | **File Evidence** added to the available Evidence Set                                  | User invokes Change Planning                         |
| **8. Change Planning**            | User invokes **Change Planning**.                                                                            | Uses the accumulated evidence to determine the concrete implementation changes required to realize the Target Model. Every planned change identifies its supporting evidence.                                                                                              | Evidence Set                            | **Change Plan**                                                                        | User invokes Evidence Extraction on plan             |
| **9. Plan Evidence Extraction**   | User invokes **Evidence Extraction** on the Change Plan.                                                     | Converts planned changes into individually addressable evidence while retaining their premise relationships.                                                                                                                                                               | Change Plan                             | **Plan Evidence** added to Evidence Set                                                | User invokes Implementation                          |
| **10. Implementation**            | User invokes **Implementation** for the approved plan.                                                       | Executes the described changes without redefining the plan.                                                                                                                                                                                                                | Change Plan / Plan Evidence + codebase  | **Candidate Implementation**                                                           | User invokes Validation                              |
| **11. Validation**                | User invokes **Validation**.                                                                                 | Tests the Candidate Implementation against the evidenced requirement, Target Model, and Change Plan. Traces failures back to the conclusions/premises responsible.                                                                                                         | Candidate Implementation + Evidence Set | **Validation Result**                                                                  | Done, or invoke the appropriate upstream skill again |

This exposes something useful: **Evidence Extraction is not really a phase. It's a reusable skill/operation.**

The actual composition is more like:

```text id="2ybh28"
User
 │
 │ invoke Reference Intake(change request, references...)
 ▼
REFERENCE INTAKE
 │
 ▼
Reference Set
 │
 │ invoke Evidence Extraction(reference set)
 ▼
EVIDENCE EXTRACTION
 │
 ▼
Evidence Set
 │
 │ invoke Evidence Review
 ▼
EVIDENCE REVIEW
 │
 ▼
Reviewed Evidence
 │
 │ invoke Target Model Proposal
 ▼
TARGET MODEL PROPOSAL
 │
 │  ↕ interactive user alignment
 ▼
Target Model
 │
 │ invoke Evidence Extraction(target model)
 ▼
EVIDENCE EXTRACTION
 │
 ▼
Target Model Evidence
 │
 │ invoke File Surface Discovery
 ▼
FILE SURFACE DISCOVERY
 │
 ▼
File Impact
 │
 │ invoke File Evidence Extraction
 ▼
FILE EVIDENCE EXTRACTION
 │
 ▼
Implementation Evidence
 │
 │ invoke Change Planning
 ▼
CHANGE PLANNING
 │
 ▼
Change Plan
 │
 │ invoke Evidence Extraction(change plan)
 ▼
EVIDENCE EXTRACTION
 │
 ▼
Plan Evidence
 │
 │ invoke Implementation
 ▼
IMPLEMENTATION
 │
 ▼
Candidate
 │
 │ invoke Validation
 ▼
VALIDATION
```

And I think there's an important design decision hiding here:

**For the prototype, the user may manually invoke each skill. But the dataflow shouldn't require that.**

The conceptual contract is just:

```text id="d98w0p"
skill invocation
    ↓
inputs
    ↓
skill process
    ↓
persisted artifact(s)
    ↓
next skill invocation
```

So later you can introduce one higher-level `software-change` skill that says, effectively:

```text id="qvhr2m"
software-change
    → reference-intake
    → evidence-extraction
    → evidence-review
    → target-model
    → evidence-extraction
    → file-surface
    → file-evidence
    → change-plan
    → ...
```

without changing the underlying dataflow at all.

That is probably the level at which I'd specify **all** of these workflows: **invocation → consumed artifacts → process → produced artifacts → next invocation**, with interactive user exchanges represented explicitly wherever they occur.
