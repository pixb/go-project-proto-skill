# Proto Message Patterns

Extended patterns for protobuf message definitions in the memos project.

## Pagination Pattern

Standard paginated list requests/responses:

```protobuf
message ListMemosRequest {
  // Page size. Max 100.
  int32 page_size = 1 [(google.api.field_behavior) = OPTIONAL];

  // Page token from a previous request.
  string page_token = 2 [(google.api.field_behavior) = OPTIONAL];

  // Sort order. Format: "field_name asc|desc".
  string order_by = 4 [(google.api.field_behavior) = OPTIONAL];

  // Filter expression. Syntax: "field_name operator value".
  string filter = 5 [(google.api.field_behavior) = OPTIONAL];
}

message ListMemosResponse {
  repeated Memo memos = 1;
  string next_page_token = 2;
}
```

## Resource Pattern

Standard resource definition with `google.api.resource`:

```protobuf
message Memo {
  option (google.api.resource) = {
    type: "memos.api.v1/Memo"
    pattern: "memos/{memo}"
    name_field: "name"
    singular: "memo"
    plural: "memos"
  };

  // Resource name: "memos/{memo_id}"
  string name = 1 [(google.api.field_behavior) = IDENTIFIER];

  // Content
  string content = 7 [(google.api.field_behavior) = REQUIRED];

  // Creator (output only)
  string creator = 3 [
    (google.api.field_behavior) = OUTPUT_ONLY,
    (google.api.resource_reference) = {type: "memos.api.v1/User"}
  ];

  // Timestamps (output only)
  google.protobuf.Timestamp create_time = 4 [(google.api.field_behavior) = OUTPUT_ONLY];
  google.protobuf.Timestamp update_time = 5 [(google.api.field_behavior) = OUTPUT_ONLY];
}
```

## Common Enum Pattern

Always start with `UNSPECIFIED = 0`:

```protobuf
enum State {
  STATE_UNSPECIFIED = 0;
  NORMAL = 1;
  ARCHIVED = 2;
}

enum Visibility {
  VISIBILITY_UNSPECIFIED = 0;
  PRIVATE = 1;
  ACTIVE = 2;
  ARCHIVED = 3;
}
```

## Update Mask Pattern

For partial updates:

```protobuf
message UpdateMemoRequest {
  Memo memo = 1 [(google.api.field_behavior) = REQUIRED];

  // Field mask specifying which fields to update.
  google.protobuf.FieldMask update_mask = 2 [
    (google.api.field_behavior) = REQUIRED
  ];
}
```

## Resource Name Pattern

Resource names follow the pattern `type/{id}` or `type/{parent}/type/{id}`:

```protobuf
// Simple resource name
string name = 1;  // "memos/{memo_id}"

// Nested resource name
string parent = 1;  // "users/{user_id}"
```

## Import Conventions

```protobuf
import "google/api/annotations.proto";       // HTTP annotations
import "google/api/client.proto";            // Client methods
import "google/api/field_behavior.proto";    // REQUIRED, OPTIONAL, OUTPUT_ONLY, IDENTIFIER
import "google/api/resource.proto";          // Resource definitions
import "google/api/resource_reference.proto"; // Cross-resource references
import "google/protobuf/empty.proto";        // Empty messages
import "google/protobuf/field_mask.proto";   // Field masks
import "google/protobuf/timestamp.proto";    // Timestamps
```

## HTTP Method Conventions

```protobuf
// Create: POST
option (google.api.http) = {
  post: "/api/v1/memos"
  body: "memo"
};

// Read: GET
option (google.api.http) = {
  get: "/api/v1/{name=memos/*}"
};

// Update: PATCH
option (google.api.http) = {
  patch: "/api/v1/{memo.name=memos/*}"
  body: "memo"
};

// Delete: DELETE
option (google.api.http) = {
  delete: "/api/v1/{name=memos/*}"
};

// List: GET
option (google.api.http) = {
  get: "/api/v1/memos"
};
```
