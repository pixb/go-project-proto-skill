# Troubleshooting

Common buf and protobuf errors and how to fix them.

## buf generate Fails

### Error: `module not found`

**Cause**: Missing dependency in `buf.yaml`.

**Fix**:
```bash
cd proto && buf dep update
```

### Error: `plugin "buf.build/..." not found`

**Cause**: Remote plugin not accessible (firewall, network).

**Fix**:
```bash
# Option 1: Use local plugin binary
go install connectrpc.com/connect/cmd/protoc-gen-connect-go@latest
# Then use local plugin in buf.gen.yaml instead of remote

# Option 2: Pre-cache plugins
buf generate --disable-symlinks
```

### Error: `go_package must be set`

**Cause**: Missing `option go_package` in `.proto` file.

**Fix**:
```protobuf
option go_package = "gen/api/v1";
```

## buf lint Fails

### Error: `ENUM_VALUE_PREFIX`

**Cause**: Enum values not prefixed with enum name.

**Fix**: Either prefix values or add to `buf.yaml` exceptions:
```yaml
lint:
  except:
    - ENUM_VALUE_PREFIX
```

### Error: `First value of enum must be 0`

**Cause**: Protobuf spec requires first enum value to be 0.

**Fix**:
```protobuf
enum State {
  STATE_UNSPECIFIED = 0;  // Always first
  NORMAL = 1;
}
```

## buf breaking Fails

### Error: `FIELD_SAME_DEFAULT`

**Cause**: Field default value changed.

**Fix**: Add to `buf.yaml` exceptions:
```yaml
breaking:
  except:
    - FIELD_SAME_DEFAULT
```

## Generated Code Issues

### Error: `cannot use proto as type *v1.Memo`

**Cause**: Import path mismatch.

**Fix**: Ensure import matches `go_package`:
```go
import v1pb "github.com/usememos/memos/gen/api/v1"
```

### Error: `undefined: proto.XXX`

**Cause**: Proto file not generated.

**Fix**:
```bash
cd proto && buf generate
# Check output exists in gen/api/v1/
```

## gRPC-Gateway Issues

### Error: `annotations.proto not found`

**Cause**: Missing `googleapis` dependency.

**Fix**: Already in `buf.yaml` deps. Run:
```bash
cd proto && buf dep update
```

### Error: HTTP routes not working

**Cause**: Missing or incorrect `google.api.http` annotations.

**Fix**: Verify each RPC method has the annotation:
```protobuf
rpc GetMemo(GetMemoRequest) returns (Memo) {
  option (google.api.http) = {
    get: "/api/v1/{name=memos/*}"
  };
}
```
