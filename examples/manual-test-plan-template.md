# Manual Test Plan

**Project**: [Project Name]
**Version**: 1.0.0
**Last Updated**: [Date]
**Test Environment**: [Development/Staging/Production]

## Table of Contents

1. [Test Overview](#test-overview)
2. [Test Environment Setup](#test-environment-setup)
3. [Authentication & Authorization](#1-authentication--authorization)
4. [User Management](#2-user-management)
5. [Core Features](#3-core-features)
6. [Data Management](#4-data-management)
7. [Integration Points](#5-integration-points)
8. [Performance Testing](#6-performance-testing)
9. [Security Testing](#7-security-testing)
10. [Accessibility Testing](#8-accessibility-testing)
11. [Cross-Browser Testing](#9-cross-browser-testing)
12. [Mobile Responsiveness](#10-mobile-responsiveness)

## Test Overview

### Purpose
This document provides comprehensive manual test cases for functional verification of the application. Each test case includes step-by-step procedures, expected results, and Playwright automation references where applicable.

### Scope
- Functional testing of all user-facing features
- Integration testing of system components
- User workflow validation
- Edge case and error handling verification

### Test Data Requirements
- Test user accounts (see Environment Setup)
- Sample data files in `_ai/test-data/`
- API keys and credentials in `.env.test`

## Test Environment Setup

### Prerequisites
```bash
# Install Playwright
npm install -D @playwright/test

# Install browsers
npx playwright install

# Setup test environment
cp .env.example .env.test
```

### Test User Accounts
| Role | Email | Password | Purpose |
|------|-------|----------|---------|
| Admin | admin@test.com | Test123! | Full system access |
| Teacher | teacher@test.com | Test123! | Teacher workflows |
| Student | student@test.com | Test123! | Student workflows |
| Guest | guest@test.com | Test123! | Limited access |

### Playwright Helper Script
```javascript
// _ai/test-scripts/manual-test-helper.js
const { chromium } = require('playwright');

async function setupTestSession() {
  const browser = await chromium.launch({ headless: false });
  const context = await browser.newContext();
  const page = await context.newPage();
  await page.goto('http://localhost:30200');
  return { browser, context, page };
}
```

---

## 1. Authentication & Authorization

### 1.1 User Login
**Test ID**: AUTH-001
**Priority**: Critical
**Automated**: Partial

#### Test Steps:
1. Navigate to login page
2. Enter valid credentials
3. Click "Login" button
4. Verify redirect to dashboard

#### Expected Results:
- User is authenticated successfully
- Session token is stored
- User is redirected to appropriate dashboard based on role

#### Playwright Script:
```javascript
// _ai/test-scripts/auth-login.js
test('User can login successfully', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', 'student@test.com');
  await page.fill('[name="password"]', 'Test123!');
  await page.click('[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

#### Edge Cases:
- [ ] Invalid email format
- [ ] Incorrect password
- [ ] Account locked after multiple failures
- [ ] Session timeout handling

---

### 1.2 User Logout
**Test ID**: AUTH-002
**Priority**: High
**Automated**: Yes

#### Test Steps:
1. Login as any user
2. Click user profile menu
3. Select "Logout"
4. Verify redirect to login page

#### Expected Results:
- Session is terminated
- Cookies/tokens are cleared
- User cannot access protected routes

---

### 1.3 Password Reset
**Test ID**: AUTH-003
**Priority**: High
**Automated**: No

#### Test Steps:
1. Navigate to login page
2. Click "Forgot Password"
3. Enter registered email
4. Check email for reset link
5. Click reset link
6. Enter new password
7. Confirm password change

#### Expected Results:
- Reset email is sent within 1 minute
- Reset link is valid for 24 hours
- Password is successfully updated
- User can login with new password

---

## 2. User Management

### 2.1 User Profile Update
**Test ID**: USER-001
**Priority**: Medium
**Automated**: Partial

#### Test Steps:
1. Login as student
2. Navigate to profile settings
3. Update profile information:
   - Name
   - Bio
   - Avatar
4. Save changes

#### Expected Results:
- Changes are saved successfully
- Profile displays updated information
- Changes persist after logout/login

#### Test Data:
```json
{
  "name": "Test User Updated",
  "bio": "This is an updated bio with special characters: !@#$%",
  "avatar": "test-avatar.jpg"
}
```

---

### 2.2 User Role Management (Admin Only)
**Test ID**: USER-002
**Priority**: High
**Automated**: No

#### Test Steps:
1. Login as admin
2. Navigate to user management
3. Search for specific user
4. Change user role
5. Verify role change

#### Expected Results:
- Only admin can access user management
- Role changes take effect immediately
- User permissions update accordingly

---

## 3. Core Features

### 3.1 Assessment Submission (Student)
**Test ID**: CORE-001
**Priority**: Critical
**Automated**: Partial

#### Test Steps:
1. Login as student
2. Navigate to assessments
3. Select an assessment
4. Complete submission form:
   - Code repository URL
   - Comments
   - Attachments
5. Submit assessment

#### Expected Results:
- Submission is recorded
- Status changes to "Submitted"
- Teacher receives notification
- Submission appears in history

#### Playwright Script:
```javascript
// _ai/test-scripts/submit-assessment.js
test('Student can submit assessment', async ({ page }) => {
  await page.goto('/assessments');
  await page.click('[data-assessment-id="123"]');
  await page.fill('[name="repo_url"]', 'https://github.com/test/repo');
  await page.fill('[name="comments"]', 'Submission complete');
  await page.click('[type="submit"]');
  await expect(page.locator('.success-message')).toBeVisible();
});
```

---

### 3.2 Assessment Review (Teacher)
**Test ID**: CORE-002
**Priority**: Critical
**Automated**: No

#### Test Steps:
1. Login as teacher
2. Navigate to submissions
3. Select a student submission
4. Review submission:
   - View code repository
   - Read comments
   - Download attachments
5. Provide feedback
6. Assign grade
7. Submit review

#### Expected Results:
- Feedback is saved
- Grade is recorded
- Student receives notification
- Status updates to "Reviewed"

---

### 3.3 Kanban Board Interaction
**Test ID**: CORE-003
**Priority**: High
**Automated**: Partial

#### Test Steps:
1. Login as student
2. Navigate to assessment board
3. Drag assessment card between columns:
   - Not Started → In Progress
   - In Progress → Ready for Review
   - Ready for Review → Completed
4. Verify status updates

#### Expected Results:
- Cards move smoothly between columns
- Status updates persist
- Progress is tracked accurately
- Audit log records changes

---

## 4. Data Management

### 4.1 Data Export
**Test ID**: DATA-001
**Priority**: Medium
**Automated**: No

#### Test Steps:
1. Login as admin
2. Navigate to data management
3. Select export options:
   - Date range
   - Data types
   - Format (CSV/JSON)
4. Generate export
5. Download file

#### Expected Results:
- Export completes within 30 seconds
- File contains correct data
- Format matches selection
- Special characters handled properly

---

### 4.2 Data Import
**Test ID**: DATA-002
**Priority**: Medium
**Automated**: No

#### Test Steps:
1. Login as admin
2. Navigate to data management
3. Select import file
4. Map columns
5. Validate data
6. Confirm import

#### Expected Results:
- Validation identifies errors
- Import processes successfully
- Data appears in system
- Rollback available if needed

---

## 5. Integration Points

### 5.1 GitHub Integration
**Test ID**: INT-001
**Priority**: High
**Automated**: No

#### Test Steps:
1. Submit assessment with GitHub URL
2. Verify repository is accessible
3. Check commit history display
4. Test branch selection
5. Verify file preview

#### Expected Results:
- Repository data loads correctly
- Private repos require authentication
- Commit history is accurate
- File preview works

---

### 5.2 Email Notifications
**Test ID**: INT-002
**Priority**: High
**Automated**: No

#### Test Steps:
1. Trigger various notifications:
   - New submission
   - Review completed
   - Grade assigned
   - System announcements
2. Check email delivery
3. Verify email content
4. Test unsubscribe link

#### Expected Results:
- Emails delivered within 5 minutes
- Content is accurate
- Links work correctly
- Unsubscribe functions

---

## 6. Performance Testing

### 6.1 Page Load Times
**Test ID**: PERF-001
**Priority**: Medium
**Automated**: Yes

#### Test Criteria:
- Homepage: < 2 seconds
- Dashboard: < 3 seconds
- Assessment list: < 2 seconds
- Submission form: < 2 seconds

#### Playwright Script:
```javascript
// _ai/test-scripts/performance.js
test('Page load performance', async ({ page }) => {
  const startTime = Date.now();
  await page.goto('/dashboard');
  const loadTime = Date.now() - startTime;
  expect(loadTime).toBeLessThan(3000);
});
```

---

### 6.2 Concurrent Users
**Test ID**: PERF-002
**Priority**: Low
**Automated**: No

#### Test Steps:
1. Simulate 50 concurrent users
2. Perform common actions
3. Monitor response times
4. Check for errors

#### Expected Results:
- System remains responsive
- No timeout errors
- Database handles load
- UI remains functional

---

## 7. Security Testing

### 7.1 SQL Injection
**Test ID**: SEC-001
**Priority**: Critical
**Automated**: Partial

#### Test Steps:
1. Enter SQL injection attempts in:
   - Login form
   - Search fields
   - Submission forms
2. Monitor for errors
3. Check database logs

#### Test Payloads:
```sql
' OR '1'='1
'; DROP TABLE users; --
<script>alert('XSS')</script>
```

#### Expected Results:
- Input is sanitized
- No database errors
- No unauthorized access
- Proper error messages

---

### 7.2 Cross-Site Scripting (XSS)
**Test ID**: SEC-002
**Priority**: Critical
**Automated**: Partial

#### Test Steps:
1. Enter XSS payloads in all input fields
2. Check if scripts execute
3. Verify output encoding

#### Expected Results:
- Scripts do not execute
- Input is properly escaped
- No alert boxes appear
- Content displays safely

---

## 8. Accessibility Testing

### 8.1 Keyboard Navigation
**Test ID**: A11Y-001
**Priority**: High
**Automated**: Partial

#### Test Steps:
1. Navigate entire app using only keyboard
2. Test all interactive elements
3. Verify focus indicators
4. Test skip links

#### Expected Results:
- All features accessible via keyboard
- Focus indicators visible
- Tab order logical
- Skip links functional

---

### 8.2 Screen Reader Compatibility
**Test ID**: A11Y-002
**Priority**: High
**Automated**: No

#### Test Steps:
1. Enable screen reader
2. Navigate all pages
3. Test form interactions
4. Verify announcements

#### Expected Results:
- Content readable
- Forms labeled properly
- Status updates announced
- Images have alt text

---

## 9. Cross-Browser Testing

### 9.1 Browser Compatibility Matrix
**Test ID**: BROWSER-001
**Priority**: High
**Automated**: Yes

| Browser | Version | Status | Notes |
|---------|---------|--------|-------|
| Chrome | Latest | ✅ | Primary browser |
| Firefox | Latest | ✅ | Full support |
| Safari | Latest | ✅ | Minor CSS differences |
| Edge | Latest | ✅ | Full support |

#### Playwright Script:
```javascript
// _ai/test-scripts/cross-browser.js
const browsers = ['chromium', 'firefox', 'webkit'];
for (const browserType of browsers) {
  test(`Works in ${browserType}`, async ({ page }) => {
    // Run critical user flows
  });
}
```

---

## 10. Mobile Responsiveness

### 10.1 Responsive Design
**Test ID**: MOBILE-001
**Priority**: High
**Automated**: Partial

#### Test Breakpoints:
- Mobile: 320px, 375px, 414px
- Tablet: 768px, 1024px
- Desktop: 1280px, 1920px

#### Test Steps:
1. Resize browser to each breakpoint
2. Verify layout adjusts properly
3. Test touch interactions
4. Check menu behavior

#### Expected Results:
- Layout responsive at all breakpoints
- No horizontal scrolling
- Touch targets appropriate size
- Menus collapse/expand correctly

#### Playwright Script:
```javascript
// _ai/test-scripts/responsive.js
const viewports = [
  { width: 375, height: 667 },  // iPhone
  { width: 768, height: 1024 }, // iPad
  { width: 1920, height: 1080 } // Desktop
];

for (const viewport of viewports) {
  test(`Responsive at ${viewport.width}px`, async ({ page }) => {
    await page.setViewportSize(viewport);
    await page.goto('/');
    await expect(page).toHaveScreenshot(`home-${viewport.width}.png`);
  });
}
```

---

## Test Execution Tracking

### Test Run Template
```markdown
## Test Run: [Date]
**Tester**: [Name]
**Environment**: [Dev/Staging/Prod]
**Browser**: [Chrome/Firefox/Safari]

### Results Summary
- Total Tests: X
- Passed: X
- Failed: X
- Blocked: X
- Skipped: X

### Failed Tests
| Test ID | Issue Description | Severity | Screenshot |
|---------|------------------|----------|------------|
| | | | |

### Notes
[Any additional observations or issues]
```

---

## Maintenance

### Adding New Test Cases
1. Create test case following the template format
2. Assign unique Test ID
3. Set appropriate priority
4. Add to relevant section
5. Create Playwright script if applicable
6. Update table of contents

### Updating Existing Tests
1. Update test steps as features change
2. Revise expected results
3. Update Playwright scripts
4. Note changes in version history

### Version History
| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0.0 | [Date] | Initial test plan | [Name] |
| | | | |

---

## Appendix

### A. Test Data Files
Located in `_ai/test-data/`:
- `users.json` - Test user data
- `assessments.json` - Sample assessments
- `submissions.csv` - Bulk submission data
- `large-file.pdf` - Performance testing file

### B. Playwright Configuration
```javascript
// playwright.config.js
module.exports = {
  testDir: './_ai/test-scripts',
  timeout: 30000,
  use: {
    baseURL: 'http://localhost:30200',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
};
```

### C. Environment Variables
```bash
# .env.test
TEST_BASE_URL=http://localhost:30200
TEST_ADMIN_EMAIL=admin@test.com
TEST_ADMIN_PASSWORD=Test123!
TEST_TIMEOUT=30000
```

### D. Quick Test Commands
```bash
# Run all manual tests with Playwright
npx playwright test --headed

# Run specific test file
npx playwright test auth-login.js

# Run with specific browser
npx playwright test --browser=firefox

# Generate test report
npx playwright show-report
```