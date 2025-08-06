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
- Chat with other model perfer using English language

**Optimization (`-zen`):**
If present, collect relevant file content and original PRD first, then:
- **o3** (default): Use `mcp__zen` chat method with full file paths in `files` parameter to optimize task specification structure
- **pro**: Use `gemini -p ”<prompt>“` bash command with file paths as arguments

**Review (`-review`):**
- **pro** (default): Use `gemini -p "<prompt>“` bash command for task specification review
- **o3**: Use `mcp__zen` chat method with full file paths in `files` parameter

**o3 Model Collaboration Process (-zen parameter)**:
1. Collect project-related file content using Glob and Read tools
2. Organize original PRD content
3. Discuss with o3 model via mcp__zen chat:
   - Task decomposition rationality
   - Interface design best practices
   - Technical architecture recommendations
   - Implementation priority suggestions
4. Optimize task specification structure based on o3 feedback

**gemini-pro Review Process (-review parameter)**:
1. Send generated task specification to gemini-pro for review
2. Collect review feedback:
   - Logic consistency check
   - Completeness verification
   - Feasibility assessment
   - Risk identification
3. Final optimization based on feedback

### Step 3: Specification Document Generation

**Guiding Principle**: Group tasks logically by shared functional modules and file dependencies. Each `Task Group` must represent a cohesive unit of work that a single sub-agent can execute efficiently with a focused context. Consolidate all sub-tasks that modify the same set of core files into one group to ensure context integrity.

**Execution Process**:
1. Run `date "+%Y_%m_%d"` to get current date
2. Create output file: `docs/specs/{YYYY_MM_dd}_{task_name}.md`
3. Generate comprehensive specification-driven task document
4. Group tasks by shared files and specification context
5. Wait for review approval before proceeding with implementation

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
    - **Instructions**: {Detailed, step-by-step implementation guidance, specifying files, frameworks, and methods to use}
    - **Objective**: {Describe the desired state or deliverable after this sub-task is complete. What should be achieved or created?}
    - **Acceptance Criteria**:
        - [ ] {A specific, testable standard for verification}
        - [ ] {Another testable standard}

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
3. **Model Collaboration**: If `-zen` is present, collaborate with o3 model before generation
4. **Generate Task Document**: Create comprehensive task specification
5. **Review Process**: If `-review` is present, review with gemini-pro model after generation
6. **Save Output**: Write final document to `docs/specs/{YYYY_MM_dd}_{task_name}.md`

## Special Notes

1. **Doc Output**: All generated task specification content uses Chinese, professional nouns are reserved in English
2. **No Test Tasks**: No test-related tasks in development specifications, as dedicated agents handle review and testing
3. **Model Collaboration**: Intelligently invoke o3 and gemini-pro models for collaborative optimization based on parameters
4. **Version Control**: Task specifications are managed as part of code versioning
5. **Human-AI Collaboration**: Clearly distinguish human input, AI generation, and collaborative review sections

## Output File Naming Convention
`docs/specs/{YYYY_MM_dd}_{feature_description}.md`

Example: `docs/specs/2025_01_15_user_settings_management.md`
