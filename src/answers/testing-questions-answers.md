---
title: Testing Questions - Answers
layout: layouts/page.njk
---

## What are some advantages/disadvantages to testing your code?

- Advantages:
    - Catches bugs early before they reach production
    - Makes refactoring safer; you can change code with confidence
    - Serves as living documentation for how the code should behave
    - Improves code design by encouraging modular, decoupled structure
    - Saves time long-term by reducing manual testing and debugging

- Disadvantages:
    - Takes time to write and maintain, especially early in a project
    - Can give a false sense of security if tests are poorly written
    - Flaky tests can slow down CI/CD pipelines and frustrate the team
    - Over-testing trivial code adds maintenance burden with little value

## What tools would you use to test your code's functionality?

- **Jest** or **Vitest** for unit and integration testing (JavaScript/TypeScript)
- **React Testing Library** for testing React components
- **Playwright** or **Cypress** for end-to-end browser testing
- **Supertest** for testing HTTP APIs in Node.js
- **Mock Service Worker (MSW)** for mocking API requests
- **Storybook** combined with visual regression tools for UI component testing

## What is the difference between a unit test and a functional/integration test?

A **unit test** tests a single function, method, or component in complete isolation, mocking all external dependencies. It answers, "Does this small piece work correctly on its own?"

A **functional/integration test** tests how multiple units work together, often simulating real user flows or interactions between modules. It answers, "Do these pieces work correctly when combined?"

The key difference is scope: unit tests are narrow and isolated, while integration tests verify the connections and behavior across modules.

## What is the purpose of a code style linting tool?

A linting tool like **ESLint** or **Prettier** enforces consistent code style and catches potential errors before runtime. It helps:

- Maintain a uniform codebase regardless of who writes the code
- Prevent common bugs (unused variables, missing returns, etc.)
- Reduce noise in code reviews by automating style concerns
- Enforce team-agreed conventions and best practices

## What are some of the testing best practices?

- **Write tests that resemble real user behavior**-test outcomes, not implementation details
- **Follow the Arrange-Act-Assert (AAA) pattern** for clear test structure
- **Keep tests independent**-one test's result should never depend on another
- **Avoid testing third-party code**-trust that libraries work, test your integration with them
- **Aim for meaningful coverage**-focus on critical paths and business logic, not arbitrary percentage targets
- **Keep tests fast**-slow tests discourage frequent running and hurt developer workflow
- **Treat test code like production code**-refactor, keep it readable, and avoid duplication
