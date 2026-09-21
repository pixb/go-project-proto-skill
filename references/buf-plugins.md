# Buf Plugin Inventory

Complete list of buf plugins used in this project and their purpose.

## Code Generation Plugins (`buf.gen.yaml`)

| Plugin | Remote Path | Purpose | Output |
|---|---|---|---|
| protobuf/go | `buf.build/protocolbuffers/go` | Generates Go structs from `.proto` messages | `*_pb.go` |
| grpc/go | `buf.build/grpc/go` | Generates gRPC client/server stubs | `*_grpc.go` |
| connectrpc/go | `buf.build/connectrpc/go` | Generates Connect RPC handlers | `*_connect.go` |
| grpc-ecosystem/gateway | `buf.build/grpc-ecosystem/gateway` | Generates gRPC-Gateway reverse proxy | `*.pb.gw.go` |
| google-gnostic-openapi | `buf.build/community/google-gnostic-openapi` | Generates OpenAPI spec from proto | `openapi.yaml` |
| bufbuild/es | `buf.build/bufbuild/es` | Generates TypeScript definitions for web | `../web/src/types/proto/*.ts` |

## Lint Rules (`buf.yaml`)

| Rule Set | Purpose |
|---|---|
| BASIC | Default lint rules for proto files |

### Excluded Lint Rules

| Rule | Why Excluded |
|---|---|
| `ENUM_VALUE_PREFIX` | Project uses short enum names (e.g., `NORMAL` not `STATE_NORMAL`) |
| `FIELD_NOT_REQUIRED` | Using `google.api.field_behavior` annotations instead |
| `PACKAGE_DIRECTORY_MATCH` | Package structure doesn't match directory layout |
| `PACKAGE_NO_IMPORT_CYCLE` | Allows circular imports within `memos.api.v1` |
| `PACKAGE_VERSION_SUFFIX` | Package names don't include version suffix |

## Breaking Change Rules

| Rule Set | Purpose |
|---|---|
| FILE | Checks for wire-compatible breaking changes per file |

### Excluded Breaking Rules

| Rule | Why Excluded |
|---|---|
| `EXTENSION_NO_DELETE` | Allows removing extensions |
| `FIELD_SAME_DEFAULT` | Allows changing field defaults |

## Dependency Management

```bash
# Add/update dependencies
cd proto && buf dep update

# Dependencies declared in buf.yaml:
# - buf.build/googleapis/googleapis (for google/api/* annotations)
```

## Installing Buf

```bash
# macOS
brew install bufbuild/buf/buf

# Go
go install github.com/bufbuild/buf/cmd/buf@latest

# npm
npm install -g @bufbuild/buf

# Docker
docker pull bufbuild/buf:latest
```
