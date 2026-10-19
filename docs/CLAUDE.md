# CLAUDE
!!FOR CLAUDE CODE TO READ!!



---



## What this project is
A rebuild of an existing Axon Ivy pizza shop, done the **low-code way** with the tools that ship with Axon Ivy 14. It is a "vibe-code" experiment.
The original, Java-heavy implementation is the **reference**, not a template. The goal is to meet the same requirements and then **compare** the two approaches.
- Requirements + roadmap: [docs/TODO.md](TODO.md) (tick the checkboxes when a feature is done)
- BPMN given by the customer: [docs/attachments/process.png](attachments/process.png)
- Original implementation: [docs/attachments/pizza-process.zip](attachments/pizza-process.zip). Extract it to the scratchpad to read it, never into the repo.
- Comparison notes: [docs/COMPARISON.md](COMPARISON.md) (write to it as you go, see below)
## User context
- The user is an apprentice. They know Camunda (open source) and are new to Axon Ivy. Explain Ivy concepts by mapping them to Camunda where that helps, and keep it short.
- Tooling: Axon Ivy **14**, **VS Code** designer (not the old Eclipse designer).
- Time box: about one week. Scope is smaller than the original.
## How we work
- **One feature at a time.** The user picks the next feature. Do not run ahead to the next one.
- Before building a feature, briefly say which Ivy built-in you will use and why. If you are unsure whether Ivy 14 can do something low-code, say so; don't guess.
- When a feature is done: tick it in TODO.md, add a short entry to COMPARISON.md, and tell the user how to test it (which start, which user).
- **No custom CSS / custom design.** Use the default Ivy / PrimeFaces / Portal look. This rules out the original's components, CSS, JS, and audio.
- Commit only when the user asks.
## Low-code ladder (stop at the first step that works)
1. **Configuration**: `config/*.yaml` (roles, users, variables, rest-clients, databases), CMS, Portal configuration.
2. **Process elements** in the process editor: User Task, Dialog Call, Alternative gateway, Task Switch Gateway, Signal / Trigger, Intermediate Event (timer), E-Mail, REST Client, Database, Sub-process, Call & Wait, etc. Use pools and lanes.
3. **Data classes** (`.d.json`), plus **Business Data** (`ivy.repo`) for persistence.
4. **Form Editor** dialogs (Form HTML dialogs), for all UI.
5. **Inline IvyScript** in element fields (mapping, conditions, short scripts). Keep each one to a few lines.
6. Hand-written JSF `.xhtml` tweaks inside a dialog, only if the Form Editor can't do it.
7. **Java**: last resort. Ask the user first, and log it in COMPARISON.md as a place where low-code fell short.
## Repo layout
- `main/`: **our project**. Everything we build goes here. Namespace `ch.frox.pizzaprocess` (same as the original).
- `portal/`, `smart-workflow-dashboard/`: imported market products. **Don't edit them.** The only exception is Portal configuration, if a feature needs it (ask first).
- `main/pom.xml` already depends on `smart-workflow` and `smart-workflow-anthropic` (AI agent element). AI is optional and out of scope unless the user asks for it.
## Ivy file-format notes (for editing files directly)
- Every DialogCall / UserTask whose flow continues **must** have the output mapping `"out" : "in"` (plus any result fields). Without it, the next element gets empty process data.
- **Data class fields: never write `"modifiers" : [ ]`.** An empty modifiers list makes the field *non-persistent* (generated as `transient`), so its value is lost at every task switch. Leave the `modifiers` key out: the default is persistent. Check the generated `.java` has no `transient`.
- Macros work in signal codes (boundary events). We link signals by business case: `pizza:delivered:<%=ivy.case.getBusinessCase().getId()%>`. Every task of one order shares the business case (Triggers attach to it).
- Files edited outside the Ivy editor may not be reloaded by the designer engine. After an external edit to a process, ask the user to open it in the process editor and save (Ctrl+S) before testing.
- Processes go in `main/process/ch/frox/pizzaprocess/*.p.json` (the user's convention). Process references (Trigger, Sub call) use the path relative to `process/`, e.g. `ch/frox/pizzaprocess/PizzaProcess:orderReceived(...)`. Element ids are `<processId>-f<n>` / `-S<n>`. Keep the ids unique and the connectors (`connect`) consistent. Keep the layout readable (pools/lanes with sensible `visual.at` coordinates).
- Data classes: `dataclass/**/*.d.json`. Dialogs: `dialog/**/` (`*.f.json` for forms, `*Data.d.json`, `*Process.p.json`).
- Ivy 14 config schema: `https://json-schema.axonivy.com/14.0/...` (the original used 15.1 headers in places, so don't copy those).
- Ivy generates Java for each data class into `main/target/generated-sources/ivy-dataclass/`, but only when its file watcher notices the file. Files written by Claude are sometimes missed, and then you get "type ... not found" errors. After writing `.d.json` files, `touch` them, check that the `.java` appears, and touch the dependent classes last (e.g. Order after Address/OrderItem).
- After generating or changing files, ask the user to open them in the Ivy editor and check that they render and validate. Use the VS Code diagnostics tool when it's available.
## Comparison log (docs/COMPARISON.md)
For each feature, write one short entry:
- how the original did it (classes or files)
- how we did it (which Ivy built-in)
- the effort, and what was easier or harder
- any limit we hit (where we needed IvyScript, xhtml, or Java)
This log is the main deliverable for the user's Berufsbildner, so keep it honest.
