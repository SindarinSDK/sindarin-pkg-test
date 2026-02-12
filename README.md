# Sindarin Test

A testing framework for the [Sindarin](https://github.com/SindarinSDK/sindarin-compiler) programming language, providing an assertion library and a cross-platform parallel test runner.

## Installation

Add the package as a dependency in your `sn.yaml`:

```yaml
dependencies:
- name: sindarin-pkg-test
  git: git@github.com:SindarinSDK/sindarin-pkg-test.git
  branch: main
```

Then run `sn --install` to fetch the package.

## Quick Start

Write a test file (`tests/test_example.sn`):

```sindarin
import "test/assert"

fn main(): void =>
    print("Testing My Feature\n")
    print("==================\n\n")

    print("Testing basic math...\n")
    check(1 + 1 == 2, "1 + 1 should be 2")
    assertEqualInt(2 * 3, 6, "2 * 3 should be 6")
    print("  basic math OK\n")

    print("Testing strings...\n")
    assertEqual("hello", "hello", "string equality")
    assertContains("hello world", "world", "should contain world")
    print("  strings OK\n")

    summary()
```

Create the expected output (`tests/test_example.expected`):

```
Testing My Feature
==================

Testing basic math...
  basic math OK
Testing strings...
  strings OK

Tests passed: 4, failed: 0
All tests passed!
```

## Documentation

### Modules

| Module | Import | Description |
|--------|--------|-------------|
| Assert | `import "test/assert"` | Test assertions and result tracking |

### Assert

Assertion functions for writing tests. All assertions increment internal pass/fail counters.

```sindarin
import "test/assert"
```

#### Core

```sindarin
check(condition: bool, message: str)        # Basic assertion
assertTrue(condition: bool, message: str)    # Assert condition is true
assertFalse(condition: bool, message: str)   # Assert condition is false
```

#### Equality

```sindarin
assertEqual(actual: str, expected: str, message: str)
assertEqualInt(actual: int, expected: int, message: str)
assertEqualLong(actual: long, expected: long, message: str)
assertEqualDouble(actual: double, expected: double, message: str)
assertEqualFloat(actual: float, expected: float, message: str)
assertEqualBool(actual: bool, expected: bool, message: str)
assertEqualChar(actual: char, expected: char, message: str)
```

#### Inequality

```sindarin
assertNotEqual(actual: str, expected: str, message: str)
assertNotEqualInt(actual: int, expected: int, message: str)
assertNotEqualLong(actual: long, expected: long, message: str)
assertNotEqualDouble(actual: double, expected: double, message: str)
```

#### Comparisons

```sindarin
assertGreaterThan(actual: int, expected: int, message: str)
assertLessThan(actual: int, expected: int, message: str)
assertGreaterThanOrEqual(actual: int, expected: int, message: str)
assertLessThanOrEqual(actual: int, expected: int, message: str)
```

#### String Assertions

```sindarin
assertContains(haystack: str, needle: str, message: str)
assertStartsWith(actual: str, prefix: str, message: str)
assertEndsWith(actual: str, suffix: str, message: str)
```

#### Counters & Summary

```sindarin
passed(): int       # Number of passed assertions
failed(): int       # Number of failed assertions
total(): int        # Total assertions (passed + failed)
reset()             # Reset all counters to zero
summary()           # Print results and pass/fail message
```

### Test Runner

The package includes a cross-platform test runner at `scripts/run_tests.sn` that discovers and executes test files with parallel support.

#### Using the Test Runner

Add the test runner to your `Makefile`:

```makefile
RUN_TESTS_SN := .sn/sindarin-pkg-test/scripts/run_tests.sn
RUN_TESTS_BIN := bin/run_tests$(EXE_EXT)

$(RUN_TESTS_BIN): $(RUN_TESTS_SN) | $(BIN_DIR)
	@echo "Compiling test runner..."
	@$(SN) $(RUN_TESTS_SN) -o $(RUN_TESTS_BIN) -l 1

test: $(RUN_TESTS_BIN)
	@$(RUN_TESTS_BIN)
```

#### Test Runner Options

```
run_tests [options] [test-dir]

Arguments:
  test-dir           Test directory (default: tests/)

Options:
  --compiler PATH    Path to Sindarin compiler (default: sn from PATH)
  --timeout SEC      Compile timeout in seconds (default: 60)
  --run-timeout SEC  Run timeout in seconds (default: 30)
  --exclude TESTS    Comma-separated list of tests to exclude
  --verbose, -v      Show detailed output
  --no-color         Disable colored output
  --parallel, -j N   Run tests with N parallel workers (default: CPU count)
  --help, -h         Show this help message
```

#### Test File Conventions

- **File naming**: `test_*.sn` for automatic discovery
- **Expected output**: `.expected` files contain expected stdout for comparison
- **Error tests**: `.expected` file with error message for expected compile failures
- **Panic tests**: `.panic` marker file for expected non-zero exit codes

## Development

### Running Self-Tests

```bash
make test
```

### Available Targets

```bash
make test       # Run self-tests
make clean      # Remove build artifacts
make help       # Show all targets
```

## Dependencies

This package depends on [sindarin-pkg-sdk](https://github.com/SindarinSDK/sindarin-pkg-sdk) for process execution, file I/O, and OS utilities used by the test runner. Dependencies are automatically managed via the `sn.yaml` package manifest.

## License

MIT License
