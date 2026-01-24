# Copilot Skills Development Guide

## Repository Overview

This repository contains **Skills** - folders of instructions, scripts, and resources that Claude (AI assistant) loads dynamically to improve performance on specialized tasks. Skills teach Claude how to complete specific tasks in a repeatable way, whether that's creating documents with brand guidelines, analyzing data using specific workflows, or automating tasks.

### Repository Structure

```
copilot-skills/
├── .claude-plugin/           # Claude Code plugin marketplace configuration
│   └── marketplace.json      # Defines plugin bundles (document-skills, example-skills)
├── skills/                   # Individual skill implementations
│   ├── algorithmic-art/      # Creative: p5.js generative art
│   ├── brand-guidelines/     # Enterprise: Anthropic branding
│   ├── canvas-design/        # Creative: Visual design
│   ├── doc-coauthoring/      # Enterprise: Document collaboration
│   ├── docx/                 # Document: Word document creation/editing
│   ├── frontend-design/      # Development: Web interface design
│   ├── internal-comms/       # Enterprise: Internal communications
│   ├── mcp-builder/          # Development: MCP server creation
│   ├── pdf/                  # Document: PDF manipulation
│   ├── pptx/                 # Document: PowerPoint creation/editing
│   ├── skill-creator/        # Meta: Skill development framework
│   ├── slack-gif-creator/    # Creative: GIF generation
│   ├── theme-factory/        # Creative: Theme generation
│   ├── web-artifacts-builder/# Development: Web artifact creation
│   ├── webapp-testing/       # Development: Playwright-based testing
│   └── xlsx/                 # Document: Excel manipulation
├── spec/                     # Agent Skills specification
│   └── agent-skills-spec.md  # Points to agentskills.io
├── template/                 # Template for new skills
│   └── SKILL.md             # Minimal skill template
├── README.md                # Repository documentation
├── .gitignore               # Git ignore rules
└── THIRD_PARTY_NOTICES.md   # License notices

```

## Skill Anatomy

Every skill follows a standard structure:

### Required Component: SKILL.md

The `SKILL.md` file is the **only required component** of a skill. It consists of:

#### 1. YAML Frontmatter (Required)

```yaml
---
name: skill-name
description: Complete description of what the skill does and when to use it
---
```

**Required Fields:**
- `name`: Unique identifier (hyphen-case, lowercase, max 64 chars)
- `description`: Comprehensive description (max 1024 chars) - **CRITICAL for skill triggering**

**Optional Fields:**
- `license`: License information
- `allowed-tools`: Tool restrictions
- `metadata`: Additional metadata

**Naming Convention:**
- Use hyphen-case (e.g., `my-skill-name`)
- Only lowercase letters, digits, and hyphens
- No leading/trailing hyphens or consecutive hyphens
- Maximum 64 characters

**Description Best Practices:**
- Include WHAT the skill does
- Include WHEN to use it (specific triggers/contexts)
- Include file types, tasks, or scenarios that trigger it
- This is the primary mechanism for skill activation
- Information in the body is only loaded AFTER triggering

#### 2. Markdown Body (Required)

The instructions, examples, and guidelines that Claude follows when the skill is active.

**Structure Options:**

1. **Workflow-Based** (sequential processes)
   - Best for: Step-by-step procedures
   - Example: DOCX skill (Workflow Decision Tree → Reading → Creating → Editing)
   - Structure: Overview → Decision Tree → Step 1 → Step 2...

2. **Task-Based** (tool collections)
   - Best for: Different operations/capabilities
   - Example: PDF skill (Quick Start → Merge → Split → Extract)
   - Structure: Overview → Quick Start → Task 1 → Task 2...

3. **Reference/Guidelines** (standards/specifications)
   - Best for: Brand guidelines, coding standards
   - Example: Brand guidelines (Overview → Colors → Typography → Features)
   - Structure: Overview → Guidelines → Specifications → Usage...

4. **Capabilities-Based** (integrated systems)
   - Best for: Interrelated features
   - Example: Product management (Overview → Core Capabilities → Features)
   - Structure: Overview → Capabilities → Feature 1 → Feature 2...

### Optional Components: Bundled Resources

#### scripts/ Directory
**Purpose:** Executable code (Python/Bash/etc.) for specific operations

**When to include:**
- Same code is repeatedly rewritten
- Deterministic reliability is needed
- Complex operations benefit from automation

**Examples:**
- `pdf/scripts/fill_fillable_fields.py` - PDF form manipulation
- `pdf/scripts/convert_pdf_to_images.py` - PDF conversion
- `docx/scripts/document.py` - Document processing modules

**Benefits:**
- Token efficient (can execute without loading into context)
- Deterministic behavior
- Reusable across tasks

**Note:** Scripts can still be read by Claude for debugging or environment adjustments

#### references/ Directory
**Purpose:** Documentation and reference material loaded into context as needed

**When to include:**
- Detailed documentation too large for SKILL.md
- Information only needed for specific use cases
- Content that benefits from progressive disclosure

**Examples:**
- `mcp-builder/reference/mcp_best_practices.md` - MCP development guidelines
- `skill-creator/references/workflows.md` - Workflow patterns
- `skill-creator/references/output-patterns.md` - Output formatting patterns

**Best Practices:**
- Keep SKILL.md under 500 lines; split longer content into references
- Include clear references from SKILL.md
- Add table of contents for files >100 lines
- Avoid deeply nested references (keep one level deep)
- Use grep search patterns for large files (>10k words)

**Anti-Pattern:** Duplicating information between SKILL.md and references

#### assets/ Directory
**Purpose:** Files used in the output Claude produces (NOT loaded into context)

**When to include:**
- Files will be copied or modified in final output
- Templates or boilerplate needed
- Images, fonts, or media files required

**Examples:**
- `brand-guidelines/assets/logo.png` - Brand assets
- `frontend-design/assets/templates/` - HTML/React boilerplate
- Typography assets: `.ttf`, `.woff2` font files

**Common Asset Types:**
- Templates: `.pptx`, `.docx`, boilerplate directories
- Images: `.png`, `.jpg`, `.svg`
- Fonts: `.ttf`, `.otf`, `.woff`, `.woff2`
- Boilerplate code: Project directories, starter files
- Data files: `.csv`, `.json`, `.xml`

### What NOT to Include

**Avoid creating these files:**
- README.md (extraneous documentation)
- INSTALLATION_GUIDE.md
- QUICK_REFERENCE.md
- CHANGELOG.md
- Other auxiliary documentation

**Rationale:** Skills should contain ONLY information needed for an AI agent to perform tasks, not user-facing documentation or development history.

## Progressive Disclosure System

Skills use a three-level loading system for context efficiency:

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - When skill triggers (<5k words, <500 lines)
3. **Bundled resources** - As needed by Claude (unlimited for scripts)

### Progressive Disclosure Patterns

**Pattern 1: High-level guide with references**
```markdown
# PDF Processing

## Quick start
Extract text with pdfplumber:
[code example]

## Advanced features
- **Form filling**: See [FORMS.md](FORMS.md)
- **API reference**: See [REFERENCE.md](REFERENCE.md)
- **Examples**: See [EXAMPLES.md](EXAMPLES.md)
```

**Pattern 2: Domain-specific organization**
```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    └── product.md (API usage, features)
```

**Pattern 3: Conditional details**
```markdown
# DOCX Processing

## Creating documents
Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).

## Editing documents
For simple edits, modify XML directly.
**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

## Skill Creation Process

### Overview

The skill creation process follows these steps:

1. **Understand the skill** - Gather concrete examples
2. **Plan reusable contents** - Identify scripts, references, assets
3. **Initialize the skill** - Run `init_skill.py`
4. **Edit the skill** - Implement resources and write SKILL.md
5. **Package the skill** - Run `package_skill.py`
6. **Iterate** - Improve based on usage

### Step 1: Understanding the Skill

**Goal:** Clearly understand concrete examples of how the skill will be used

**Approach:**
- Gather examples from users or generate validated examples
- Ask clarifying questions:
  - "What functionality should this skill support?"
  - "Can you give examples of how this skill would be used?"
  - "What would a user say that should trigger this skill?"
- Avoid overwhelming users with too many questions at once
- Focus on understanding the functionality the skill should support

**Example Questions for Image Editor Skill:**
- What functionality should the image-editor skill support?
- Can you give examples of how this would be used?
- What user requests should trigger this skill?

### Step 2: Planning Reusable Contents

**Goal:** Identify which scripts, references, and assets would be helpful

**Approach:** For each example, consider:
1. How would I execute this from scratch?
2. What reusable resources would help when executing repeatedly?

**Examples:**

**PDF Editor Skill** (Query: "Help me rotate this PDF")
- Analysis: Rotating a PDF requires rewriting the same code each time
- Resource: `scripts/rotate_pdf.py` script

**Frontend Webapp Builder** (Query: "Build me a todo app")
- Analysis: Frontend webapps need the same boilerplate each time
- Resource: `assets/hello-world/` template with HTML/React boilerplate

**BigQuery Skill** (Query: "How many users logged in today?")
- Analysis: Querying BigQuery requires rediscovering schemas each time
- Resource: `references/schema.md` documenting table schemas

### Step 3: Initializing the Skill

**Tool:** `skills/skill-creator/scripts/init_skill.py`

**Usage:**
```bash
python skills/skill-creator/scripts/init_skill.py <skill-name> --path <output-directory>
```

**Examples:**
```bash
# Create in skills directory
python skills/skill-creator/scripts/init_skill.py my-new-skill --path skills

# Create in custom location
python skills/skill-creator/scripts/init_skill.py my-api-helper --path /custom/location
```

**What It Creates:**
```
my-new-skill/
├── SKILL.md (with TODO placeholders)
├── scripts/
│   └── example.py (example script)
├── references/
│   └── api_reference.md (example reference)
└── assets/
    └── example_asset.txt (example asset)
```

**Next Steps:**
1. Edit SKILL.md to complete TODO items
2. Customize or delete example files
3. Add your actual resources

### Step 4: Editing the Skill

#### 4.1 Learn Proven Design Patterns

**Resources:**
- `references/workflows.md` - Sequential and conditional workflows
- `references/output-patterns.md` - Template and example patterns

#### 4.2 Implement Reusable Contents

**Order:** Start with bundled resources (scripts, references, assets), then SKILL.md

**For Scripts:**
- Test by actually running them
- Ensure no bugs and correct output
- For many similar scripts, test representative samples

**For References:**
- Organize by domain or topic
- Add table of contents for long files
- Keep one level deep from SKILL.md

**For Assets:**
- Ensure files are ready to use
- Test templates and boilerplate
- Verify media files work correctly

**Clean Up:** Delete any example files not needed for your skill

#### 4.3 Writing SKILL.md

**Frontmatter:**
```yaml
---
name: skill-name
description: What the skill does AND when to use it. Include specific triggers, file types, or tasks that activate this skill.
---
```

**Description Guidelines:**
- Include both WHAT and WHEN
- List all triggering scenarios
- Don't repeat in body (body loads after triggering)
- Example: "Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. Use when Claude needs to work with professional documents (.docx files) for: (1) Creating new documents, (2) Modifying or editing content, (3) Working with tracked changes, (4) Adding comments, or any other document tasks"

**Body Guidelines:**
- Use imperative/infinitive form
- Choose appropriate structure (workflow/task/reference/capabilities)
- Include only essential procedural instructions
- Move detailed information to references
- Reference bundled resources clearly
- Keep under 500 lines when possible

**Writing Style:**
- Concise is key (context window is shared)
- Default assumption: Claude is already very smart
- Only add context Claude doesn't already have
- Challenge each piece: "Does Claude really need this?"
- Prefer concise examples over verbose explanations

### Step 5: Packaging the Skill

**Tool:** `skills/skill-creator/scripts/package_skill.py`

**Usage:**
```bash
python skills/skill-creator/scripts/package_skill.py <path/to/skill-folder> [output-directory]
```

**Examples:**
```bash
# Package to current directory
python skills/skill-creator/scripts/package_skill.py skills/my-skill

# Package to specific directory
python skills/skill-creator/scripts/package_skill.py skills/my-skill ./dist
```

**What It Does:**
1. **Validates** the skill automatically:
   - YAML frontmatter format and required fields
   - Skill naming conventions
   - Description completeness
   - File organization and references

2. **Packages** the skill if validation passes:
   - Creates `.skill` file (zip format)
   - Names it `<skill-name>.skill`
   - Includes all files with proper directory structure

**If Validation Fails:**
- Script reports errors and exits
- Fix errors and run again
- Common issues:
  - Missing required fields
  - Invalid naming convention
  - Description too long or contains angle brackets
  - Unexpected frontmatter properties

### Step 6: Iteration

**Approach:**
1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Identify improvements to SKILL.md or resources
4. Implement changes and test again

**Iteration is continuous** - skills improve with real-world usage

## Core Design Principles

### 1. Conciseness is Key

**Principle:** The context window is a public good

- Skills share context with system prompt, conversation history, other skills' metadata, and user requests
- Default assumption: Claude is already very smart
- Only add context Claude doesn't already have
- Challenge each piece: "Does this justify its token cost?"
- Prefer concise examples over verbose explanations

### 2. Set Appropriate Degrees of Freedom

Match specificity to task fragility and variability:

**High Freedom (text-based instructions)**
- Use when: Multiple approaches valid, context-dependent decisions, heuristics guide approach
- Example: Design suggestions, creative tasks

**Medium Freedom (pseudocode/scripts with parameters)**
- Use when: Preferred pattern exists, some variation acceptable, configuration affects behavior
- Example: Template-based generation with options

**Low Freedom (specific scripts, few parameters)**
- Use when: Operations fragile/error-prone, consistency critical, specific sequence required
- Example: PDF form filling, OOXML editing

**Analogy:** Think of Claude exploring a path:
- Narrow bridge with cliffs → specific guardrails (low freedom)
- Open field → many routes (high freedom)

### 3. Progressive Disclosure

**Principle:** Load information only when needed

**Three Levels:**
1. Metadata (always loaded) - ~100 words
2. SKILL.md body (loaded on trigger) - <5k words
3. Resources (loaded as needed) - unlimited

**Benefits:**
- Minimizes context bloat
- Keeps essential information accessible
- Allows deep resources without overhead

### 4. Avoid Duplication

**Anti-Patterns:**
- Information in both SKILL.md and references
- Repeating information across multiple files
- Including details in metadata that belong in body

**Best Practice:**
- Each piece of information lives in ONE place
- Choose the right level (metadata/body/reference)
- Reference from higher to lower levels

## Example Skills Analysis

### Simple Skill: brand-guidelines

**Structure:**
```
brand-guidelines/
└── SKILL.md (74 lines)
```

**Characteristics:**
- No bundled resources needed
- All information in SKILL.md
- Reference/Guidelines pattern
- Concise specification of colors and typography

**Learning:** Not all skills need scripts/references/assets

### Complex Skill: algorithmic-art

**Structure:**
```
algorithmic-art/
├── SKILL.md (404 lines)
└── templates/
    ├── viewer.html (extensive HTML template)
    └── generator_template.js (p5.js patterns)
```

**Characteristics:**
- Two-phase process (philosophy creation → implementation)
- Heavy use of templates (assets)
- Detailed instructions for creative coding
- Emphasis on using templates as starting point

**Learning:** Complex creative tasks need extensive guidance and templates

### Meta Skill: skill-creator

**Structure:**
```
skill-creator/
├── SKILL.md (356 lines)
├── scripts/
│   ├── init_skill.py (initialization automation)
│   ├── package_skill.py (packaging automation)
│   └── quick_validate.py (validation logic)
└── references/
    ├── workflows.md (workflow patterns)
    └── output-patterns.md (output formatting patterns)
```

**Characteristics:**
- Workflow-based structure
- Scripts automate skill creation workflow
- References provide design patterns
- Self-documenting (teaches skill creation)

**Learning:** Meta-skills can guide their own creation process

### Technical Skill: mcp-builder

**Structure:**
```
mcp-builder/
├── SKILL.md (236 lines)
├── reference/
│   ├── mcp_best_practices.md
│   ├── python_mcp_server.md
│   ├── node_mcp_server.md
│   └── evaluation.md
└── scripts/
    └── example_evaluation.xml
```

**Characteristics:**
- Four-phase process (Research → Implementation → Review → Evaluation)
- Heavy use of references for different languages/topics
- Progressive disclosure for technical depth
- External documentation loading (fetch from GitHub)

**Learning:** Technical skills benefit from organized reference materials

### Document Skill: docx

**Structure:**
```
docx/
├── SKILL.md (196 lines)
├── docx-js.md (comprehensive docx-js guide)
├── ooxml.md (OOXML editing guide)
└── scripts/
    ├── templates/ (document templates)
    └── [utility scripts]
```

**Characteristics:**
- Workflow decision tree (Read/Create/Edit)
- Two major reference files (500-600 lines each)
- Mandatory full-file reads for references
- Separation of concerns (creation vs editing)

**Learning:** Document manipulation benefits from decision trees and comprehensive references

## Validation and Quality

### Validation Script

**Tool:** `skills/skill-creator/scripts/quick_validate.py`

**What It Checks:**
1. SKILL.md exists
2. Valid YAML frontmatter format
3. Required fields present (name, description)
4. Allowed properties only (name, description, license, allowed-tools, metadata)
5. Name follows hyphen-case convention
6. Name length ≤64 characters
7. Description length ≤1024 characters
8. No angle brackets in description

**Usage:**
```bash
python skills/skill-creator/scripts/quick_validate.py <skill-directory>
```

**Integration:** Automatically run by `package_skill.py` before packaging

### Quality Checklist

**SKILL.md:**
- [ ] YAML frontmatter valid
- [ ] Name in hyphen-case
- [ ] Description includes WHAT and WHEN
- [ ] Body under 500 lines (or split into references)
- [ ] Clear structure (workflow/task/reference/capabilities)
- [ ] Concise language (no unnecessary verbosity)
- [ ] Examples over explanations

**Scripts:**
- [ ] Tested and working
- [ ] Executable permissions set
- [ ] Clear docstrings
- [ ] Error handling included
- [ ] Representative samples tested (if many similar scripts)

**References:**
- [ ] Table of contents for files >100 lines
- [ ] Clearly referenced from SKILL.md
- [ ] One level deep from SKILL.md
- [ ] No duplication with SKILL.md
- [ ] Organized by domain/topic (if multiple)

**Assets:**
- [ ] Files ready to use
- [ ] Templates tested
- [ ] Media files verified
- [ ] No unnecessary files

## Plugin System (Claude Code)

### Marketplace Configuration

**File:** `.claude-plugin/marketplace.json`

**Structure:**
```json
{
  "name": "anthropic-agent-skills",
  "owner": {
    "name": "Owner Name",
    "email": "email@example.com"
  },
  "metadata": {
    "description": "Repository description",
    "version": "1.0.0"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Plugin description",
      "source": "./",
      "strict": false,
      "skills": [
        "./skills/skill1",
        "./skills/skill2"
      ]
    }
  ]
}
```

### Current Plugins

**document-skills:**
- xlsx, docx, pptx, pdf
- Document processing suite

**example-skills:**
- algorithmic-art, brand-guidelines, canvas-design, doc-coauthoring
- frontend-design, internal-comms, mcp-builder, skill-creator
- slack-gif-creator, theme-factory, web-artifacts-builder, webapp-testing
- Demonstration of various capabilities

### Using in Claude Code

**Add marketplace:**
```bash
/plugin marketplace add anthropics/skills
```

**Install plugin:**
```bash
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

**Or browse and install:**
1. Select "Browse and install plugins"
2. Select "anthropic-agent-skills"
3. Select plugin to install
4. Select "Install now"

## Best Practices Summary

### DO:
✅ Keep SKILL.md concise and under 500 lines
✅ Include WHEN to use in description
✅ Use progressive disclosure (references for details)
✅ Test scripts before including
✅ Validate before packaging
✅ Use imperative/infinitive form in instructions
✅ Organize references by domain/topic
✅ Delete unused example files
✅ Challenge each piece of information
✅ Prefer examples over explanations

### DON'T:
❌ Duplicate information across files
❌ Create auxiliary documentation (README, CHANGELOG, etc.)
❌ Include "when to use" only in body (put in description)
❌ Use angle brackets in description
❌ Exceed length limits (name: 64, description: 1024)
❌ Create deeply nested reference structures
❌ Add context Claude already has
❌ Include untested scripts
❌ Forget to set executable permissions on scripts
❌ Create skills without concrete examples

## Common Patterns

### Decision Tree Pattern

**Use when:** Multiple workflow paths based on conditions

**Example:**
```markdown
## Workflow Decision Tree

### Reading/Analyzing Content
Use "Text extraction" or "Raw XML access" sections

### Creating New Document
Use "Creating a new Word document" workflow

### Editing Existing Document
- **Your own document + simple changes** → "Basic OOXML editing"
- **Someone else's document** → "Redlining workflow" (recommended)
- **Legal/academic/business docs** → "Redlining workflow" (required)
```

### Template Pattern

**Use when:** Consistent output format needed

**Example:**
```markdown
## Report Structure

ALWAYS use this exact template:

# [Analysis Title]

## Executive Summary
[Overview]

## Key Findings
- Finding 1
- Finding 2

## Recommendations
1. Specific action
2. Specific action
```

### Examples Pattern

**Use when:** Quality depends on seeing good examples

**Example:**
```markdown
## Commit Message Format

**Example 1:**
Input: Added user authentication with JWT tokens
Output:
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

Follow this style: type(scope): brief description, then details.
```

### Sequential Workflow Pattern

**Use when:** Clear step-by-step process

**Example:**
```markdown
Filling a PDF form involves these steps:

1. Analyze the form (run analyze_form.py)
2. Create field mapping (edit fields.json)
3. Validate mapping (run validate_fields.py)
4. Fill the form (run fill_form.py)
5. Verify output (run verify_output.py)
```

## Technical Details

### File Format

**SKILL.md:**
- UTF-8 encoding
- Markdown format
- YAML frontmatter (between `---` markers)
- Standard markdown in body

**.skill files:**
- ZIP archive with `.skill` extension
- Contains entire skill directory
- Maintains directory structure
- Portable and distributable

### Validation Rules

**Name:**
- Regex: `^[a-z0-9-]+$`
- No leading/trailing hyphens
- No consecutive hyphens
- Max 64 characters

**Description:**
- String type
- No angle brackets (`<`, `>`)
- Max 1024 characters
- Should be comprehensive

**Allowed Frontmatter Properties:**
- `name` (required)
- `description` (required)
- `license` (optional)
- `allowed-tools` (optional)
- `metadata` (optional)

### Script Requirements

**Executable Scripts:**
- Set executable permissions (`chmod +x`)
- Include shebang (`#!/usr/bin/env python3`)
- Include docstring with usage
- Handle errors gracefully
- Test before committing

**Python Scripts:**
- Use Python 3
- Include imports
- Add `if __name__ == "__main__":` guard
- Provide CLI help/usage

## Resources

### Official Documentation
- **Agent Skills Specification:** https://agentskills.io/specification
- **What are skills?** https://support.claude.com/en/articles/12512176-what-are-skills
- **Using skills in Claude:** https://support.claude.com/en/articles/12512180-using-skills-in-claude
- **Creating custom skills:** https://support.claude.com/en/articles/12512198-creating-custom-skills
- **Engineering blog:** https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills

### Repository Resources
- **Template:** `template/SKILL.md` - Minimal skill template
- **Spec:** `spec/agent-skills-spec.md` - Points to specification
- **Skills:** `skills/` - Example implementations
- **Tools:** `skills/skill-creator/scripts/` - Development tools

### Key Files to Study
- `skills/skill-creator/SKILL.md` - Meta guide for skill creation
- `skills/skill-creator/references/workflows.md` - Workflow patterns
- `skills/skill-creator/references/output-patterns.md` - Output patterns
- `skills/algorithmic-art/SKILL.md` - Complex creative skill example
- `skills/mcp-builder/SKILL.md` - Technical skill example
- `skills/docx/SKILL.md` - Document manipulation example

## Troubleshooting

### Validation Errors

**"Missing 'name' in frontmatter"**
- Add `name: skill-name` to YAML frontmatter

**"Missing 'description' in frontmatter"**
- Add `description: ...` to YAML frontmatter

**"Name should be hyphen-case"**
- Use lowercase letters, digits, and hyphens only
- Example: `my-skill-name` not `My_Skill_Name`

**"Description cannot contain angle brackets"**
- Remove `<` and `>` from description
- Use alternative formatting

**"Name is too long"**
- Reduce name to ≤64 characters

**"Description is too long"**
- Reduce description to ≤1024 characters
- Move details to SKILL.md body

**"Unexpected key(s) in SKILL.md frontmatter"**
- Only use allowed properties: name, description, license, allowed-tools, metadata
- Remove any other properties

### Common Issues

**Skill not triggering:**
- Check description includes WHEN to use (specific triggers)
- Ensure description mentions file types, tasks, scenarios
- Test with explicit requests matching description

**Context window bloat:**
- Split SKILL.md if >500 lines
- Move details to references/
- Challenge each paragraph's necessity

**Scripts not executing:**
- Check executable permissions (`chmod +x script.py`)
- Verify shebang line (`#!/usr/bin/env python3`)
- Test script independently
- Check for syntax errors

**References not loading:**
- Ensure clear references in SKILL.md
- Check file paths are correct
- Verify files exist in skill directory
- Add table of contents for long files

## Conclusion

Skills are a powerful way to extend Claude's capabilities with specialized knowledge and workflows. By following this guide, you can create high-quality skills that are:

- **Concise:** Use context window efficiently
- **Discoverable:** Clear triggers in description
- **Maintainable:** Well-organized structure
- **Reusable:** Bundled resources avoid duplication
- **Validated:** Pass quality checks
- **Effective:** Tested with real examples

Start with the template, study existing skills, and iterate based on real usage. The skill creation process is designed to be straightforward while producing professional results.
