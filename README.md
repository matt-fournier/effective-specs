# The Practitioner's Guide to Writing Effective Specs for AI Coding Agents

> A framework for structuring, scoping, and iterating on specifications that keep AI agents focused, productive, and aligned with your intent.

**Based on:** [Addy Osmani, "How to Write a Good Spec for AI Agents" (O'Reilly Radar, Feb 2026)](https://www.oreilly.com/radar/how-to-write-a-good-spec-for-ai-agents/) · GitHub's analysis of 2,500+ agent configuration files · Simon Willison's "Vibe Engineering" · Anthropic's context engineering guidelines.

---

## Table of Contents

- [Executive Summary](#executive-summary)
- [Principle 1: Start with Vision, Let the AI Draft the Details](#principle-1-start-with-vision-let-the-ai-draft-the-details)
- [Principle 2: Structure the Spec Like a Professional Document](#principle-2-structure-the-spec-like-a-professional-document)
- [Principle 3: Break Tasks into Modular Prompts, Not One Giant Prompt](#principle-3-break-tasks-into-modular-prompts-not-one-giant-prompt)
- [Principle 4: Build in Self-Checks, Constraints, and Human Expertise](#principle-4-build-in-self-checks-constraints-and-human-expertise)
- [Principle 5: Test, Iterate, and Evolve the Spec](#principle-5-test-iterate-and-evolve-the-spec)
- [Common Antipatterns to Avoid](#common-antipatterns-to-avoid)
- [Quick-Start Spec Template](#quick-start-spec-template)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Executive Summary

Simply throwing a massive specification at an AI agent doesn't work. Context window limits and the model's attention budget get in the way. The key is to write **smart specs**: documents that guide the agent clearly, stay within practical context sizes, and evolve with the project.

This guide distills five core principles from industry practice—drawn from practitioners like Addy Osmani and Simon Willison, and backed by GitHub's study of over 2,500 agent configuration files—into an actionable framework you can apply immediately.

> **TL;DR:** Aim for a clear spec covering just enough nuance (structure, style, testing, boundaries) to guide the AI without overwhelming it. Break large tasks into smaller ones. Plan first in read-only mode, then execute and iterate continuously.

---

## Principle 1: Start with Vision, Let the AI Draft the Details

**Kick off your project with a concise high-level spec, then have the AI expand it into a detailed plan.**

Instead of overengineering upfront, begin with a clear goal statement and a few core requirements. Treat this as a "product brief" and let the agent generate a more elaborate spec from it. This leverages the AI's strength in elaboration while you maintain control of direction.

### Why This Works

LLM-based agents excel at fleshing out details when given a solid high-level directive, but they need a clear mission to avoid drifting off course. By providing a short outline and asking the AI to produce a full specification, you create a persistent reference—a shared source of truth that both you and the agent can consult.

### The Practical Workflow

1. **Write a high-level brief.** Focus on *what* and *why*, not the nitty-gritty *how*. Think user stories and acceptance criteria: Who is the user? What do they need? What does success look like?

2. **Prompt the agent to draft a detailed spec.** Ask it to produce an overview, feature list, tech stack suggestions, data model, and implementation plan.

3. **Review and refine.** Correct hallucinations, align with your vision, and save as `SPEC.md`. This file persists between sessions, anchoring the AI whenever work resumes.

### Use Plan Mode

Tools like Claude Code offer a **Plan Mode** (`Shift+Tab`) that restricts the agent to read-only operations—it can analyze your codebase and create detailed plans but won't write any code until you're ready. Start here:

- Describe what you want to build
- Let the agent draft a spec while exploring your existing code
- Ask it to clarify ambiguities by questioning you about the plan
- Have it review for architecture, best practices, security risks, and testing strategy
- Refine until there's no room for misinterpretation
- **Only then** exit Plan Mode and let the agent execute

### Keep It Goal-Oriented

A high-level spec for an AI agent should focus on *what* and *why* more than the nitty-gritty *how* (at least initially). As one engineer observed, writing good documentation first means the model may be able to build the matching implementation from that input alone. The spec is that documentation.

---

## Principle 2: Structure the Spec Like a Professional Document

**Treat your AI spec as a structured document (PRD) with clear sections, not a loose pile of notes.**

A formal, well-organized spec gives the agent a blueprint to follow and reduces ambiguity. AI models handle well-structured text better than free-form prose.

### The Six Core Areas Every Spec Must Cover

GitHub's analysis of over 2,500 agent configuration files revealed a clear pattern. The most effective specs cover six areas:

| Area | What to Include | Examples |
|------|----------------|----------|
| **1. Commands** | Full executable commands with flags, placed early in the spec | `npm test`, `pytest -v`, `npm run build` |
| **2. Testing** | Framework, file locations, coverage expectations, how to run | Jest in `tests/`, 80% coverage minimum |
| **3. Project Structure** | Where source, tests, docs, and configs live | `src/` for code, `tests/` for tests, `docs/` for docs |
| **4. Code Style** | Real code snippets showing your conventions | Naming, formatting, one example of good output |
| **5. Git Workflow** | Branch naming, commit format, PR requirements | `feat/*`, `fix/*`, conventional commits |
| **6. Boundaries** | Three-tier system: always do, ask first, never do | "Never commit secrets" (most common constraint) |

### Use a Consistent Format

```markdown
# Project Spec: My Team's Tasks App

## Objective
- Build a web app for small teams to manage tasks...

## Tech Stack
- React 18+, TypeScript, Vite, Tailwind CSS
- Node.js/Express backend, PostgreSQL, Prisma ORM

## Commands
- Build: `npm run build` (compiles TypeScript, outputs to dist/)
- Test: `npm test` (runs Jest, must pass before commits)
- Lint: `npm run lint --fix` (auto-fixes ESLint errors)

## Project Structure
- `src/` – Application source code
- `tests/` – Unit and integration tests
- `docs/` – Documentation

## Boundaries
- ✅ Always: Run tests before commits, follow naming conventions
- ⚠️ Ask first: Database schema changes, adding dependencies
- 🚫 Never: Commit secrets, edit node_modules/, modify CI config
```

### The Four-Phase Gated Workflow

Rather than writing a spec and setting it aside, integrate it into a gated workflow where each phase must be validated before proceeding:

| Phase | What Happens | Your Role |
|-------|-------------|-----------|
| **1. Specify** | Define what you're building and why. Focus on user journeys and success criteria. | You describe; the agent generates a detailed spec. |
| **2. Plan** | Get technical. Define stack, architecture, constraints, compliance. | You set constraints; the agent produces a technical plan. |
| **3. Task Breakdown** | Break the plan into small, reviewable, testable chunks. | The agent decomposes work into isolated, implementable tasks. |
| **4. Implement** | Execute tasks one by one. Review focused changes, not massive diffs. | The agent codes; you verify at each checkpoint. |

**The critical insight:** Your primary role is to steer. The coding agent does the bulk of the writing. Each phase has a specific job, and you don't move to the next one until the current task is fully validated. This prevents what Willison calls "house of cards code"—fragile AI outputs that collapse under scrutiny.

### Additional Structural Tips

- **Be specific about your stack.** Say "React 18 with TypeScript, Vite, and Tailwind CSS," not "React project." Include versions and key dependencies.
- **Make the spec a living document.** Update it as you and the agent make decisions. Version-control it alongside your code.
- **Consider `agents.md` for specialized personas.** Define `@docs-agent` for technical writing, `@test-agent` for QA, `@security-agent` for code review—each with its own focused spec.
- **Design for Agent Experience (AX).** Clean, parseable formats: OpenAPI schemas, `llms.txt` files, explicit type definitions.

---

## Principle 3: Break Tasks into Modular Prompts, Not One Giant Prompt

**Divide and conquer: give the AI one focused task at a time rather than a monolithic prompt with everything at once.**

Trying to stuff the entire project into a single prompt is a recipe for confusion. You risk hitting token limits *and* the model losing focus due to the **"curse of instructions"**—too many directives causing it to follow none of them well.

### The Curse of Too Much Context

Research confirms that as you pile on more instructions into the prompt, the model's adherence to each one drops significantly. Even top models struggle when asked to satisfy many requirements simultaneously. The solution: **decompose complex requirements into sequential, simple instructions.** Focus the AI on one subproblem at a time, get that done, then move on.

### Strategies for Modular Context

#### Divide the Spec into Components

Split your spec into parts—backend API spec, frontend UI spec, database schema spec. Feed only the relevant section to the agent for each task. Don't mix authentication tasks with database schema changes in one go.

#### Build an Extended Table of Contents

Have the agent create a summary that condenses each spec section into a few key points. This acts as an index the agent can consult, while fine details remain offloaded until needed. The agent carries a "mental map" of the spec without consuming its entire context window.

#### Use Subagents for Different Domains

Specialized agents—each configured for a specific area of expertise and given only the relevant portion of the spec—boost accuracy and allow parallel work on independent tasks.

#### Use In-Line Directives and Code TODOs

Scaffold your code with `// TODO` comments that describe what needs to be done, and have the agent fill them one by one. Each TODO acts as a mini-spec for a small task.

### Single Agent vs. Multi-Agent Approaches

| | Single Agent | Multi-Agent |
|---|---|---|
| **Strengths** | Simpler setup; easier to debug; lower overhead | Higher throughput; domain specialists; handles complex interdependencies |
| **Challenges** | Context overload on big projects; single point of failure | Coordination overhead; potential conflicts; needs shared memory |
| **Best For** | Isolated modules; small-to-medium projects; early prototyping | Large codebases; parallel code + test + review; independent features |
| **Tips** | Use spec summaries; refresh context per task; start fresh sessions | Limit to 2–3 agents initially; define clear boundaries; use MCP for tool sharing |

> **The bottom line:** Small, focused context beats one giant prompt. Provide one task focus and relevant info only. A well-fed AI is like a well-fed function: give it only the inputs it needs for the job at hand.

---

## Principle 4: Build in Self-Checks, Constraints, and Human Expertise

**Make your spec not just a to-do list but also a guide for quality control—and don't be afraid to inject your own expertise.**

A good spec anticipates where the AI might go wrong and sets up guardrails. Think of the spec as both coach and referee.

### The Three-Tier Boundary System

The most effective specs use a three-tier boundary system rather than a simple list of don'ts:

| | Tier | Description | Examples |
|---|---|---|---|
| ✅ | **Always Do** | Actions the agent should take without asking. | Run tests before commits; follow naming conventions; log errors to monitoring. |
| ⚠️ | **Ask First** | Actions that require human approval before proceeding. | Modifying database schemas; adding new dependencies; changing CI/CD config. |
| 🚫 | **Never Do** | Hard stops. The agent must not perform these under any circumstances. | Commit secrets or API keys; edit vendor directories; remove failing tests. |

### Verification Strategies

**Self-Verification.** Instruct the AI to verify its work against the spec automatically. Append prompts like: *"After implementing, compare the result with the spec and confirm all requirements are met. List any spec items that are not addressed."*

**LLM-as-a-Judge.** For subjective criteria—code style, readability, architectural adherence—use a second agent to review the first agent's output against your quality guidelines. This adds semantic evaluation beyond syntax checks.

**Conformance Testing.** Build language-independent test suites (often YAML-based) that any implementation must pass. These act as a contract derived directly from the spec.

**Leverage Testing in the Spec.** Incorporate sample inputs and expected outputs directly. A robust test suite gives agents superpowers: they can validate and iterate quickly when tests fail.

### Inject Your Domain Knowledge

- State relationships explicitly (e.g., "products and categories have a many-to-many relationship").
- Mention library-specific pitfalls and workarounds you've discovered.
- Encode style preferences: "use functional components over class components in React."
- Include small examples that anchor the AI to the exact format you want.

**Match spec detail to task complexity.** Don't underspec a hard problem (the agent will flail), but don't overspec a trivial one (the agent will get tangled in unnecessary instructions).

---

## Principle 5: Test, Iterate, and Evolve the Spec

**Think of spec writing and agent building as an iterative loop: test early, gather feedback, refine the spec, and leverage tools to automate checks.**

The initial spec is not the end—it's the beginning of a cycle.

### The Continuous Improvement Loop

1. **Test after each milestone.** Don't wait until the end. Run tests or do quick manual checks after each major function. If something fails, update the spec or prompt before proceeding.

2. **Iterate on the spec itself.** When you discover the spec was incomplete or unclear, update it. Then explicitly resync the agent: *"I have updated the spec as follows… adjust accordingly."*

3. **Use context management tools.** RAG, MCP (Model Context Protocol), and vector databases can auto-fetch relevant chunks of your spec on the fly, rather than requiring you to provide the whole thing every time.

4. **Version-control the spec.** Commit it to your repo. Use `git diff` and `blame` to track evolution. Treat the spec like code: maintain it diligently.

5. **Monitor and log agent actions.** Review trace logs and chain-of-thought outputs. If something goes wrong, trace it back to spec ambiguity and fix the root cause.

### Optimize for Cost and Speed

Use model selection strategically. Use a cheaper/faster model for initial drafts and repetitive tasks, and reserve the most capable model for planning and complex reasoning. Not every subagent needs to be top-tier; a test-running or linting agent could be a smaller model. Don't feed 20K tokens if 5K will do—more tokens can mean diminishing returns.

---

## Common Antipatterns to Avoid

| Antipattern | Why It Fails | What to Do Instead |
|---|---|---|
| **Vague prompts** | "Build me something cool" gives the agent nothing to anchor on. | Be specific about inputs, outputs, and constraints. Define the agent's persona and responsibilities. |
| **Overlong contexts** | Dumping 50 pages into a prompt overwhelms the model. Context length ≠ context quality. | Use hierarchical summaries or RAG to surface only what's relevant. |
| **Skipping human review** | Passing tests ≠ correct, secure, or maintainable code. | Always review critical code paths. AI code can look solid but collapse under untested edge cases. |
| **Prototyping = Production** | Rapid AI prototyping is great for exploration, not for shipping. | Know which mode you're in. Apply full rigor before production. |
| **Ignoring the "lethal trifecta"** | Speed (outpacing review) + nondeterminism (varying outputs) + low cost (encouraging shortcuts). | Build verification into the process. Your review cadence must match the agent's output speed. |
| **Missing core areas** | Incomplete specs leave the agent guessing on critical decisions. | Use the six-area checklist: commands, testing, structure, style, git workflow, boundaries. |

---

## Quick-Start Spec Template

Use this skeleton as a starting point for any new AI-assisted project. Fill in the sections relevant to your context, then let the agent expand the details.

```markdown
# Project Spec: [Project Name]

## Objective
Build [what] for [who] so they can [why].
Success means [measurable criteria].

## Tech Stack
[Language] [version], [framework], [database], [key dependencies with versions]

## Commands
- Build: [full command with flags]
- Test: [full command with flags]
- Lint: [full command with flags]

## Project Structure
- `src/` – Application source code
- `tests/` – Unit and integration tests
- `docs/` – Documentation

## Code Style
[One real code snippet showing your conventions]

## Git Workflow
- Branch naming: [pattern]
- Commit format: [convention]
- PR requirements: [rules]

## Boundaries
- ✅ Always: [safe actions the agent should take without asking]
- ⚠️ Ask first: [high-impact changes requiring approval]
- 🚫 Never: [hard stops]

## Success Criteria
[Sample inputs/outputs, test cases, or acceptance criteria]
```

---

## Conclusion

Writing an effective spec for AI coding agents requires solid software engineering principles combined with adaptation to LLM quirks. The five principles form a complete framework:

1. **Start with a high-level vision** and let the AI draft the details.
2. **Structure the spec like a professional document**, covering the six core areas.
3. **Break tasks into modular prompts and context**, not one giant prompt.
4. **Build in self-checks, constraints**, and your domain expertise.
5. **Test, iterate, and evolve** the spec continuously.

The spec isn't just for the AI—it helps you as the developer maintain oversight and ensures the AI's work meets real requirements. It's a living artifact in collaboration with the AI, not a one-time contract you can't change.

> Working with AI agents is a form of management. You provide clear instructions (the spec), ensure they have the necessary context, and give actionable feedback. The spec sets the stage, but monitoring and feedback during execution are key. Follow these guidelines and your AI agent will be far less likely to break down under large contexts or wander off into nonsense.

---

## Sources and Further Reading

- [Addy Osmani, "How to Write a Good Spec for AI Agents" – O'Reilly Radar, Feb 2026](https://www.oreilly.com/radar/how-to-write-a-good-spec-for-ai-agents/)
- [GitHub Blog, "How to Write a Great agents.md" – Analysis of 2,500+ repos](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [GitHub Blog, "Spec-Driven Development with AI"](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Simon Willison, "Vibe Engineering"](https://simonwillison.net/2025/Oct/7/vibe-engineering/)
- [Anthropic, "Effective Context Engineering for AI Agents"](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [The "Curse of Instructions" – Research Paper](https://maxpool.dev/research-papers/curse_of_instructions_report.html)

---

