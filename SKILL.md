---
name: go-project-proto-skill
description: >-
  Protocol buffer configuration for Connect RPC + gRPC-Gateway using buf,
  including plugins, imports, annotations, and code generation. Activate when
  creating proto definitions, generating Go code from protos, configuring buf
  plugins, adding gRPC-Gateway HTTP annotations, or defining protobuf services
  and messages for the Go project (memos).
license: MIT
metadata:
  author: pix
  version: 1.0.0
  created: 2025-09-20
  last_reviewed: 2025-09-20
  review_interval_days: 180
  dependencies:
    - url: https://buf.build
      name: buf CLI
      type: cli
---

# Protocol Buffer Configuration (buf)

## When to Use This Skill

Activate when the user asks to:
- Create new `.proto` service or message definitions
- Generate Go code from protobuf definitions
- Configure `buf.yaml` or `buf.gen.yaml`
- Add gRPC-Gateway HTTP annotations to services
- Run `buf generate`, `buf lint`, or `buf breaking`
- Define protobuf messages with `google.api.resource` annotations
- Set up buf dependencies (`buf dep update`)

**Does NOT activate for**: writing Go server implementations (→ go-project-server), database models (→ go-project-store), or general Go conventions (→ go-project-conventions).

## Data Source

This skill is a **configuration reference** — it defines patterns and conventions for protobuf files in the project. No external API is called. The data source is the project's own `proto/` directory.

See `references/buf-plugins.md` for the complete buf plugin inventory and `references/proto-patterns.md` for extended message patterns.

## Workflows

### Workflow 1: Create a New Proto Service

1. **Create directory structure**
   ```bash
   mkdir -p proto/api/v1
   ```

2. **Create buf configuration files** (if not already present)
   ```bash
   touch proto/buf.yaml
   touch proto/buf.gen.yaml
   ```

3. **Write the service definition** in `proto/api/v1/{name}_service.proto`
   - Use `syntax = "proto3"`
   - Package: `memos.api.v1`
   - Import `google/api/annotations.proto` for HTTP bindings
   - Add `google.api.http` options to each RPC method

4. **Generate code**
   ```bash
   cd proto && buf generate
   ```

5. **Verify generated output** in `gen/api/v1/`
   - `*_pb.go` — Protobuf messages
   - `*_connect.go` — Connect RPC handlers
   - `*_grpc.go` — gRPC stubs
   - `openapi.yaml` — OpenAPI spec

### Workflow 2: Add buf Dependencies

```bash
cd proto && buf dep update
```

This populates `buf.lock` with resolved dependency versions.

### Workflow 3: Lint and Check Breaking Changes

```bash
# Lint proto files
cd proto && buf lint

# Check breaking changes against main branch
cd proto && buf breaking --against .git#main
```

## Available Scripts

No Python scripts — this is a pure configuration skill. All operations use `buf` CLI commands directly.

## Available Analyses

### Service Definition Pattern

Each service file follows this structure:
- **Imports**: `google/api/annotations.proto`, `google/api/field_behavior.proto`, `google/api/resource.proto`
- **RPC methods**: Annotated with `google.api.http` for RESTful HTTP mapping
- **Method signatures**: Documented with `google.api.method_signature`

### Resource Definition Pattern

Resources use `google.api.resource` for REST naming:
```protobuf
option (google.api.resource) = {
  type: "memos.api.v1/Memo"
  pattern: "memos/{memo}"
  name_field: "name"
};
```

### Field Behavior Annotations

| Annotation | Usage |
|---|---|
| `REQUIRED` | Field is required |
| `OPTIONAL` | Field is optional |
| `OUTPUT_ONLY` | Field is server-generated |
| `IDENTIFIER` | Field is the resource identifier |

## Error Handling

- **Missing proto files**: Server implementations cannot import generated types and will fail to compile. Always create proto definitions first.
- **buf generate fails**: Check `buf.yaml` deps are resolved (`buf dep update`) and `buf.gen.yaml` plugins are accessible.
- **Lint errors**: Run `buf lint` to see specific violations. Common fixes: add `STATE_UNSPECIFIED = 0` as first enum value, use `google.api.field_behavior` annotations.

## Gotchas

- **Proto files must be created before server code.** Without generated Go types, server implementations will not compile. The dependency chain is: proto → generate → store → server.
- **`go_package` must be set** in each `.proto` file via `option go_package = "gen/api/v1"`. Without it, `buf generate` produces code in the wrong import path.
- **The `buf.gen.yaml` remote plugins** require network access on first run. If behind a firewall, pre-cache plugins or use local plugin binaries.
- **gRPC-Gateway annotations** require `google/api/annotations.proto` in deps. The `buf.yaml` already declares `buf.build/googleapis/googleapis` — do not duplicate it.
- **Enum first value must be 0** per protobuf spec. Always start enums with `*_UNSPECIFIED = 0`.
- **Generated code lives in `gen/`** which is gitignored. Run `buf generate` after cloning or pulling.

## Keywords for Detection

**Entities**: proto, protobuf, buf, gRPC, Connect RPC, gRPC-Gateway, OpenAPI
**Metrics**: code generation, lint, breaking changes, HTTP annotations
**Actions**: generate, lint, define, annotate, configure, create proto

**Activation examples:**
- "Create a new proto service for user management"
- "Add gRPC-Gateway HTTP annotations to the memo service"
- "Run buf generate to regenerate Go code"
- "Configure buf.gen.yaml with new plugins"

**Does NOT activate for:**
- Writing Go server handler implementations
- Defining database store interfaces
- General Go code style questions

## Usage Examples

### Example 1: Create MemoService Proto

**Question**: "Create a proto definition for a MemoService with CRUD operations"

**Generated proto** (`proto/api/v1/memo_service.proto`):
```protobuf
syntax = "proto3";
package memos.api.v1;
import "google/api/annotations.proto";
import "google/api/field_behavior.proto";
option go_package = "gen/api/v1";

service MemoService {
  rpc CreateMemo(CreateMemoRequest) returns (Memo) {
    option (google.api.http) = { post: "/api/v1/memos" body: "memo" };
    option (google.api.method_signature) = "memo";
  }
  rpc GetMemo(GetMemoRequest) returns (Memo) {
    option (google.api.http) = { get: "/api/v1/{name=memos/*}" };
    option (google.api.method_signature) = "name";
  }
}
```

**Then run**: `cd proto && buf generate`

### Example 2: Add a New Dependency

**Question**: "I need to import the `google/api/field_behavior.proto` annotations"

**Check**: Already declared in `buf.yaml` deps (`buf.build/googleapis/googleapis`). Just add the import in your `.proto` file.

### Example 3: Lint Proto Files

**Question**: "Check if my proto files pass lint"

**Command**: `cd proto && buf lint`

**Fix common errors**:
- `ENUM_VALUE_PREFIX` — prefix enum values with `ENUM_NAME_`
- First enum value must be `0`

## References

| File | When to read it |
|---|---|
| `references/buf-plugins.md` | Complete list of buf plugins and their purpose |
| `references/proto-patterns.md` | Extended message patterns, pagination, resource references |
| `references/troubleshooting.md` | Common buf errors and fixes |
