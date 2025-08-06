---
allowed-tools: Bash(git:*), Bash(mkdir), Read, Grep
description: Implementation task executor using unified artisan-impl approach - processes each top-level task with high-quality code generation and automatic progress tracking
---

## Phase 1: Check Requirements and Load Task File

1. Parse arguments `$ARGUMENTS` to extract task file name (required)
2. If task file name is not provided, exit with error "No task file specified"
3. Load `docs/specs/{task_file_name}` task file, if it exists. If not, exit with error "Specs file not found"

## Phase 2: Parse Task Organization and Execute Per Top-level Task

### Parse Available Tasks
Analyze the task file structure to identify all top-level tasks under "## 5. Task Decomposition & Implementation Directives" section:
- Find all "### Task Group X:" entries where X is a number
- Extract task titles, related files, and purposes
- Determine execution order based on task dependencies and file relationships

### Execute Each Top-level Task Separately
For EACH top-level task group found (Task Group 1, Task Group 2, etc.):

Use the **artisan-impl** sub agent to:
1. Implement [current task specification requirements] with unified implementation and validation approach
2. Automatically update task checkboxes in the specification file
3. Provide consolidated implementation and progress report

The artisan-impl agent handles BOTH implementation AND progress tracking in a single atomic operation, ensuring consistency between code state and project tracking.

## Phase 3: Final Validation

After all individual task groups are completed:
- Verify overall project completion status in specification file
- Generate final implementation summary if all tasks complete
- Report any tasks that remain incomplete or failed

## 🔄 Streamlined Workflow

```
Main Agent → impl-task command
    ↓
impl-task → artisan-impl sub-agent
    ↓
artisan-impl → Implements task AND updates progress atomically
    ↓
Returns unified report with implementation + tracking confirmation
    ↓
impl-task → Next task group or completion
```

## Usage

```bash
# Execute implementation with automatic progress tracking
/impl-task user_management_tasks

# Process another specification file
/impl-task authentication_tasks
