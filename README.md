# Codex C.R.E.W. (Codex Role-based Efficient Workflow)

## A role-based multi-model workflow for Codex, built to get more done with less burn.

A Codex workflow that delegates work to specialised subagents, matching models and reasoning levels to each task to improve speed and reduce token usage. Sol leads the work and checks the results; Terra handles bounded implementation and visual checks, Luna extracts targeted information, and Astra contributes strategic thinking when needed.

I created it to reduce overall token burn and make subagents faster and more useful. I wanted clear roles and sensible model choices built into the workflow, so I could focus on what I was making instead of deciding which model should handle each piece. Small tasks can stay with the Lead, independent tool calls can run together, and delegation is reserved for work that benefits from it or requires independent checking.

So far, it seems to be working well for me: I'm getting a lot more work done and creating more, without needing to use **OpenAI’s generously frequent resets** as often. That's my experience, not a controlled benchmark or a guarantee 😁. I'm sharing the setup prompt so others can inspect it, adapt it and see whether it helps their own workflow. Your mileage may vary.

**Use at your own risk.** CREW is an install prompt not a skill. The install prompt changes global Codex settings and instructions. Review the prompt before installing it, keep the backups, and check that the resulting behaviour suits your projects and account. **This is only useful if you are a heavy user of Codex and subagents.**

## What it does

- Configures **Sol/high** as the primary Lead and generic-worker fallback, with nine native role definitions. Exceptional architecture or causal debugging can use **xhigh**.
- Routes narrow extraction to **Luna/low** and bounded implementation to **Terra/medium**, using fresh, self-contained briefs. Higher-risk implementation uses **Sol/high** with a fresh adversarial Verifier. **Astra/medium** provides selective strategic consultation; an optional **Terra/medium** visual-QA role handles specified Figma edits and scripted UI checks. Sol retains coordination and sign-off.
- Adds one `product-ui-design` skill: frame a single user job and concrete output, simplify the interface, and check essential states and accessibility.
- Preserves unrelated settings and safeguards, requires explicit approval for software acquisition, and specifies private backups and selective rollback. The workflow allows at most three concurrent workers.

## Root defaults and worker presets

These are the presets specified by the **current version of the setup prompt**. They describe the intended installation, not a guarantee of availability in every account or client.

| Session or role                        | Model           | Reasoning | Purpose                                                                                                                                                              |
|----------------------------------------|-----------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Primary session and `dev_lead`         | `gpt-6.1-sol`   | `high`    | Lead the work, coordinate workers, integrate results and decide completion. Use `xhigh` only for exceptional architecture, causal debugging or dependent invariants. |
| `dev_builder_fast`                     | `gpt-6.1-terra` | `medium`  | Bounded implementation, semantic fixes, UI edits and documentation, with a focused self-check.                                                                       |
| `dev_builder`                          | `gpt-6.1-sol`   | `high`    | Implementation involving uncertainty, shared contracts, architecture, security or other FULL-path risks.                                                             |
| `dev_designer`                         | `gpt-6.1-sol`   | `high`    | Frame the user job and design the interface, interactions and accessibility.                                                                                         |
| `dev_investigator`                     | `gpt-6.1-sol`   | `high`    | Research and diagnose unresolved questions, separating evidence from inference.                                                                                      |
| `dev_verifier`                         | `gpt-6.1-sol`   | `high`    | Fresh, independent adversarial review of correctness, security and acceptance criteria.                                                                              |
| `dev_utility`                          | `gpt-6-luna`    | `low`     | Targeted extraction, exact lookups and straightforward mechanical work.                                                                                              |
| `dev_lead_astra` — optional Strategist | `gpt-6-astra`   | `medium`  | Bounded strategic decisions in product, creative direction, business or architecture; no ongoing coordination.                                                       |
| `dev_visual_qa` — optional visual QA   | `gpt-6.1-terra` | `medium`  | Specified Figma edits and scripted UI checks; Sol retains design judgment and sign-off.                                                                              |
| Generic subagent fallback              | `gpt-6.1-sol`   | `high`    | Default for workers without a more specific model or reasoning override.                                                                                             |

The primary normally performs the Lead role; `dev_lead` is a matching template, not an instruction to launch a second coordinator. Both optional roles are installed for selective use. There are nine named roles, not nine workers running at once: the limit is **three concurrent workers**, excluding the primary, with at most one Astra worker. The prompt requests **Standard speed** through client-supported settings.

Root defaults govern sessions that inherit them. Named workers have their own model and reasoning pins, so changing the primary does **not** automatically update those workers or the generic fallback. Explicit chat, project, profile or managed overrides may take precedence. Reasoning effort is a requested setting, not a fixed token allowance or a speed guarantee.

Delegation is selective: small tasks can stay direct, independent safe tool calls can run together, and substantial extraction can go to Luna to keep source material out of the Lead's context. Efficiency does not remove required independent review or visual checks.

## How to use it

1. **Start a *new* Codex chat** and select **GPT-6.1 Sol with high reasoning** for the review and installation. This is my recommended starting point for inspecting configuration, preserving existing settings and checking the merge; it is not a benchmarked requirement. This choice is for the installation chat; the prompt still specifies its own model presets for the installed workflow. **Attach** the `CREW_setup_prompt.md` to the chat and ask: ***“Review this against my installation and recommend any compatibility changes. Don’t install it yet.”***
2. Review the proposed changes. When ready, explicitly authorise installation: ***“Apply this workflow, preserve unrelated settings, run the bounded validation, and report what is verified or still unverified.”*** The prompt directs Codex to inspect the actual clients, resolve paths and supported settings, back up affected files, and merge changes.
3. Read the installation report, retain the rollback information, and start a fresh chat. Existing chats and deliberate model, profile or project overrides may keep different settings.

Validation has one combined ceiling across clients and retries: **12 minutes, seven worker launches, three concurrent workers and one corrective retry**. The worker cap excludes the primary and permits at most one Astra worker. Downloads, upgrades and live external edits require separate approval.

## Updating settings *after* installation

Ask Codex to make a scoped update against the **actual installation**, rather than rerunning an older setup prompt that could restore old presets. Specify which settings should change: the primary, named roles and generic fallback are separate choices. New model releases do not automatically update this workflow.

For example, replace the placeholders and ask:

> Review changing the primary session, `dev_lead` and generic subagent fallback to `[model]` / `[reasoning]`. Keep all other roles and settings unchanged. Inspect the effective Codex home, client support and overrides, and show the proposed changes. Don't apply them yet.

The review should locate the effective `config.toml` for root and fallback settings, the named custom-agent files (normally `agents/<role>.toml`), and matching instructions in managed `AGENTS.md` blocks, any relevant global override and skill references. Changing a pin without updating instructions that repeat it can leave contradictory guidance. Existing manifests should identify managed files and any renamed roles.

Once the proposal is right, authorise the update:

> Apply the reviewed changes. Back up affected files, preserve unrelated settings and safeguards, and keep model pins and managed instructions consistent. Validate client support and run checks relevant to the changed settings within the existing combined test budget. Report configuration checks separately from observed runtime results, identify overrides and remaining limits, and retain selective rollback instructions. Do not download or upgrade anything.

**Start a fresh chat afterward, restarting the client if required by the report.** Verify the actual primary and any changed workers rather than relying on their self-reported identities. If independent review is required but unavailable, it remains unverified. If you want the changes to be reproducible elsewhere, separately request a new version of the setup prompt and update this table; editing documentation alone does not change an installation.

## Limitations

The specified model identifiers, reasoning levels, role discovery, tools and settings may not be available in your account or clients. Inspect compatibility before activation; do not silently substitute models. Desktop and CLI verification are reported separately, and static checks do not prove runtime behavior. Root defaults affect every session that inherits them, including noncoding work.

Routing rules guide delegation; they do not guarantee delegation, token savings or future compliance. Direct reads remain available, with no blocking hook. Roles inherit permissions rather than providing separate security sandboxes; approval instructions are not technical enforcement. Bounded smoke tests do not establish general security or human usability.
