# Ralph - Autonomous Development Agent

Ralph is a script that runs GitHub Copilot CLI in a loop to autonomously implement user stories from a PRD (Product Requirements Document).

## Prerequisites

- [GitHub Copilot CLI](https://docs.github.com/en/copilot/github-copilot-in-the-cli) installed and authenticated
- Bash shell

## Quick Start

```bash
# Run Ralph in the current directory
./scripts/ralph/ralph.sh .

# Run Ralph targeting a specific project
./scripts/ralph/ralph.sh /path/to/your-project
```

## Usage

```bash
./ralph.sh [target_dir] [max_iterations] [model]
```

| Argument | Default | Description |
|----------|---------|-------------|
| `target_dir` | `.` | Directory containing your project |
| `max_iterations` | `10` | Maximum number of agent loops |
| `model` | `claude-opus-4.5` | AI model to use |

### Examples

```bash
# Default: current dir, 10 iterations, claude-opus-4.5
./ralph.sh

# Target a specific project
./ralph.sh ~/projects/my-api

# Run with more iterations
./ralph.sh ~/projects/my-api 25

# Use a different model
./ralph.sh ~/projects/my-api 10 gpt-5
```

## Setup Your Project

### 1. Create the Ralph directory structure

```bash
mkdir -p scripts/ralph
```

### 2. Copy Ralph files to your project

Copy these files to `scripts/ralph/` in your target project:
- `ralph.sh` - The main runner script
- `prompt.md` - Instructions for the AI agent
- `prd.json` - Your user stories
- `progress.txt` - Learning log (can be empty initially)

### 3. Configure `prd.json`

Define your user stories:

```json
{
  "branchName": "feature/my-feature",
  "userStories": [
    {
      "id": "US-001",
      "title": "Add user authentication",
      "acceptanceCriteria": [
        "Login endpoint at /auth/login",
        "Returns JWT token",
        "Type checking passes",
        "Tests pass"
      ],
      "priority": 1,
      "passes": false,
      "notes": ""
    },
    {
      "id": "US-002",
      "title": "Add user registration",
      "acceptanceCriteria": [
        "Register endpoint at /auth/register",
        "Validates email format",
        "Hashes passwords",
        "Tests pass"
      ],
      "priority": 2,
      "passes": false,
      "notes": ""
    }
  ]
}
```

### 4. Initialize `progress.txt`

Create an empty file or seed it with codebase patterns:

```markdown
## Codebase Patterns
- Package manager: Prefer `uv` over pip
- Testing: Use pytest with fixtures
- Add patterns here as Ralph discovers them
```

## How It Works

1. Ralph reads the PRD and progress log
2. Picks the highest priority story where `passes: false`
3. Implements that ONE story
4. Runs linting, type checking, and tests
5. Commits changes with message `feat: [ID] - [Title]`
6. Updates `prd.json` to mark story as `passes: true`
7. Appends learnings to `progress.txt`
8. Repeats until all stories pass or max iterations reached

When all stories are complete, Ralph outputs `<promise>COMPLETE</promise>` and exits.

## Available Models

- `claude-opus-4.5` (default)
- `claude-sonnet-4.5`
- `claude-sonnet-4`
- `claude-haiku-4.5`
- `gpt-5.2`
- `gpt-5.1`
- `gpt-5`
- `gpt-5-mini`
- `gpt-4.1`
- `gemini-3-pro-preview`

## Tips

- **Start small**: Begin with 1-2 simple stories to validate your setup
- **Be specific**: Clear acceptance criteria = better results
- **Check the branch**: Ralph verifies it's on the correct branch before working
- **Review commits**: Ralph commits after each story - review before pushing
- **Seed patterns**: Pre-populate `progress.txt` with known codebase patterns
