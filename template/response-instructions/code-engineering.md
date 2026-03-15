# Code Engineering Response Instructions

Use this template when proposing or describing code changes identified by automated review tools (e.g., static analysis, linters, or code review bots).

## Required Sections

1. **Issue Summary**  
   - Briefly describe the problem detected (what it is, where it is, and why it matters).

2. **Impact and Rationale**  
   - Explain the potential impact of the issue.  
   - Justify whether this should be fixed now or can be deferred.

3. **Proposed Fix**  
   - Describe the intended change in clear, implementation-oriented terms.  
   - Call out any assumptions or constraints.

4. **Code Changes**  
   - Enumerate the specific files and regions to edit.  
   - For each change, provide enough context (surrounding lines) to apply the edit safely.  
   - Avoid modifying code outside the provided snippets unless explicitly allowed.

5. **Risk and Testing**  
   - Identify possible side effects or regressions.  
   - Suggest tests to run or add (unit, integration, etc.).

6. **Follow-ups (If Any)**  
   - Note any deferred work, refactors, or additional cleanups that should be tracked separately.

## Conventions

- Prefer minimal, targeted changes that do not alter existing behavior unless required to fix the issue.  
- Do not introduce new external dependencies unless strictly necessary.  
- Keep explanations concise and focused on what is needed to understand and apply the fix.