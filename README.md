# AttoUnit

([Atto = 10^-18](https://en.wikipedia.org/wiki/Atto-))

A tiny (~100 lines), easy to use, header only unit testing framework for C.

Originally created to test [my Intel 8080
emulator](https://github.com/gunshippenguin/i8080), AttoUnit was created with
three main goals in mind:

- Be incredibly quick and easy to set up
- Output test failures verbosely such that they can be quickly debugged
- Minimize boilerplate

## Installation

Just include `attounit.h` in all your test source files and you're good to go.

Note that AttoUnit requires a compiler that supports
[GNU C extensions](https://gcc.gnu.org/onlinedocs/gcc/C-Extensions.html).

## Usage

AttoUnit separates test code into test suites, each of which is contained in one
source file. At the top of your source file should be the macro `TEST_SUITE`,
which takes exactly one argument, the name of the test suite. This can be any
valid C identifier.

Following `TEST_SUITE`, should be two macros, `BEFORE_EACH` and `AFTER_EACH`.
Like most other testing frameworks, these let you define a block of code to be
run before and after each test case. Note that due to limitations of C macros,
these **have to** be present in each test suite source file, even if they
contain no code.

Following this, are one or more `TEST_CASE` macros, each taking one argument,
the name of the test (again this can be any valid C identifier). Within each
test case, you can use any of the assertion macros AttoUnit provides you (see
below).

The macro `TEST_MAIN()` must appear exactly once in your code. This expands to
an entry point for your testing binary containing code that automatically runs
all defined test cases.

Here's an example of how you would use AttoUnit to test the strtol standard
library function:

```C
#include "attounit.h"
#include <stdlib.h>

TEST_SUITE(strtol);
BEFORE_EACH() {}
AFTER_EACH() {}

TEST_CASE(strtol_parses_integer) {
  char *num = "123";
  long res = strtol(num, NULL, 10);
  ASSERT_EQUAL(res, 123);
}

TEST_CASE(strtol_parses_integer_with_leading_space) {
  char *num = "     123";
  long res = strtol(num, NULL, 10);
  ASSERT_EQUAL(res, 123);
}

TEST_MAIN()
```

If both these tests were to fail, AttoUnit would output something like the
following:

```
✗ Assertion failed in test/strtol_test.c
	Suite: strtol, Case: strtol_parses_integer
	res == 123
	Expected 0 to equal 123
✗ Assertion failed in test/strtol_test.c
	Suite: strtol, Case: strtol_parses_integer_with_leading_space
	res == 0
	Expected 5 to equal 0
=========================================================
✗ 2 assertions failed
```

## Assertion Macros

AttoUnit provides the following assertion macros:

- `ASSERT_TRUE(val)`
  - Assert that val is true (ie. not 0)
- `ASSERT_FALSE(val)`
  - Assert that val is false (ie. 0)
- `ASSERT_NULL(val)`
	- Assert that the given value is equal to `NULL`
- `ASSERT_NOT_NULL(val)`
	- Assert that val is not equal to `NULL`
- `ASSERT_EQUAL(a, b)` / `ASSERT_EQUAL_FMT(a, b, fmt)`
	- Assert that a is equal to b using the `==` operator
- `ASSERT_NOT_EQUAL(a, b)` / `ASSERT_NOT_EQUAL_FMT(a, b, fmt)`
	- Assert that a is not equal to b using the `!=` operator
- `ASSERT_GREATER(a, b)` / `ASSERT_GREATER_FMT(a, b, fmt)`
	- Assert that a is greater than b using the `>` operator
- `ASSERT_LESS(a, b)` / `ASSERT_LESS_FMT(a, b, fmt)`
	- Assert that a is less than b using the `<` operator

Note that when outputting a failure, values are by default output as signed
integers using the `%d` format specifier to printf. If you want to print
failures in another format (eg. when comparing 2 float values), use the
assertion macro of the same name with the `_FMT` postfix. These macros take a
third parameter that will let you provide a format specifier. For example:

```C
float f1 = 0.5;
float f2 = 2.5;
ASSERT_GREATER_FMT(f1, f2, %f);
```

will output:

```
✗ Assertion failed in test.c
	Suite: floats, Case: floats_equal
	f1 > f2
	Expected 0.500000 to be greater than 2.500000
=========================================================
✗ 1 assertions failed
```

## License

[MIT](https://github.com/GunshipPenguin/attounit/blob/master/LICENSE) © Rhys
Rustad-Elliott
