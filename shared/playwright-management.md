# Playwright Management Guidelines

This document outlines the required approach for working with Playwright browser automation tools across all workflows. These guidelines must be followed strictly to ensure reliable and efficient test automation.

## Critical Requirements

### 1. Browser Installation Prohibition

- **NEVER attempt to install browsers** - they are already installed in the environment
- Do NOT use `mcp__playwright__browser_install` under any circumstances
- Skip any browser installation steps mentioned in documentation

### 2. Error Handling Protocol

- **IMMEDIATELY stop execution if any Playwright operation fails**
- Consider Playwright failures as blocking issues that must be resolved before proceeding
- Do not continue with other implementation tasks until Playwright is working correctly
- Document all error details including console logs, screenshots, and network activity

### 3. Verification Requirements

- Verify browser automation is working with simple tests before proceeding to complex operations
- Include evidence of successful Playwright usage in the implementation documentation
- Never mark Playwright-dependent tasks as complete until browser automation is fully functional

## Debugging Approach

When Playwright operations fail:

1. **Capture detailed error information**:
   - Screenshot of the current page state
   - Browser console messages
   - Network request logs
   - Specific error messages from Playwright

2. **Analyze the root cause**:
   - Is it a selector issue? (element not found)
   - Is it a timing issue? (element not ready)
   - Is it a browser configuration issue?
   - Is it a network/connectivity issue?

3. **Implement targeted fixes**:
   - Update selectors if elements can't be found
   - Add appropriate waiting mechanisms
   - Fix network/connectivity issues
   - Resolve any browser configuration problems

4. **Verify the fix**:
   - Test the specific operation that failed
   - Run a complete end-to-end test to ensure no regression

5. **Document the solution**:
   - Record what was fixed and how
   - Note any patterns that might affect other tests
   - Update selectors or approaches in related tests

## Common Issues and Solutions

### Element Not Found

```javascript
// Problem
await page.click("button.submit"); // Element not found

// Solution - Use more reliable selectors
await page.click("button[data-testid='submit-button']");
// OR use text content
await page.click("button:has-text('Submit')");
```

### Timing Issues

```javascript
// Problem
await page.click(".dynamic-content"); // Fails because content isn't loaded yet

// Solution - Wait for element to be available
await page.waitForSelector(".dynamic-content");
await page.click(".dynamic-content");
```

### Page Navigation Issues

```javascript
// Problem
await page.click("a.next-page");
await page.fill("#search", "query"); // Fails because page is still loading

// Solution - Wait for navigation to complete
await Promise.all([
  page.waitForNavigation(),
  page.click("a.next-page")
]);
await page.fill("#search", "query");
```

## Integration with Test-Driven Development

Always follow these steps when implementing Playwright tests:

1. **RED Phase**: Write a failing Playwright test that defines expected browser behavior
2. **GREEN Phase**: Implement the minimal code to make the test pass
3. **REFACTOR Phase**: Improve the implementation without changing behavior

Document each phase with screenshots and evidence of test results.

## Reporting Issues

If you encounter persistent Playwright issues that cannot be resolved through debugging:

1. Document the exact issue with full error details
2. Create reproduction steps that consistently demonstrate the problem
3. Note environment details (OS, browser versions, etc.)
4. Escalate to the user or development team with this information

By following these guidelines, you will ensure reliable browser automation and efficient resolution of any Playwright-related issues.