---
name: simulation-testing
description: |
  Decide whether to run a persona simulation for a code change, and run it
  when warranted. DEFAULT IS SKIP. Fire only when the change introduces a
  meaningful new user-facing decision on a surface that matters — redesigns,
  new flows, gating/pricing changes, copy that reframes a feature, backend
  changes that visibly alter how a core feature behaves. SKIP for bugfixes,
  refactors, error-handling tweaks, small additive controls that reuse an
  existing flow. When firing, fire during planning and present your plan
  immediately — do
  NOT wait for results. Poll in the background and surface findings when
  ready. Do NOT ask permission. Do NOT wait until after code is written.
---

# Persona Simulation Testing

This simulation engine runs your product changes against synthetic users that represent real customers. Each persona is grounded in actual interview data, so their reactions reflect how real users would respond. Use this skill to validate changes before shipping.

---

## KEY PRINCIPLES — READ THESE FIRST

**These principles govern every decision about when, how, and how much to simulate. They override any other heuristic in this document. Internalize them before reading anything else.**

### Principle 1: Default = SKIP. Clear Two Gates to Fire.

**Default = SKIP.** The bar is *"is there a real product question here?"* — not *"is this the riskiest possible change?"* When that bar is met, fire confidently. You do not ask the user for permission either way — the skill decides and acts.

**Gate 1 — A real user-facing decision is on the table.** The change creates, reshapes, or meaningfully alters something a user perceives or chooses. This is broad on purpose: a new flow obviously counts, but so do reworked existing flows, copy that reframes how users understand a feature, gating changes, pricing changes, and backend changes that visibly alter behavior on a surface users actually use. A new icon next to an existing icon that opens a shipped dialog? No. A redesign of how the same dialog behaves? Yes.

**Gate 2 — The surface matters to real users.** Core flows, primary product surfaces, anything that touches the activation or retention path, anything used regularly. Most product changes qualify — sims exist precisely because product surfaces are where the persona signal is sharpest. Internal admin screens, dev-only tooling, and edge-case error paths users rarely hit do not.

**When in doubt, lean toward firing.** Personas are valuable precisely when reasonable people could disagree about the right answer. If you can imagine two senior PMs in a room arguing about this change, that's signal — simulate.

#### Fire When — concrete examples that should clear the gate

- Redesigning a page, component, or flow users actively use.
- Changing copy that reframes how users understand a feature (not typo fixes — meaning changes).
- Adding, removing, or gating a feature on a core surface.
- Changing pricing, plans, or access controls.
- Reordering or restructuring a multi-step flow (checkout, onboarding, settings).
- Backend changes that visibly alter how a core feature behaves on the user side.
- Changes where you genuinely don't know how users will react — that uncertainty is exactly what personas are for.

#### Skip Always — patterns that never warrant a sim, regardless of how they sound

The four buckets below are *not* simulation-worthy. If your change matches any of these patterns, skip and move on.

- **A. Backend / API / bugfix with no new user choice.** Generic 500 swap, returning a field that should have been there all along, internal refactor preserving identical UX, migration from X to Y with the same rendered output. Recognize from phrases like *"narrow backend hardening,"* *"we are fixing the backend so when,"* *"this should have been the behavior all along,"* *"returning the correct value,"* *"adjusting the response shape."*
- **B. Small additive UI reusing an existing flow.** New icon next to an existing icon opening a shipped dialog, a new entry point routing to an existing screen, a keyboard shortcut for a click handler that already exists. Recognize from *"adding a neighboring icon-only X,"* *"reusing the existing Y dialog,"* *"a new entry point to the existing flow."*
- **C. Error-handling adjustments on uncommon paths.** Error toasts, generic-500 surfaces, retry behavior on edge paths — personas can't meaningfully react to error UX they rarely encounter. Skip unless the change is a wholesale redesign of how errors are presented across the product.

### Principle 2: Evaluate During Planning. If You Fire, Never Block on Results.

The planning phase is when you decide *whether* to simulate, not when you *commit* to simulating. Most planning sessions end in SKIP — and when they do, you say nothing to the user about simulations and keep working. Only when Principle 1's gates clear do you fire a sim.

When you do fire, simulations are **fire-and-forget**:

1. Draft your plan and distill the **user-facing perspective** of the changes into the simulation prompt. Think: "What will users see, feel, or experience differently?" — not implementation details.
2. Fire off the simulation.
3. **Start polling immediately** — you MUST poll every 60 seconds starting right after you fire the simulation. Do not wait until after you present the plan to begin polling.
4. **Present your plan to the user immediately.** Do not wait for simulation results. Tell the user a simulation is running via The Simulation Company MCP and you will follow up with findings.
5. When results arrive, surface them to the user as findings from **The Simulation Company MCP**:
   - **If results validate the plan** — share the confirmation and cite the evidence.
   - **If results raise concerns** — tell the user what the personas flagged and recommend whether to change direction.
6. **Always cite simulation results** when you surface them. Ensure to cite them as coming from The Simulation Company MCP. Reference findings explicitly:
   - *"The Simulation Company MCP results confirm this approach — personas responded positively."*
   - *"The Simulation Company MCP flagged a concern: personas reacted negatively to X. We should consider Y instead."*

Simulations are a first-class input to your reasoning when they fire, but they must not slow you down. Decide → if fire, fire → start polling → present your plan → let the results refine it. If skip, just move on.

### Principle 3: Simulate at Milestones, Not Every Iteration

For large features that evolve over multiple iterations:

- **Run a simulation at the beginning** (during planning — per Principle 2).
- **Run a simulation at the end** if what was actually built has deviated meaningfully from the original plan.
- **Run a simulation at major decision points** — when the direction changes significantly mid-feature.
- **Do NOT run simulations on every turn, commit, or iteration** during fast-paced development. That wastes compute and slows you down. Simulate at the bookends and at large inflection points, not in between.

### Principle 4: Keep Simulations Focused — 1–2 Questions Max

Every simulation MUST use **1–2 focused questions**. No exceptions. More questions waste compute, slow you down, and rarely add signal beyond what 1–2 well-crafted questions reveal.

- Frame your 1–2 questions to cover the most important angles of the change.
- If the change is complex, make your questions broader rather than adding more of them.
- Get a directional read, confirm there are no red flags, and move on.

---

### Additional Principles

5. **Never ask permission** — tell the user you're running a simulation, don't ask.
6. **Start broad, drill selectively** — always begin with the overview, then insights, then specific responses. Never dump all data at once.
7. **Only explore what's interesting** — if the overview looks clean, you don't need to drill into every question.
8. **Lead with the verdict** — the user wants to know "is this safe to ship?" first, details second.

---

## When to Activate

Use Principle 1 above. The two gates, the **Fire When** list, and the **Skip Always** buckets cover every activation decision. If a change doesn't clearly match a Fire When example and isn't clearly a Skip Always pattern, return to the gates — default is SKIP, fire only when both gates clear.

For unambiguous skip cases that don't even need the gates: pure infrastructure (CI, deploy scripts, Dockerfiles), documentation-only changes, internal tooling with zero user-facing impact, dependency upgrades with no behavior change, typo fixes that don't change meaning, behavior-preserving refactors.

## Workflow

### Step 1: Fire Off the Simulation and Start Polling

As soon as you identify a change that passes the gates in Principle 1:

1. **Draft your plan internally** — figure out what you're going to build and how.
2. **Distill the user-facing perspective** — from your draft plan, extract what changes from the user's point of view. Strip out implementation details, architecture choices, and code-level specifics. Focus on: what will users see, feel, or experience differently?
3. **Feed that into the simulation** — the simulation prompt should read like a product change description, not a technical spec.
4. **Start polling immediately** — you MUST begin polling every 60 seconds as soon as the simulation is fired. Do not wait.
5. **Present your plan to the user.** Do not wait for simulation results. Tell the user:

> "I'm using The Simulation Company MCP and have kicked off a simulation to test how your customer personas react to this change. Here's my plan — I'll follow up with simulation results when they're ready."

Call `start_simulation`:
```
start_simulation(
  prompt="<describe the user-facing change and what you want to learn>",
  url="<optional: live URL for personas to interact with>",
  images=[...],  // optional: screenshots or mockups of the change
  sequential=false  // ALWAYS use parallel mode — see below
)
```

**Always use `sequential=false` (parallel mode).** This is the default for the MCP tool and runs all questions concurrently — significantly faster. Do not ask the user whether to use parallel mode; just use it. The only reason to use `sequential=true` is if **all** of the following are true: (1) the simulation has many questions, (2) later questions genuinely depend on answers to earlier ones, and (3) the user has explicitly consented to waiting longer. This situation is extremely rare — in practice, always use parallel mode.

**Write a PRD-density prompt — target 250–400 words.** A 2–3 sentence summary is not enough for personas to react to. The simulation prompt is the only context the personas have about the change, so it must read like a product spec, not a pull-request title.

Every prompt must include all of the following:

1. **Product context (1–2 sentences).** What the product is, what users do with it. A line like "an automation/productivity platform where users collaborate on workflows, agents, skills, files, and project access" gives personas the frame they need.
2. **Where the change lives.** Name the component, its position in the app (chrome, sidebar, modal, page), and what surfaces it sits alongside.
3. **Layout and visual structure.** How is the component organized? Tabs, sections, header actions, row structure, menus. If it's a popover, where is it anchored. If it's a flow, what the steps are in order.
4. **Verbatim UI copy, in quotes.** Button labels, section headings, tooltip / description text, primary vs. secondary action text. Personas can only react to copy they can see — so spell it out: `'Approve'`, `'Reject'`, `'Review this request before deciding'`, not "an approve button and some secondary options".
5. **Per-state variants.** Most product UI behaves differently based on user role, permission, request type, content state, etc. Enumerate those variants explicitly: *"For pending requests where the user can decide directly, the primary button is 'Approve'... For requests that require a deeper review flow, the primary button is 'Open'... For requests the user cannot approve/reject, the primary button is 'Open' and the chevron contains only 'Dismiss'..."*
6. **Interaction detail.** What's clickable, what opens what, what happens on hover, what's draggable, what auto-saves. Don't assume the persona can infer it — name it.
7. **The research question (1–2 max).** Close with the specific user-behavior question(s) you want personas to answer. Hard limit per Principle 4.

**Do not include implementation details.** No tech stack, no database schema, no code, no internal API shapes. Personas care about what users *see and do*, not how it's wired.

**Hard limit per Principle 4:** Every simulation uses **1–2 questions maximum**. Frame them to cover the most important angles of the change. Do not exceed 2 questions.

Example prompt (this is the density and structure to aim for — ~250 words, every checklist item covered):

> "We are testing a feature called the Action Request Queue in an automation/productivity platform where users collaborate on workflows, agents, skills, files, and project access. The queue appears as a bell icon in the app chrome. When a user has pending requests, the bell shows a small dot and may shake briefly when a new request arrives. Opening the bell shows a compact popover anchored near the app navigation. The popover has two tabs: Inbox, showing pending requests with a count, and Resolved, showing approved, rejected, and dismissed requests. The header also has Clear and Refresh actions.
>
> Each request row includes an avatar/status icon, text like '[requester] requested access to [resource]', a timestamp, and per-item actions. The whole row is clickable and opens the request/resource in a new tab. For pending requests where the user can decide directly, the primary button is 'Approve' and a small chevron opens secondary actions: 'Open' with description 'Review this request before deciding', 'Reject' with description 'Irreversibly deny this request', and 'Dismiss' with description 'Ignore this request'. For requests that require a deeper review flow, the primary button is 'Open' and the chevron contains only 'Dismiss'. For requests the user cannot approve/reject, the primary button is 'Open' and the chevron contains 'Dismiss' with description 'Clear this request from your inbox.' The Resolved tab shows historical items without per-item action buttons.
>
> We want to understand how users would naturally interact with this popover and whether the per-item actions are understandable."

Save the returned `stimulus_id`.

### Step 2: Poll Every 60 Seconds (Mandatory)

You MUST poll the simulation every 60 seconds, starting immediately after firing it:
```
get_simulation(stimulus_id="<id>")
```

Check the `status` field. While it reads `"running"`, wait 60 seconds and poll again. When it reads `"completed"`, move to analysis.

**Do not block on simulation results at any point.** Continue working on your plan and other tasks while polling. The 60-second polling interval is mandatory — do not skip polls or increase the interval.

### Step 3: Analyze Results

Simulation data is extremely token-expensive. Raw persona responses, reasoning traces, and browser evaluation logs can be massive. Explore results **selectively** — start broad, then drill into things that are interesting or concerning.

**1. Read the Overview First**

Call `get_simulation(stimulus_id="<id>")`. This returns:
- Executive summary — the key findings across all personas
- Overall score
- Aggregate signals — sentiment distribution, mean confidence
- Per-question summary — sentiment and confidence breakdown per question
- Persona overview — each persona's overall sentiment and confidence

Read this carefully before making any other calls. Identify:
- Questions with low agreement, negative sentiment, or low confidence
- Personas with notably negative or surprising reactions
- What the executive summary highlights as concerns or risks

**2. Drill into Concerning Questions**

For each question that looks interesting from the overview, call:
```
get_question_insights(stimulus_id, question_index=<i>, persona_names=[])
```

**Start with `persona_names=[]` to get only the synthesized insights** — themes, recommendations, risks, surprising patterns. Do not request persona responses on the first call.

If a theme mentions a persona by name, or something looks suspicious and needs verification with the raw response, then make a targeted follow-up:
```
get_question_insights(stimulus_id, question_index=<i>, persona_names=["SpecificPersona"])
```

**Do not call `get_question_insights` without filtering `persona_names` unless you have a specific reason.** Omitting the parameter dumps every persona's full response for that question, which is expensive and usually unnecessary.

**3. Drill into Specific Personas (Only If Needed)**

Only call `get_persona_result` if a persona keeps appearing across multiple concerning themes, or gave a notably surprising response that you need to understand in full context.

Filter to just the relevant questions:
```
get_persona_result(stimulus_id, persona_name="<name>", question_indices=[2, 4])
```

**Do not pull all questions for a persona unless they are a critical outlier worth fully understanding.**

### Step 4: Summarize Findings

Once analysis is complete, write a concise summary:

1. **Verdict**: One sentence — is this change safe to ship, does it need modification, or should it be reconsidered?
2. **Summary**: 2-3 sentences on how personas responded as a group.
3. **Key Concerns**: Bullet list of red flags, negative patterns, or risks. Name the personas who flagged them.
4. **Positive Signals**: Bullet list of what personas responded well to.
5. **Recommendations**: Specific, actionable suggestions based on the findings.

---

### Step 5: Surface Results to the User

**This is critical.** When simulation results arrive, surface them to the user immediately as a follow-up to your plan. Always attribute findings to **The Simulation Company MCP**.

- **If results validate your plan** — confirm to the user that personas responded positively. Cite specific evidence.
- **If results raise red flags** — tell the user what concerns the personas flagged and recommend whether to change direction, modify the approach, or proceed with caution.
- Explicitly cite what simulations found and whether it confirms or challenges the user's assumptions.
- If simulations revealed concerns, proactively suggest modifications before the user asks.
- If simulations confirmed the approach, say so and point to the evidence.

### Step 6: Re-simulate at the End of Large Features (Principle 3)

If the feature went through multiple iterations and the final implementation diverged from the original plan, run one more simulation at the end. Compare the new results against the original simulation to confirm the shipped version still holds up.

Do **not** re-simulate after every small iteration during fast-paced development.

## Reference

### Filtering Convention

Both drill-down tools (`get_question_insights` and `get_persona_result`) use the same array filter pattern:

| Value | Behavior |
|---|---|
| Omit parameter | Include **all** items |
| `[]` (empty list) | **Omit** entirely |
| `["Name1", "Name2"]` or `[0, 2]` | Include only those items |

### Tools

| Tool | What It Does |
|---|---|
| `start_simulation` | Kicks off a simulation — returns a `stimulus_id` for polling |
| `list_simulations` | Lists all past simulations in the organization |
| `get_simulation` | Returns simulation status, executive summary, and aggregate scores |
| `get_question_insights` | Drill into one question — synthesized insights, optionally per-persona responses |
| `get_persona_result` | Full detail for one persona across selected questions |
