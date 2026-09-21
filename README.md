# go-project-proto-skill

Protocol buffer configuration for Connect RPC + gRPC-Gateway using buf, including plugins, imports, annotations, and code generation.

## Installation

### Option 1: Direct Clone

```bash
git clone <repo-url> ~/.config/opencode/skills/go-project-proto-skill
```

### Option 2: Symlink

```bash
ln -s /path/to/go-project-proto-skill ~/.config/opencode/skills/go-project-proto-skill
```

## Prerequisites

- **buf CLI** installed ([installation guide](https://buf.build/docs/installation))
- **Go** 1.21+ installed

## Usage

### Create a Proto Service

Ask the agent:

> "Create a proto service for memo management with CRUD operations"

### Generate Code

```bash
cd proto && buf generate
```

### Lint Proto Files

```bash
cd proto && buf lint
```

### Check Breaking Changes

```bash
cd proto && buf breaking --against .git#main
```

## Files

- `SKILL.md` — Full skill definition with patterns, workflows, and examples
- `AGENTS.md` — Companion file for cross-tool compatibility
- `references/buf-plugins.md` — Complete buf plugin inventory
- `references/proto-patterns.md` — Extended message patterns
- `references/troubleshooting.md` — Common errors and fixes

## Example

```
User: "Create a proto definition for a UserService"
Agent: [creates proto/api/v1/user_service.proto with proper annotations]
Agent: runs buf generate
Agent: reports generated files in gen/api/v1/
```

## License

MIT
