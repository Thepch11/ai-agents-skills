# Skills Quick Start Guide

## What are Skills?

Skills are folders containing instructions and resources that Claude loads to perform specialized tasks. Think of them as "onboarding guides" for specific domains.

## 30-Second Skill Anatomy

```
my-skill/
├── SKILL.md (required)    # Instructions with YAML frontmatter
├── scripts/    (optional) # Executable code
├── references/ (optional) # Detailed documentation
└── assets/     (optional) # Templates and files for output
```

## Creating Your First Skill in 5 Minutes

### 1. Initialize

```bash
cd /path/to/copilot-skills
python skills/skill-creator/scripts/init_skill.py my-first-skill --path skills
```

### 2. Edit SKILL.md

Replace TODOs with your content:

```yaml
---
name: my-first-skill
description: What it does AND when to use it (file types, tasks, triggers)
---

# My First Skill

## Overview
[1-2 sentences explaining what this enables]

## How to Use
[Instructions, examples, or workflows]
```

### 3. Validate

```bash
python skills/skill-creator/scripts/quick_validate.py skills/my-first-skill
```

### 4. Package

```bash
python skills/skill-creator/scripts/package_skill.py skills/my-first-skill
```

Done! You now have `my-first-skill.skill` ready to distribute.

## Minimal SKILL.md Template

```markdown
---
name: my-skill
description: Complete description of what the skill does and when to use it. Include specific triggers like file types, tasks, or scenarios.
---

# My Skill

## Overview

Brief introduction to what this skill enables.

## Usage

Instructions on how to use this skill.

## Examples

Concrete examples of the skill in action.
```

## Common Patterns Cheat Sheet

### Decision Tree Pattern
```markdown
## Workflow Decision Tree

Task type A? → Follow workflow A
Task type B? → Follow workflow B
Task type C? → Follow workflow C
```

### Template Pattern
```markdown
## Output Format

ALWAYS use this structure:

# [Title]
## Section 1
## Section 2
```

### Examples Pattern
```markdown
## Examples

**Input:** User request
**Output:** 
```
Expected response
```
```

### Sequential Steps Pattern
```markdown
Process involves these steps:

1. First step (use tool X)
2. Second step (use tool Y)
3. Third step (verify)
```

## Skill Structure Decision Matrix

| If you have... | Include in... | Example |
|---------------|---------------|---------|
| Simple instructions | SKILL.md only | brand-guidelines |
| Repeated code | scripts/ | pdf/scripts/rotate_pdf.py |
| Detailed docs | references/ | mcp-builder/reference/ |
| Templates/media | assets/ | frontend/assets/templates/ |
| >500 lines | Split into references | docx skill |

## Quick Validation Rules

✅ **Name:**
- hyphen-case only
- Max 64 chars
- No leading/trailing/consecutive hyphens

✅ **Description:**
- Include WHAT and WHEN
- Max 1024 chars
- No angle brackets

✅ **SKILL.md:**
- YAML frontmatter between `---`
- Required: name, description
- Optional: license, allowed-tools, metadata

## Common Mistakes to Avoid

❌ Missing "when to use" in description
❌ Putting "when to use" only in body (too late!)
❌ Creating README.md (not needed)
❌ Duplicating info between SKILL.md and references
❌ Exceeding 500 lines in SKILL.md (split into references)
❌ Not testing scripts before including
❌ Using angle brackets in description
❌ Including unnecessary context Claude already knows

## Progressive Disclosure Strategy

**Metadata (always loaded):**
```yaml
name: my-skill
description: Brief but complete description with triggers
```

**SKILL.md body (loaded when triggered):**
```markdown
Core instructions, workflows, and references to other files
```

**References (loaded as needed):**
```
references/
├── detailed_guide.md      # Load when needed
├── api_reference.md       # Load when needed
└── advanced_patterns.md   # Load when needed
```

## Example: Real Skill Breakdown

### Simple Skill (brand-guidelines)

**Structure:** Just SKILL.md (74 lines)

**Content:**
- Colors specification
- Typography rules
- Application guidelines

**When to use:** Reference/Guidelines pattern for simple specifications

### Complex Skill (docx)

**Structure:**
```
docx/
├── SKILL.md (196 lines) - Decision tree and workflow
├── docx-js.md (~500 lines) - Document creation guide
├── ooxml.md (~600 lines) - Document editing guide
└── scripts/templates/ - Document templates
```

**When to use:** Workflow-based pattern with extensive references

## Useful Commands

```bash
# Initialize new skill
python skills/skill-creator/scripts/init_skill.py SKILL_NAME --path skills

# Validate skill
python skills/skill-creator/scripts/quick_validate.py skills/SKILL_NAME

# Package skill
python skills/skill-creator/scripts/package_skill.py skills/SKILL_NAME

# Package to specific directory
python skills/skill-creator/scripts/package_skill.py skills/SKILL_NAME ./dist
```

## Testing Your Skill

1. **Validate structure:**
   ```bash
   python skills/skill-creator/scripts/quick_validate.py skills/your-skill
   ```

2. **Test scripts:**
   ```bash
   python skills/your-skill/scripts/script_name.py --help
   python skills/your-skill/scripts/script_name.py [test args]
   ```

3. **Review references:**
   - Check all files referenced in SKILL.md exist
   - Verify table of contents for long files
   - Ensure no duplication

4. **Use in practice:**
   - Test with real tasks
   - Notice pain points
   - Iterate and improve

## Iteration Workflow

```
Use skill → Notice issue → Fix issue → Test → Package → Repeat
```

**Common improvements:**
- Add missing instructions
- Clarify confusing parts
- Add more examples
- Split long SKILL.md
- Add helper scripts
- Update descriptions

## Resource Recommendations

**For complex workflows:**
- Read: `skills/skill-creator/references/workflows.md`

**For consistent output:**
- Read: `skills/skill-creator/references/output-patterns.md`

**For technical skills:**
- Study: `skills/mcp-builder/SKILL.md`

**For creative skills:**
- Study: `skills/algorithmic-art/SKILL.md`

**For document skills:**
- Study: `skills/docx/SKILL.md`, `skills/pdf/SKILL.md`

## Getting Help

1. **Read full guide:** `DEVELOPMENT_GUIDE.md`
2. **Study examples:** Browse `skills/` directory
3. **Check specification:** https://agentskills.io/specification
4. **Review patterns:** `skills/skill-creator/references/`

## One-Pager: Essential Rules

1. **SKILL.md is required** - Everything else is optional
2. **Description triggers the skill** - Include WHEN to use
3. **Keep SKILL.md concise** - Under 500 lines, use references
4. **Validate before packaging** - Catch errors early
5. **Test scripts** - Ensure they work before including
6. **No duplication** - Each fact lives in one place
7. **Progressive disclosure** - Load details only when needed
8. **Conciseness wins** - Context window is shared
9. **Examples over explanations** - Show, don't tell
10. **Iterate based on usage** - Improve from real tasks

## Next Steps

After creating your first skill:

1. ✅ Review existing skills for patterns
2. ✅ Read full development guide
3. ✅ Study skill-creator references
4. ✅ Create skills for your workflows
5. ✅ Share and iterate

---

**Remember:** Skills should contain ONLY what an AI agent needs to perform tasks. Start simple, validate often, and iterate based on real usage.
