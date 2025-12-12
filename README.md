# quotient
A framework for AI testing.

> **[TLDR: Quickstart for pros →](TLDR.md)**

## Premise

Unit tests are great, E2E tests useful.
But both can become heavy-
we easily get bogged down in the minutia of tests that validate I/O or verify visual effects.
Ultimately users don't care if an API call responds with specifically formatted data.
They care, for example, that they clicked and later their saved data reappears.
They care, for example, that, if they aren't authorized but should be, that they will be guided back to security.

The first example may be within reach of _typical_ unit/E2E testing;
The second is not- at least not practically efficiently. 
That kind of quality assurance is typically the realm of the human. 
But now we have coding agents that can perform such tasks. 

What would such a test look like? 
```
If a user is not logged in and tries to acces a secure feature they should redirected to login with a friendly message.
```
So, while there are several unit tests that can validate the elements of UI and IO within that process...the overreaching test is intractable without more intellect. 

What we need is a framework for running pass/fail, high-level, process type tests.

## Quotient Test Structure

Quotient tests are written as natural language specifications that describe expected user outcomes. Tests are stored in markdown files with a `.qt.md` extension.

### Test File Format

A quotient test file contains one or more test cases written as clear, actionable statements:

```markdown
# Feature: User Authentication

## Test: Unauthorized access redirects to login
If a user is not logged in and tries to access a secure feature, they should be restricted and redirected to login with a friendly message.

## Test: Session persistence
When a user logs in and later (less the 3 days) returns to the application, their session should be restored and they should not need to log in again.

## Test: Failed login feedback
If a user enters incorrect credentials, they should see a clear error message and be given the opportunity to try again or reset their password.
```

### Test Anatomy

Each test consists of:

0. **Feature** - A blurb describing the feature being tested
1. **Context** - The starting conditions (e.g., "user is not logged in")
2. **Action** - What the user attempts (e.g., "tries to access a secure feature")
3. **Expectation** - The observable outcome (e.g., "redirected to login with a friendly message")

### Organizing Tests

Tests can be organized by feature or user journey:

```
tests/
├── auth.qt.md           # Authentication tests
├── checkout.qt.md       # Checkout flow tests
├── dashboard.qt.md      # Dashboard feature tests
└── onboarding.qt.md     # New user onboarding tests
```

## Running Quotient Tests with a Coding Agent

Quotient tests are executed by AI coding agents (such as Claude) that can interpret natural language specifications and interact with your application.

### Agent Prompt Template

To direct a coding agent to run quotient tests, provide instructions like:

```
Run the quotient tests in the `tests/*.qt.md` directory against the application at [URL or local path].

For each test:
1. Read and understand the test specification
2. Set up the required preconditions
3. Perform the described user actions
4. Verify the expected outcome
5. Report PASS or FAIL with observations

Summarize results at the end with a count of passed/failed tests.
```

### Detailed Agent Instructions

For more control, you can provide explicit instructions:

```
Execute quotient tests with the following protocol:

SETUP:
- Application URL: http://localhost:3000
- Test files: tests/*.qt.md
- Browser/environment: headless Chrome via Playwright

FOR EACH TEST:
1. Parse the test to identify:
   - Preconditions to establish
   - User actions to perform
   - Expected outcomes to verify

2. Execute the test:
   - Navigate to the application
   - Establish preconditions (clear cookies, set up test user, etc.)
   - Perform the specified actions
   - Observe and capture the results

3. Evaluate the outcome:
   - Compare actual behavior against expected behavior
   - Consider "friendly" and "clear" as qualitative assessments
   - Use judgment for ambiguous outcomes

4. Report results:
   - PASS: Expected outcome was achieved
   - FAIL: Expected outcome was not achieved (include details)
   - SKIP: Test could not be executed (include reason)

OUTPUT FORMAT:
For each test, report:
- Test name
- Status (PASS/FAIL/SKIP)
- Observations
- Screenshots or logs if relevant
```

### Integration with CI/CD

To run quotient tests in a CI/CD pipeline, invoke the coding agent as part of your workflow:

```yaml
# Example GitHub Actions workflow
jobs:
  quotient-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Start application
        run: npm start &
      - name: Run quotient tests
        run: |
          # Invoke your AI agent with the test prompt
          claude-code --prompt "Run quotient tests in tests/*.qt.md against the application."
```

## Writing Effective Quotient Tests

### Best Practices

1. **Be specific about outcomes, flexible about implementation**
   ```
   ✓ "User should see their saved items listed"
   ✗ "User should see a <ul> element with class 'items-list'"
   ```

2. **Focus on user-perceivable behavior**
   ```
   ✓ "Error message should clearly explain what went wrong"
   ✗ "API should return 400 status code"
   ```

3. **Include context that matters**
   ```
   ✓ "On a mobile device, the navigation should collapse into a hamburger menu"
   ✓ "For users with slow connections, a loading indicator should appear"
   ```

4. **Test the unhappy path**
   ```
   ✓ "If payment fails, user should not lose their cart contents"
   ✓ "If the server is unavailable, user should see a helpful offline message"
   ```

### Example Test Suite

```markdown
# Feature: Shopping Cart

## Test: Add item to cart
When a user clicks "Add to Cart" on a product, the cart icon should update to show the new item count and a confirmation should appear.

## Test: Cart persistence
If a user adds items to their cart and leaves the site, their cart should still contain those items when they return.

## Test: Remove item from cart
When a user removes an item from their cart, it should disappear from the list and the total should update accordingly.

## Test: Empty cart state
When a user's cart is empty, they should see a friendly message encouraging them to browse products, not a blank page.

## Test: Checkout with empty cart
If a user somehow navigates to checkout with an empty cart, they should be redirected to continue shopping with an explanatory message.
```

## Quotient Test Results

Agents report results in a structured format:

```
╔════════════════════════════════════════════════════════════╗
║                    QUOTIENT TEST RESULTS                   ║
╠════════════════════════════════════════════════════════════╣
║ Feature: Shopping Cart                                     ║
╠────────────────────────────────────────────────────────────╣
║ ✓ PASS  Add item to cart                                   ║
║ ✓ PASS  Cart persistence                                   ║
║ ✓ PASS  Remove item from cart                              ║
║ ✗ FAIL  Empty cart state                                   ║
║         → Showed "Cart (0)" but no encouraging message     ║
║ ✓ PASS  Checkout with empty cart                           ║
╠────────────────────────────────────────────────────────────╣
║ TOTAL: 4 passed, 1 failed, 0 skipped                       ║
╚════════════════════════════════════════════════════════════╝
```

## When to Use Quotient Tests

Quotient tests shine in scenarios where traditional testing becomes impractical or insufficient:

### Use Quotient Tests For

- **User journey validation** - Testing complete workflows from the user's perspective (e.g., "user can sign up, configure their profile, and make their first purchase")
- **Qualitative UX assessment** - Evaluating subjective criteria like "friendly error messages" or "intuitive navigation"
- **Cross-cutting concerns** - Testing behaviors that span multiple components or services
- **Accessibility and usability** - Verifying that interfaces are understandable and usable
- **Edge cases in user behavior** - Scenarios that are hard to anticipate and codify in traditional tests
- **Rapid prototyping validation** - Quick sanity checks during early development before investing in comprehensive test suites
- **Documentation as tests** - When your acceptance criteria are already written in natural language

### Don't Use Quotient Tests For

- **Performance benchmarks** - Use dedicated performance testing tools for measuring response times, throughput, or resource usage
- **Precise numerical validation** - When you need exact values (e.g., "calculate 15% tax on $100.00 equals $15.00 exactly")
- **Regression testing of specific bugs** - Unit tests with explicit assertions are better for preventing known bugs from returning
- **API contract testing** - Schema validation and contract tests are more appropriate for API correctness
- **Security vulnerability scanning** - Use specialized security tools, not AI interpretation
- **High-frequency test runs** - Quotient tests involve AI inference, making them slower and more expensive than traditional tests
- **Deterministic requirements** - When outcomes must be 100% reproducible with no interpretation needed

### The Sweet Spot

Quotient tests work best as a **complement** to your existing test suite:

```
Unit Tests          → Logic correctness, edge cases, fast feedback
Integration Tests   → Component interactions, API contracts
E2E Tests           → Critical user paths, smoke tests
Quotient Tests      → Holistic UX, qualitative outcomes, user intent
```

Think of quotient tests as the "does this actually make sense to a human?" layer that sits above your technical test pyramid.

## Comparison with Traditional Testing

| Aspect | Unit/E2E Tests | Quotient Tests |
|--------|---------------|----------------|
| Specification | Code assertions | Natural language |
| Executor | Test framework | AI coding agent |
| Focus | Implementation correctness | User experience |
| Brittleness | High (tied to implementation) | Low (outcome-focused) |
| Coverage | Narrow and precise | Broad and qualitative |
| Best for | Regression, edge cases | User journeys, UX validation |

Quotient tests complement—not replace—traditional tests. Use unit tests for logic correctness, E2E tests for critical paths, and quotient tests for holistic user experience validation.
