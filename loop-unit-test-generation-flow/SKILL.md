---
name: loop-unit-test-generation-flow
description: Multi-agent TDD workflow for test generation. Use when the user wants to generate comprehensive tests for a component or composable using the-analyst, the-test-writer, and the-test-runner in a feedback loop. Invoke with a file path.
version: 1.0.0
---

# Ralph Wiggum Multi-Agent Test Generation Workflow

This skill orchestrates three specialized subagents in a feedback loop to generate comprehensive tests for Vue components and composables using TDD principles.

## When to Use

Invoke this skill when:
- User says "ralph wiggum" or "/ralph-wiggum" followed by a file path
- User wants to generate tests with iterative refinement
- User wants comprehensive test coverage for a component or composable

## Prerequisites

Ensure these subagents exist in `.cursor/agents/`:
- `the-analyst.md` - Analyzes code and identifies edge cases
- `the-test-writer.md` - Writes Vitest tests following project conventions
- `the-test-runner.md` - Executes tests and reports results

## Rules Reference

This workflow follows the conventions defined in `.cursor/rules/site-studio/`:

### From `test-workflow.mdc`:
- Test location: `app/tests/{type}/{path}/{name}.test.ts`
- Check existing patterns before writing (helpers, fixtures, mocks)
- Use test type patterns for components, composables, stores, utils
- Mock dependencies with `vi.stubGlobal`
- Use fixture factory pattern

### From `unit-tests.mdc`:
- Use `.test.ts` extension (NOT `.spec.ts`)
- Follow AAA pattern (Arrange-Act-Assert)
- Component testing: use `shallowMountWithDefaults`, `data-testid`, avoid `wrapper.vm`
- Composable testing: test as pure functions, NOT inside components
- Store testing: fresh Pinia instance per test with `setActivePinia(createPinia())`
- Fixtures: factory functions like `createMockData(overrides)`
- Edge cases: `null`, `undefined`, `''`, `[]`, `{}`, `0`, `NaN`, boundaries

## Workflow Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    RALPH WIGGUM WORKFLOW                     │
├─────────────────────────────────────────────────────────────┤
│  INPUT: File path (e.g., app/composables/useTargetRules.ts) │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │ the-analyst │ -> │ test-writer │ -> │ test-runner │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│         ^                                    │              │
│         │          FEEDBACK LOOP             │              │
│         └────────────────────────────────────┘              │
│                                                             │
│  MAX ITERATIONS: 3                                          │
│  EXIT: All tests pass OR max iterations reached             │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  OUTPUT: Final report with test files and coverage summary  │
└─────────────────────────────────────────────────────────────┘
```

## Execution Instructions

When this skill is invoked, follow these steps exactly:

### Step 0: Initialize

1. **Validate input**: Ensure the target file exists and is a `.vue` or `.ts` file
2. **Determine test path**: Calculate the corresponding test file path
   - Source: `app/components/foo/bar.vue` → Test: `app/tests/components/foo/bar.test.ts`
   - Source: `app/composables/useFoo.ts` → Test: `app/tests/composables/useFoo.test.ts`
3. **Initialize state**:
   ```
   iteration = 0
   maxIterations = 3
   testsPassing = false
   filesCreated = []
   iterationHistory = []
   ```

### Step 1: Run the-analyst (Analysis Phase)

Launch the-analyst subagent with this prompt template:

```
You are analyzing a source file to prepare for test generation.

**Target File**: [TARGET_FILE_PATH]

**Context**: 
- This is iteration [ITERATION_NUMBER] of the Ralph Wiggum workflow
- Project: Site Studio (Nuxt.js/Vue.js visual site builder)
- Test framework: Vitest with @vue/test-utils

[IF ITERATION > 1, INCLUDE:]
**Previous Test Results**:
[PASTE TEST_RUNNER_REPORT FROM PREVIOUS ITERATION]

**IMPORTANT - Check Existing Patterns First**:
Before suggesting new helpers/fixtures/mocks, check what already exists:
1. `app/tests/helpers/mount.ts` - for `shallowMountWithDefaults`, `mountWithDefaults`
2. `app/tests/helpers/components/` - for component-specific helpers
3. `app/tests/fixtures/` - for existing fixtures in the same domain
4. `app/tests/__mocks__/` - for existing mocks

**Your Task**:
1. Read and analyze the target file
2. Check existing test helpers, fixtures, and mocks that can be reused
3. Identify all public functions, methods, computed properties, and emitted events
4. List edge cases that need testing (MANDATORY - test ALL of these):
   - `null` inputs
   - `undefined` inputs
   - Empty strings `''`
   - Empty arrays `[]`
   - Empty objects `{}`
   - Zero `0` and `NaN` for numeric inputs
   - Boundary conditions (min/max values)
   - Error states
   - Async behavior
5. Identify dependencies that need mocking with `vi.stubGlobal`

**Return your analysis in this format**:

## Analysis Summary
[Brief description of what this file does]

## File Type
[Component / Composable / Store / Util]

## Existing Patterns to Reuse
- Helpers: [List any existing helpers that can be used]
- Fixtures: [List any existing fixtures that can be used]
- Mocks: [List any existing mocks that can be used]

## Public API to Test
- [Function/method 1]: [What it does]
- [Function/method 2]: [What it does]

## Edge Cases (REQUIRED)
1. null input: [How to test]
2. undefined input: [How to test]
3. Empty string: [How to test]
4. Empty array: [How to test]
5. Empty object: [How to test]
6. Zero/NaN: [How to test]
7. Boundary conditions: [Specific boundaries]
8. Error states: [Error scenarios]
...

## Dependencies to Mock
- [Dependency 1]: Use `vi.stubGlobal('[name]', ...)`
- [Dependency 2]: Use `vi.stubGlobal('[name]', ...)`

## Recommended Test Structure
- describe('[Component/Composable Name]')
  - describe('[Scenario 1]')
    - it('[Test case 1]')
    - it('[Test case 2]')
  - describe('[Scenario 2]')
    ...
```

**Save the analyst's output** as `ANALYST_REPORT` for the next step.

### Step 2: Run the-test-writer (Test Creation Phase)

Launch the-test-writer subagent with this prompt template:

```
You are writing tests based on an analysis report.

**Target File**: [TARGET_FILE_PATH]
**Test File**: [TEST_FILE_PATH]

**Analyst Report**:
[PASTE ANALYST_REPORT]

**Project Context**:
- Framework: Vitest with jsdom environment
- Component testing: @vue/test-utils
- Test location: app/tests/ (mirrors app/ structure)
- Fixtures: app/tests/fixtures/
- Helpers: app/tests/helpers/

**MANDATORY RULES** (from .cursor/rules/site-studio/):

### Import Pattern (ALWAYS use this):
```ts
import { beforeEach, describe, expect, it, vi } from 'vitest';
```

### AAA Pattern (ALWAYS follow):
```ts
it('should [expected behavior]', async () => {
  // Arrange - setup data and mocks
  const { wrapper } = mountMyComponent({ props: { ... } });
  
  // Act - perform the action
  await wrapper.find('[data-testid="submit"]').trigger('click');
  
  // Assert - verify the result
  expect(wrapper.emitted('submit')).toBeTruthy();
});
```

### Test Type Patterns:

**For Components (.vue)**:
```ts
import { shallowMountWithDefaults } from '../../helpers/mount';

describe('ComponentName', () => {
  beforeEach(() => { vi.clearAllMocks(); });
  
  describe('when [scenario]', () => {
    it('should [behavior]', () => {
      // Use data-testid for selectors, NOT CSS classes
      // Test emitted events and DOM output, NOT wrapper.vm internals
    });
  });
});
```

**For Composables (use*.ts)** - Test as PURE FUNCTIONS, not inside components:
```ts
describe('useComposable', () => {
  beforeEach(() => { vi.clearAllMocks(); });
  
  it('should [behavior]', () => {
    const { result, action } = useComposable();
    expect(result.value).toBe(expected);
  });
});
```

**For Stores (stores/*.ts)**:
```ts
import { createPinia, setActivePinia } from 'pinia';

describe('store name', () => {
  beforeEach(() => {
    setActivePinia(createPinia());
  });
  
  it('initializes with default state', () => { /* ... */ });
});
```

**For Utils (utils/*.ts)**:
```ts
describe('functionName', () => {
  it('returns [result] for [input]', () => { /* ... */ });
  it('handles null/undefined gracefully', () => { /* ... */ });
  it('handles empty values', () => { /* ... */ });
});
```

### Fixture Factory Pattern (ALWAYS use for fixtures):
```ts
// app/tests/fixtures/[domain].fixture.ts
export function createMockData(overrides = {}) {
  return { id: 1, name: 'default', ...overrides };
}
```

### Mocking Nuxt Auto-imports:
```ts
vi.stubGlobal('useLocale', () => ({ $gettext: (k: string) => k }));
vi.stubGlobal('composableName', vi.fn(() => ({ ... })));
```

### DO NOT:
- Access `wrapper.vm` internals (private state, methods)
- Use CSS class selectors (`.btn-primary`)
- Test composables inside component wrappers
- Use `.spec.ts` extension (use `.test.ts`)

[IF ITERATION > 1, INCLUDE:]
**Previous Test Issues**:
[PASTE ISSUES FROM TEST_RUNNER_REPORT]

Fix these issues while preserving working tests.

**Your Task**:
1. Create or update the test file at [TEST_FILE_PATH]
2. Create fixtures using factory pattern at app/tests/fixtures/[name].fixture.ts
3. Create helpers if needed at app/tests/helpers/[path]/[name].ts
4. Follow TDD: Write tests that will initially fail
5. Include ALL edge cases: null, undefined, '', [], {}, 0, NaN, boundaries

**Return**:
1. Full code for each file you create/update
2. List of files created/modified
```

**Track files created** by the test-writer and add to `filesCreated`.

### Step 3: Run the-test-runner (Verification Phase)

Launch the-test-runner subagent with this prompt template:

```
You are verifying tests created by the test-writer.

**Test File**: [TEST_FILE_PATH]

**Your Task**:
1. Run the test file: `npx vitest run [TEST_FILE_PATH]`
2. Analyze the output
3. Report results

**Return your report in this format**:

## Test Results: [PASSED/FAILED]

**Summary**: X passed, Y failed, Z total

**Passed Tests**:
- [Test name 1]
- [Test name 2]

**Failed Tests** (if any):
### [Test name]
- **Error**: [Error message]
- **Expected**: [What was expected]
- **Actual**: [What happened]
- **Likely Cause**: [Analysis of why it failed]
- **Suggested Fix**: [What needs to change]

**Overall Assessment**:
[Brief summary of test health and what needs attention]
```

**Save the runner's output** as `TEST_RUNNER_REPORT`.

### Step 4: Evaluate and Loop

```
iteration++
iterationHistory.push({
  iteration: iteration,
  passed: [count from TEST_RUNNER_REPORT],
  failed: [count from TEST_RUNNER_REPORT],
  summary: [brief summary]
})

IF all tests pass:
  testsPassing = true
  GOTO Step 5 (Generate Report)

ELSE IF iteration < maxIterations:
  GOTO Step 1 (with TEST_RUNNER_REPORT as context)

ELSE:
  testsPassing = false
  GOTO Step 5 (Generate Report)
```

### Step 4.5: Rules Compliance Check

Before generating the final report, verify compliance with Site Studio rules:

**Checklist**:
- [ ] Test file uses `.test.ts` extension (not `.spec.ts`)
- [ ] Test file mirrors source path structure
- [ ] Imports use: `import { beforeEach, describe, expect, it, vi } from 'vitest'`
- [ ] Each `it()` block follows AAA pattern (Arrange-Act-Assert)
- [ ] `beforeEach` includes `vi.clearAllMocks()`
- [ ] Components: Uses `shallowMountWithDefaults` or component helper
- [ ] Components: Uses `data-testid` selectors, NOT CSS classes
- [ ] Components: Does NOT access `wrapper.vm` internals
- [ ] Composables: Tested as pure functions, NOT inside component wrappers
- [ ] Stores: Uses `setActivePinia(createPinia())` in `beforeEach`
- [ ] Fixtures: Uses factory pattern `createMockData(overrides)`
- [ ] Edge cases tested: null, undefined, '', [], {}, 0, NaN, boundaries
- [ ] Mocks use `vi.stubGlobal` for Nuxt auto-imports

If any items fail, note them in the report under "Rules Compliance Issues".

### Step 5: Generate Final Report

After the loop completes, output this report to the user:

```markdown
# Ralph Wiggum Test Generation Report

## Target File
`[TARGET_FILE_PATH]`

## Status: [SUCCESS / PARTIAL SUCCESS / NEEDS ATTENTION]

## Iteration History
| Iteration | Passed | Failed | Notes |
|-----------|--------|--------|-------|
| 1         | X      | Y      | [summary] |
| 2         | X      | Y      | [summary] |
| ...       | ...    | ...    | ... |

## Files Created/Modified
- `[file1]`
- `[file2]`
- ...

## Test Coverage Summary
- Total test cases: [count]
- Happy paths: [count]
- Edge cases: [count]
- Error handling: [count]

## Rules Compliance
- [x] `.test.ts` extension
- [x] AAA pattern followed
- [x] Correct test type pattern (component/composable/store/util)
- [x] Factory pattern for fixtures
- [x] Edge cases covered
- [ ] [Any non-compliant items]

## Remaining Issues (if any)
- [Issue 1]
- [Issue 2]

## Next Steps
[Recommendations for follow-up if tests still failing]
```

## Example Invocation

**User says:**
```
/ralph-wiggum app/composables/useTargetRules.ts
```

**Agent response:**
1. Validates file exists
2. Launches the-analyst → Gets analysis
3. Launches the-test-writer → Creates test file
4. Launches the-test-runner → Runs tests
5. If failures, loops back with feedback
6. Generates final report

## Parallel Execution Notes

- Do NOT run subagents in parallel - they depend on each other's output
- Run sequentially: analyst → test-writer → test-runner
- Each iteration is a complete cycle through all three

## Error Handling

**If file doesn't exist:**
```
Error: Target file not found: [path]
Please provide a valid path to a .vue or .ts file.
```

**If subagent fails:**
- Log the error in iteration history
- Attempt to continue with available information
- Report the issue in final summary

**If tests never pass after 3 iterations:**
- Generate report with PARTIAL SUCCESS status
- List all remaining issues
- Suggest manual review

## Configuration

Default settings (can be mentioned by user to override):
- `maxIterations`: 3
- `testFramework`: vitest
- `generateFixtures`: true
- `generateHelpers`: true

## Tips for Best Results

1. Start with composables (simpler than components)
2. Ensure source file has clear, testable logic
3. Review generated tests before committing
4. Run the workflow again if you make source changes
