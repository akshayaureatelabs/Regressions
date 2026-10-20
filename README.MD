Main tumhare Live vs Staging QA Evidence Engine ke liye ek complete `README.md` / project specification Markdown file taiyar karunga. Ismein updated architecture, saare 8 add-ons, CLI commands, configuration, evidence structure, verdict rules, security, implementation phases aur acceptance criteria ek hi jagah honge.

File mein planned features aur actually implemented/tested features ko clearly separate rakhenge, taaki koi fabricated test result ya unsupported claim na ho.

Neeche complete Markdown document hai. Ise `README.md` naam se project ke root folder mein save kar sakte ho.

Writing

# Live vs Staging QA Evidence Engine

**Automated Regression Verification, Visual Comparison, Performance Diagnostics & Release Quality Gate**

| Project detail | Value |
| --- | --- |
| Project name | QA Evidence Engine |
| Initial target website | Naseej KW |
| Platform | Local computer CLI |
| Runtime | Node.js |
| Browser automation | Playwright |
| Primary language | JavaScript (ES Modules) |
| Environments | Live and Staging |
| Report formats | HTML, JSON, PDF |
| Evidence | Screenshots, visual diffs, traces, network logs, hashes |
| Execution model | Local, reproducible, evidence-first |
| Current implementation status | Specification and implementation plan; runtime verification pending |

---

## 1\. Project Overview

The Live vs Staging QA Evidence Engine is a local command-line application designed to verify whether website changes deployed to a staging environment have introduced functional regressions, visual defects, content inconsistencies, accessibility issues, SEO problems, or performance regressions compared with the live website.

The system is designed primarily for e-commerce websites where developers regularly optimize Core Web Vitals, JavaScript execution, CSS delivery, images, fonts, CDN configuration, caching, and page rendering.

The tool must do more than compare two websites. It must verify expected behavior, preserve execution evidence, classify differences, and produce a transparent release recommendation.

### Primary objective

Verify that staging changes achieve their intended goals without breaking existing functionality, while providing reproducible evidence for every reported result.

### Core principle

> No evidence, no PASS. No verified coverage, no unconditional release approval.

The system must never fabricate test results, silently discard failures, or claim that untested functionality has been verified.

## 2\. Project Goals

The system must:

1. Compare equivalent routes on Live and Staging.
2. Execute repeatable browser tests using Playwright.
3. Verify expected functionality through explicit assertions.
4. Detect HTTP failures, missing assets, console errors, and JavaScript exceptions.
5. Compare screenshots and identify visual differences.
6. Measure page-loading and performance metrics under controlled conditions.
7. Investigate Core Web Vitals using appropriate lab and field data.
8. Execute e-commerce journeys using approved test accounts and test data.
9. Detect intermittent failures and preserve retry history.
10. Classify differences using explicit, explainable rules.
11. Protect approved baselines from silent modification.
12. Run SEO and accessibility audits.
13. Generate HTML, JSON, and PDF reports.
14. Preserve Playwright traces, screenshots, logs, and artifact hashes.
15. Track historical results and recurring regressions.
16. Apply configurable release gates based on severity, coverage, and evidence quality.

## 3\. Non-Goals and Limitations

The system must not claim to:

- Prove that every possible website interaction is defect-free.
- Guarantee that no regression exists outside the tested scope.
- Treat identical Live and Staging failures as proof of correct functionality.
- Treat a successful retry as proof that the initial failure never occurred.
- Equate FCP or total page-load time with Core Web Vitals.
- Equate Total Blocking Time (TBT) with Interaction to Next Paint (INP).
- Treat a headless Chrome mobile emulation as a real iOS Safari test.
- Certify full WCAG accessibility compliance using automated scans alone.
- Claim that a SHA-256 hash proves the correctness of a test or the identity of its operator.
- Claim that a staging lab measurement represents real-user field performance.
- Perform destructive production actions or place real customer orders.

Every report must state the scope, limitations, skipped tests, unresolved findings, and evidence gaps.

---

## 4\. Target Environments

The initial configuration targets the following example environments.

**Live â€” baseline**

`https://www.naseejkw.com/`

**Staging â€” testing target**

`https://test1.naseejkw.com/`

These URLs are configuration defaults only. Their current availability, behavior, and performance have not been verified by this specification.

### Environment responsibilities

| Environment | Purpose | Permitted activity |
| --- | --- | --- |
| Live | Reference baseline | Read-only navigation, screenshots, GET requests, non-mutating diagnostics |
| Staging | Candidate release | Read-only checks and approved functional tests |
| Local machine | Test execution and storage | Browser automation, analysis, reports, and historical records |

### Live safety requirements

Automated Live testing must not:

- Submit forms that create or modify records.
- Add products to the production cart.
- Change quantities or customer account settings.
- Create orders, initiate real payments, or modify inventory.
- Delete content or perform administrative actions.
- Execute arbitrary application JavaScript that mutates production state.

Even a GET request can have side effects on a poorly designed application. All requests and test actions must therefore follow an explicit safety policy rather than relying exclusively on HTTP method names.

Staging mutations must use approved test accounts, isolated test data, and sandbox payment systems where applicable.

---

## 5\. Technology Stack

### Required technologies

| Technology | Purpose |
| --- | --- |
| Node.js | Application runtime |
| JavaScript ES Modules | Implementation language |
| Playwright | Browser automation, assertions, screenshots, and traces |
| Chromium | Initial automated browser |
| Lighthouse | Complementary lab performance diagnostics |
| HTML | Human-readable reports |
| JSON | Machine-readable evidence |
| PDF | Shareable report export |
| SHA-256 | Artifact integrity checks |
| Local filesystem | Evidence storage and report history |

### Optional integrations

These should be introduced only when required:

- Axe-core for automated accessibility testing.
- An image comparison library for screenshot pixel diffs.
- Google PageSpeed Insights or CrUX data for eligible field-performance assessments.
- SQLite if JSON-backed historical reporting eventually becomes insufficient.
- An optional AI-assisted classifier for explaining differences.

AI-generated explanations must remain separate from deterministic test outcomes. An AI classifier must never override a failed assertion or convert an unresolved defect into a PASS.

### Dependency policy

- Use supported, pinned dependency versions.
- Commit the package lockfile.
- Document the Node.js version used for verified runs.
- Keep browser versions consistent within paired comparisons.
- Record installed tool versions in every run manifest.
- Review dependency updates before adopting them in release-critical workflows.

---

## 6\. Project Architecture

```
qa-evidence-engine/
â”œâ”€â”€ README.md
â”œâ”€â”€ package.json
â”œâ”€â”€ package-lock.json
â”œâ”€â”€ qa.config.json
â”œâ”€â”€ .env.example
â”œâ”€â”€ .gitignore
â”œâ”€â”€ src/
â”‚   â”œâ”€â”€ cli.mjs
â”‚   â”œâ”€â”€ config/
â”‚   â”‚   â””â”€â”€ validate.mjs
â”‚   â”œâ”€â”€ runner/
â”‚   â”‚   â”œâ”€â”€ paired-runner.mjs
â”‚   â”‚   â”œâ”€â”€ browser-factory.mjs
â”‚   â”‚   â””â”€â”€ retry-policy.mjs
â”‚   â”œâ”€â”€ tests/
â”‚   â”‚   â”œâ”€â”€ page-health.mjs
â”‚   â”‚   â”œâ”€â”€ navigation.mjs
â”‚   â”‚   â”œâ”€â”€ search.mjs
â”‚   â”‚   â”œâ”€â”€ product-listing.mjs
â”‚   â”‚   â”œâ”€â”€ product-details.mjs
â”‚   â”‚   â”œâ”€â”€ cart.mjs
â”‚   â”‚   â””â”€â”€ checkout.mjs
â”‚   â”œâ”€â”€ audits/
â”‚   â”‚   â”œâ”€â”€ network.mjs
â”‚   â”‚   â”œâ”€â”€ visual-diff.mjs
â”‚   â”‚   â”œâ”€â”€ performance.mjs
â”‚   â”‚   â”œâ”€â”€ seo.mjs
â”‚   â”‚   â””â”€â”€ accessibility.mjs
â”‚   â”œâ”€â”€ analysis/
â”‚   â”‚   â”œâ”€â”€ classification.mjs
â”‚   â”‚   â”œâ”€â”€ flaky-tests.mjs
â”‚   â”‚   â”œâ”€â”€ baseline-lock.mjs
â”‚   â”‚   â””â”€â”€ release-gate.mjs
â”‚   â”œâ”€â”€ evidence/
â”‚   â”‚   â”œâ”€â”€ collector.mjs
â”‚   â”‚   â”œâ”€â”€ manifest.mjs
â”‚   â”‚   â””â”€â”€ redaction.mjs
â”‚   â””â”€â”€ reporters/
â”‚       â”œâ”€â”€ json.mjs
â”‚       â”œâ”€â”€ html.mjs
â”‚       â””â”€â”€ pdf.mjs
â”œâ”€â”€ baselines/
â”œâ”€â”€ tests/
â”‚   â””â”€â”€ fixtures/
â”œâ”€â”€ dashboard/
â”‚   â””â”€â”€ README.md
â””â”€â”€ reports/
    â””â”€â”€ .gitkeep
```

The directory tree describes the target architecture. Files and commands become available only after their corresponding modules are implemented.

### Module responsibilities

**CLI and configuration**

- Parse commands and options.
- Validate URLs, routes, viewports, test profiles, and safety policies.
- Refuse unsafe or ambiguous configurations.
- Assign a unique ID to every run.

**Runner**

- Execute equivalent tests against Live and Staging.
- Record browser configuration and test attempts.
- Apply bounded retries without deleting original failures.
- Enforce environment-specific permissions.

**Test modules**

- Contain explicit expected behavior and assertions.
- Separate functionality tests from parity comparisons.
- Report missing prerequisites and untested scenarios.

**Evidence collector**

- Record screenshots, traces, request details, console messages, and test outcomes.
- Generate a manifest with file hashes.
- Redact credentials and sensitive data before reports are shared.

**Analysis**

- Classify differences.
- Identify potential flaky tests.
- Apply baseline policies.
- Calculate release recommendations.

**Reporters**

- Render the same underlying evidence into JSON, HTML, and PDF.
- Avoid inventing information absent from the source records.

---

## 7\. Configuration

A proposed initial `qa.config.json`:

```
{
  "project": "Naseej KW",
  "liveBase": "https://www.naseejkw.com",
  "stagingBase": "https://test1.naseejkw.com",
  "locale": "ar-KW",
  "runs": 3,
  "navigationTimeoutMs": 30000,
  "settleMs": 1500,
  "viewports": [
    {
      "name": "desktop",
      "width": 1440,
      "height": 900,
      "isMobile": false,
      "hasTouch": false
    },
    {
      "name": "mobile",
      "width": 375,
      "height": 812,
      "isMobile": true,
      "hasTouch": true
    }
  ],
  "routes": [
    {
      "name": "home",
      "path": "/"
    },
    {
      "name": "summer-plp",
      "path": "/summer/european-fabrics.html"
    },
    {
      "name": "red-shumagh-plp",
      "path": "/ghutra-shemagh/red-shumagh.html"
    },
    {
      "name": "marubeni-pdp",
      "path": "/marubeni-shiruku.html"
    },
    {
      "name": "search",
      "path": "/catalogsearch/result/?q=shiruku"
    }
  ],
  "performance": {
    "enabled": true,
    "measuredRuns": 5,
    "separateColdAndWarmRuns": true
  },
  "artifacts": {
    "screenshots": true,
    "traces": true,
    "pdf": true,
    "networkLogs": true
  },
  "releaseGate": {
    "blockOnCriticalFailure": true,
    "blockOnUnresolvedCriticalFinding": true,
    "requireCoverageReview": true,
    "requireBaselineApproval": true
  }
}
```

This is a proposed configuration schema, not a guarantee that the existing starter code supports every field.

### Configuration validation rules

1. Both environment URLs must use HTTPS unless a documented local test exception is explicitly enabled.
2. Live and Staging must have different origins.
3. Route paths must resolve to their intended environment.
4. External-origin navigation must be explicitly allowed.
5. Run counts must have defined limits.
6. Viewport dimensions must be positive and valid.
7. Unknown or unsupported settings must generate a clear error.
8. Secrets must not be embedded in the configuration file.
9. Live and Staging authentication states must never be shared accidentally.
10. Missing required configuration must not silently fall back to unsafe defaults.

---

## 8\. Standard Verdict System

Every test must produce one of four primary statuses.

| Status | Definition |
| --- | --- |
| `PASS` | All required assertions passed and required evidence was captured |
| `FAIL` | A required assertion failed or a defined failure threshold was breached |
| `INCONCLUSIVE` | The available evidence cannot reliably establish the outcome |
| `NOT TESTED` | The test did not execute |

The system may also assign separate comparison labels:

- `MATCH`
- `DIFF`
- `IMPROVED`
- `REGRESSED`
- `UNRESOLVED`

These labels must never replace the primary test status.

### Example

Suppose the product price is missing on both Live and Staging.

| Check | Live | Staging | Result |
| --- | --- | --- | --- |
| Product price exists | FAIL | FAIL | Shared functional failure |
| Price-presence parity | Same missing state | Same missing state | MATCH |
| Release readiness | â€” | â€” | Depends on severity and policy |

The result must not claim that the product price functionality is healthy.

### Evidence requirements for PASS

A PASS record should include:

- Test ID and test version.
- Expected behavior.
- Actual observed behavior.
- Environment and exact URL.
- Browser version and relevant configuration.
- Timestamp and attempt number.
- Assertion result.
- Relevant logs or artifact references.

If a required assertion or evidence collector fails, the test must not receive an unconditional PASS.

---

## 9\. Add-on A: Smart Difference Classification

The classification engine will group detected differences into understandable categories.

### Categories

1. Functional regression.
2. Visual-only difference.
3. Content or product-data mismatch.
4. Performance regression.
5. SEO regression.
6. Accessibility issue.
7. Environment-specific difference.
8. Transient or potentially flaky failure.
9. Intentional, approved change.
10. Unresolved difference.

### Classification evidence

Every finding must include:

- Finding ID.
- Affected route and viewport.
- Live and Staging observations.
- Expected behavior.
- Evidence references.
- Severity and confidence.
- Classification reason.
- Whether human review is required.

### Classification rules

Deterministic rules take precedence over AI suggestions.

For example, a missing product image with an observed HTTP 404 is a verified asset failure. Whether the failure is transient, caused by CDN propagation, or harmless requires additional evidence.

A classifier may suggest a likely cause but must not close the finding without satisfying the defined closure criteria.

---

## 10\. Add-on B: Complete E-commerce Journeys

The test engine will implement independent user journeys with explicit assertions.

### Navigation journey

- Load the homepage.
- Verify the expected header and navigation controls.
- Open the desktop menu.
- Open the mobile burger menu.
- Verify important category links.
- Confirm that internal navigation resolves correctly.

### Search journey

- Open the search interface.
- Enter a controlled query.
- Submit the query.
- Verify the destination URL.
- Verify that the expected results container appears.
- Check for empty results, application errors, and broken product images.

### Product listing journey

- Open a configured product listing page.
- Verify the listing container.
- Check product-card structure.
- Test sorting and pagination where applicable.
- Verify image loading and link destinations.

### Product detail journey

- Open a known test product.
- Verify product identity and expected content.
- Validate price presence and formatting.
- Check gallery images.
- Verify required product options or variants.
- Confirm that invalid or missing selections are handled correctly.

### Add-to-Cart journey

- Select a valid variant where required.
- Add the product to the cart on Staging.
- Verify the appropriate request and response.
- Verify the resulting cart state and quantity.
- Verify the minicart count and confirmation UI.
- Check that the selected variant and price are preserved.

### Cart and checkout journey

- Verify cart rows, quantity, product identity, and totals.
- Check validation for invalid or missing fields.
- Verify checkout steps and navigation.
- Use a sandbox payment gateway if payment testing is required.
- Do not create actual production orders.

### Authentication

Authenticated tests require a dedicated test account.

Credentials must be provided through environment variables or a secure local secret store. Authentication state must be protected, excluded from version control, and redacted from shared artifacts.

If authentication cannot be established, affected tests must be marked `NOT TESTED` or `INCONCLUSIVE`, rather than PASS.

---

## 11\. Add-on C: Visual Regression Diff Engine

The visual module will compare equivalent screenshots captured with controlled settings.

### Required comparison conditions

- Identical viewport dimensions.
- Identical browser version where possible.
- Identical locale and timezone.
- Equivalent consent and authentication state.
- Equivalent page and interaction state.
- Consistent screenshot capture procedure.

### Initial viewport profiles

| Profile | Viewport |
| --- | --- |
| Desktop | 1440 Ã— 900 |
| Mobile | 375 Ã— 812 |
| Tablet | 768 Ã— 1024 |

### Visual outputs

For every comparison, store:

1. Original Live screenshot.
2. Original Staging screenshot.
3. Highlighted difference image.
4. Pixel-difference percentage.
5. Configured threshold.
6. Masked-region configuration.
7. Classification and review status.

### Dynamic content

Masks may be used for approved dynamic elements, such as rotating promotional content or timestamps.

Do not mask essential elements such as product prices, cart controls, primary navigation, product images, or checkout buttons merely to make a comparison pass.

Visual equality does not prove behavioral equality, and visual differences do not automatically indicate a functional regression.

---

## 12\. Add-on D: Performance and Core Web Vitals Diagnostics

The performance engine will distinguish browser-level navigation timings, lab performance metrics, and real-user field data.

### Metrics

| Metric | Purpose |
| --- | --- |
| TTFB | Measures time to the first byte of the response |
| FCP | Measures the first contentful paint |
| LCP | Measures the largest contentful paint |
| CLS | Measures cumulative layout shift |
| TBT | Provides a lab diagnostic for main-thread blocking |
| INP | Measures real-user interaction responsiveness when suitable field data is available |
| DOMContentLoaded | Records the navigation lifecycle event |
| Load event | Records the browser load event |
| Resource count | Helps identify changes in page dependencies |
| Transfer size | Helps compare transferred resources when measurable |

### Test procedure

1. Use equivalent browser configurations.
2. Separate cold and warm cache experiments.
3. Record individual run values.
4. Begin with five measured runs per route, environment, and viewport.
5. Report median, range, and variability.
6. Preserve failed and interrupted runs.
7. Use identical network conditions where practical.
8. Record measurement limitations.

### Core Web Vitals interpretation

The current Core Web Vitals are LCP, INP, and CLS.

Common field-data thresholds at the 75th percentile are:

- LCP: 2.5 seconds or less.
- INP: 200 milliseconds or less.
- CLS: 0.1 or less.

These thresholds are for field assessment, not a guarantee that a single lab run should meet the same values.

A Lighthouse or other lab run can provide useful diagnostics. It must not be represented as a replacement for real-user field data.

If CrUX or another suitable field-data source does not provide eligible data for a staging domain, report field status as unavailable. Do not invent field measurements.

### Performance verdicts

A performance improvement should be reported only when:

- The metric was measured successfully.
- The comparison conditions were sufficiently equivalent.
- Run-to-run variability was considered.
- The reported improvement is calculated from recorded values.
- Measurement limitations are documented.

---

## 13\. Add-on E: Flaky Test Intelligence

The retry system will retain every test attempt.

### Example

| Attempt | Result |
| --- | --- |
| 1 | FAIL |
| 2 | PASS |
| 3 | PASS |

The final finding must preserve the initial failure and classify the test as potentially flaky.

It must not report that the test passed consistently.

### Required capabilities

- Configurable retry limits.
- Original failure preservation.
- Attempt-specific screenshots and traces.
- Failure-signature comparison.
- Historical flakiness rate.
- Classification of infrastructure failures versus application failures.
- Explicit distinction between retry success and stable success.

A flaky test must not be silently excluded from release-gate calculations.

---

## 14\. Add-on F: Release Gate and Baseline Lock

### Baseline policy

An approved baseline must be versioned and protected from silent changes.

Each baseline record should include:

- Baseline ID.
- Source run ID.
- Environment and route scope.
- Browser and viewport configuration.
- Artifact hashes.
- Approval timestamp.
- Reviewer identity, when authenticated approval is implemented.
- Change reason.

### Baseline update process

1. Generate candidate evidence.
2. Compare candidate evidence against the approved baseline.
3. Review the differences.
4. Approve or reject the proposed update.
5. Preserve the previous baseline.
6. Record the decision and rationale.

### Release gate conditions

A release recommendation may be blocked by:

- Critical functional failures.
- Unresolved critical or high-severity findings.
- Required tests that have not executed.
- Missing required evidence.
- Unapproved baseline changes.
- Performance budget violations.
- Authentication or test-data failures affecting critical flows.

### Release recommendation statuses

- `READY FOR REVIEW`
- `BLOCKED`
- `INCONCLUSIVE`
- `APPROVED`, only when an explicit authorized approval workflow has been completed.

The tool must not claim that a release was approved merely because automated checks passed.

---

## 15\. Add-on G: SEO and Accessibility Audit

### SEO checks

- Page title and meta description.
- Canonical URL.
- Robots directives.
- Indexability-related headers.
- Heading hierarchy.
- Internal links and broken destinations.
- Image alternative text.
- Structured-data validity.
- Environment-specific canonical and indexing differences.
- Sitemap and robots.txt consistency where applicable.

Staging indexing controls must be reviewed carefully. A deliberate staging `noindex` directive is not automatically a regression.

### Accessibility checks

- Accessible names for controls.
- Form labels and error messages.
- Keyboard navigation.
- Focus visibility and focus order.
- Dialog and menu interaction.
- Basic semantic structure.
- Image alternative text.
- Common automated WCAG violations.
- Contrast checks where supported.

### Reporting

Every finding must include:

- Rule or audit ID.
- Affected URL.
- Relevant element or selector.
- Observed issue.
- Severity.
- Evidence or scan output.
- Suggested remediation.

Automated accessibility testing provides useful coverage but does not replace manual keyboard, assistive-technology, or complete WCAG conformance testing.

---

## 16\. Add-on H: Historical Dashboard

The first historical interface will read saved local JSON reports.

A database is not required for the initial MVP.

### Dashboard capabilities

- List previous runs.
- Filter by environment, route, viewport, status, and severity.
- Compare two run summaries.
- Track recurring failures.
- Display performance trends.
- Identify flaky tests.
- View approved baseline versions.
- Open screenshots, traces, and visual diffs.
- Track unresolved findings.

### Historical integrity

A report must not be overwritten silently.

Each run must have a unique ID, and the dashboard must distinguish missing historical data from a successful test.

If a previous run used a different browser version, viewport, or test configuration, the dashboard must indicate that the comparison is not fully equivalent.

---

## 17\. Evidence Collection and Integrity

Every test execution must preserve enough information to reproduce or investigate the result.

### Standard evidence record

```
{
  "testId": "PDP-PRICE-001",
  "environment": "staging",
  "route": "/marubeni-shiruku.html",
  "status": "NOT_TESTED",
  "expected": "Product price is visible and valid",
  "actual": null,
  "reason": "Example schema; no test execution recorded",
  "artifacts": [],
  "attempt": 1
}
```

This is an illustrative schema example, not an actual test result.

### Evidence package

```
reports/<run-id>/
â”œâ”€â”€ report.html
â”œâ”€â”€ report.pdf
â”œâ”€â”€ report.json
â”œâ”€â”€ findings.json
â”œâ”€â”€ manifest.json
â”œâ”€â”€ screenshots/
â”‚   â”œâ”€â”€ live/
â”‚   â”œâ”€â”€ staging/
â”‚   â””â”€â”€ diffs/
â”œâ”€â”€ traces/
â”œâ”€â”€ network/
â”‚   â””â”€â”€ requests.json
â”œâ”€â”€ performance/
â”‚   â””â”€â”€ metrics.json
â””â”€â”€ logs/
    â””â”€â”€ execution.log
```

### Manifest requirements

The manifest should record:

- Run ID and timestamps.
- Tool and dependency versions.
- Browser version.
- Environment URLs.
- Routes and viewports.
- Test configuration.
- Test counts and statuses.
- Artifact paths and SHA-256 hashes.
- Retry history.
- Findings and release-gate outcome.

Hashes must be independently verifiable. For stronger audit requirements, a separately protected or digitally signed manifest may be added.

### Evidence protection

- Redact passwords, cookies, authorization headers, and access tokens.
- Avoid collecting payment details or unnecessary personal data.
- Restrict access to authenticated traces and storage state.
- Exclude secrets and generated reports from version control.
- Define retention and cleanup policies.

---

## 18\. HTML, JSON, and PDF Reports

All report formats must be generated from the same underlying run data.

### Executive summary

The report should show:

- Project and run ID.
- Live and Staging URLs.
- Test date and timezone.
- Browser and configuration.
- Tester identity, when supplied and verified.
- Total tests and outcome counts.
- Release recommendation.
- Critical findings.
- Coverage gaps.
- Evidence package location.

### Functional regression matrix

| Test | Live | Staging | Comparison | Evidence |
| --- | --- | --- | --- | --- |
| Homepage loads | Recorded status | Recorded status | Match/difference | Artifact reference |
| Search journey | Actual assertion | Actual assertion | Match/difference | Trace and screenshot |
| Product price | Actual assertion | Actual assertion | Match/difference | DOM evidence |
| Add-to-Cart | Actual assertion | Actual assertion | Match/difference | Action and response log |
| Checkout | Actual assertion | Actual assertion | Match/difference | Test artifacts |

Empty or missing values must remain visibly missing; the reporting layer must not infer a successful result.

### Performance report

Include individual run values, medians, variability, comparison conditions, and metric availability.

### Findings register

Each finding should include:

- ID and severity.
- Description and reproduction steps.
- Environment and route.
- Expected versus actual behavior.
- Evidence references.
- Classification and confidence.
- Owner and status, where assigned.
- Closure criteria and closure evidence.

### Visual gallery

Display Live and Staging screenshots together with the highlighted difference image.

### Final recommendation

Use a qualified statement such as:

> No regression was detected within the verified test scope. Remaining risks and untested scenarios are listed below.

Never use an unconditional statement such as â€œthe entire website is guaranteed to be defect-free.â€

---

## 19\. Proposed CLI Interface

The following commands define the intended interface. They must be implemented and verified before being documented as working commands.

```
# Validate configuration
npm run qa -- validate

# Run the configured test suite
npm run qa -- run

# Run a selected route
npm run qa -- run --route home

# Run a selected viewport
npm run qa -- run --viewport mobile

# Compare saved runs
npm run qa -- compare <run-a> <run-b>

# Review candidate baseline changes
npm run qa -- baseline review

# Approve a baseline update
npm run qa -- baseline approve <id>

# Regenerate reports from saved evidence
npm run qa -- report <run-id>

# View historical summaries
npm run qa -- history
```

Commands must fail safely and return meaningful process exit codes.

### Exit-code policy

- `0`: Execution completed and configured release checks passed.
- `1`: One or more configured assertions failed or the release gate was blocked.
- `2`: Configuration, setup, or execution error prevented a valid result.
- Additional codes may be introduced for incomplete or inconclusive runs, provided they are documented consistently.

The report remains authoritative for detailed test statuses. A process exit code alone must not be interpreted as complete website verification.

---

## 20\. Installation and Setup Guide

### Prerequisites

- A supported Node.js release.
- npm.
- Internet access to the target websites.
- Permission to test the configured environments.
- A local directory with the project files.

### Installation

After the project implementation is available:

```
npm install
npx playwright install chromium
```

Verify the runtime:

```
node --version
npm --version
```

The project should document the exact Node.js version used for verified builds and provide a lockfile for reproducible dependency installation.

### Configure environments

1. Review `qa.config.json`.
2. Verify that the Live and Staging URLs are correct.
3. Confirm the permitted route inventory.
4. Select the required viewports and locale.
5. Configure safe test-data and authentication requirements.
6. Verify that Live-side mutations are disabled.
7. Run configuration validation.

### Run the initial audit

Once the CLI is implemented:

```
npm run qa -- validate
npm run qa -- run
```

Inspect the generated JSON, HTML, PDF, screenshots, traces, and manifest.

Do not use the resulting report for release approval until the test suite and evidence pipeline have themselves been validated.

### Authenticated testing

Authenticated testing should be enabled only after:

- A dedicated test account exists.
- Authentication state can be created securely.
- The state file is excluded from version control.
- Credentials and tokens are redacted from logs.
- The tests are restricted to approved environments.
- Logout, expiration, and failed authentication cases are handled.

---

## 21\. Implementation Roadmap

### Phase 1 â€” Reliable CLI MVP

Implement:

- Configuration validation.
- Paired route runner.
- Browser and viewport management.
- HTTP and navigation checks.
- Console and network error collection.
- Screenshots and Playwright traces.
- JSON and HTML reports.
- PDF generation.
- Artifact hashing.
- Honest verdict logic.
- Initial automated tests for the engine itself.

**Completion criteria:** The project installs, runs locally, generates verifiable artifacts, and correctly reports controlled pass, fail, and incomplete test cases.

### Phase 2 â€” Functional regression

Implement:

- Navigation and menu tests.
- Search journey.
- Product listing and product detail checks.
- Product option selection.
- Staging-only Add-to-Cart tests.
- Cart and checkout tests.
- Secure authentication support.
- Explicit expected-state assertions.

**Completion criteria:** Each implemented flow has independent tests and reproducible evidence.

### Phase 3 â€” Visual and performance verification

Implement:

- Screenshot pixel diffs.
- Difference thresholds and controlled masks.
- Repeated cold/warm performance runs.
- LCP and CLS collection.
- Complementary Lighthouse diagnostics.
- Interaction diagnostics.
- Performance trend records.

**Completion criteria:** Visual and performance findings are reproducible and are not confused with functional test results.

### Phase 4 â€” Intelligent analysis

Implement:

- Deterministic difference classification.
- Retry and flakiness analysis.
- Finding deduplication.
- Failure signatures.
- Root-cause hypotheses.
- Evidence-based closure rules.

**Completion criteria:** Every classification includes a reason and retains the original evidence.

### Phase 5 â€” Release governance

Implement:

- Versioned baselines.
- Explicit approval workflow.
- Configurable release gates.
- Severity and ownership tracking.
- Required coverage checks.
- Historical dashboard.
- Baseline change audit log.

**Completion criteria:** Unresolved critical failures, missing required coverage, and unapproved baseline changes can block release recommendations.

### Phase 6 â€” SEO and accessibility

Implement:

- Metadata and canonical checks.
- Robots and structured-data audits.
- Broken internal-link detection.
- Automated accessibility scanning.
- Keyboard and focus checks.
- Finding exports and remediation guidance.

**Completion criteria:** Results contain actual scan evidence and clearly document the limits of automated coverage.

---

## 22\. Automated Tests for the QA Engine

The testing tool itself must be tested before it is trusted to audit websites.

Use controlled local fixtures to simulate:

1. A healthy page.
2. A page returning HTTP 404.
3. A page with an uncaught JavaScript exception.
4. A page with a broken image.
5. A page with a missing required product price.
6. A page that fails once and succeeds on retry.
7. A page that never reaches the expected state.
8. A page with a visual difference.
9. A navigation timeout.
10. An invalid configuration.
11. An unapproved baseline update.
12. A report-generation failure.

### Expected outcomes

The test suite must verify that:

- Failures are not classified as PASS.
- Missing assertions are not silently ignored.
- Retries preserve earlier attempts.
- Missing evidence is reported.
- Report generation failures are visible.
- Hashes match the generated artifacts.
- Secret values do not appear in shared reports.
- A release gate blocks the appropriate critical failures.
- Unexecuted tests remain `NOT TESTED`.

These are acceptance requirements, not results already obtained.

---

## 23\. Security and Privacy

The engine must follow least-privilege principles.

- Store credentials outside source control.
- Never print passwords or tokens.
- Redact cookies and authorization headers.
- Protect Playwright traces containing authenticated pages.
- Use dedicated test accounts.
- Avoid real customer data.
- Prevent unapproved navigation to external origins.
- Apply request and action allowlists where appropriate.
- Limit concurrency and retries.
- Respect authorization and rate limits.
- Keep Live-side operations read-only.
- Document retention and cleanup rules.
- Require explicit permission for any test that may change remote state.

Reports intended for external sharing must be checked for personal data, internal URLs, secrets, and sensitive request payloads.

---

## 24\. Final Acceptance Checklist

The project may be considered MVP-complete only when the following are verified:

- [ ]Clean installation succeeds.
- [ ]Configuration validation works.
- [ ]Live and Staging routes are paired correctly.
- [ ]Browser and viewport configuration is recorded.
- [ ]HTTP status and navigation failures are captured.
- [ ]Console errors and failed network requests are recorded.
- [ ]Required screenshots are generated.
- [ ]Playwright traces are generated and readable.
- [ ]JSON reports contain actual execution results.
- [ ]HTML reports accurately reflect JSON evidence.
- [ ]PDF reports are generated successfully.
- [ ]Artifact hashes can be recalculated.
- [ ]Functional tests use explicit expected behavior.
- [ ]Incomplete tests are not marked PASS.
- [ ]Retry history is preserved.
- [ ]Visual differences can be inspected.
- [ ]Performance metrics include measurement limitations.
- [ ]Authenticated tests use protected credentials.
- [ ]Live mutation protection is tested.
- [ ]Baseline changes require approval.
- [ ]Release-gate rules are covered by automated tests.
- [ ]Historical runs remain independently identifiable.
- [ ]All unresolved findings appear in the final report.
- [ ]No test results are fabricated.

---

## 25\. Definition of Release Readiness

The tool should recommend release readiness only when the configured critical test suite has completed, required assertions have passed, required evidence is available, and the applicable release policy has been satisfied.

A successful run means only that the configured checks passed under the recorded conditions.

It does not guarantee that every possible defect has been eliminated.

The report must always preserve:

- Test scope.
- Coverage gaps.
- Unresolved differences.
- Intermittent failures.
- Measurement limitations.
- Unapproved changes.
- Manual verification requirements.

## 26\. Current Project Status

**Status: Specification prepared; implementation and runtime verification pending.**

The project architecture, add-ons, safety requirements, reporting model, verdict rules, and implementation phases are defined in this document.

The existing starter code has not been certified as a complete or production-ready implementation. No Live or Staging tests are claimed to have been executed as part of preparing this specification.

The next milestone is to implement and validate the corrected CLI core and evidence collector before adding the full e-commerce journeys, visual-diff engine, advanced performance diagnostics, release governance, and historical dashboard.

---

**Project:** Live vs Staging QA Evidence Engine\
**Initial target:** Naseej KW\
**Primary goal:** Reliable, reproducible, evidence-backed regression verification\
**Operating principle:** Honest results, explicit coverage, safe execution, and auditable release decisions.

### Save karne ka tarika

1. Apne project folder mein `README.md` file banao.
2. Upar diye gaye document ka content usmein paste karo.
3. File ko UTF-8 encoding mein save karo.
4. Ise project ke root directory mein `package.json` ke saath rakho.

Note: Yeh complete project specification hai, executable application ka replacement nahi. Ismein diye gaye planned commands aur features ko actual code mein implement aur verify karna abhi baaki hai.
