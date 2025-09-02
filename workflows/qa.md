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
- Check: Bug investigation evidence in `bug-investigation/` folder (if bug fix task)

**Understand Planning Commitments**:
- Read planning objectives from technical plan
- Review user-approved scope and approach
- Note specific package/library decisions made
- Identify test coverage targets and TDD requirements

### 1.5 CI Status Verification (MANDATORY FIRST STEP)

**CRITICAL - ZERO TOLERANCE FOR FAILING TESTS**: Before proceeding with any manual verification, you MUST verify that all automated CI checks are passing.

**Check CI Status**:
- Navigate to the GitHub PR or branch
- Verify all CI checks show green ✅ status
- Confirm builds are successful for all environments  
- Ensure automated tests are passing
- Verify linting and code quality checks pass

**CI Status Requirements**:
- ✅ All builds passing (admin, client-app, main app)
- ✅ ALL automated tests passing (NO EXCEPTIONS)
- ✅ Linting checks passing (no errors)
- ✅ Any other configured CI checks passing

**ZERO TOLERANCE POLICY**:
- ✅ NO failing tests regardless of perceived relevance
- ✅ NO subjective assessment of test "relatedness"
- ✅ ALL tests must be green - NO EXCEPTIONS

**If ANY CI Checks Are Failing**:
1. **IMMEDIATELY STOP QA PROCESS** - Do not proceed with manual verification
2. **RETURN TO EXECUTION** with CI failure details
3. **NO MANUAL VERIFICATION** until all checks pass
4. **NO EXCEPTIONS** for "unrelated" test failures
5. Only restart QA after all CI checks are passing

**Test Verification Commands**:
```bash
# Run complete test suite and verify ALL pass
poetry pytest tests/ --verbose
# All tests must show PASSED - no FAILED allowed
```

**CI Verification Template**:
```markdown
## CI Status Verification
**STATUS**: ✅ PASS / ❌ FAIL

**Build Status**: ✅ All builds passing / ❌ Build failures detected
**Test Execution**: ✅ ALL tests passing / ❌ Test failures detected  
**Linting**: ✅ No lint errors / ❌ Lint errors detected
**Other Checks**: ✅ All checks passing / ❌ Additional failures detected

**Action Required If Failing**: 
IMMEDIATELY return to execution workflow to fix ALL issues before proceeding with QA.
```

### 1.6 File Management Verification (MANDATORY SECOND STEP)

**CRITICAL - CLEAN FILE MANAGEMENT**: Verify no redundant or duplicate files exist.

**Mandatory File Management Checks**:
- ✅ NO files with duplicate suffixes (.new, .updated, .copy, .backup, .old, etc.)
- ✅ NO renamed files leaving old versions behind
- ✅ ALL changes made directly to original files
- ✅ NO temporary or alternate versions of modified files

**If Redundant Files Found**:
1. **STOP QA PROCESS**
2. **RETURN TO EXECUTION** with file cleanup requirements
3. **REQUIRE**: Consolidation of changes into original files
4. **REQUIRE**: Removal of all duplicate/redundant files

**File Detection Commands**:
```bash
# Check for common redundant file patterns
find . -name "*.new" -o -name "*.updated" -o -name "*.copy" -o -name "*.backup" -o -name "*.old"

# Look for potential duplicates with similar names
find . -type f | sort | uniq -d

# Check git status for renamed files
git status --porcelain | grep "^R"
```

**File Management Verification Template**:
```markdown
## File Management Verification
**STATUS**: ✅ PASS / ❌ FAIL

**Redundant Files Check**: ✅ No duplicates found / ❌ Redundant files detected
**Original File Modifications**: ✅ All changes in original files / ❌ Changes in alternate versions
**Clean Repository**: ✅ No temporary files / ❌ Cleanup required

**Action Required If Failing**: 
Return to execution workflow to consolidate changes and remove redundant files.
```

### 2. Bug Investigation Validation (When Applicable)

**For Bug Fix Tasks Only**: When the task involves bug fixes, perform systematic validation of the investigation and resolution process.

#### 2.1 Bug Investigation Evidence Review

**Check Investigation Completeness**:
```markdown
### Bug Investigation Evidence Checklist

#### Investigation Structure Present
- [ ] `bug-investigation/reproduction/` folder exists
- [ ] `bug-investigation/root-cause/` folder exists  
- [ ] `bug-investigation/resolution/` folder exists

#### Reproduction Evidence
- [ ] Failing test that captures original bug behavior
- [ ] Manual reproduction steps documented (if UI bug)
- [ ] Playwright evidence for UI issues (screenshots/videos)
- [ ] Error logs and system state captured

#### Root Cause Analysis  
- [ ] Code path analysis completed
- [ ] Data flow tracing documented
- [ ] Dependency analysis performed
- [ ] Timing/race condition investigation (if applicable)

#### Resolution Validation
- [ ] TDD evidence shows RED-GREEN-REFACTOR cycle
- [ ] Original failing test now passes
- [ ] Regression prevention tests added
- [ ] No side effects introduced to existing functionality
```

#### 2.2 Bug Fix Validation Process

**Verify Bug Resolution**:
1. **Original Bug Reproduction**: Confirm the failing test from investigation actually captured the bug
2. **Fix Effectiveness**: Verify the implemented fix resolves the specific issue
3. **Regression Prevention**: Ensure comprehensive test coverage prevents similar bugs
4. **No Side Effects**: Confirm fix doesn't break existing functionality

**Bug Fix Verification Commands**:
```bash
# Run the original failing test to confirm it now passes
npm test -- --testNamePattern="Bug.*[BUG-ID]"

# Run regression prevention test suite
npm test -- --testPathPattern="regression"

# Verify no existing functionality broken
npm test
```

#### 2.3 Bug Investigation Quality Gates

**Investigation Quality Requirements**:
- ✅ Root cause definitively identified and documented
- ✅ Bug reproducible through automated test
- ✅ Fix follows TDD methodology with full cycle evidence
- ✅ Comprehensive regression tests prevent recurrence
- ✅ Pattern documented for team learning
- ✅ Resolution validated across environments
- ✅ Performance impact assessed and acceptable

**Bug Investigation Template**:
```markdown
## Bug Investigation Validation
**STATUS**: ✅ PASS / ❌ FAIL

### Investigation Completeness
**Evidence Structure**: ✅ Complete / ❌ Missing elements
**Root Cause Analysis**: ✅ Definitive / ❌ Inconclusive
**Reproduction Method**: ✅ Reliable / ❌ Inconsistent

### Resolution Quality
**TDD Compliance**: ✅ Full cycle documented / ❌ Incomplete evidence
**Regression Prevention**: ✅ Comprehensive tests / ❌ Insufficient coverage
**Side Effects**: ✅ None detected / ❌ Issues found

### Investigation Learning
**Pattern Documentation**: ✅ Complete / ❌ Missing
**Team Knowledge**: ✅ Shared / ❌ Not documented

**Action Required If Failing**: 
Return to bug investigation process to address gaps before continuing.
```

### 3. Manual Functional Testing with Playwright

**Execute Manual Test Plan**:
- Load `_ai/manual-test-plan.md` and identify relevant test sections
- Review sections specified in the technical plan
- Use Playwright for browser-based functional testing
- Document test execution results

**Playwright Testing Process**:
1. **Setup Playwright Session**: Launch browser in headed mode for visibility
2. **Execute Test Cases**: Follow test procedures from manual test plan
3. **Capture Evidence**: Take screenshots of key test points
4. **Document Results**: Record pass/fail status and any issues found

**Manual Test Execution Template**:
```markdown
## Manual Functional Testing Results

**Test Plan Sections Executed**: 
- Section X.Y: [Section Name] - ✅ PASS / ❌ FAIL
- Section X.Z: [Section Name] - ✅ PASS / ❌ FAIL

**New Test Cases Executed**:
- [Test Case Description] - ✅ PASS / ❌ FAIL

**Playwright Session Details**:
- Browser: Chromium/Firefox/Safari
- Test Date: [Date]
- Tester: [Name/Role]

**Issues Found**:
- [Issue description and severity]

**Screenshots/Evidence**:
- [Links to screenshots in artifacts/ folder]
```

### 4. Quality Verification

#### 4.1 Objective Verification
**Questions to Answer**:
- Were all planning objectives achieved?
- Was user-approved scope delivered completely?
- Were all acceptance criteria met?
- Were technical plan commitments honored?
- Were package/library decisions followed as planned?
- Were manual test plan requirements satisfied?
- For bug fixes: Was the specific bug resolved completely?

**Verification Methods**:
- Compare deliverables against technical plan objectives
- Check that implementation matches planned approach
- Verify all checklist items from technical plan are complete
- Confirm user sign-off requirements were met
- Verify manual test plan sections passed
- For bug fixes: Validate original issue no longer occurs

#### 4.2 Functional Verification
**Questions to Answer**:
- Does core functionality work as specified?
- Do error handling mechanisms function properly?
- Do integration points work correctly?
- Does performance meet requirements?
- Do user workflows function end-to-end?
- For bug fixes: Does the fix work in all edge cases?

**Verification Methods**:
- Test core functionality manually
- Verify error cases are handled gracefully
- Check integration with existing components
- Run automated tests if available
- Test edge cases and boundary conditions
- For bug fixes: Test original bug scenario plus related edge cases

#### 4.3 Quality Assessment
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
- For bug fixes: Are regression prevention measures adequate?

**Verification Methods**:
- Review code quality and structure
- Check consistency with existing codebase patterns
- Verify test coverage against planned targets
- Review TDD evidence for completeness
- Assess technical debt impact
- Evaluate maintainability and readability
- For bug fixes: Validate regression test comprehensiveness

### 5. QA Documentation

**Create QA Report**: `qa-report.md` in the task folder

#### QA Report Template

```markdown
# QA Report: [Task Name]

**Date**: [Current Date]
**Technical Plan**: technical-plan.md
**QA Status**: ✅ PASS / ⚠️ NEEDS WORK / ❌ FAIL
**Task Type**: Feature Development / Bug Fix / Enhancement

## CI Status Verification
**Build Status**: ✅ PASS / ❌ FAIL
**Test Execution**: ✅ PASS / ❌ FAIL  
**Linting**: ✅ PASS / ❌ FAIL
**Other CI Checks**: ✅ PASS / ❌ FAIL

**Notes**: [Any specific CI-related observations]

## File Management Verification
**Redundant Files Check**: ✅ PASS / ❌ FAIL
**Original File Modifications**: ✅ PASS / ❌ FAIL
**Clean Repository**: ✅ PASS / ❌ FAIL

**Notes**: [Any specific file management observations]

## Bug Investigation Validation (If Applicable)
**Investigation Completeness**: ✅ PASS / ❌ FAIL / N/A
**Root Cause Analysis**: ✅ PASS / ❌ FAIL / N/A
**Resolution Quality**: ✅ PASS / ❌ FAIL / N/A
**Regression Prevention**: ✅ PASS / ❌ FAIL / N/A

**Notes**: [Bug investigation and resolution quality observations]

## Manual Functional Testing
**Test Plan Sections Executed**: [List sections from _ai/manual-test-plan.md]
**Test Cases Passed**: X/Y
**Critical Issues Found**: Yes/No
**Browser Used**: Chromium/Firefox/Safari
**Evidence Captured**: Yes/No (see artifacts/)

**Notes**: [Test execution observations and issues]

## Executive Summary

- **CI Status Verification**: ✅ PASS / ❌ FAIL
- **File Management Verification**: ✅ PASS / ❌ FAIL
- **Bug Investigation Validation**: ✅ PASS / ❌ FAIL / N/A
- **Manual Functional Testing**: ✅ PASS / ❌ FAIL
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

### 6. QA Status Classification

#### ✅ PASS
- **CI Status Verification passes** (all automated checks green)
- **File Management Verification passes** (no redundant files)
- **Bug Investigation Validation passes** (if applicable - investigation complete and resolution validated)
- **Manual Functional Testing passes** (test plan requirements met)
- All three quality verification areas pass
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

### Bug Investigation Quality Assessment
- Verify root cause analysis is definitive, not speculative
- Ensure bug reproduction is reliable and automated
- Validate that regression prevention is comprehensive
- Confirm fix addresses the specific issue without side effects

### Documentation Standards
- Keep QA reports concise but thorough
- Use clear, specific language in action items
- Provide examples of good vs. problematic code when helpful
- Link to relevant documentation or standards

## Example QA Report

```markdown
# QA Report: User Login Timeout Bug Fix

**Date**: 2024-12-06
**Technical Plan**: technical-plan.md  
**QA Status**: ✅ PASS
**Task Type**: Bug Fix

## Executive Summary

- **CI Status Verification**: ✅ PASS
- **File Management Verification**: ✅ PASS
- **Bug Investigation Validation**: ✅ PASS
- **Manual Functional Testing**: ✅ PASS
- **Objective Verification**: ✅ PASS
- **Functional Verification**: ✅ PASS
- **Quality Assessment**: ✅ PASS

## Bug Investigation Validation
**Investigation Completeness**: ✅ PASS
**Root Cause Analysis**: ✅ PASS  
**Resolution Quality**: ✅ PASS
**Regression Prevention**: ✅ PASS

**Notes**: Comprehensive investigation with clear root cause identified (session token expiration race condition). TDD resolution with extensive regression tests covering various timeout scenarios.

## 1. Objective Verification

### ✅ Requirements Met
- Login timeout bug resolved completely
- Session handling improved as planned
- User experience restored to expected behavior
- All acceptance criteria met

## 2. Functional Verification

### ✅ Functions Correctly
- Login process handles timeouts gracefully
- Session refresh mechanism works correctly
- Error messages provide clear user guidance
- All user workflows function end-to-end

## 3. Quality Assessment

### ✅ Quality Standards Met
- Test coverage increased to 94% (exceeded 85% target)
- Code follows established patterns
- Comprehensive regression test suite added
- Documentation updated with timeout handling patterns

## QA Sign-off

**Overall Result**: Ready for Production
**Next Steps**: Deploy to production
**Re-QA Required**: No
```

## QA Completion and Next Steps

Once QA is complete:

1. **Save QA Report**: Ensure `qa-report.md` is saved in task folder
2. **Update Manual Test Plan**: Add any new test cases discovered during QA to `_ai/manual-test-plan.md`
3. **Update Current State**: If returning to execution, update technical plan
4. **Archive on Completion**: Move entire task folder to `_ai/archive/` when done
5. **Transition**: 
   - If PASS: Ready for production or deployment
   - If NEEDS WORK/FAIL: Return to execution workflow

The QA workflow ensures systematic quality verification while providing clear, actionable feedback for the execution workflow to address any identified issues.