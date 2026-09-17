---
name: i-want
description: The user "wants something" from an Agent. Help them figure out what a text-producing LLM can actually do for them.
---

## Goal

Tell the user up front: this skill produces a document capturing the spirit of what they want, which they can hand off to an agent for discovery / prototyping / detailed planning.

Align on three things at a bird's-eye level, then document them materially:

1. **What the user wants** — The user's goal, stated in terms of what agents will do to achieve that goal. "To build..." "To write a plan for..." etc.
2. **How they want to get there** — how much setup work, how much code, how rough a prototype (something manual to hit the ground running) vs. polished result they're after.
3. **What is actually possible** — reality-check before planning any integrations, API keys, or moving parts. Of the three items, this is the only one that should mention specific technologies or integrations. Keep it as abstract as is reasonably possible to propose useful abstract descriptions for the other items.

## None of them are done until all three are aligned

These three items relate to one another fundamentally, so expect to refine all of them together, not in sequence.

- **"What the user wants" is ambiguous by default.** "I want to answer my emails automatically" or "I want to figure out the best way to do X" could mean many different things in terms of what an agent should actually do or produce. Your job at this stage is to map their intention onto concrete agent actions or outputs.
- **"How they want to get there" is often the most important early alignment.** It sets expectations for effort, complexity, and ownership. In some ways, this is the *true* description of what they want.
- **"What is actually possible"** an oft-missed step, but before you plan integrations requiring api keys or any kind of integration or moving part at all, you must first clarify that these things are possible in the users domain, or possible to request/configure & that the configuration is aligned "how they want to get there" cost. Avoid proposing things to the user that involve lengthy development cycles if they dont have time to troubleshoot. And avoid proposing integrations that seem simple but in reality would require the user to request some access that they're not going to get approved for.

## What is actually possible

This step may require research, and may require you to help the user figure out who to ask - if they are enthusiastic about builds and integrations and willing to request access, but they don't know what's possible or how to find out: check what tools are available (slack/jira mcp) and propose discovering for them the feasibility of access-request paths, environment setup etc.

## Prototyping / Hand-off

Work with the user to identify which specific quick-prototyping a la "what is actually possible" would best empower the planning and implementation of their idea.

Once aligned, document this fourth and final alignment materially along with the other items:

4. **Prototyping suggestions**  — Converting "what is actually possible" into material reality — *FAST* — if we correctly identify the friction points and a fast path to legitimatley prove them out, the user will feel like they have UNLOCKED SUPERPOWERS.

## Process

- Present each of the three items concisely — 3-5 sentences max, ideally.
- Iterate with the user until all three are locked in and documented.
- With the user's approval, carry out the discovery and documentation of "What is possible" or when necessary, an approval roadmap for getting various accesses that would open doors for what they want.
- Once the user confirms the three descriptions perfectly capture their intent, and the document is up to date:
  - Tell them where the file lives.
  - (If `/prototyping` skill is present) Tell them to start a fresh agent and run the `/prototyping` skill.
  - (If `/prototyping` skill is not present) Tell them to start a fresh agent and point them at this file! (ONLY IF NO SIBLING SKILL PRESENT: write a short hand-off for the agent instructing them to carry out and materially document the suggestions laid out in 4.)
  
