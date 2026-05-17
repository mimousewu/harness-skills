# Harness Skills

A Claude Code plugin providing a structured product management workflow through Design Thinking elicitation. Each skill produces artifacts that feed into the next, forming a complete pipeline from product vision to software architecture.

## Skills

| Skill | Description | Invocation |
|-------|-------------|------------|
| **product-framer** | Frame Vision, Personas, and Capabilities | `/harness-skills:product-framer` |
| **journey-designer** | Map user journeys and derive Features from pain points | `/harness-skills:journey-designer` |
| **user-story** | Create INVEST-compliant user stories with acceptance criteria | `/harness-skills:user-story` |
| **event-storming** | Domain discovery producing a DDD domain model | `/harness-skills:event-storming` |
| **sad-generator** | Generate a Software Architecture Document (arc42 + C4) | `/harness-skills:sad-generator` |

## Workflow

The skills form a chain where each consumes the previous skill's output:

```
product-framer → journey-designer → user-story → event-storming → sad-generator
```

All artifacts are written to a `product-management/` directory:

```
product-management/
  vision.md
  personas/
  capabilities.yaml
  capabilities/
  journeys/
  features/
  user-stories/
  domain-model/
  architecture/
    sad.md
```

Each skill can also be used standalone if you provide the required context.

## Installation

### OpenCode

Clone the repo and symlink the `skills/` directory into OpenCode's config:

```bash
git clone https://github.com/mimousewu/harness-skills.git ~/repos/harness-skills
ln -sf ~/repos/harness-skills/skills ~/.config/opencode/skills/harness
```

### Claude Code

**Step 1: Register the marketplace** (one-time, global)

Add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "harness-skills": {
      "source": {
        "source": "github",
        "repo": "mimousewu/harness-skills"
      }
    }
  }
}
```

Then install the plugin:

```
/plugin install harness-skills@harness-skills
```

**Step 2: Enable per project**

In each project where you want to use harness-skills, add `.claude/settings.json` at the project root:

```json
{
  "enabledPlugins": {
    "harness-skills@harness-skills": true
  }
}
```

This keeps the plugin available globally but only activates it (and its CLAUDE.md pipeline guide) in projects that opt in.

Reload with `/reload-plugins`.

## Usage

Skills trigger automatically on natural language or via slash commands:

- "Plan a new product" / "Define what to build" triggers **product-framer**
- "Map the user journey" / "Critical user journey" triggers **journey-designer**
- "Write user stories" / "Break this epic into stories" triggers **user-story**
- "Model the domain" / "Discover aggregates" triggers **event-storming**
- "Generate SAD" / "Architecture document" triggers **sad-generator**

## License

MIT
