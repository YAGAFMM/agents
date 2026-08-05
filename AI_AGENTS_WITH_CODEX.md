# AI Agents with Codex CLI: A Beginner's Study Guide

> Updated: August 5, 2026  
> Audience: someone new to AI agents and Codex CLI

This guide explains five ideas in a useful learning order:

1. What an AI agent is
2. How context windows and prompting work
3. What tools are
4. What skills are and why they are useful
5. How to build a skill

You do not need to memorize everything at once. First learn the mental model, then use the flashcards to review.

## How to use the flashcards

Every large topic ends with a flashcard table. Click **Show answer** in the Answer column to reveal an answer.

<details>
<summary>Try it: What does AI mean?</summary>

Artificial Intelligence.

</details>

VS Code's built-in Markdown preview supports these collapsible `<details>` elements. Open this file and press `Ctrl+Shift+V` to open the preview.

---

## 1. What is an AI agent?

### Start with the basic terms

**AI**, or artificial intelligence, is a broad name for computer systems that perform tasks associated with intelligence, such as understanding language, recognizing patterns, planning, and solving problems.

A **large language model (LLM)** is a type of AI model trained to work with language and other kinds of data. Given some input, it predicts and generates an appropriate continuation. It can explain code, suggest a plan, or decide which available action is useful next.

A **model** and an **agent** are not the same thing:

- A model produces an output from the input it receives.
- An agent uses a model as part of a system that can pursue a goal, inspect an environment, use tools, observe results, and continue until it finishes or becomes blocked.

A helpful beginner formula is:

```text
AI agent = model + goal + context + tools + action loop + permissions
```

### The agent loop

An agent usually works through a loop like this:

```text
Receive goal
    ↓
Inspect the available context
    ↓
Choose the next useful action
    ↓
Use a tool or produce an answer
    ↓
Observe the result
    ↓
Continue, correct course, verify, or finish
```

This is a simplified mental model, not a claim that you can see every internal computation. What matters is that the agent can take an action, learn from the result, and choose another action.

For example, if you ask Codex to fix a failing test, it may:

1. Read the test and relevant source files.
2. Run the test to reproduce the failure.
3. Interpret the error.
4. Edit the source code.
5. Run the test again.
6. Review the resulting Git diff.
7. Report what changed and whether verification passed.

Ordinary chat generation might only suggest a fix. Agentic work can inspect and change the real project, subject to the permissions you give it.

### Codex as a coding agent

Codex is OpenAI's coding agent. In the CLI, it works from a current working directory and can use developer tools to understand, edit, test, and review a codebase.

It has three important boundaries:

- **Available information:** Codex only has the context it was given or can access.
- **Available tools:** It can only take actions through tools exposed in the session.
- **Permissions:** Sandboxing and approval rules limit what those tools may do.

An agent is not automatically correct just because it can act. Models are probabilistic and can misunderstand a requirement, select the wrong file, or state an unsupported fact. That is why good agent workflows include tests, checks, source inspection, and human review.

### Autonomy does not mean unlimited authority

**Autonomy** means the agent can choose and perform intermediate steps without you spelling out every one. It does not mean the agent should have unlimited access or make every decision for you.

A useful division of responsibility is:

- You define the goal, important constraints, and acceptable risk.
- The agent handles appropriate intermediate work.
- Tools make actions possible.
- Permissions establish hard boundaries.
- Verification provides evidence that the result works.
- You review important or irreversible outcomes.

### Topic 1 flashcards

| Question | Answer |
| --- | --- |
| What does AI mean? | <details><summary>Show answer</summary>Artificial Intelligence.</details> |
| What is an LLM? | <details><summary>Show answer</summary>A large language model: an AI model trained to understand and generate language and related data.</details> |
| Is a model the same as an agent? | <details><summary>Show answer</summary>No. A model generates outputs; an agent uses a model within a system that can pursue goals and take actions.</details> |
| What are the main parts of an AI agent? | <details><summary>Show answer</summary>A model, goal, context, tools, an action loop, and permissions.</details> |
| What is the agent loop? | <details><summary>Show answer</summary>Inspect context, choose an action, act, observe the result, and repeat or finish.</details> |
| What is an environment? | <details><summary>Show answer</summary>The world the agent can inspect or affect, such as a code repository, terminal, or connected service.</details> |
| What does agent autonomy mean? | <details><summary>Show answer</summary>The agent can choose useful intermediate steps without the user specifying every step.</details> |
| Does autonomy mean unlimited permission? | <details><summary>Show answer</summary>No. Permissions and approvals still limit what the agent can do.</details> |
| Why must agent work be verified? | <details><summary>Show answer</summary>AI can misunderstand, make mistakes, or produce unsupported claims. Tests and review provide evidence.</details> |
| What is Codex? | <details><summary>Show answer</summary>OpenAI's coding agent for understanding, editing, testing, and reviewing software.</details> |

---

## 2. Context windows and prompting

### What is context?

**Context** is the information available to the model for its current response or decision. In a Codex session, it can include:

- Your current prompt and earlier conversation
- Instructions supplied by Codex
- Applicable `AGENTS.md` guidance
- File contents or selections
- Skill names and descriptions, plus full skill instructions when selected
- Tool results such as command output, errors, and Git diffs
- Summaries of earlier work

Context is not the same as permanent memory. If a fact is not present in the active context, available through a tool, or already learned during training, the model may not have it when making a decision.

### What is a context window?

The **context window** is the maximum amount of information the model can consider at one time. It is measured in **tokens**.

A token is a small unit of text. It may be a whole short word, part of a longer word, punctuation, or code. Tokens are not exactly the same as words.

The context window has to hold more than your latest message. Conversation history, instructions, file content, and tool output also consume space.

```text
Context window
├── Product and session instructions
├── Your messages
├── Agent responses
├── Relevant files and repository guidance
├── Loaded skill instructions
└── Tool calls and their results
```

When a chat becomes long, useful requirements can become harder to distinguish from old logs and unrelated details. Two helpful informal terms are:

- **Context pollution:** useful information is buried under noisy or irrelevant material.
- **Context rot:** performance becomes less reliable as low-value context accumulates.

Codex can compact a long chat automatically. In the CLI, `/compact` summarizes earlier context to free space while retaining important details. A summary is lossy, so keep essential requirements in clear, durable places rather than relying on a detail mentioned once far back in the chat.

### Good context is relevant, not merely large

More context is not always better. Give Codex the information that can change the answer:

- Mention the relevant file paths.
- Include the exact error and reproduction steps.
- Explain important architecture or compatibility rules.
- State what must not change.
- Remove giant logs when a short relevant excerpt is enough.
- Start a new chat when the work changes to a separate problem.

In Codex CLI, mention paths explicitly or use `/mention` and `@` path autocomplete. Codex can inspect other files when needed, but pointing to a good starting place reduces unnecessary exploration.

### What is a prompt?

A **prompt** is the question, instruction, or goal you give the model. Prompting does not require magic words or complicated syntax. Clear everyday language is usually best.

For meaningful coding tasks, a reliable structure is:

```text
Goal: What should change or be created?
Context: Which files, errors, examples, or facts matter?
Constraints: What rules or boundaries must be followed?
Done when: How should Codex verify completion?
```

Example:

```text
Fix the crash when a user submits the signup form without a middle name.

Context:
- The handler is in src/auth/signup.ts.
- The failing test is tests/auth/signup.test.ts.
- The database column is intentionally nullable.

Constraints:
- Do not change the public API response shape.
- Keep support for Node 22.

Done when:
- Add or update a regression test.
- Run the focused test and the type checker.
- Summarize the change and any remaining risk.
```

This prompt gives Codex a result to pursue without micromanaging every command.

### Prompting is a conversation

Your first prompt does not have to be perfect. You can correct or refine the work with follow-up messages:

- "Keep the implementation, but simplify the error message."
- "Before editing, explain the root cause with file references."
- "Do not add a dependency; use the standard library."
- "Run only the focused tests for now."

For a complex or ambiguous task, ask Codex to plan before implementation. In supported Codex surfaces, `/plan` helps separate investigation and decisions from editing.

### Where different instructions belong

Do not put every recurring rule in every prompt:

| Need | Best place |
| --- | --- |
| Applies only to this task | Current prompt |
| Repository conventions and test commands | `AGENTS.md` |
| Reusable workflow for a recognizable type of task | Skill |
| Durable Codex settings | `config.toml` |

### Topic 2 flashcards

| Question | Answer |
| --- | --- |
| What is context? | <details><summary>Show answer</summary>The information available to the model for its current response or decision.</details> |
| What is a context window? | <details><summary>Show answer</summary>The maximum amount of information a model can consider at one time.</details> |
| What unit measures model context? | <details><summary>Show answer</summary>Tokens.</details> |
| Are tokens exactly the same as words? | <details><summary>Show answer</summary>No. A token can be a word, part of a word, punctuation, or code.</details> |
| Name four things that can consume context. | <details><summary>Show answer</summary>Messages, instructions, file contents, loaded skills, and tool results are examples.</details> |
| What is context pollution? | <details><summary>Show answer</summary>When useful information is buried under noisy or irrelevant content.</details> |
| What does `/compact` do in Codex CLI? | <details><summary>Show answer</summary>It summarizes earlier chat content to free context-window space while trying to retain important details.</details> |
| Is more context always better? | <details><summary>Show answer</summary>No. Relevant, focused context is usually better than large amounts of noise.</details> |
| What four parts make a strong Codex prompt? | <details><summary>Show answer</summary>Goal, context, constraints, and done-when or verification criteria.</details> |
| Where should repository-wide test commands go? | <details><summary>Show answer</summary>Usually in `AGENTS.md`.</details> |
| Where should a one-time constraint go? | <details><summary>Show answer</summary>In the current prompt.</details> |
| Must the first prompt be perfect? | <details><summary>Show answer</summary>No. Use follow-up messages to steer, correct, and refine the result.</details> |

---

## 3. Tools: shell, filesystem, Git, and more

### What is a tool?

A **tool** is a defined capability an agent can call to inspect information or take an action. The model decides what it wants to do; the tool performs the operation and returns a result the model can observe.

```text
Model chooses an action
        ↓
Tool receives structured input
        ↓
The environment performs the action
        ↓
Tool returns output or an error
        ↓
Model decides what to do next
```

Without tools, a model can explain a command or draft code in its response. With the right tools and permissions, an agent can run the command or edit the actual file.

### Common tools in Codex

#### Filesystem tools

Filesystem capabilities let Codex inspect directories, read files, and edit files. They are useful for learning a repository and making precise changes.

Examples:

- List files in a project.
- Search for all uses of a function.
- Read `package.json` to discover test commands.
- Update a source file.
- Create a new test.

The current working directory matters because it tells Codex which project you placed in scope.

#### Shell tools

A **shell** is a text interface for running programs and commands. PowerShell is your current shell on Windows. Bash and Zsh are common on macOS and Linux.

Codex can use the shell to run developer programs such as:

```powershell
rg "TODO" .
npm test
python --version
git status
```

The shell is powerful because it can start many other programs. It is also risky: commands can install software, change files, access the network, or delete data. Treat an unfamiliar command as something to understand before approving it.

#### Git tools

**Git** is a version-control system. It records snapshots and differences so you can understand how a project changed.

Useful beginner commands include:

```powershell
git status       # Show changed, staged, and untracked files
git diff         # Show unstaged changes
git diff --staged # Show staged changes
git log --oneline # Show a compact commit history
```

Git is not the same as GitHub:

- Git is the version-control software.
- GitHub is a service that hosts Git repositories and adds collaboration features.

An uncommitted change is not safely stored in Git history yet. Always review a diff before accepting or committing agent changes.

#### Web and external-service tools

Some facts live outside the repository or change frequently. Web search can retrieve public current information. MCP servers and app connectors can expose external systems such as documentation, issue trackers, or team services.

**MCP**, or Model Context Protocol, is an open standard for connecting AI applications to external tools and data. MCP is not itself a skill. An MCP server provides capabilities; a skill can teach Codex a repeatable workflow that uses them.

### Permissions, sandboxing, and approvals

Codex runs local commands within a **sandbox**, which limits filesystem and network access. An **approval policy** controls when it asks before crossing a boundary or taking a sensitive action.

Think of the three ideas this way:

- **Tool:** what action is technically available.
- **Sandbox:** where and how that action is allowed to operate.
- **Approval:** when you must authorize an exception or sensitive action.

Examples of increasing risk:

| Type of action | Example | Typical concern |
| --- | --- | --- |
| Read-only | Read a source file | Exposure of sensitive data |
| Local write | Edit a file in the workspace | Incorrect or unwanted change |
| External access | Download a dependency | Untrusted content or data transfer |
| Side effect | Open an issue or send a message | Affects other systems or people |
| Destructive | Delete files or rewrite Git history | Difficult recovery or data loss |

Before approving a command, check:

1. What exact command will run?
2. Which files, service, or account can it affect?
3. Is the action reversible?
4. Is the scope narrow enough?
5. Can a read-only check provide more confidence first?

### Tools provide evidence, not certainty

A successful tool call only proves that the tool completed. It does not automatically prove the overall task is correct.

For example:

- A file edit succeeding does not prove the code compiles.
- One test passing does not prove all related behavior works.
- `git status` showing a clean tree does not prove the deployed service is healthy.

A strong verification loop chooses checks that match the requested outcome.

### Topic 3 flashcards

| Question | Answer |
| --- | --- |
| What is an agent tool? | <details><summary>Show answer</summary>A defined capability the agent can call to inspect information or take an action.</details> |
| What does a tool return to the model? | <details><summary>Show answer</summary>An observation such as output, data, or an error.</details> |
| What is a shell? | <details><summary>Show answer</summary>A text interface for running commands and programs.</details> |
| What is the current shell in this workspace? | <details><summary>Show answer</summary>PowerShell.</details> |
| What do filesystem tools do? | <details><summary>Show answer</summary>They inspect directories and read, create, or edit files within allowed boundaries.</details> |
| What does `git status` show? | <details><summary>Show answer</summary>The state of tracked, changed, staged, and untracked files in the working tree.</details> |
| What does `git diff` show? | <details><summary>Show answer</summary>Differences in file content, normally unstaged changes unless options specify otherwise.</details> |
| Are Git and GitHub the same? | <details><summary>Show answer</summary>No. Git is version-control software; GitHub is a hosting and collaboration service built around Git repositories.</details> |
| What is a sandbox? | <details><summary>Show answer</summary>An isolation boundary that limits where and how tools can operate.</details> |
| What is an approval policy? | <details><summary>Show answer</summary>A rule for when Codex must ask permission before an action.</details> |
| What does MCP mean? | <details><summary>Show answer</summary>Model Context Protocol.</details> |
| What does an MCP server provide? | <details><summary>Show answer</summary>External data, tools, authentication, and controlled actions.</details> |
| Does a successful file edit prove the task is correct? | <details><summary>Show answer</summary>No. The result still needs relevant tests, checks, or review.</details> |

---

## 4. Skills and why they are useful

### What is a skill?

A **skill** is a folder of reusable instructions and optional resources that teaches ChatGPT or Codex how to complete a repeatable workflow.

Every skill has a required `SKILL.md` file. It may also have:

- `references/` for policies, schemas, examples, and background material
- `assets/` for templates or files to copy or transform
- `scripts/` for deterministic computation or file processing
- `agents/openai.yaml` for optional interface metadata, invocation policy, and tool dependencies

A simple skill looks like this:

```text
my-skill/
└── SKILL.md
```

A larger skill might look like this:

```text
release-notes/
├── SKILL.md
├── references/
│   └── writing-style.md
├── assets/
│   └── release-template.md
└── scripts/
    └── collect_commits.ps1
```

### Why use a skill?

Use a skill when a workflow is repeated often enough that copying a long prompt becomes unreliable or annoying.

Skills can provide:

- **Consistency:** the same important steps and output structure each time.
- **Reuse:** one workflow can be used across many chats.
- **Discoverability:** Codex can select a skill from its name and description.
- **Maintainability:** update the workflow in one place.
- **Context efficiency:** detailed instructions load only when the skill is selected.
- **Team sharing:** repository skills can travel with the project.

Good skill candidates include release-note drafting, log triage, a standard debugging flow, PR review against a checklist, or creating a project-specific report.

Do not create a skill for a single small request. First complete the task normally, notice what repeats, and then package the stable method.

### Progressive disclosure

Skills use **progressive disclosure**:

1. Codex initially sees lightweight metadata: the skill's name, description, and path.
2. If a request matches, Codex loads the full `SKILL.md`.
3. The instructions can tell Codex when to load supporting references or run scripts.

This keeps every installed skill from filling the context window at the same time.

### How a skill activates

A skill can activate in two ways:

- **Explicitly:** in Codex CLI, use `/skills` or type `$` to mention a skill, such as `$skill-creator`.
- **Implicitly:** Codex can select a skill when your request matches its description.

The description is therefore important. It should clearly state what the skill does and when it should or should not trigger. Detailed procedure belongs in the body of `SKILL.md`.

### Skill locations in Codex

The most useful locations for a beginner are:

| Scope | Location | Use |
| --- | --- | --- |
| Repository | `.agents/skills/` within the repository path | Skills shared with or scoped to a project |
| User | `$HOME/.agents/skills/` | Personal skills available across repositories |
| System | Bundled with Codex | General skills provided by OpenAI |

Codex detects skill changes automatically. Restart Codex if a new or updated skill does not appear.

### Do not confuse these concepts

| Concept | Main purpose |
| --- | --- |
| Prompt | Tell Codex what you need in the current task |
| `AGENTS.md` | Give durable repository guidance and commands |
| Skill | Package a reusable task workflow |
| Tool | Perform an action or retrieve information |
| MCP server | Expose live external data and controlled actions |
| Plugin | Distribute a bundle that may include skills, tools, MCP configuration, apps, or assets |

A skill and a tool often cooperate. For example, an issue-triage skill can define which issue data to fetch, what decisions to make, and how to format the report. An MCP server can supply the live issue data and the action for updating a label.

### Topic 4 flashcards

| Question | Answer |
| --- | --- |
| What is a skill? | <details><summary>Show answer</summary>A folder of reusable instructions and resources for a repeatable workflow.</details> |
| What file is required in every skill? | <details><summary>Show answer</summary>`SKILL.md`.</details> |
| What are three optional skill folders? | <details><summary>Show answer</summary>`references/`, `assets/`, and `scripts/`.</details> |
| Why are skills useful? | <details><summary>Show answer</summary>They make repeated workflows more consistent, reusable, discoverable, and maintainable.</details> |
| What is progressive disclosure? | <details><summary>Show answer</summary>Codex first sees small skill metadata, then loads full instructions only when the skill is selected.</details> |
| How can a skill activate explicitly in Codex CLI? | <details><summary>Show answer</summary>Use `/skills` or type `$` to mention the skill.</details> |
| How can a skill activate implicitly? | <details><summary>Show answer</summary>Codex matches the user's request to the skill description.</details> |
| Where do personal skills live? | <details><summary>Show answer</summary>`$HOME/.agents/skills/`.</details> |
| Where can repository skills live? | <details><summary>Show answer</summary>In `.agents/skills/` within the repository path.</details> |
| What is the difference between a skill and a tool? | <details><summary>Show answer</summary>A skill describes a reusable workflow; a tool performs an action or retrieves information.</details> |
| What is the difference between `AGENTS.md` and a skill? | <details><summary>Show answer</summary>`AGENTS.md` gives durable repository guidance; a skill defines a reusable workflow for a recognizable task.</details> |
| When should you avoid creating a skill? | <details><summary>Show answer</summary>When the task is a one-off and no stable, repeated workflow exists yet.</details> |

---

## 5. Build your first skill

This section turns the official **Build skills** guidance into a beginner workflow.

### Step 1: Choose one recognizable job

Start with two or three realistic requests that should use the skill. Also write down requests that should not use it.

Example goal: create a study guide from a Markdown document.

Should trigger:

- "Turn this chapter into a beginner study guide."
- "Make flashcards from this Markdown file."

Should not trigger:

- "Fix the TypeScript build."
- "Summarize today's weather."

A focused goal produces a better skill than a vague skill that tries to do everything.

### Step 2: Decide the skill's boundary

Before writing instructions, answer:

- What input does the workflow expect?
- What steps should Codex follow?
- What output should the user receive?
- Which facts must not be invented?
- When should the skill ask a question, stop, or decline?
- Which files or tools does it need?
- What proves the workflow succeeded?

Split the workflow into separate skills if different requests have different triggers, inputs, or success criteria.

### Step 3: Create the folder

For a repository skill, create:

```text
.agents/
└── skills/
    └── markdown-study-guide/
        └── SKILL.md
```

You can ask the built-in creator to scaffold it:

```text
$skill-creator Create a repository skill named markdown-study-guide. It should turn a supplied Markdown document into a beginner explanation and collapsible flashcards without inventing missing facts.
```

Instruction-only skills are a good default. Add scripts only when deterministic processing or external tooling is genuinely useful.

### Step 4: Write `SKILL.md`

`SKILL.md` begins with YAML frontmatter containing a name and description. The body contains the workflow instructions.

```md
---
name: markdown-study-guide
description: Create a beginner study guide and collapsible flashcards from a supplied Markdown document. Use when the user asks to study, learn, quiz, or make flashcards from Markdown content. Do not use for code fixes or unrelated summaries.
---

# Markdown study guide

1. Read the complete source document before drafting.
2. Identify the major concepts and preserve important qualifications.
3. Explain each concept in plain language for a beginner.
4. After each major section, add a two-column Question and Answer table.
5. Wrap every answer in an HTML `details` element with a `Show answer` summary.
6. Do not invent facts missing from the source. Mark uncertainty clearly.
7. Save the result as Markdown and verify that headings, tables, and details elements render correctly.
```

The description controls **when Codex considers the skill**. Put activation language and boundaries there. Put steps, formats, safety rules, and success checks in the body.

Good skill instructions are:

- Imperative: "Read the source," not "The source could be read."
- Specific about inputs and outputs.
- Clear about forbidden assumptions.
- Focused on one workflow.
- Explicit about verification.

### Step 5: Add supporting resources only when needed

Keep the main instructions short enough to scan. Move detailed material into supporting folders:

```text
markdown-study-guide/
├── SKILL.md
├── references/
│   └── flashcard-writing-rules.md
└── assets/
    └── study-guide-template.md
```

In `SKILL.md`, say exactly when to read each reference or use each asset. Do not create a script just to perform work Codex can reliably do with instructions and existing tools.

Use a script when deterministic behavior matters, such as validating a schema, calculating values, or transforming many files in a fixed way.

### Step 6: Declare a required MCP tool if applicable

Many skills do not need MCP. If yours depends on live external information or actions, the tool dependency can be declared in `agents/openai.yaml`, and `SKILL.md` should still explain how and when to use it.

The division remains:

- The MCP server supplies live data and controlled actions.
- The skill supplies the workflow, decisions, and output requirements.

### Step 7: Test activation and output

Official guidance recommends testing at least five categories:

1. **Direct request:** clearly names the task.
2. **Indirect request:** expresses the same goal in different words.
3. **Incomplete request:** should cause a useful follow-up question.
4. **Negative request:** should not activate the skill.
5. **Edge case:** should not invent information or take an unsupported action.

Diagnose failures in two layers:

- If the wrong skill activates, refine the **description**.
- If the correct skill activates but behaves poorly, refine the **instructions** or supporting resources.

Test the finished artifact too. For this example, open the Markdown preview, toggle several answers, check table rendering, and verify that the explanations agree with the source.

### Step 8: Improve from real use

Use the skill on representative work. When it fails repeatedly, update the smallest relevant instruction and test again. Avoid adding dozens of speculative rules before you encounter the problems they solve.

Keep a skill local while it is still changing:

- Put it in the repository if it is project-specific or should be shared with that repository.
- Put it in your user skills folder if it reflects your personal workflow across projects.
- Package it as a plugin when you need installable distribution or want to bundle skills with related integrations.

### A build checklist

- [ ] The skill handles one recognizable user goal.
- [ ] `SKILL.md` has a valid `name` and `description`.
- [ ] The description states when the skill should and should not trigger.
- [ ] Instructions define inputs, steps, output, boundaries, and verification.
- [ ] Supporting files are included only when useful.
- [ ] `SKILL.md` says when to use each supporting file or tool.
- [ ] Direct and indirect requests activate the skill.
- [ ] Unrelated requests do not activate it.
- [ ] Incomplete and risky requests are handled safely.
- [ ] The produced result has been checked, not merely generated.

### Topic 5 flashcards

| Question | Answer |
| --- | --- |
| What should you choose before creating a skill? | <details><summary>Show answer</summary>One recognizable user goal with realistic requests that should and should not trigger it.</details> |
| What two frontmatter fields are required in `SKILL.md`? | <details><summary>Show answer</summary>`name` and `description`.</details> |
| What controls when Codex considers a skill? | <details><summary>Show answer</summary>The skill's name and especially its description.</details> |
| Where do detailed workflow steps belong? | <details><summary>Show answer</summary>In the body of `SKILL.md`.</details> |
| What should skill instructions define? | <details><summary>Show answer</summary>Expected input, steps, output, forbidden assumptions, stop or question conditions, resources, and success checks.</details> |
| When should one workflow be split into multiple skills? | <details><summary>Show answer</summary>When parts have different triggers, inputs, or success criteria.</details> |
| What is the default choice: instructions or scripts? | <details><summary>Show answer</summary>Prefer instructions unless deterministic processing or external tooling is needed.</details> |
| What belongs in `references/`? | <details><summary>Show answer</summary>Detailed policies, schemas, examples, and background material.</details> |
| What belongs in `assets/`? | <details><summary>Show answer</summary>Templates or files the workflow should copy or transform.</details> |
| What belongs in `scripts/`? | <details><summary>Show answer</summary>Deterministic computation or file-processing logic.</details> |
| What five test categories should a skill cover? | <details><summary>Show answer</summary>Direct, indirect, incomplete, negative, and edge-case requests.</details> |
| A wrong skill activates. What should you refine first? | <details><summary>Show answer</summary>The skill description.</details> |
| The right skill activates but gives inconsistent output. What should you refine? | <details><summary>Show answer</summary>The instructions or supporting resources.</details> |
| When is a plugin useful? | <details><summary>Show answer</summary>When you need installable distribution or want to bundle skills with related tools, integrations, or assets.</details> |

---

## A practical learning path

Try these exercises in order:

1. **Observe an agent:** ask Codex to explain a tiny repository and list which files it inspected.
2. **Practice context:** ask the same question once vaguely and once with relevant file paths and compare the answers.
3. **Practice prompting:** give Codex a goal, context, constraints, and done-when checks.
4. **Watch tool use:** ask it to make a small safe change, run a focused test, and show the Git diff.
5. **Review permissions:** read every approval request and identify the exact resource and side effect.
6. **Notice repetition:** save prompts or corrections you use more than once.
7. **Build one small skill:** use `$skill-creator` to package the clearest repeated workflow.
8. **Test the skill:** try direct, indirect, incomplete, negative, and edge-case prompts.

The central mental model to remember is:

```text
You provide direction and boundaries.
Context provides relevant information.
The model chooses useful next steps.
Tools let the agent inspect and act.
Permissions limit those actions.
Verification tells you whether the result deserves trust.
Skills make a proven workflow reusable.
```

## Official documentation and further reading

- [Codex best practices](https://learn.chatgpt.com/guides/best-practices)
- [Prompting](https://learn.chatgpt.com/docs/prompting)
- [Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- [Agent approvals and security](https://learn.chatgpt.com/docs/agent-approvals-security)
- [Sandboxing](https://learn.chatgpt.com/docs/sandboxing)
- [Custom instructions with AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [Skills in Codex and ChatGPT](https://learn.chatgpt.com/docs/build-skills)
- [Build skills for plugins](https://developers.openai.com/plugins/build/skills)
- [Skills concepts](https://developers.openai.com/plugins/concepts/skills)
- [Model Context Protocol](https://learn.chatgpt.com/docs/extend/mcp)

> Documentation changes over time. Use these official pages when an exact command, path, setting, or product capability matters.
