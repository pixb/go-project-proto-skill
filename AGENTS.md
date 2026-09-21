# go-project-proto-skill

> Protocol buffer configuration for Connect RPC + gRPC-Gateway using buf, including plugins, imports, annotations, and code generation.

## Purpose

Configures protobuf definitions, buf tooling, and code generation for the Go project (memos). Creates `.proto` service and message files, generates Go code via `buf generate`, and manages gRPC-Gateway HTTP annotations.

## Activation

This skill activates when users ask about proto definitions, buf configuration, code generation, gRPC annotations, or protobuf services. Invoke with `/go-project-proto-skill` or ask naturally.

**Example queries:**
- "Create a proto service definition for user management"
- "Add gRPC-Gateway HTTP annotations"
- "Run buf generate to regenerate Go code"
- "Configure buf.gen.yaml plugins"

## Usage

Provide a service name and desired RPC methods. The skill generates proto files with proper annotations, runs code generation, and verifies output in `gen/api/v1/`.

## Gotchas

- **Proto files must be created before server code.** Without generated Go types, server implementations will not compile. Dependency chain: proto → generate → store → server.
- **`go_package` must be set** in each `.proto` file. Without it, generated code lands in the wrong import path.
- **buf remote plugins** require network on first run. Pre-cache behind firewalls.
- **Enum first value must be 0** per protobuf spec. Always start with `*_UNSPECIFIED = 0`.
- **Generated code in `gen/`** is gitignored. Re-run `buf generate` after clone/pull.

## Implementation

Full skill definition, references, and buf configuration patterns are in SKILL.md. See SKILL.md for complete instructions.

## Files

- `SKILL.md` — Full skill definition (buf config, proto patterns, workflows)
- `references/buf-plugins.md` — Complete buf plugin inventory
- `references/proto-patterns.md` — Extended message patterns
- `references/troubleshooting.md` — Common buf errors and fixes
