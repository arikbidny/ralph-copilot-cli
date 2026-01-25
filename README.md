# Ralph Wiggum: Autonomous Development for Proof of Concepts

> **Credit:** This guide is inspired by [JeredBlu's Ralph Wiggum Guide](https://github.com/JeredBlu/guides/blob/main/Ralph_Wiggum_Guide.md). I've adapted and extended it to fit my specific workflow using the `/create-prd` command and GitHub Copilot CLI.

---

## What is Ralph Wiggum?

Ralph Wiggum is a method for running GitHub Copilot CLI in a continuous autonomous loop. Each iteration runs in a **fresh context window**, allowing the agent to work through a series of tasks until completion without context bloat.

### When to Use Ralph Wiggum

**Ideal for:**
- Starting projects from scratch
- Building proof of concepts (POCs) with a clearly defined scope
- Greenfield development where requirements are well-understood
- Projects where you can define "done" precisely

**Not ideal for:**
- Complex existing codebases
- Vibe coding or exploratory work without clear goals
- Projects with ambiguous requirements
- Situations requiring frequent human judgment calls

The key insight: **Ralph Wiggum works best when you have a clear plan.** If you don't know exactly what you're building, stop and figure that out first. The `@workspace Use .copilot/create-prd.md` prompt helps you do exactly that.

---

## Why Not Just Use Copilot Chat?

Standard chat interfaces have a stateful context that can get bloated or confused over long multi-step tasks. In contrast, the Ralph Wiggum loop (`ralph.sh`) starts a **fresh context window** for each iteration, feeding in the concise project state from `PROMPT.md`, `prd.md`, and `activity.md`. This minimizes hallucination and keeps the agent focused on the immediate next task.

---

## Prerequisites

### 1. GitHub Copilot CLI

You need GitHub Copilot CLI installed and authenticated. See the [official documentation](https://docs.github.com/en/copilot/github-copilot-in-the-cli/using-github-copilot-in-the-cli) for setup.

### 2. Vercel Agent Browser CLI

Install the agent-browser CLI for headless browser automation:

```bash
npm install -g agent-browser
agent-browser install  # Downloads Chromium
```

On Linux, include system dependencies:
```bash
agent-browser install --with-deps
```

This tool allows Copilot to verify its work visually by taking screenshots and interacting with your running application.

### 3. Project Setup Files

This repository includes everything you need:
- `.copilot/create-prd.md` - The instructions for creating the PRD
- `.github/skills/agent-browser-skill/SKILL.md` - Agent browser instructions
- `PROMPT.md` - Template for the Ralph loop
- `ralph.sh` - The bash loop script
- `activity.md` - Activity logging template
- `screenshots/` - Directory for visual verification

---

## The Process

### Step 1: Create Your PRD with `/create-prd`

Run the `copilot --agent=.copilot/create-prd.md --prompt "Run this agent"` command in github copilot CLI:

```
copilot --agent=.copilot/create-prd.md --prompt "Run this agent"
```

This interactive command will:custom instructions

Use the custom instructions in `.copilot/create-prd.md` to guide the creation of your PRD. You might say: "Help me create a PRD for my new app using the create-prd instructions."

This process will:

1. **Ask discovery questions** one at a time:
   - What problem are you solving?
   - Who is your target audience?
   - What are the 3-5 core features?
   - What tech stack do you want?
   - What architecture approach?
   - UI/UX preferences?
   - Authentication requirements?
   - Third-party integrations?
   - Success criteria?

2. **Research options** if you're unsure about tech stack or architecture

3. **Generate `prd.md`** with:
   - Complete project requirements
   - Tech stack decisions
   - JSON task list with atomic, verifiable tasks

4. **Update `PROMPT.md`** with your specific:
   - Start commands for your tech stack
   - Build/lint commands
   - Project-specific instructions

5. **Create/verify `activity.md`** for logging progress

### Step 2: Verify Your Setup

After the PRD is created, **verify these files before running the loop**:

**Check `prd.md`:**
- Are all features captured in the task list?
- Are tasks atomic (completable in one iteration)?
- Are tasks in the correct dependency order?
- Is the success criteria clear?

**Check `PROMPT.md`:**
- Is the start command correct for your tech stack?
- Are build/lint commands accurate?

This verification step is **critical**. The quality of your Ralph Wiggum run depends entirely on the quality of your PRD and configuration.

### Step 3: Run the Ralph Loop

Once verified, start the autonomous loop:

```bash
./ralph.sh 20
```

The number is your maximum iterations. Start with 10-20 for smaller projects.

The script will:
1. Read `PROMPT.md` and feed it to Copilot CLI
2. Copilotrations reached

### Step 4: Monitor Progress

While Ralph runs, you can monitor:

- **`activity.md`** - Detailed log of what was accomplished each iteration
- **`screenshots/`** - Visual verification of each completed task
- **Git commits** - One commit per task with clear messages
- **Terminal output** - Real-time progress

---

## File Reference

### `prd.md` (Generated)

Your Product Requirements Document with a JSON task list:

```json
[
  {
    "category": "setup",
    "description": "Initialize Next.js project with TypeScript",
    "steps": [
      "Run create-next-app with TypeScript template",
      "Install additional dependencies",
      "Verify dev server starts"
    ],
    "passes": false
  }
]
```

Tasks should be:
- **Atomic**: One logical unit of work
- **Verifiable**: Clear success criteria
- **Ordered**: Respect dependencies
- **Categorized**: setup, feature, integration, styling, testing

### `PROMPT.md`

Instructions for each iteration. References `@prd.md` and `@activity.md`. Updated by `/create-prd` with your specific start commands.

### `.claude/settings.json`

Permissions and sandbox configuration. Updated by `/create-prd` based on your tech stack to ensure the agent can run all necessary commands.

Example permissions that might be added based on your PRD:
- `Bash(prisma:*)` for Prisma CLI
- `Bash(supabase:*)` for Supabase CLI
- `Bash(firebase:*)` for Firebase CLI
- `Bash(vercel:*)` for Vercel CLI
- `Bash(docker compose:*)` for Docker workflows

### `ralph.sh`
the PRD creation process with your specific start commands.

### `ralph.sh`

The bash loop script. Key features:
- Fresh context window per iteration
- Completion detection via `<promise>COMPLETE</promise>`
- File existence validation
- Color-coded output
- Graceful handling of max iterations
- Automatically passes `--allow-all-paths` and `--allow-all-urls` for autonomous operation

### `activity.md`

Activity log where the agent records:
- Date and time
- Task worked on
- Changes made
- Commands run
- Screenshot filename
- Issues and resolutions

---

## Best Practices

### 1. Plan Thoroughly

The PRD creation process exists because **planning is everything**. Don't skip the discovery questions. Don't rush through them. A well-defined PRD is the difference between a successful Ralph run and wasted API credits.

### 2. Keep Scope Tight

Ralph Wiggum is for proof of concepts, not production applications. Define the minimum viable version of your idea. You can always iterate later.

### 3. Verify Before Running

Always review `prd.md` and `PROMPT.md` before starting the loop. Catching issues here saves iterations.

### 4. Start with Fewer Iterations

Use 10-20 iterations initially. You can always run more if needed. This prevents runaway costs if something goes wrong.

### 5. Monitor the First Few Iterations

Watch the first 2-3 iterations to ensure things are working correctly. Check that:
- The dev server starts properly
- Agent-browser can access localhost
- Tasks are being marked as passing correctly
- Activity log is being updated

---

## Troubleshooting

### Agent gets stuck in a loop
- Check if the task is too ambiguous
- Review `activity.md` to see what it's attempting
- Consider breaking the task into smaller steps

### Agent can't access localhost
- Verify the dev server is running
- Check that the port in `PROMPT.md` matches your actual port
- Ensure agent-browser is installed correctly

### Context issues / hallucinations
- This shouldn't happen with the bash loop (fresh context each iteration)

### Max iterations reached without completion
- Review remaining tasks in `prd.md`
- Check if tasks are too large or ambiguous
- Run again with `./ralph.sh 30` or more iterations

---

## Links

- [Original Ralph Wiggum Guide by JeredBlu](https://github.com/JeredBlu/guides/blob/main/Ralph_Wiggum_Guide.md)
- [Vercel Agent Browser CLI](https://github.com/vercel-labs/agent-browser)
- [GitHub Copilot CLI Documentation](https://docs.github.com/en/copilot/github-copilot-in-the-cli/using-github-copilot-in-the-cli