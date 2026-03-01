# QA Work Sample — Lock-in (Feature: Generate Study Summary)

**Document Control**

- **Version**: 1.0
- **Author**: QA Team Lead
- **Last Updated**: March 2, 2026
- **Review Status**: Approved
- **Related Documents**:
  - Technical Specification: `SPEC-2026-045`
  - Requirements Document: `REQ-SUMMARY-001`

---

## Table of Contents

1. [Feature Overview](#1-feature-overview)
2. [Scope & Objectives](#2-scope--objectives)
3. [Risk Assessment](#3-risk-assessment)
4. [Test Environment](#4-test-environment)
5. [Test Strategy](#5-test-strategy)
6. [Entry & Exit Criteria](#6-entry--exit-criteria)
7. [Test Cases](#7-test-cases)
8. [Test Data Management](#8-test-data-management)
9. [Bug Report Examples](#9-bug-report-examples)
10. [Test Metrics & Reporting](#10-test-metrics--reporting)
11. [Regression Suite](#11-regression-suite)
12. [Sign-off](#12-sign-off)

---

## 1. Feature overview

**Feature ID**: `FEAT-SUMMARY-001`  
**Priority**: High  
**User Story**: As a student, I want to generate a study summary from a transcript or video so that I can quickly revise key points without re-watching the entire content.

### 1.1 Acceptance Criteria

- ✓ User can initiate summary generation from any valid transcript
- ✓ Generation progress is clearly communicated to the user
- ✓ Generated summaries are persistent and retrievable
- ✓ Errors are handled gracefully with actionable feedback
- ✓ Feature respects rate limits and tier restrictions
- ✓ Summary quality meets minimum readability standards (validated by product)
- ✓ Average generation time < 15 seconds for standard transcripts (<30 min)

### 1.2 Primary User Goals

- Create an accurate, concise summary from an existing transcript
- Avoid data loss or confusion if something goes wrong (network, auth, etc.)
- Keep the extension responsive and predictable while generation is in progress
- Receive clear feedback on progress, limits, and errors

---

## 2. Scope & Objectives

### 2.1 Testing Scope

**In Scope:**

- Functional testing of summary generation feature (end-to-end)
- UI/UX validation (loading states, error messages, button behaviour)
- Integration testing (extension ↔ backend API)
- Error handling and recovery scenarios
- Authentication and authorization validation
- Rate limiting and tier-based restrictions
- Data persistence and retrieval
- Browser compatibility (Chrome stable, recent versions)
- Basic performance validation (response times, UI responsiveness)
- Accessibility basics (keyboard navigation, screen reader compatibility)

**Out of Scope:**

- AI model accuracy/quality evaluation (owned by ML team)
- Load/stress testing at scale (handled by performance team)
- Cross-browser testing beyond Chrome (future iteration)
- Mobile app testing (separate test plan)
- Backend unit/integration tests (owned by dev team)

### 2.2 Test Objectives

1. Verify the feature meets all acceptance criteria and requirements
2. Ensure robust error handling prevents user frustration or data loss
3. Validate performance within acceptable parameters
4. Identify critical and high-severity defects before production release
5. Establish regression suite for future releases
6. Ensure accessibility standards are met (WCAG 2.1 AA minimum)

---

## 3. Risk Assessment

| Risk ID | Risk Description                                                    | Likelihood | Impact | Mitigation Strategy                                                                  | Owner       |
| ------- | ------------------------------------------------------------------- | ---------- | ------ | ------------------------------------------------------------------------------------ | ----------- |
| RSK-001 | Network failures during generation cause data loss                  | High       | High   | Implement retry mechanism, clear error messages, test network interruption scenarios | Dev Team    |
| RSK-002 | Rate limiting not enforced correctly, leading to backend overload   | Medium     | High   | Thorough testing of rate limits across tiers, monitor backend metrics                | QA + DevOps |
| RSK-003 | Large transcripts cause browser freeze or timeout                   | Medium     | High   | Test boundary values, implement progress indicators, timeout handling                | Dev Team    |
| RSK-004 | Auth token expiry during generation causes silent failure           | Medium     | Medium | Test token expiry scenarios, implement token refresh mechanism                       | Dev Team    |
| RSK-005 | Malformed transcripts break parser, exposing error details          | Low        | Medium | Fuzz testing with malformed inputs, sanitize error messages                          | Dev Team    |
| RSK-006 | Summary generation degrades performance of other extension features | Low        | Medium | Performance testing with concurrent operations                                       | QA Team     |
| RSK-007 | Accessibility issues prevent keyboard-only navigation               | Medium     | Medium | Accessibility audit, keyboard navigation testing                                     | QA Team     |
| RSK-008 | Cross-origin issues in specific YouTube page contexts               | Low        | High   | Test across various YouTube page types (video, playlist, embed)                      | QA Team     |

---

## 4. Test environment

### 4.1 Hardware & OS

- **Primary**: Windows 10 Pro (22H2), 16GB RAM, Intel i7
- **Secondary**: Windows 11 (23H2), 16GB RAM, AMD Ryzen 7
- **Screen Resolutions**: 1920×1080, 2560×1440, 3840×2160

### 4.2 Browser & Extension

- **Browser**: Chrome 132.0.x (stable channel)
- **Extension**: Lock-in v0.5.0
  - Build: `abcd1234` (staging)
  - Manifest version: V3
- **Browser Settings**: Default (no ad blockers or conflicting extensions)

### 4.3 Backend Environment

- **API Endpoint**: `https://api-staging.lockin.app`
- **Environment**: `staging`
- **Database**: Staging DB (isolated from production)
- **Feature Flags**: `summary_generation_v2=enabled`

### 4.4 Test Accounts

| Account Type  | Email                            | Tier | Rate Limit | Auth Method    | Notes                       |
| ------------- | -------------------------------- | ---- | ---------- | -------------- | --------------------------- |
| Free Tier     | `qa.free+summary@lockin.test`    | Free | 5/day      | Email/Password | Default test account        |
| Pro Tier      | `qa.pro+summary@lockin.test`     | Pro  | 50/day     | Email/Password | Premium features enabled    |
| Expired Trial | `qa.expired+summary@lockin.test` | Free | 5/day      | Email/Password | Trial ended, downgraded     |
| Edge Case     | `qa.edge+summary@lockin.test`    | Free | 5/day      | Google OAuth   | Unicode name, special chars |

### 4.5 Test Content Repository

| Content ID         | Description          | Duration | Token Count | Special Characteristics |
| ------------------ | -------------------- | -------- | ----------- | ----------------------- |
| `TC-SHORT-001`     | Math lecture         | 2 min    | ~500        | Clean, well-formatted   |
| `TC-STANDARD-001`  | History documentary  | 15 min   | ~4,000      | Typical use case        |
| `TC-LONG-001`      | Full course lecture  | 90 min   | ~30,000     | Near upper boundary     |
| `TC-MALFORMED-001` | Corrupted transcript | 10 min   | ~3,000      | Missing timestamps      |
| `TC-MARKUP-001`    | Transcript with HTML | 8 min    | ~2,500      | HTML entities, tags     |
| `TC-EMPTY-001`     | Empty transcript     | 0 min    | 0           | Edge case               |
| `TC-SPECIAL-001`   | Non-English content  | 12 min   | ~3,500      | Chinese subtitles       |

### 4.6 Test Data Reset Procedure

1. Clear extension local storage: `chrome.storage.local.clear()`
2. Clear browser cache and cookies for `*.lockin.app`
3. Reset test account data via admin API: `POST /api/admin/reset-account`
4. Verify clean state: no cached summaries or pending operations
5. Log in fresh and verify session establishment

### 4.7 Monitoring & Debugging Tools

- Chrome DevTools (Console, Network, Application)
- Extension background service worker logs
- Staging API monitoring dashboard
- Sentry error tracking (staging environment)
- Network throttling profiles: Fast 3G, Slow 3G, Offline

---

## 5. Test strategy

### 5.1 Testing Approach

**Test Design Techniques:**

1. **Equivalence Partitioning**: Group transcript lengths (short, standard, long); user tiers (free, pro); network conditions (good, poor, offline)
2. **Boundary Value Analysis**: Test at limits (0 chars, max token count, rate limit thresholds)
3. **State Transition Testing**: Validate state machine (idle → generating → success/error → idle)
4. **Error Guessing**: Explore edge cases based on experience (double-submit, navigation, tab switching)
5. **Decision Table Testing**: Auth status × network condition × transcript state combinations

### 5.2 Test Types & Coverage

#### 5.2.1 Functional Testing

- **Coverage Goal**: 100% of acceptance criteria, 90%+ of user flows
- **Method**: Manual exploratory + structured test cases
- **Focus**: Feature correctness, data integrity, user workflows

#### 5.2.2 Integration Testing

- **Coverage Goal**: All critical API endpoints
- **Method**: Manual + automated API tests
- **Focus**: Extension ↔ backend communication, auth flow, data synchronization

#### 5.2.3 UI/UX Testing

- **Coverage Goal**: All interactive states and error messages
- **Method**: Manual testing with design spec validation
- **Focus**: Loading states, button behaviour, error message clarity, responsive design

#### 5.2.4 Performance Testing (Basic)

- **Benchmarks**:
  - Summary generation < 15s (standard transcripts)
  - UI response time < 200ms for user actions
  - No main-thread freezes > 500ms
- **Method**: Manual timing + Chrome Performance profiler
- **Metrics**: Time to Interactive, First Contentful Paint, API response times

#### 5.2.5 Accessibility Testing

- **Standards**: WCAG 2.1 Level AA
- **Checks**:
  - Keyboard navigation (Tab, Enter, Escape)
  - Screen reader compatibility (NVDA/JAWS)
  - Focus management during loading states
  - Color contrast ratios (4.5:1 minimum)
  - ARIA labels for dynamic content

#### 5.2.6 Security Testing (Basic)

- Auth token handling (no exposure in logs/console)
- XSS prevention in summary output
- HTTPS enforcement
- Permission scope validation

### 5.3 Test Execution Strategy

- **Phase 1 - Component Testing** (Days 1-2): Happy paths, core functionality
- **Phase 2 - Integration Testing** (Days 3-4): Error scenarios, edge cases, API integration
- **Phase 3 - Regression Testing** (Day 5): Smoke tests, regression suite
- **Phase 4 - Exploratory Testing** (Day 6): Unscripted exploration, usability assessment
- **Phase 5 - Sign-off** (Day 7): Defect triage, metrics review, go/no-go decision

### 5.4 Defect Management

**Severity Definitions:**

- **Critical**: Feature completely unusable or causes data loss
- **High**: Major functionality broken, workaround exists
- **Medium**: Partial functionality affected, minor impact on UX
- **Low**: Cosmetic issues, minor UX improvements

**Priority Definitions:**

- **P0**: Blocks release, must fix immediately
- **P1**: Should fix before release
- **P2**: Can defer to next release
- **P3**: Backlog

**Exit Criteria for Release:**

- 0 Critical/P0 defects open
- < 2 High/P1 defects open (with go-ahead from Product)
- All acceptance criteria validated
- Regression suite passes 100%

---

## 6. Entry & Exit Criteria

### 6.1 Entry Criteria

- ✓ Feature development complete (dev team sign-off)
- ✓ Test environment provisioned and accessible
- ✓ Test accounts created and verified
- ✓ Test data repository populated
- ✓ Extension build deployed to staging
- ✓ Backend API endpoints available and documented
- ✓ Test plan reviewed and approved by stakeholders

### 6.2 Exit Criteria

- ✓ All planned test cases executed (100%)
- ✓ Test coverage meets targets (90%+ for critical paths)
- ✓ 0 Critical defects, < 2 High defects
- ✓ All P0/P1 defects resolved and verified
- ✓ Regression suite passes without failures
- ✓ Performance benchmarks met
- ✓ Accessibility audit passed (WCAG 2.1 AA)
- ✓ Test summary report published
- ✓ Stakeholder sign-off obtained

---

## 7. Test cases

### 7.1 Test Case Overview

| Category       | Test Cases       | Priority | Req. Traceability |
| -------------- | ---------------- | -------- | ----------------- |
| Happy Path     | TC-001 to TC-005 | P0       | REQ-SUMMARY-001   |
| Error Handling | TC-010 to TC-020 | P0-P1    | REQ-SUMMARY-002   |
| Performance    | TC-030 to TC-035 | P1       | REQ-SUMMARY-003   |
| Accessibility  | TC-040 to TC-045 | P1       | REQ-SUMMARY-004   |
| Security       | TC-050 to TC-053 | P0       | REQ-SUMMARY-005   |

### 7.2 Happy Path Test Cases

**TC-001: Generate summary from valid transcript (standard length)**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-001
- **Test Data**: `TC-STANDARD-001`
- **Preconditions**:
  - User logged in as `qa.free+summary@lockin.test` (free tier)
  - Transcript `TC-STANDARD-001` loaded successfully in Study tab
  - User has not exceeded daily rate limit (< 5 summaries generated today)
- **Test Steps**:
  1. Navigate to YouTube video with transcript `TC-STANDARD-001`
  2. Open the Lock-in extension Study tab
  3. Verify transcript is visible and loaded
  4. Click "Generate summary" button
  5. Observe loading state and wait for completion
  6. Verify summary content appears
- **Expected Results**:
  - Loading indicator (spinner/progress) appears immediately (< 200ms)
  - "Generate summary" button becomes disabled during processing
  - Button text changes to "Generating..." or similar
  - Progress is visible to user (estimated time or progress bar)
  - On success (< 15s), summary appears in Summary panel
  - Summary content is non-empty (minimum 50 characters)
  - Summary is formatted with proper line breaks and structure
  - UI returns to idle state with button re-enabled
  - Console shows no errors (error level)
  - Network tab shows successful API call (status 200)
- **Postconditions**:
  - Summary is persisted and retrievable on page reload
  - Rate limit counter incremented (4 remaining for free tier)

**TC-002: Retrieve existing summary on page reload**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-001
- **Preconditions**:
  - User has previously generated summary for transcript `TC-STANDARD-001`
  - Summary was successfully saved (verified in TC-001)
- **Test Steps**:
  1. Close and reopen Chrome browser
  2. Navigate to same YouTube video
  3. Open Lock-in extension Study tab
  4. Wait for content to load
- **Expected Results**:
  - Previously generated summary loads automatically
  - Summary content matches what was generated in TC-001
  - No duplicate summaries appear
  - Correct transcript-summary association maintained
  - Loading time < 2 seconds
- **Postconditions**: Summary remains accessible

**TC-003: Generate summary for different transcripts sequentially**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-001
- **Test Data**: `TC-SHORT-001`, `TC-STANDARD-001`
- **Test Steps**:
  1. Generate summary for `TC-SHORT-001`
  2. Verify summary A is created successfully
  3. Navigate to different video with `TC-STANDARD-001`
  4. Generate summary for `TC-STANDARD-001`
  5. Verify summary B is created successfully
  6. Navigate back to first video
  7. Verify summary A is still accessible
- **Expected Results**:
  - Each transcript gets its own unique summary
  - No cross-contamination between summaries
  - Both summaries remain accessible independently
  - Rate limit correctly tracks both operations

**TC-004: Pro tier user generates summary (higher rate limit)**

- **Priority**: P1
- **Requirement**: REQ-SUMMARY-001, REQ-SUMMARY-002
- **Test Data**: `TC-STANDARD-001`
- **Preconditions**:
  - User logged in as `qa.pro+summary@lockin.test` (Pro tier)
- **Test Steps**:
  1. Generate summary successfully
  2. Verify rate limit reflects Pro tier (50/day)
- **Expected Results**:
  - Summary generates successfully
  - UI indicates Pro tier benefits (e.g., "49 summaries remaining today")
  - No functional differences in summary quality/content

**TC-005: Regenerate summary for same transcript**

- **Priority**: P1
- **Requirement**: REQ-SUMMARY-001
- **Preconditions**:
  - User has existing summary for transcript
- **Test Steps**:
  1. Open Study tab with existing summary
  2. Click "Regenerate summary" (or equivalent)
  3. Confirm regeneration if prompted
  4. Wait for completion
- **Expected Results**:
  - Clear confirmation prompt appears (if destructive action)
  - New summary replaces old one (or versioning if supported)
  - No duplicate summaries created
  - User explicitly acknowledges potential data loss

### 7.3 Error Handling & Edge Cases

**TC-010: Empty or unavailable transcript**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-002
- **Test Data**: `TC-EMPTY-001`
- **Test Steps**:
  1. Navigate to video with no transcript available
  2. Open Study tab
  3. Observe "Generate summary" button state
  4. Attempt to trigger summary generation (if possible)
- **Expected Results**:
  - Button is disabled with tooltip: "Transcript not available"
  - Clear message displayed: "Transcript not yet available. We'll notify you when it's ready."
  - No API call made to backend
  - No loading state entered
- **Alternative**: If button is enabled:
  - Backend returns validation error (400)
  - User sees: "Unable to generate summary. No transcript found."

**TC-011: Very long transcript (boundary test)**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-002, REQ-SUMMARY-003
- **Test Data**: `TC-LONG-001` (90 min, ~30k tokens)
- **Test Steps**:
  1. Load long transcript in Study tab
  2. Click "Generate summary"
  3. Monitor progress and completion
- **Expected Results**:
  - System accepts request (within documented limits)
  - Clear progress indicator shown (e.g., "This may take up to 30s")
  - Summary generates within 30 seconds
  - No browser freeze or unresponsiveness
  - User can scroll/interact with page during generation
  - No duplicate requests sent
- **Alternative**: If exceeds limit:
  - Clear error before generation: "This transcript is too long (90 min). Maximum supported length is 60 minutes."
  - Suggestion provided: "Try a shorter video or skip to specific sections."

**TC-012: Network disconnection mid-generation**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-002
- **Test Steps**:
  1. Start generating summary for standard transcript
  2. After 3 seconds, disable network via DevTools (set to "Offline")
  3. Wait for timeout/error handling
  4. Re-enable network
  5. Attempt to retry
- **Expected Results**:
  - Request times out gracefully (within 30s)
  - User-friendly error message: "Network connection lost. Please check your connection and try again."
  - "Generate summary" button becomes re-enabled
  - No infinite loading spinner
  - No partial/corrupted summary saved
  - Retry button or re-enabled original button allows retry
  - Successful retry after network restored

**TC-013: Navigation away during generation**

- **Priority**: P1
- **Test Steps**:
  1. Start generating summary
  2. Switch to different Chrome tab
  3. Wait 10 seconds
  4. Return to original tab
  5. Navigate to different YouTube video
  6. Return to original video
- **Expected Results**:
  - Background process continues (or cancels gracefully)
  - On return: either shows completed summary or clear status
  - No ghost/stuck loading indicators
  - State is recoverable and predictable
  - No duplicate API calls from navigation

**TC-014: Double-submit protection**

- **Priority**: P0
- **Test Steps**:
  1. Click "Generate summary" button twice rapidly (< 100ms apart)
  2. Try keyboard (Enter) and mouse simultaneously
  3. Monitor network requests
- **Expected Results**:
  - Only ONE API request sent (idempotent behaviour)
  - Button disabled immediately on first click
  - Subsequent clicks ignored/debounced
  - No duplicate summaries created
  - No race conditions or inconsistent state

**TC-015: Expired authentication session**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-005
- **Preconditions**:
  - Simulate expired auth token (manually expire in dev tools or wait for natural expiry)
- **Test Steps**:
  1. With expired session, click "Generate summary"
  2. Observe error handling
- **Expected Results**:
  - API returns 401 Unauthorized
  - Clear, actionable error: "Your session has expired. Please sign in again."
  - User redirected to login or shown login prompt
  - After re-auth, user can resume and retry
  - No stuck loading state
  - No sensitive data exposed in error messages

**TC-016: Malformed transcript data**

- **Priority**: P1
- **Test Data**: `TC-MALFORMED-001`, `TC-MARKUP-001`
- **Test Steps**:
  1. Load transcript with missing timestamps and broken segments
  2. Generate summary
  3. Observe handling
- **Expected Results**:
  - System sanitizes/normalizes input OR returns safe error
  - No raw HTML/markup exposed in UI
  - No stack traces visible to user
  - Error message if unable to process: "We couldn't process this transcript. Please try refreshing the page."
  - No browser crash or extension lockup

**TC-017: Rate limit exceeded**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-002
- **Preconditions**:
  - Free tier account at 4/5 daily summaries used
- **Test Steps**:
  1. Generate 5th summary (should succeed)
  2. Attempt 6th summary
- **Expected Results**:
  - 5th summary succeeds with warning: "1 summary remaining today"
  - 6th attempt shows clear rate limit message:
    - "Daily limit reached (5/5). Upgrade to Pro for 50 summaries/day, or try again tomorrow."
    - Upgrade CTA button shown
    - Button disabled or shows clear state
  - Backend returns 429 Too Many Requests
  - Client does not retry automatically
  - Existing summaries remain accessible

**TC-018: Backend service unavailable (500 error)**

- **Priority**: P0
- **Test Steps**:
  1. Simulate backend error (mock 500 response or staging downtime)
  2. Attempt to generate summary
- **Expected Results**:
  - User sees: "Service temporarily unavailable. Please try again in a few moments."
  - Retry button provided
  - No technical error details exposed
  - Graceful fallback, no app crash

**TC-019: Timeout (request exceeds maximum duration)**

- **Priority**: P1
- **Test Steps**:
  1. Simulate slow backend (intentionally delayed response > 30s)
  2. Observe client timeout handling
- **Expected Results**:
  - Client times out request after 30s
  - Error message: "Request took too long. Please try again."
  - Button re-enabled for retry
  - No memory leaks from hanging requests

**TC-020: Concurrent summaries for different tabs**

- **Priority**: P2
- **Test Steps**:
  1. Open two different YouTube videos in separate tabs
  2. Initiate summary generation in both simultaneously
- **Expected Results**:
  - Both requests handled independently
  - No interference or cross-tab pollution
  - Correct summaries associated with correct transcripts
  - Rate limit correctly incremented for both

### 7.4 Performance Test Cases

**TC-030: Generation time for standard transcript**

- **Priority**: P1
- **Requirement**: REQ-SUMMARY-003
- **Test Data**: `TC-STANDARD-001`
- **Acceptance Criteria**: < 15 seconds
- **Test Steps**:
  1. Clear cache and state
  2. Generate summary
  3. Measure time from button click to summary display
  4. Repeat 5 times, calculate average
- **Expected Results**:
  - Average generation time < 15s
  - No generation exceeds 20s
  - Consistent performance across runs

**TC-031: UI responsiveness during generation**

- **Priority**: P1
- **Test Steps**:
  1. Start generation
  2. Attempt to scroll page, click other UI elements
  3. Record any freezes via Chrome Performance profiler
- **Expected Results**:
  - No main -thread freeze > 500ms
  - Page remains scrollable and interactive
  - Other extension features remain functional

**TC-032: Memory usage**

- **Priority**: P2
- **Test Steps**:
  1. Monitor memory usage (Chrome Task Manager)
  2. Generate 10 summaries sequentially
  3. Check for memory leaks
- **Expected Results**:
  - Extension memory usage remains stable (< 100MB)
  - No continuous memory growth
  - Memory released after operations complete

**TC-033: Initial load time with existing summary**

- **Priority**: P1
- **Acceptance Criteria**: < 2 seconds
- **Test Steps**:
  1. Navigate to page with existing summary
  2. Open Study tab
  3. Measure time to summary display
- **Expected Results**: Summary visible within 2 seconds

**TC-034: API response time**

- **Priority**: P1
- **Test Steps**:
  1. Monitor network tab for API calls
  2. Record response times for summary generation endpoint
- **Expected Results**:
  - Standard transcripts: API responds within 10s
  - Long transcripts: API responds within 25s

### 7.5 Accessibility Test Cases

**TC-040: Keyboard navigation**

- **Priority**: P1
- **Requirement**: REQ-SUMMARY-004
- **Standards**: WCAG 2.1 Level AA
- **Test Steps**:
  1. Use Tab key to navigate to "Generate summary" button
  2. Press Enter to activate
  3. Monitor loading state with keyboard only
  4. Tab through summary content once displayed
- **Expected Results**:
  - Button receives clear focus indicator (visible outline)
  - Enter/Space activates button
  - Focus trapped appropriately during loading modal (if applicable)
  - Summary content is keyboard-accessible
  - No keyboard traps

**TC-041: Screen reader compatibility**

- **Priority**: P1
- **Tools**: NVDA or JAWS
- **Test Steps**:
  1. Enable screen reader
  2. Navigate to and activate "Generate summary"
  3. Monitor announcements during loading
  4. Listen to summary content readout
- **Expected Results**:
  - Button has clear label: "Generate summary"
  - Loading state announced: "Generating summary, please wait"
  - Completion announced: "Summary generated successfully"
  - Summary content properly structured with headings (if applicable)
  - ARIA live regions update appropriately

**TC-042: Focus management**

- **Priority**: P1
- **Test Steps**:
  1. Activate summary generation with keyboard
  2. Monitor focus during and after generation
- **Expected Results**:
  - Focus remains on logical element (button or loading indicator)
  - Focus moves to summary container when complete
  - Focus never lost or sent to unexpected location

**TC-043: Color contrast**

- **Priority**: P1
- **Tools**: Chrome DevTools or WAVE
- **Test Steps**:
  1. Inspect all text and button states
  2. Measure contrast ratios
- **Expected Results**:
  - Normal text: minimum 4.5:1 contrast
  - Large text (18pt+): minimum 3:1 contrast
  - Button states (default, hover, disabled) all meet requirements

**TC-044: Text resizing**

- **Priority**: P2
- **Test Steps**:
  1. Increase browser zoom to 200%
  2. Generate and view summary
- **Expected Results**:
  - UI remains usable and readable
  - No text truncation or overlap
  - Buttons remain clickable

**TC-045: Reduced motion preference**

- **Priority**: P2
- **Test Steps**:
  1. Enable system "Reduce motion" setting
  2. Generate summary
- **Expected Results**:
  - Animations disabled or simplified
  - Loading indicator uses non-animated alternative (if applicable)

### 7.6 Security Test Cases

**TC-050: Auth token security**

- **Priority**: P0
- **Requirement**: REQ-SUMMARY-005
- **Test Steps**:
  1. Generate summary
  2. Inspect Console, Network tab, Local Storage
  3. Check for token exposure
- **Expected Results**:
  - No auth tokens logged to console
  - Tokens not visible in plain text
  - HTTPS enforced for all API calls
  - Secure, HttpOnly cookies used (if applicable)

**TC-051: XSS prevention in summary output**

- **Priority**: P0
- **Test Data**: Mock backend response containing `<script>alert('XSS')</script>`
- **Test Steps**:
  1. Return summary with malicious script tags
  2. Render summary in UI
- **Expected Results**:
  - Script tags sanitized/escaped
  - No JavaScript execution
  - Content displayed as plain text

**TC-052: HTTPS enforcement**

- **Priority**: P0
- **Test Steps**:
  1. Inspect all network requests during summary generation
- **Expected Results**:
  - All API calls use HTTPS
  - No mixed content warnings
  - SSL/TLS properly configured

**TC-053: Permission scope validation**

- **Priority**: P1
- **Test Steps**:
  1. Review extension manifest permissions
  2. Verify only necessary permissions requested
- **Expected Results**:
  - Extension requests minimal required permissions
  - No excessive/unnecessary data access

---

## 8. Test Data Management

### 8.1 Test Data Strategy

- **Repository**: Centralized test content library with version control
- **Ownership**: QA team maintains test transcripts; product team reviews for realism
- **Refresh Cycle**: Quarterly review to ensure relevance and coverage
- **Privacy**: All test data anonymized; no real user data used in testing

### 8.2 Test Data Inventory

See section 4.5 for complete test content repository.

### 8.3 Data Validation

- Each test transcript validated for format consistency
- Token counts verified via tokenizer tool matching production environment
- Malformed test data intentionally crafted and documented
- Regular audits to prevent data rot

---

## 9. Bug Report Examples

### 9.1 Bug Report Template

**Standard Format for All Defects:**

- **Bug ID**: [Tracking system ID]
- **Title**: [Concise, descriptive title]
- **Severity**: Critical / High / Medium / Low
- **Priority**: P0 / P1 / P2 / P3
- **Status**: New / In Progress / Resolved / Closed
- **Environment**: [OS, browser, extension version, backend environment]
- **Test Case**: [Reference to failed test case, if applicable]
- **Requirement**: [Traceability to requirement]
- **Steps to Reproduce**: [Numbered, repeatable steps]
- **Expected Behavior**: [Clear description of correct behavior]
- **Actual Behavior**: [Clear description of observed behavior]
- **Attachments**: [Screenshots, videos, logs, network traces]
- **Notes**: [Additional context, workarounds, related issues]
- **Reproducibility**: Always / Sometimes / Rare
- **Assignee**: [Developer assigned]
- **Discovered By**: [QA engineer name]
- **Discovered Date**: [Date]

---

### 9.2 Sample Bug Reports

**BUG-001: Summary button stays disabled after network error**

- **Bug ID**: `BUG-SUMMARY-001`
- **Title**: Generate Summary button remains disabled after network error, blocking retry
- **Severity**: High
- **Priority**: P0 (blocks retry functionality, poor UX)
- **Status**: New → Resolved
- **Environment**:
  - OS: Windows 11 Pro (23H2)
  - Browser: Chrome 132.0.6834.83 (stable)
  - Extension: Lock-in v0.5.0 (build `abcd1234`)
  - Backend: Staging (`https://api-staging.lockin.app`)
- **Test Case**: TC-012 (Network error mid-generation)
- **Requirement**: REQ-SUMMARY-002 (Error handling)
- **Reproducibility**: Always (100% reproduction rate in 5 attempts)

**Steps to Reproduce**:

1. Log in as `qa.free+summary@lockin.test`
2. Navigate to YouTube video with transcript `TC-STANDARD-001`
3. Open Lock-in extension Study tab
4. Click "Generate summary" button
5. Wait 3 seconds for request to initiate
6. Open Chrome DevTools → Network tab → Set throttling to "Offline"
7. Wait 10-15 seconds for error to appear
8. Observe button state after error toast dismisses

**Expected Behavior**:

- User sees toast notification: "Network connection lost. Please check your connection and try again."
- "Generate summary" button returns to enabled state
- User can retry after restoring network connection

**Actual Behavior**:

- Toast notification appears correctly
- **Button remains disabled (greyed out, not clickable)**
- User must close and reopen Study tab to restore functionality
- Console error logged: `Uncaught (in promise) NetworkError` in `SummaryPanel.tsx:123`

**Attachments**:

- Screen recording: `/qa/lock-in/videos/BUG-001-button-disabled-network-error.mp4`
- Console logs: `/qa/lock-in/logs/BUG-001-console-log.txt`
- Network trace: `/qa/lock-in/logs/BUG-001-network-trace.har`

**Root Cause**: Error handler in `SummaryPanel.tsx` line 123 catches network error correctly but doesn't reset button state (`isGenerating`) to `false`.

**Proposed Fix**:

```javascript
catch (error) {
  setIsGenerating(false); // <-- ADD THIS LINE
  showErrorToast('Error generating summary. Please try again.');
  logError(error);
}
```

**Discovered By**: QA Engineer - Sarah Chen  
**Discovered Date**: 2026-02-28  
**Assigned To**: Dev Team - Frontend (Alex Kumar)

---

**BUG-002: Duplicate summaries created on rapid double-click**

- **Bug ID**: `BUG-SUMMARY-002`
- **Title**: Double-clicking "Generate Summary" creates duplicate summary records
- **Severity**: Medium
- **Priority**: P1 (data integrity issue, confusing UX)
- **Status**: New → Resolved
- **Environment**: Windows 10 Pro, Chrome 132.0.x, Lock-in v0.5.0 (staging)
- **Test Case**: TC-014 (Double-submit protection)
- **Requirement**: REQ-SUMMARY-001
- **Reproducibility**: Always

**Steps to Reproduce**:

1. Log in as `qa.free+summary@lockin.test`
2. Navigate to video with transcript `TC-STANDARD-001`
3. Open Study tab
4. Rapidly double-click "Generate summary" button (< 100ms between clicks)
5. Wait for completion (~10-12 seconds)
6. Observe summary panel and backend logs

**Expected Behavior**:

- Only ONE summary generated per activation
- Single API call to `POST /api/summaries`

**Actual Behavior**:

- TWO summary records created
- Two API calls sent within 150ms
- Rate limit incorrectly decremented twice

**Attachments**:

- Video: `/qa/lock-in/videos/BUG-002-duplicate-summaries.mp4`
- Network trace: `/qa/lock-in/logs/BUG-002-network-trace.har`

**Root Cause**: No client-side debouncing on button click handler; button disabled state set asynchronously.

**Proposed Fix**: Implement debounce (300ms) on button click handler + synchronous button disable before async operations.

**Discovered By**: QA Engineer - Marcus Johnson  
**Discovered Date**: 2026-03-01

---

**BUG-003: Screen reader doesn't announce generation completion**

- **Bug ID**: `BUG-SUMMARY-003`
- **Title**: Screen reader users not notified when summary generation completes
- **Severity**: Medium
- **Priority**: P1 (accessibility compliance - WCAG 2.1 violation)
- **Status**: New → Resolved
- **Environment**: Windows 10, NVDA 2023.3, Chrome 132.0.x, Lock-in v0.5.0
- **Test Case**: TC-041 (Screen reader compatibility)
- **Requirement**: REQ-SUMMARY-004 (Accessibility - WCAG 2.1 AA)
- **Reproducibility**: Always

**Steps to Reproduce**:

1. Enable NVDA screen reader
2. Navigate to video with transcript
3. Open Study tab, Tab to "Generate summary" button
4. Activate with Enter key, wait for completion
5. Listen for screen reader announcements

**Expected Behavior**:

- NVDA announces: "Generating summary, please wait" (during generation)
- NVDA announces: "Summary generated successfully" (on completion)

**Actual Behavior**:

- No announcement during or after generation
- Screen reader users unaware summary is ready

**Impact**: WCAG 2.1 Level AA violation (4.1.3 Status Messages)

**Attachments**:

- Video with NVDA output: `/qa/lock-in/videos/BUG-003-screen-reader-no-announce.mp4`
- NVDA log: `/qa/lock-in/logs/BUG-003-nvda-log.txt`

**Root Cause**: Missing ARIA live region for status updates

**Proposed Fix**: Add ARIA live region with polite announcements:

```html
<div aria-live="polite" aria-atomic="true" className="sr-only">
  {statusMessage}
</div>
```

**Discovered By**: Accessibility QA - Jordan Lee  
**Discovered Date**: 2026-03-01

---

### 9.3 Defect Triage Process

**Daily Triage Meeting** (15 min):

- Review new bugs from previous 24 hours
- Assign severity and priority
- Assign owner and target fix version

**Severity/Priority Matrix**:

| Severity | Priority | Response Time              | Example                                              |
| -------- | -------- | -------------------------- | ---------------------------------------------------- |
| Critical | P0       | Fix immediately (same day) | Data loss, security breach, complete feature failure |
| High     | P0       | Fix immediately            | Major functionality broken, no workaround            |
| High     | P1       | Fix before release         | Major functionality broken, workaround exists        |
| Medium   | P1       | Fix before release         | Minor functionality issue, UX degradation            |
| Medium   | P2       | Next release               | Cosmetic, low impact                                 |
| Low      | P2/P3    | Backlog                    | Nice-to-have improvements                            |

**Release Blockers**:

- Any P0 defect
- P1 defects at discretion of Product Owner
- Accessibility violations (P1 minimum)

---

## 10. Test Metrics & Reporting

### 10.1 Test Execution Metrics

**Tracking Dashboard**: [Link to Jira/TestRail/internal tool]

| Metric                       | Target             | Status          | Notes                               |
| ---------------------------- | ------------------ | --------------- | ----------------------------------- |
| Test Cases Executed          | 100%               | ✓ 53/53         | All planned cases completed         |
| Test Cases Passed            | ≥ 95%              | ✓ 50/53 (94.3%) | 3 failures logged as bugs           |
| Test Coverage (Requirements) | ≥ 90%              | ✓ 95%           | All critical requirements covered   |
| Defects Found                | N/A                | 8 total         | 0 Critical, 3 High, 4 Medium, 1 Low |
| Defects Resolved             | ≥ 95% P0/P1        | ✓ 3/3           | All P0/P1 defects fixed             |
| Regression Pass Rate         | 100%               | ✓ 100%          | Full regression suite passed        |
| Performance Benchmarks Met   | 100%               | ✓ 100%          | All within limits                   |
| Accessibility Audit          | Pass (WCAG 2.1 AA) | ✓ Pass          | 1 issue found and fixed             |

### 10.2 Test Summary Report

**Test Cycle**: Feature Test - Generate Study Summary  
**Test Period**: February 28 - March 5, 2026  
**Test Environment**: Staging  
**Tester(s)**: Sarah Chen (Lead), Marcus Johnson, Jordan Lee (Accessibility)

**Overall Status**: ✅ **PASS** (Ready for Production Release)

**Summary**:

- All 53 planned test cases executed successfully
- 50 test cases passed (94.3% pass rate)
- 3 test cases failed, resulting in 3 high-priority bugs (all resolved)
- 5 additional issues discovered during exploratory testing
- All P0/P1 defects resolved and verified
- Regression testing completed with 100% pass rate
- Performance benchmarks met or exceeded
- Accessibility audit passed

**Key Findings**:

- ✅ Core functionality works as expected for standard use cases
- ✅ Error handling robust
- ✅ Rate limiting correctly enforced across tiers
- ✅ Performance within acceptable parameters
- ⚠️ Accessibility improvements completed
- ⚠️ Double-submit protection implemented

**Risk Assessment**: **Low Risk** - All critical paths tested and verified

**Recommendation**: **APPROVED for production release**

**Post-Release Monitoring**:

- Monitor error rates in Sentry for first 48 hours
- Track API response times and timeout rates
- Monitor support tickets for summary-related issues
- Review rate limit metrics

### 10.3 Defect Summary

| Severity  | Found | Resolved | Open  | Resolution Rate |
| --------- | ----- | -------- | ----- | --------------- |
| Critical  | 0     | 0        | 0     | N/A             |
| High      | 3     | 3        | 0     | 100%            |
| Medium    | 4     | 3        | 1     | 75%             |
| Low       | 1     | 0        | 1     | 0%              |
| **Total** | **8** | **6**    | **2** | **75%**         |

**Open Defects**:

- BUG-SUMMARY-006 (Medium/P2): Minor UI text alignment issue on 4K displays - deferred to v0.6
- BUG-SUMMARY-008 (Low/P3): Tooltip wording improvement - backlog

### 10.4 Test Coverage Summary

**Requirements Coverage**: 95% (38/40 requirements tested)

**Code Coverage** (Frontend): 87%

- Summary component: 92%
- Error handlers: 95%
- API integration: 81%

**User Flow Coverage**: 100% (all critical user journeys tested)

---

## 11. Regression Suite

### 11.1 Regression Testing Strategy

**Purpose**: Ensure new summary feature does not break existing functionality

**Execution Timing**:

- Before each staging deployment
- Before production release
- Weekly during active development

**Execution Method**: Manual (automation planned for future sprint)

**Estimated Duration**: 45 minutes

### 11.2 Regression Test Checklist

#### **Authentication & Session Management**

- [ ] REG-001: User can log in with email/password
- [ ] REG-002: User can log in with Google OAuth
- [ ] REG-003: User can log out successfully
- [ ] REG-004: Session persists across browser restarts
- [ ] REG-005: Session expires after configured timeout
- [ ] REG-006: Expired session prompts re-authentication gracefully
- [ ] REG-007: Multiple accounts work without cross-contamination

#### **Study Tab Core Functionality**

- [ ] REG-010: Study tab opens correctly from extension popup
- [ ] REG-011: Study tab closes without errors or memory leaks
- [ ] REG-012: Multiple Study tabs can coexist without conflicts
- [ ] REG-013: Study tab state persists across tab switches
- [ ] REG-014: Navigation within YouTube doesn't break Study tab

#### **Transcript Functionality**

- [ ] REG-020: Transcript loads for videos with available captions
- [ ] REG-021: Missing transcript shows appropriate message
- [ ] REG-022: Transcript is scrollable and searchable
- [ ] REG-023: Transcript timestamps sync with video playback
- [ ] REG-024: Long transcripts load without performance degradation
- [ ] REG-025: Transcript text is selectable and copyable

#### **Summary Feature** (New - Smoke Tests)

- [ ] REG-030: Generate summary from standard transcript (happy path)
- [ ] REG-031: Summary persists after page reload
- [ ] REG-032: Rate limit enforced correctly (free vs. pro tier)
- [ ] REG-033: Error handling works for network failures
- [ ] REG-034: Retry works after initial failure
- [ ] REG-035: No duplicate summaries created
- [ ] REG-036: Summary content is readable and properly formatted

#### **Notes Functionality**

- [ ] REG-040: User can create new notes
- [ ] REG-041: User can edit existing notes
- [ ] REG-042: User can delete notes
- [ ] REG-043: Notes persist across sessions
- [ ] REG-044: Notes remain associated with correct transcripts
- [ ] REG-045: Notes + summaries coexist without data loss

#### **Performance & Stability**

- [ ] REG-050: Extension loads within 2 seconds
- [ ] REG-051: Memory usage stays under 100MB during normal use
- [ ] REG-052: No main-thread freezes > 500ms
- [ ] REG-053: Page remains interactive during all operations
- [ ] REG-054: No console errors at error level during normal flows

#### **UI/UX Consistency**

- [ ] REG-060: All buttons respond to clicks with visual feedback
- [ ] REG-061: Loading states are always clearly indicated
- [ ] REG-062: Error messages are user-friendly (no stack traces)
- [ ] REG-063: Modal dialogs close properly (Escape key, overlay click)
- [ ] REG-064: Tooltips display on hover where expected

#### **Data Integrity**

- [ ] REG-070: No data loss during network interruptions
- [ ] REG-071: Local storage doesn't exceed quota
- [ ] REG-072: Data sync with backend remains consistent
- [ ] REG-073: User data is properly isolated

### 11.3 Regression Execution Log

**Test Cycle**: Pre-Production Regression (v0.5.0)  
**Date**: March 5, 2026  
**Executed By**: Sarah Chen  
**Environment**: Staging  
**Result**: ✅ PASS (70/70 checks passed)

**Failed Checks**: None  
**Notes**: All regression checks passed. No new issues introduced.

---

## 12. Sign-off

### 12.1 Test Completion Sign-off

**Feature**: Generate Study Summary (FEAT-SUMMARY-001)  
**Version**: Lock-in Extension v0.5.0  
**Test Cycle End Date**: March 5, 2026

| Role             | Name             | Status      | Signature | Date       |
| ---------------- | ---------------- | ----------- | --------- | ---------- |
| QA Lead          | Sarah Chen       | ✅ Approved | [Signed]  | 2026-03-05 |
| QA Engineer      | Marcus Johnson   | ✅ Approved | [Signed]  | 2026-03-05 |
| Accessibility QA | Jordan Lee       | ✅ Approved | [Signed]  | 2026-03-05 |
| Engineering Lead | Alex Kumar       | ✅ Reviewed | [Signed]  | 2026-03-05 |
| Product Manager  | Taylor Rodriguez | ✅ Approved | [Signed]  | 2026-03-05 |

### 12.2 Release Recommendation

**Status**: ✅ **APPROVED FOR PRODUCTION RELEASE**

**Justification**:

- All acceptance criteria met and verified
- 0 Critical/P0 defects open
- All P1 defects resolved and verified
- Regression testing completed successfully (100% pass rate)
- Performance benchmarks met or exceeded
- Accessibility standards compliance achieved (WCAG 2.1 AA)
- Test coverage exceeds targets (95%+ requirements coverage)
- Risk assessment indicates LOW RISK for production deployment

**Conditions**:

- Post-release monitoring plan in place (first 48 hours)
- Rollback plan documented and tested
- Feature flag available for emergency disable if needed
- Support team briefed on new feature

**Known Issues** (Non-Blocking):

- BUG-SUMMARY-006 (Medium/P2): Minor UI text alignment on 4K displays - deferred to v0.6.0
- BUG-SUMMARY-008 (Low/P3): Tooltip wording suggestion - backlog

### 12.3 Post-Release Validation Plan

**Timeline**: First 7 days after production deployment

**Day 1-2 (Critical Monitoring)**:

- [ ] Monitor error rates in Sentry (target: < 0.5% error rate)
- [ ] Track API response times (target: P95 < 15s)
- [ ] Monitor rate limit enforcement
- [ ] Review first 100 user-generated summaries
- [ ] Monitor support ticket volume and sentiment

**Day 3-7 (Stability Monitoring)**:

- [ ] Weekly summary of key metrics vs. baseline
- [ ] User feedback analysis
- [ ] Performance metrics trend analysis
- [ ] Accessibility feedback from assistive technology users
- [ ] Identify any edge cases not covered in testing

**Success Criteria**:

- Error rate < 1% of total summary generation attempts
- P95 response time < 20s
- No Critical defects reported
- Support ticket volume < 5% of active users
- Positive user sentiment > 70%

**Escalation Plan**:

- If error rate > 5%: Immediate investigation, consider feature flag disable
- If Critical defect found: Emergency hotfix process initiated
- If user sentiment < 50% positive: Product review meeting within 24 hours

---

## Appendix A: Glossary

**Terms & Acronyms**:

- **API**: Application Programming Interface
- **ARIA**: Accessible Rich Internet Applications
- **P0/P1/P2/P3**: Priority levels (0 = highest)
- **P95**: 95th percentile (metric where 95% of values fall below)
- **UAT**: User Acceptance Testing
- **WCAG**: Web Content Accessibility Guidelines
- **XSS**: Cross-Site Scripting

---

## Appendix B: References

- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [Chrome Extension Best Practices](https://developer.chrome.com/docs/extensions/mv3/best_practices/)
- Lock-in Internal Test Standards: `docs/qa-standards-v2.md`
- Lock-in Extension Architecture: `docs/architecture/extension-overview.md`
- Backend API Documentation: `https://api-docs.lockin.app`

---

**END OF TEST PLAN**

_This document is version-controlled in Git. For change history, see repository commit log._
