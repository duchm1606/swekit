# Root Cause Tracing

Bugs manifest deep in call stack. Your instinct is to fix where error appears — that's treating a symptom.

**Core principle:** Trace backward through call chain until you find original trigger. Fix at source.

---

## When to Use

- Error happens deep in execution
- Stack trace shows long call chain
- Unclear where invalid data originated
- Need to find which code triggers problem

---

## The Process

### 1. Observe Symptom

```
Error: git init failed in /Users/project/src
```

### 2. Find Immediate Cause

```go
exec.Command("git", "init").Run() // cwd = projectDir
```

### 3. Ask: What Called This?

```
InitRepo(projectDir)
  ← CreateWorkspace(config)
  ← Setup(opts)
  ← main()
```

### 4. Trace the Value

```
projectDir = "" (empty!)
  ← config.Dir was never set
  ← opts.Directory not passed
  ← CLI flag missing   ← ROOT CAUSE
```

### 5. Fix at Source

Don't fix `InitRepo` to handle empty string.
Fix CLI to require directory flag.

---

## Adding Stack Traces

When you can't trace manually:

```go
func InitRepo(dir string) error {
    // Add before problematic operation
    log.Printf("DEBUG InitRepo: dir=%q cwd=%q stack=%s",
        dir, os.Getwd(), debug.Stack())
    
    return exec.Command("git", "init").Run()
}
```

**Run and capture:**
```bash
go test ./... 2>&1 | grep "DEBUG InitRepo"
```

---

## Multi-Layer Tracing

For systems with multiple components:

```
Layer 1: Input    → log entry point
Layer 2: Process  → log transformation
Layer 3: Output   → log final state
```

Run once. Find which layer breaks. Investigate that layer.

---

## Example: Empty Directory Bug

**Symptom:** `.git` created in source directory

**Trace:**
1. `git init` runs in `cwd` ← empty cwd parameter
2. `WorkspaceManager` called with empty dir
3. `Session.Create()` passed empty string
4. Test accessed `config.Dir` before init
5. Config returns `""` before setup

**Root cause:** Top-level access before initialization

**Fix:** Make Dir a getter that throws if accessed early

**Defense in depth:**
- Layer 1: Validate dir in CreateWorkspace
- Layer 2: Validate not empty in WorkspaceManager
- Layer 3: Refuse git init outside allowed paths

---

## Key Principle

```
Found immediate cause
    ↓
Can trace one level up?
    ↓ yes
Trace backwards
    ↓
Is this the source?
    ↓ no → keep tracing
    ↓ yes
Fix at source
    ↓
Add validation at each layer
    ↓
Bug impossible
```

**NEVER fix just where error appears.**

---

## Tips

| Context | Approach |
|---------|----------|
| In tests | Use `fmt.Println` or `log.Printf` (logger may be suppressed) |
| Before operation | Log BEFORE dangerous operation, not after failure |
| Include context | Directory, cwd, env vars, timestamps |
| Capture stack | `debug.Stack()` in Go, `new Error().stack` in JS |
