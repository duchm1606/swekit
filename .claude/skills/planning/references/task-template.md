# Task Template

## Single Task

````markdown
### Task {N}.{M}: {Specific Action}

**Story:** Story {N} - {story name}
**Creates:** {What this task produces}

**Files:**
- Create: `exact/path/to/new_file.go`
- Modify: `exact/path/to/existing.go:45-60`
- Test: `exact/path/to/file_test.go`

**Steps:**

- [ ] **Step 1: Write failing test**

```go
func TestSpecificBehavior(t *testing.T) {
    // Arrange
    input := &Input{Field: "value"}
    
    // Act
    result, err := Function(input)
    
    // Assert
    require.NoError(t, err)
    assert.Equal(t, expected, result)
}
```

- [ ] **Step 2: Run test, verify failure**

```bash
go test ./path/to/package/... -run TestSpecificBehavior -v
```

Expected output:
```
--- FAIL: TestSpecificBehavior (0.00s)
    file_test.go:15: undefined: Function
FAIL
```

- [ ] **Step 3: Implement minimal code**

```go
// exact/path/to/new_file.go
package mypackage

func Function(input *Input) (*Result, error) {
    return &Result{
        Field: input.Field,
    }, nil
}
```

- [ ] **Step 4: Run test, verify pass**

```bash
go test ./path/to/package/... -run TestSpecificBehavior -v
```

Expected output:
```
=== RUN   TestSpecificBehavior
--- PASS: TestSpecificBehavior (0.00s)
PASS
```

- [ ] **Step 5: Commit**

```bash
git add exact/path/to/new_file.go exact/path/to/file_test.go
git commit -m "feat(package): add Function for specific behavior"
```
````

---

## No Placeholders Rule

### Bad Examples

```markdown
- [ ] Implement the handler logic
- [ ] Add appropriate validation
- [ ] Handle edge cases
- [ ] Similar to Task 1.2
- [ ] Write tests for the above
- [ ] Add error handling as needed
```

### Good Examples

```markdown
- [ ] **Step 3: Implement handler**
```go
func (h *Handler) Create(ctx context.Context, req *CreateRequest) (*Response, error) {
    if req.Name == "" {
        return nil, errors.New("name is required")
    }
    
    entity := &Entity{
        ID:   uuid.New(),
        Name: req.Name,
    }
    
    if err := h.repo.Save(ctx, entity); err != nil {
        return nil, fmt.Errorf("save entity: %w", err)
    }
    
    return &Response{ID: entity.ID.String()}, nil
}
```
```

---

## Step Granularity

Each step should take **2-5 minutes**.

| Too Big | Right Size |
|---------|------------|
| "Implement the service" | "Write failing test for Create method" |
| "Add all tests" | "Run test, verify failure" |
| "Set up the module" | "Create file with package declaration" |

---

## File Path Rules

| Bad | Good |
|-----|------|
| `tests/` | `internal/auth/handler_test.go` |
| `src/handlers` | `internal/api/handlers/user.go:45-60` |
| "appropriate location" | `cmd/server/main.go` |
| "the test file" | `pkg/service/user_test.go` |

---

## Command Rules

Always include expected output:

```bash
# Command
go test ./internal/auth/... -v

# Expected output (success)
=== RUN   TestAuthHandler_Login
--- PASS: TestAuthHandler_Login (0.01s)
PASS
ok      myproject/internal/auth    0.015s

# Expected output (failure)
--- FAIL: TestAuthHandler_Login (0.00s)
    handler_test.go:25: expected token, got empty string
FAIL
```
