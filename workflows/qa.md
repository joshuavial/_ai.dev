# Quality Assurance Workflow

# IMPORTANT: Read Boot Protocol First
> **CRITICAL**: When adopting this workflow, you MUST first read the core instructions and follow the boot protocol to ensure proper initialization. Do NOT skip this step as it's essential for correct workflow operation.
>
> 1. Read core instructions: `_ai.bws/core-instructions.md`
> 2. Follow boot protocol: `_ai.bws/protocols/boot.md`
> 3. Only then proceed with this workflow

This document outlines the quality assurance (QA) process for verifying completed implementation work. QA operates at the task level, producing actionable feedback documents that integrate with the execution workflow.

## QA Purpose and Scope

### When to Begin QA

QA is triggered when:
- A task's implementation is marked complete in its technical plan
- All TDD cycles are documented with evidence
- The execution workflow indicates readiness for quality verification

### QA Operates at Task Level

- **Scope**: Single task folder in `_ai/tasks/[issue-id]-[task-letter]-[task-name]/`
- **Input**: `technical-plan.md` with completed implementation
- **Output**: `qa-report.md` with verification results and action items

## 3-Question Quality Framework

QA evaluation centers on three core questions:

1. **Objective Verification**: Does it meet the objectives?
2. **Functional Verification**: Does it function?
3. **Quality Assessment**: Is it well factored, easy to maintain, consistent, and well tested?

## QA Process

### 1. QA Preparation

**Locate Task Folder**:
- Navigate to: `_ai/tasks/[issue-id]-[task-letter]-[task-name]/`
- Review: `technical-plan.md` for objectives and commitments
- Check: TDD evidence in `tdd-evidence/` folder (if present)
- Review: Any artifacts in `artifacts/` folder

**Understand Planning Commitments**:
- Read planning objectives from technical plan
- Review user-approved scope and approach
- Note specific package/library decisions made
- Identify test coverage targets and TDD requirements

### 1.5 CI Status Verification (MANDATORY FIRST STEP)

**CRITICAL**: Before proceeding with any manual verification, you MUST verify that all automated CI checks are passing.

**Check CI Status**:
- Navigate to the GitHub PR or branch
- Verify all CI checks show green ✅ status
- Confirm builds are successful for all environments  
- Ensure automated tests are passing
- Verify linting and code quality checks pass

**CI Status Requirements**:
- ✅ All builds passing (admin, client-app, main app)
- ✅ All automated tests passing
- ✅ Linting checks passing (no errors)
- ✅ Any other configured CI checks passing

**If CI Checks Are Failing**:
1. **STOP QA PROCESS** - Do not proceed with manual verification
2. Return task to execution workflow with CI failure details
3. Create action items for fixing CI issues
4. Only restart QA after all CI checks are passing

**CI Verification Template**:
```markdown
## CI Status Verification
**STATUS**: ✅ PASS / ❌ FAIL

**Build Status**: ✅ All builds passing / ❌ Build failures detected
**Test Execution**: ✅ All tests passing / ❌ Test failures detected  
**Linting**: ✅ No lint errors / ❌ Lint errors detected
**Other Checks**: ✅ All checks passing / ❌ Additional failures detected

**Action Required If Failing**: 
Return to execution workflow to fix CI issues before proceeding with QA.
```

### 2. Quality Verification

#### 2.1 Objective Verification
**Questions to Answer**:
- Were all planning objectives achieved?
- Was user-approved scope delivered completely?
- Were all acceptance criteria met?
- Were technical plan commitments honored?
- Were package/library decisions followed as planned?

**Verification Methods**:
- Compare deliverables against technical plan objectives
- Check that implementation matches planned approach
- Verify all checklist items from technical plan are complete
- Confirm user sign-off requirements were met

#### 2.2 Functional Verification
**Questions to Answer**:
- Does core functionality work as specified?
- Do error handling mechanisms function properly?
- Do integration points work correctly?
- Does performance meet requirements?
- Do user workflows function end-to-end?

**Verification Methods**:
- Test core functionality manually
- Verify error cases are handled gracefully
- Check integration with existing components
- Run automated tests if available
- Test edge cases and boundary conditions

#### 2.3 Quality Assessment
**Questions to Answer**:
- Is code readable and well-structured?
- Are appropriate abstractions and patterns used?
- Does it follow existing code patterns and conventions?
- Does it use established architectural approaches?
- Are naming and structure consistent?
- Does test coverage meet planned targets?
- Are tests meaningful and cover edge cases?
- Is TDD evidence complete (RED-GREEN-REFACTOR cycles)?
- Was any obvious technical debt introduced?
- Were maintainability considerations addressed?

**Verification Methods**:
- Review code quality and structure
- Check consistency with existing codebase patterns
- Verify test coverage against planned targets
- Review TDD evidence for completeness
- Assess technical debt impact
- Evaluate maintainability and readability

### 3. QA Documentation

**Create QA Report**: `qa-report.md` in the task folder

#### QA Report Template

```markdown
# QA Report: [Task Name]

**Date**: [Current Date]
**Technical Plan**: technical-plan.md
**QA Status**: ✅ PASS / ⚠️ NEEDS WORK / ❌ FAIL

## CI Status Verification
**Build Status**: ✅ PASS / ❌ FAIL
**Test Execution**: ✅ PASS / ❌ FAIL  
**Linting**: ✅ PASS / ❌ FAIL
**Other CI Checks**: ✅ PASS / ❌ FAIL

**Notes**: [Any specific CI-related observations]

## Executive Summary

- **CI Status Verification**: ✅ PASS / ❌ FAIL
- **Objective Verification**: ✅ PASS / ❌ FAIL
- **Functional Verification**: ✅ PASS / ❌ FAIL  
- **Quality Assessment**: ✅ PASS / ❌ FAIL

## 1. Objective Verification

### ✅ Requirements Met
- [List objectives that were successfully achieved]
- [Note planning commitments that were honored]

### ❌ Requirements Not Met  
- [List objectives that failed verification]
- [Note planning commitments that were not honored]
- **Action Required**: [Specific steps needed for each issue]

## 2. Functional Verification

### ✅ Functions Correctly
- [List functional areas that work properly]
- [Note successful integrations and workflows]

### ❌ Functional Issues
- [List functional problems found with specific examples]
- [Note integration or workflow failures]
- **Action Required**: [Specific fixes needed for each issue]

## 3. Quality Assessment

### ✅ Quality Standards Met
- [List quality aspects that pass review]
- [Note successful code patterns and test coverage]

### ❌ Quality Issues
- [List code quality problems: structure, consistency, testing]
- [Note technical debt or maintainability concerns]
- **Action Required**: [Specific improvements needed for each issue]

## Action Items for Execution

### Critical (Must Fix Before Production)
- [ ] [Specific actionable item with clear success criteria]
- [ ] [Specific actionable item with clear success criteria]

### Recommended (Should Fix)  
- [ ] [Specific actionable item with clear success criteria]
- [ ] [Specific actionable item with clear success criteria]

### Optional (Nice to Have)
- [ ] [Specific actionable item with clear success criteria]

## QA Sign-off

**Overall Result**: Ready for Production / Needs Rework / Critical Issues Found

**Next Steps**: 
- If PASS: Ready for production deployment
- If NEEDS WORK: Return to execution workflow to address action items
- If FAIL: Requires significant rework before re-evaluation

**Re-QA Required**: Yes/No (if action items are addressed)
```

### 4. QA Status Classification

#### ✅ PASS
- **CI Status Verification passes** (all automated checks green)
- All three manual verification areas pass
- No critical issues found
- Ready for production

#### ⚠️ NEEDS WORK  
- Minor issues in one or more areas
- Critical items must be fixed
- Recommended items should be addressed
- Return to execution workflow with action items

#### ❌ FAIL
- Major issues in multiple areas
- Significant rework required
- Does not meet basic quality standards
- Requires substantial execution cycle before re-QA

## Integration with Execution Workflow

### QA Feedback Loop

When QA identifies issues:

1. **Execution Reviews QA Report**: Read `qa-report.md` in task folder
2. **Address Action Items**: Use TDD cycle for fixes
3. **Update QA Report**: Check off completed items
4. **Request Re-QA**: When all critical items resolved

### QA Action Item Resolution

For each QA issue being addressed in execution:

```markdown
## Addressing QA Issue: [Issue Description]

### TDD Approach
- [ ] RED: Write test for the fix
- [ ] GREEN: Implement minimal fix  
- [ ] REFACTOR: Improve implementation
- [ ] Verify: Complete test suite passes

### Resolution Notes
- [Description of what was changed]
- [Impact on other components]
- [Link to updated QA report item]
```

## QA Best Practices

### Systematic Approach
- Always follow the 3-question framework systematically
- Document specific examples for each finding
- Provide actionable guidance, not just "fix this"
- Reference specific files and line numbers when applicable

### Integration Focus
- Pay special attention to how changes integrate with existing code
- Verify that new patterns align with established conventions
- Check that dependencies are properly managed
- Ensure consistent error handling approaches

### Test Quality Assessment
- Verify tests actually test the intended behavior
- Check that edge cases are covered appropriately
- Ensure tests will catch regressions
- Validate that TDD cycles were genuinely followed

### Documentation Standards
- Keep QA reports concise but thorough
- Use clear, specific language in action items
- Provide examples of good vs. problematic code when helpful
- Link to relevant documentation or standards

## Example QA Report

```markdown
# QA Report: Xero OAuth Integration

**Date**: 2024-12-06
**Technical Plan**: technical-plan.md  
**QA Status**: ⚠️ NEEDS WORK

## Executive Summary

- **Objective Verification**: ✅ PASS
- **Functional Verification**: ✅ PASS
- **Quality Assessment**: ❌ FAIL

## 1. Objective Verification

### ✅ Requirements Met
- OAuth flow implemented as planned
- Token refresh mechanism working
- Integration with existing auth system complete

## 2. Functional Verification

### ✅ Functions Correctly
- OAuth authorization completes successfully
- Token refresh triggers automatically
- Error cases return appropriate responses

## 3. Quality Assessment

### ❌ Quality Issues
- Test coverage at 67%, target was 85%
- Hardcoded API endpoints should be configurable
- Error handling inconsistent with project patterns

## Action Items for Execution

### Critical (Must Fix Before Production)
- [ ] Increase test coverage to minimum 85% for XeroAuthService
- [ ] Move API endpoints to configuration file

### Recommended (Should Fix)
- [ ] Standardize error handling to match existing auth patterns
- [ ] Add logging for OAuth flow steps

## QA Sign-off

**Overall Result**: Needs Rework
**Next Steps**: Address critical items and update test coverage
**Re-QA Required**: Yes
```

## QA Completion and Next Steps

Once QA is complete:

1. **Save QA Report**: Ensure `qa-report.md` is saved in task folder
2. **Update Current State**: If returning to execution, update technical plan
3. **Archive on Completion**: Move entire task folder to `_ai/archive/` when done
4. **Transition**: 
   - If PASS: Ready for reflection or production
   - If NEEDS WORK/FAIL: Return to execution workflow

The QA workflow ensures systematic quality verification while providing clear, actionable feedback for the execution workflow to address any identified issues.