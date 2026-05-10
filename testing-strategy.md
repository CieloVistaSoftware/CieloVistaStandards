---
dewey: 000.3
id: tiered-test-strategy-generic
title: Tiered Test Strategy (Generic)
project: global
description: Testing proves correctness. NO GAPS ALLOWED. Every bug found MUST have: 1. Entry in bug registry ({project}/bug-registry.json or equivalent) 2. Reg…
status: active
tags: [testing, strategy, tiered]
category: 800 — Global Standards
created: 2026-03-16
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: testing-strategy.md
---
# Tiered Test Strategy (Generic)

## Core Principle
**Testing proves correctness. NO GAPS ALLOWED.**

Every bug found MUST have:
1. Entry in bug registry (`{project}/bug-registry.json` or equivalent)
2. Regression test in `tests/regression/` or equivalent
3. Documentation of root cause and fix
4. Coverage in unit/integration tests for affected code paths

---

## Tier-Based Test Structure

Organize your test suite into sequential tiers, where each tier gates the next:

**Tier 1: Static Compliance**
- Fastest to run, no dependencies
- Checks: code quality, linting, type checking, file integrity, metadata validation
- **Gates:** All other tiers — if any fail, stop
- Examples: TypeScript compilation, schema validation, coverage reports

**Tier 2: Unit Tests**
- Test individual functions/methods in isolation
- Scope: Pure functions, business logic, edge cases, error handling
- **Gates:** Integration and functional tests
- Examples: utility functions, helpers, validators

**Tier 3: Integration Tests**
- Test modules/components working together
- Scope: Module interactions, data flow, external API mocks
- **Gates:** End-to-end tests
- Examples: database queries, multi-component workflows

**Tier 4: Functional Tests**
- Test user-facing features end-to-end
- Scope: Complete user workflows, browser rendering, server responses
- **Gates:** Regression tests
- Examples: UI rendering, form submissions, navigation

**Tier 5: Regression Tests**
- Test specific bug fixes (prevents re-breaking)
- Scope: Historical bugs with documented root causes
- **No gates:** Runs last, always
- Examples: bug fix verification tests

---

## Recommended Test Structure

Adapt this structure to your project type and framework:

```text
tests/
├── compliance/              # Tier 1: Static checks
│   ├── linting.spec.ts
│   ├── type-checking.spec.ts
│   ├── coverage.spec.ts     # Meta-test: ensures coverage
│   └── schema-validation.spec.ts
├── unit/                    # Tier 2: Unit tests
│   ├── utils.spec.ts
│   ├── validators.spec.ts
│   └── helpers.spec.ts
├── integration/             # Tier 3: Integration tests
│   ├── api.spec.ts
│   ├── database.spec.ts
│   └── workflow.spec.ts
├── functional/              # Tier 4: Functional tests (E2E, UI, etc.)
│   ├── user-flows.spec.ts
│   ├── ui-components.spec.ts
│   └── api-endpoints.spec.ts
└── regression/              # Tier 5: Bug fix verification
    └── regression-tests.spec.ts
```

**Note:** Not all tiers may apply to your project. Skip tiers that don't fit (e.g., a CLI tool may not need UI tests). Always keep Tier 5 for bug tracking.

---

## Tier Gating (Enforcement)

Configure your test runner to enforce sequential tiers. Examples:

**Playwright:**
```typescript
// playwright.config.ts
const fullyParallel = false;
export const projects = [
  {
    name: 'tier-1-compliance',
    testDir: 'tests/compliance',
    use: { ...devices['Desktop Chrome'] },
  },
  {
    name: 'tier-2-unit',
    testDir: 'tests/unit',
    dependencies: ['tier-1-compliance'],
  },
  // ... continue for each tier
];
```

**Jest/Vitest:**
```json
"test:tier1": "vitest run tests/compliance",
"test:tier2": "vitest run tests/unit",
"test:tier3": "vitest run tests/integration",
"test:all": "npm run test:tier1 && npm run test:tier2 && npm run test:tier3"
```

**Make the enforcement clear**: If Tier 1 fails, all other tests are blocked.

---

## Bug Tracking Process

### 1. Bug Found

When a bug is discovered, log it in your bug registry (JSON or spreadsheet):

```json
{
  "id": "BUG-YYYY-MM-DD-NNN",
  "title": "Brief description",
  "severity": "HIGH | MEDIUM | LOW",
  "component": "File or module where bug lives",
  "function": "Affected function/method (if applicable)",
  "rootCause": "Why it happened",
  "symptom": "What user observed",
  "stepsToReproduce": ["Step 1", "Step 2"],
  "fix": "Code change applied",
  "affectedCode": "Paths to all modified files",
  "regressionTests": ["Test file paths where fix is verified"],
  "testCases": ["Specific test names"]
}
```

### 2. Fix Applied

1. Write and commit the fix
2. **Add a regression test** in `tests/regression/` that verifies the fix
3. Update the bug registry with test references
4. Run all tiers to ensure the fix works and nothing broke

### 3. Verification

Implement meta-tests (Tier 1 compliance) to enforce:
- ✅ All bugs in registry have regression tests
- ✅ All regression test names map to bug IDs
- ✅ No `untestedBugs` remain (count = 0)

Example meta-test:
```typescript
test('all bugs have regression tests', () => {
  const bugs = require('../bug-registry.json');
  const untestedBugs = bugs.filter(b => !b.regressionTests?.length);
  expect(untestedBugs).toEqual([], 
    `${untestedBugs.length} bugs have no regression tests`
  );
});
```

---

## Regression Test Example

Bug: User's email not validated before submission

### 1. Bug Registry Entry:

```json
{
  "id": "BUG-2024-12-19-001",
  "title": "Email validator accepts invalid format",
  "severity": "HIGH",
  "component": "validators/email.ts",
  "function": "validateEmail()",
  "rootCause": "Regex missing domain TLD requirement",
  "symptom": "Form submitted with invalid email like 'test@domain'",
  "stepsToReproduce": [
    "Enter 'test@domain' in email field",
    "Click submit",
    "Form submits without error"
  ],
  "fix": "Updated regex to /^[^@]+@[^@]+\\.[a-z]{2,}$/i",
  "affectedCode": ["src/validators/email.ts"],
  "regressionTests": ["tests/regression/regression-tests.spec.ts"],
  "testCases": ["Email validator rejects missing TLD"]
}
```

### 2. Regression Test (Vitest example):

```typescript
describe('BUG-2024-12-19-001: Email validation', () => {
  it('rejects invalid emails without TLD', () => {
    expect(validateEmail('test@domain')).toBe(false);
  });
  
  it('accepts valid emails with TLD', () => {
    expect(validateEmail('test@domain.com')).toBe(true);
  });
  
  it('rejects email without @ symbol', () => {
    expect(validateEmail('testdomain.com')).toBe(false);
  });
});
```

### 3. Run Tests:

```bash
# Run only regression tests
npm run test:regression

# Run all tiers
npm run test:all
```

---

## Coverage Requirements

All projects MUST enforce:

✅ **Tier 1 (Compliance):** Passes → unblocks Tier 2  
✅ **Tier 2 (Unit):** Passes → unblocks Tier 3  
✅ **Tier 3 (Integration):** Passes → unblocks Tier 4  
✅ **Tier 4 (Functional):** Passes → unblocks Tier 5  
✅ **Tier 5 (Regression):** No untestedBugs (100% bug coverage)  

---

## Common Patterns by Project Type

### API/Backend Project
```text
Tier 1: Type checking, linting
Tier 2: Unit tests for handlers, middleware, validators
Tier 3: Database + API integration tests
Tier 4: End-to-end API tests (real/mock server)
Tier 5: Regression tests for fixed API bugs
```

### Frontend Component Library
```text
Tier 1: Type checking, linting, component discovery
Tier 2: Unit tests for component logic
Tier 3: Component integration (parent/child)
Tier 4: UI rendering, interactions, accessibility
Tier 5: Regression tests for fixed rendering bugs
```

### VS Code Extension
```text
Tier 1: Type checking, linting
Tier 2: Unit tests for utilities, helpers
Tier 3: Command registration, activation tests
Tier 4: E2E extension behavior (if possible)
Tier 5: Regression tests for fixed extension bugs
```

### CLI Tool
```text
Tier 1: Type checking, linting
Tier 2: Unit tests for command logic
Tier 3: Command integration tests
Tier 4: E2E CLI workflows
Tier 5: Regression tests for fixed CLI bugs
```

---

## Implementation Notes

1. **Adapt to your needs:** Not every project needs every tier. Skip tiers that don't apply.
2. **Keep Tier 5 always:** Bug tracking and regression tests scale with project age.
3. **Automate gating:** Use your test runner's native features (dependencies, groups, scripts) to enforce sequencing.
4. **Use skip/only strategically:** During development, you can skip earlier tiers locally (`npm run test:tier3`), but CI must run all required tiers.
5. **Document exceptions:** If a tier is skipped, document why in your project's README or test documentation.
```xml
        {
          "name": "REGRESSION: src goes to dataset NOT native attribute",
          "bugId": "BUG-2024-12-19-001",
          "expect": {
            "checks": {
              "datasetHas": { "src": "..." },
              "nativeAttributeAbsent": "src"
            }
          }
        }

```

---

## Summary

| What | Where | Required |
|------|-------|----------|
| Bug tracking | `data/bug-registry.json` | YES |
| Regression tests | `tests/regression/` | YES |
| Component tests | `tests/behaviors/ui/` | CRITICAL |
| Permutation tests | `tests/behaviors/permutation-compliance.spec.ts` | YES |
| Schema test section | `src/behaviors/schema/*.schema.json` | RECOMMENDED |
| Coverage validation | `tests/compliance/test-coverage-compliance.spec.ts` | AUTO |

**Remember: Testing proves wellbeing. NO GAPS ALLOWED.**
