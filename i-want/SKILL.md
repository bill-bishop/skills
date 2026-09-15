---
name: i-want
description: The user "wants something" from an Agent. Help them figure out what a text-producing LLM can actually do for them.
---

- Alignment on super high level "what the user wants" & "how they want to get there" & "what is actually possible" 
- Present them to the user concisely, maximum 3-5 sentences each ideally
- Iterate until these bird's eye view alignments are locked in and materially documented 
- You will likely need to refine all three throughout the alignment process, as the three items relate to one another fundamentally
- "how they want to get there" is important - maybe the most import early-alignment, how much setup work do they want to do, how much code to they want to produce, how rough of a prototype process would they like to hit the ground running
- "what is actually possible" is an oft-missed step, but before you plan integrations requiring api keys or any kind of integration or moving part at all, you must first clarify that these things are possible in the users domain, or possible to request/configure & that the configuration is aligned "how they want to get there" cost

- If a user says "I want to answer my emails automatically," this could mean many things in terms of what the user wants an agent to do or produce. If a user says "I want to figure out the best way to do X" that may entail some research prework and clear judgements tied back to evidence, or they may just be asking you to opine openly. Therefore, at this step, your goal is map their intention onto what they want agents to do or produce.

Tell the user up front that the goal of this skill is to create a document which captures the spirit of what they want, which they can hand off to an agent to begin more detailed planning.

Once the user feels your three descriptions capture their intent perfectly and the artifact is up to date, instruct the user on the location of the file, and tell them to hand it to a fresh agent using the /model-dataflow skill