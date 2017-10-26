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

```swift
describe("Movie") {

  context("when initializing from dictionary") {

    let movie: Movie!

    let name = "The Dark Knight Rises"
    let genre = "Action"
    let year = 2012

    beforeEach {
      movie = Movie(dict: ["name": name, "genre": genre, "year": year])
    }

    it("maps the name property") {
      expect(movie.name) == name
    }

    it("maps the genre property") {
      expect(movie.genre) == genre
    }

    it("maps the year property") {
      expect(movie.year) == year
    }
  }
}
```

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

### Fixtures

We already established that tests should be readable and compact. This is not often possible or straightforward. The system under test might have a number of dependencies, and reducing it might not be possible at the time.

In such case it is useful to be able to initialize all the dependencies of the system under test using one liners, and also have meaningful defaults.

Such kind of default values are called [fixtures](https://github.com/junit-team/junit4/wiki/Test-fixtures), and can be defined as:

> A test fixture is a fixed state of a set of objects used as a baseline for running tests. The purpose of a test fixture is to ensure that there is a well known and fixed environment in which tests are run so that results are repeatable.

Here's an example to follow when providing fixtures for your components.

```swift
// APIService+Fixture.swift
extension APIService {

  static func fixture(
    pageSize: Int = 42,
    networkService: NetworkService = NetworkService.fixture()
  ) -> APIService {
    return APIService(
      pageSize: pageSize,
      networkService: networkService,
      prefix: "foobar"
    )
  }
}

let apiService = APIService.fixture()
let otherAPIService = APIService.fixture(pageSize: 2)
```

An important thing to note is that when writing the unit test for `APIService` we would **not** use it `fixture` method to get the instance to test, but rather the actual `init`. Fixtures are only meant to use when providing dependencies.

## Code Organization

### One new line between every `describe`, `context`, `it`, `beforeEach`, and `afterEach` block

Vertical spacing helps maintaining readability by making the code easier to scan for the eye. Consistency also helps readability, code that looks familiar takes less time to be mentally parsed.

In order to promote vertical spacing and consistency this convention applies: "there should be a new line on top of every _group_, that is, on top of every `describe`, `context`, `it`, `beforeEach`, `afterEach`". All the examples in this document follow this rule.

Note that there should be no new line between the closing `}` of those blocks, in order to keep the code more compact where readability is not important.
