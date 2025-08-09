---
description: Generate development task documentation from PRD files with optional AI model collaboration for optimization and review
argument-hint: [-zen [o3|pro]] [-review [o3|pro]]
allowed-tools: Read, Write, Bash, Grep, Glob, mcp__zen
---

# PRD Development Task Documentation Generator

## Core Workflow

This command generates specification task documents from PRD files. Implementation work begins only after document review and approval.

## Usage Examples

```bash
/prd-tasks                    # Basic generation
/prd-tasks -zen              # Optimize with o3 (default)
/prd-tasks -zen pro          # Optimize with gemini-pro
/prd-tasks -review           # Review with gemini-pro (default)
/prd-tasks -review o3        # Review with o3
/prd-tasks -zen -review      # Optimize with o3, review with gemini-pro
/prd-tasks -zen pro -review o3  # Optimize with gemini-pro, review with o3
```

### Step 1: Requirements Analysis & Understanding

**Execution Actions**:
1. Read and parse PRD document from `docs/specs/prd.txt`
2. Parse all requirements in PRD into implementable specifications
3. Identify interface contracts and system dependencies
4. Extract acceptance criteria, workflows, and integration requirements
5. Map specification implementation needs
6. Check available tools and resources
7. Group tasks by shared specification context and file dependencies

### Step 2: Parameter Processing & Model Collaboration

**Tips**:
- `Bash(gemini -p "<prompt>")`: gemini can be used to generate code snippets, documentation, and other content based on the provided prompt and file paths.
- Chat with other model using English

**Optimization (`-zen`):**
If present, collect relevant file content and original PRD first, then:
- **o3** (default): Use `mcp__zen` chat method with full file paths in `files` parameter to optimize task specification structure
- **pro**: Use `gemini -p ”<prompt>“` bash command with file paths as arguments

**Review (`-review`):**
- **pro** (default): Use `gemini -p "<prompt>“` bash command for task specification review
- **o3**: Use `mcp__zen` chat method with full file paths in `files` parameter

**Collaboration Process (-zen parameter)**:
1. Collect project-related file content using Glob and Read tools
2. Organize original PRD content
3. **Prepare task structuring context**:
  - Include complete "Task Group Structuring & Splitting Guidelines"
  - Provide "Development Task Specification Template Structure"
  - Share current project file structure and dependencies
4. Discuss with o3 model or gemini chat:
  - Task decomposition rationality **according to grouping guidelines**
  - Validate each Task Group represents a self-contained functional module
  - Ensure all changes to the same module are in the same Task Group
  - Check sub-task count (2-4 per group) and naming conventions
  - Interface design best practices
  - Technical architecture recommendations
  - Implementation priority suggestions
5. Optimize task specification structure based on o3 feedback

**Review Process (-review parameter)**:
1. Send generated task specification to gemini or o3 model for review
2. Collect review feedback:
   - Logic consistency check
   - Completeness verification
   - Feasibility assessment
   - Risk identification
3. Final optimization based on feedback

### Step 3: Specification Document Generation

**Execution Process**:
1. Run `date "+%Y_%m_%d"` to get current date
2. Create output file: `docs/specs/{YYYY_MM_dd}_{task_name}.md`
3. Generate comprehensive specification-driven task document
4. Group tasks by `Task Group Structuring & Splitting Guidelines` (including operation batching rules)
5. Wait for review approval before proceeding with implementation

## Task Group Structuring & Splitting Guidelines

### 1. Top-Level Grouping Principle
1. **Group by Functional Domain & Code Boundary**
   - A top-level Task Group **must** represent a self-contained functional module impacting a distinct set of core files.
   - All changes to the same module **must** be in the same Task Group, even if they address different logical concerns.
   - If two tasks touch the same set of files, merge them into one Task Group to keep context unified.
   **✅ Example**:
     - `Auth Module Route & Service Refactor`
     - `Speaking Module Refactor`
     - `Object Storage Module Refactor`
   **❌ Avoid** phase-based or narrative groups like "Phase 1: Setup" or "Pilot Refactoring".

2. **Purpose of Top-Level Groups**
   - Each group should be independently completable and testable.
   - Groups should be organized to allow **parallel execution** without excessive cross-group dependencies.

### 2. Sub-Task Splitting Rules
1. **Optimal Size**: Adapt task group overall goal to reasonably divide the number of sub-tasks.
   - Each sub-task should be a complete, small unit of work that contributes to delivering the feature.
   - Keep sub-tasks small enough for clear acceptance criteria, but large enough to avoid micro-tasks.
   - **CRITICAL**: Merge similar operations on multiple files into ONE sub-task to avoid redundant processing.

2. **Operation Batching**
   - **Merge similar operations**: When multiple files need identical operations, combine them into a single sub-task.
      **✅ Example**:
        - `Refactor all admin route files to direct service instantiation`
        - `Migrate all speaking module routes and services`
      **❌ Avoid**:
        - `Refactor admin/users.ts`
        - `Refactor admin/system.ts` (separate sub-task for same operation)
  - **File Operation Batching Rules**:
      - If refactoring pattern is identical across files → single sub-task listing all files
      - If each file needs unique logic → separate sub-tasks
      - Instructions should specify: "Apply to files: `file1.ts`, `file2.ts`, `file3.ts`"

3. **Deliverable-Focused Naming**
   - Name sub-tasks after the **outcome**, not the action steps.
   **✅ Example**:
     - `Migrate all DB queries in auth.ts to AuthService`
     - `Controllerize auth.ts routes`
   **❌ Avoid**:
     - `Analyze route file`
     - `Refactoring preparation`

4. **Logical Order** (if dependencies exist)
   - Within a Task Group, sequence sub-tasks so earlier tasks unblock later ones.
   - Example sequence: Logic Migration → Interface Update → Edge Case Fix.


### 3. Acceptance Criteria Standard
1. **Integrate Testing & QA Into Each Task**
   - Do **not** create separate Task Groups for “Testing” or “Documentation”.
   - Add testing, quality, and type safety requirements into each sub-task’s **Acceptance Criteria**.
2. **Mandatory Items for Development Tasks**:
   - [ ] All database access is moved to the service layer
   - [ ] API request/response types comply with `shared` module definitions
   - [ ] Unit tests for all new/changed service methods
   - [ ] Integration tests for all updated routes
   - [ ] TypeScript passes strict type checks
   - [ ] Test coverage ≥ 80%
   - [ ] Passes ESLint architectural rules with no violations

### 4. Naming Conventions
1. **Top-Level Group Names** = `{Module Name} + {Action}`
   **✅** `Auth Module Route Controllerization`
   **✅** `Speaking Practice Refactor`
   **❌** `First Stage of Migration`
2. **File & Method Naming**: Follow existing codebase conventions (kebab-case for files, PascalCase for service classes, camelCase for methods).

### 5. Prohibited Practices
- ❌ Splitting by project phase (Foundation, Pilot, Cleanup) at top-level.
- ❌ Having “Testing Only” or “Documentation Only” groups.
- ❌ Creating micro-tasks with vague outcomes (“Investigate X” without a deliverable).
- ❌ Separating related file changes into different Task Groups.

## Development Task Specification Template Structure

Generate task documents using this structure:

```markdown
---
# YAML Front Matter: Provides structured metadata for AI
project: "{project_name}"
taskID: "{unique_task_id}"
version: 1.0
status: "DRAFT" # DRAFT | PENDING_REVIEW | APPROVED | IN_PROGRESS
author: "prd-task-doc"
rawPRD: "{raw PRD}"
---

# AI Development Task Specification: {task_title}

## 1. Core Intent
> **[Human Input]** This section describes business objectives and user value

**Business Objectives**:
- {Core business objectives extracted from PRD}

**Success Criteria**:
- {Measurable success metrics defined from PRD}


## 2. Context & Boundaries

- **Primary Impact Scope**:
  - **Codebase**: {GitHub repository link or project path}
  - **Core Files/Modules**:
    - `{file_path}` - (Purpose: {file function description})
    - `{file_path}` - (Purpose: {file function description})

- **Prohibited Modification Scope**:
  - `{path}/**` - (Reason: {reason for prohibition})


## 3. Visual Logic Models (Optional & Extensible)
> Only include diagrams that **directly help implementation** or **resolve ambiguity**.
> If no diagram is necessary, write: “N/A – logic is trivial or already covered by code.”

### 3.x {Diagram Type} (e.g., State Machine / ER / Deployment / User Journey)
- **Purpose**: {一句话说明这张图解决什么问题}
- **Diagram**:
```mermaid
{Mermaid code here}
```
- **Notes**: {任何补充说明，例如“仅覆盖核心状态，异常状态见代码注释”}


## 4. Interface & Data Definitions (Pick One or Both as Needed)

### 4.1 External API (if any)
- **Protocol**: {HTTP/gRPC/GraphQL/…}
- **Short Description**: {1-2 句话说明用途}
- **Contract Source**:
  - Option A – Link: `{link_to_existing_OpenAPI_or_proto_file}`
  - Option B – Inline: `{minimal table or bullet list of endpoint, method, brief params}`
- **Notes**: {任何特殊鉴权、分页、速率限制等}

### 4.2 Internal Data Shapes (if not obvious from the code)
- Source of truth: Link to the relevant file, e.g. src/models/order.go or db/migrations/001_orders.sql.
- Additional notes: Short sentences covering critical fields, allowed enum values, nullability, etc.
- Example:
  - Field status accepts only pending, paid, shipped, or cancelled; defaults to pending.

If there is nothing to add, simply write:
No additional interface or data definitions are required; refer to the source code.


## 5. Task Decomposition & Implementation Directives

### Task Group 1: {functional_module_name}
**Purpose**: {High-level goal for this functional module and why it's grouped this way}
**Related Files**: `{file_path1}`, `{file_path2}` (List all core files this group will modify or reference)
**Requirements**: {Brief specification description being implemented by this group}

- **[ ] 1.1: {specific_sub_task_title}**
    - **Input**: {Clear input parameters, data, or preconditions for this specific sub-task}
    - **Instructions**:
      1. {Step-by-step implementation guidance}
      2. {For multiple files: "Apply to all files in: `dir/*.ts`" or "Process files: `file1.ts`, `file2.ts`, `file3.ts`"}
      3. {Specify frameworks, patterns, and methods to use}
    - **Objective**: {Describe the desired state or deliverable after this sub-task is complete. What should be achieved or created?}
    - **Acceptance Criteria**:
        - [ ] {A specific, testable standard for verification}
        - [ ] {Another testable standard}
        - [ ] {For multi-file tasks: "All specified files processed successfully"}

- **[ ] 1.2: {another_feature_implementation}**
    - **Input**: {Clear input parameters, data, or preconditions for this specific sub-task}
    - **Instructions**: {Detailed, step-by-step implementation guidance, specifying files, frameworks, and methods to use}
    - **Objective**: {Describe the desired state or deliverable after this sub-task is complete. What should be achieved or created?}
    - **Acceptance Criteria**:
        - [ ] {A specific, testable standard for verification}
        - [ ] {Another testable standard}

### Task Group 2: {another_functional_module}
{Continue with same structure...}


## 6. Implementation Constraints & Guidelines

- **Technology Stack**: {Specified libraries/frameworks}
- **Performance Requirements**: {Non-functional requirements}
- **Coding Standards**: {Design patterns or coding style}
- **Error Handling**: {Exception handling guidelines}


## 7. Review Checklist
> **[Human Review]** All items must be checked before status changes to APPROVED

- [ ] **Logic Consistency**: Are Mermaid diagrams completely consistent with task decomposition logic?
- [ ] **Contract Accuracy**: Do interface and type definitions precisely reflect PRD requirements?
- [ ] **Implementability**: Are acceptance criteria for each subtask clear, specific, and verifiable?
- [ ] **Boundary Completeness**: Are all boundary conditions and exception cases considered?
- [ ] **Scope Compliance**: Does implementation plan strictly adhere to defined scope?
- [ ] **Task Group Division**: Does the task group division comply with the standards (merging related files into one task group)?

## Implementation Progress Tracking
- **Requirements Coverage**: [To Implement/Total Requirements]
- **Interface Contracts**: [To Complete/Total Contracts]
- **Acceptance Criteria**: [To Meet/Total Criteria]
- **Specification Compliance**: [To Verify/Total Specifications]
```

## Execution Instructions

Based on the provided arguments ($ARGUMENTS), execute the appropriate workflow:

1. **Read PRD**: Always start by reading `docs/specs/prd.txt`
2. **Parameter Processing**: Check for `-zen` and/or `-review` parameters
3. **Model Collaboration**: If `-zen` is present, collaborate with o3 model or gemini before generation
4. **Generate Task Document**: Create comprehensive task specification
5. **Review Process**: If `-review` is present, review with gemini or o3 model after generation
6. **Save Output**: Write final document to `docs/specs/{YYYY_MM_dd}_{task_name}.md`

## Language Guidelines
- **Chinese**: Business logic description, requirements specification, implementation description
- **English**:
  - Technical terms (API, REST, GraphQL)
  - File/folder paths
  - Code snippets and variable names
  - Framework/library names
- **Mixed Example**: "将所有 `auth.ts` 中的数据库查询迁移到 `AuthService` 服务层"
