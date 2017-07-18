# iflix iOS (opinonated) unit testing guidelines

## Core Guidelines

We write unit tests to ensure the correctness of the production code, while developing and while changing it.

The tests are a tool to help us move faster in project life cycle, by moving a bit slower in the code writing part.

To make this possible we chose to follow few guidelines, and adopt a certain style

- Test behaviour not implementation
- F.I.R.S.T
- Tests should read like sentences

### Test behaviour not implementation

Tests should verify what the code does, behaviour, not how it does it, implementation.

By focusing on testing the behaviour we achieve two goals. The first is that tests can be read as live documentation of what each unit is supposed to do and should be used. The second and most important is that testing the behaviour allows us to change the implementation, refactoring, and be confident that no regression will be introduced if the tests still pass.

### F.I.R.S.T

Unit tests should be [F.I.R.S.T](https://pragprog.com/magazines/2012-01/unit-tests-are-first):

- **Fast**. A fast test suite makes getting feedback on the code development by the tests easier. There are various strategies to help design tests that are fast, which well see later.
- **Isolated**. Each unit tests should test a single component, and have a single reason to fail. This will make understanding and working with the test easier, as well as investigating the reason for the failure.
- **Repeatable**. If no code has been changed, multiple test runs should give the same result. Tests that fail intermittently (_flaky tests_) could be a sign of design issues in the test code itself, or worse unexpected state assumptions in the production code under test.
- **Self-verifying**. Tests should succeed or fail in an unambiguous way. This will make the test more rigorous, and provide a clearer investigation starting point if a failure occurs.
- **Timely**. Unit tests should be written before the production code, or at least while it is being developed. Doing this provides a faster feedback loop on the code being written, and also ensure the code itself is testable from the start.

### Tests should read like sentences

For tests to be good aid to development they need to be readable. That goes beyond the code itself, having the test structure read like a sentence makes it more effective at describing behaviour.

For these reasons we chose to use [Quick](https://github.com/Quick/Quick) and [Nimble](https://github.com/Quick/Nimble) rather thank vanilla XCTest, for the [RSpec style]() structure they offer

```swift
describe("BananService") {
  context("when fetching bananas") {
    context("if the request succeeds") {
      it("returns bananas") { }
    }
    context("if the request fails") {
      it("returns a banana error wrapping the received one") { }
    }
  }
}
```

## Other Guidelines

### One assertion per test

Each `it` block should have only one assertion. This is in line with the _Isolated_ principle.

### AAA: Arrange, Act, Assert

Each test should be organised in [three steps](http://wiki.c2.com/?ArrangeActAssert).

1. Arrange the necessary inputs for the system under test
2. Act on the system under test
3. Assert the expected behaviour

The ideal test would have one line per step, although this is not always feasible.

```swift
describe("Emojificator") {
  it("add sparkle emoji to the given string") {
    // Arrange
    let emojificator = Emojificator()

    // Act
    let output = emojificator.addEmoji("foo")

    // Assert
    expect(output) == "✨ foo ✨"
  }
}
```
