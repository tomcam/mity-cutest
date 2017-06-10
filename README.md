# An introduction to C Unit Testing Using CUTest (cutest.h) by mity

This step-by-step tutorial shows how to create a unit test suite for modules written in C. Unit testing means writing separate test programs that exercise your existing C modules with unexpected input. As you add to your unit test suite your overall testing costs decrease and well-written tests make you and your users more confident in the quality of the software you're writing. 

The target audience is intermediate C programmers who want a little handholding with an easy-to-understand unit testing framework, in this case Mity's [CUTest](https://github.com/mity/cutest/). It assumes you are using a command-line C compiler (gcc in this case). It contains full copy/pastable code to:

* Create a tiny library written in C consisting of a single source and single header file. This is a library file so it doesn't have a `main()`.
* Compile it using the command line (Unix/MacOS)
* Add simple unit tests by including [cutest.h](https://github.com/mity/cutest) by Martin Mitáš, aka [mity](https://github.com/mity) on GitHub. These tests are simple boolean expressions wrapped by macros such as `TEST_CHECK()`. Tests evaluating to zero are failures, and nonzeros are successes.
* Run the unit tests. See how both success and failure look.

#### NOTE: What's Missing From This Tutorial?

**Feel free to contact me at tomcampbell at gmail.com with complaints or suggestions!**

## Create the library module to test

The first step in this tutorial is to write some normal C code. The library will consist of a simple function to compute the area of a circle. It has some error checking, which is not only good practice but illustrates how to implement tests with incorrect inputs and show expected behavior. Because it's a library there will be no `main()` function in the source; CUTest supplies it automatically.

### Create a directory named circle

This is meant to simulate a real project, so give it a directory. In this example, it is off a directory called `c` in the user's working directory.

```bash
# Create a directory named c and a subdirectory named
# circle in the user's working directory
$ mkdir -p ~/c/circle

# Change to it
$ cd ~/c/circle
```

### Create the library source files

* Create a header file named area.h:

```c
/* area.h */
#ifndef __AREA__
#define __AREA__
float area(float radius);
#define PI 3.1415927
#endif
```

* Create the library file area.c:

```c
/* area.c */
#include "area.h"

/* Obtain the area of a circle given its radius.
 * Return -1 if given an invalid value.
 */
float area(float radius)
{
    if (radius <= 0)
        return -1;    
    return PI * radius * radius;
}
```

Determining the area of a circle is easy, but normally examples contain no error checking. Remember, this is meant to simulate what you'd do with your real code, so this crucial line does the trick:

```c
    if (radius <= 0)
        return -1;    
```

* Make sure area.c compiles correctly:

```bash
$ gcc -std=c99 fsize.c -c
```

#### Notes

If you're not used to compiling with the command line, some notes.

* `gcc` is the name of the compiler. On MacOS you may need to download [Xcode](https://developer.apple.com/xcode/downloads/). If you haven't done so before you will be required to create a free developer's account for the privilege.
* The command-line flag `-std=c99` forces a compile using the [C99 standard](https://en.wikipedia.org/wiki/C99) version of the language. It is useful here primarily because this code uses `//` comments instead of the older `/*  */` style.
* The command-line flag `-c` means compile but do not make an executable. Compile and correct your source as many times as is necessary until `gcc` yields no output, which means success. There's no `main` in this code so it wouldn't work anyway.

## Create the mininum test harness with a main() and no tests

To test your library, you need a driver file with a `main()` from which you will call the tests. CUTest generates it automatically from a list of test functions you supply it. Let's create the simplest possible test harness using `cutest.h` just to make sure everything compiles properly and the executable runs.

### Obtain cutest.h

First you need `cutest.h`, which can be found on GitHub at [https://github.com/mity/cutest/](https://github.com/mity/cutest/).

* Copy the file [cutest.h](https://raw.githubusercontent.com/mity/cutest/master/include/cutest.h) from [https://raw.githubusercontent.com/mity/cutest/master/include/cutest.h](https://raw.githubusercontent.com/mity/cutest/master/include/cutest.h) into your project directory. 

Yes, Git users, this is an ugly cheat because it bypasses normal use of git and GitHub, but version control isn't part of the tutorial.

### Create and compile the test harness test_area.c

The absolute minimal test configuration is... no tests, but this step lets you get the generated `main()` function running. The driver executable will be named `test_area` Do this to ensure CUTest compiles without any distractions.

* First, create the minimal test harness `test_area.c` as follows:

```c
/* test_area.c */
#include "cutest.h"
#include "area.h"

TEST_LIST = {
    { 0 }
};
```

The filename can be anything. It doesn't need to start with `test_`.

* Compile the files area.c and test_area.c and create an executable with this command line:

```bash
$ gcc -std=c99 area.c test_area.c -o test_area
```

Compile and correct your source as many times as is necessary until `gcc` yields no output, which means success.

* Run the file:

```bash
$ ./test_area
```

The output looks like this:

```bash

Summary:
  SUCCESS: All unit tests have passed.
```

#### Notes

* This shows that a `main()` function was called. The `area()` function never executed because nothing has yet been defined to do so.
* The compile-only flag `-c` has been omitted, of course, because the goal is to create an executable.
* There is a new command-line option. The command-line flag `-o`, meaning it is followed by the name you wish to give your output file (the executable). 

## Add a simple unit test

So `main()` ran but no tests were run because none has yet been defined. Time to add one to `test_area.c`.

The first test couldn't be easier. It simply verifies that the constant PI, defined in the file `area.h`, is correct to 7 digits to the right of the decimal point.

Here's what you need to do to add a test case:

1. Write a test function prototype returning void with void arguments
2. Add a description in quotes, followed by the the name of that test function only (not its entire signature) to the `TEST_LIST` array macro declaration
3. Implement the function
4. Add a TEST_CHECK macro to your test function. It is a macro that evaluates to a nonzero/zero result. (Another macro named TEST_CHECK_ will be discussed later.)
5. Recompile and run your tests

Here's that process in detail. Don't worry, the whole file is shown at the end if you're not sure what happens in each step.

### 1. Add the prototype above the `TEST_LIST` macro declaration

To perform its auto-generation magic, CUTest expects all your test functions return `void` and contain a `void` parameter list. Add this prototype above the `TEST_LIST` macro in the test harness test_area.c 

```c
void pi_accurate_to_7_digits(void);

TEST_LIST = {
    { 0 }
};
```

### 2. Add a simple description in quotes followed by the function's name to the TEST_LIST macro

* Add a simple description in quotes, followed by a comma, and that function's name to the TEST_LIST macro. 
* Enclose all this in curly braces. 
* It is actually an array declaration so end each entry in the array with a comma too.
* Make sure it appears the `{ 0 }` element

The description appears when the unit tests are run. Here's the whole thing:

```c
void pi_accurate_to_7_digits(void);

TEST_LIST = {
    { "PI accurate to 7 digits", pi_accurate_to_7_digits },
    { 0 }
};
```

### 3. Add the code for that function below the TEST_LIST macro. 

Now write the function itself. Place it below the `TEST_LIST` declaration. There's no code here because this function will contain nothing but a `TEST_CHECK` macro. However, arbitrary C code is allowed here.

```c
TEST_LIST = {
    { "PI accurate to 7 digits?", pi_accurate_to_7_digits },
    { 0 }
};

void pi_accurate_to_7_digits(void)
{
    /* Your code goes here */
}

```

### 4. Insert TEST_CHECK macros in the code

Finally, the test itself. 

The test function is any old C function as long as it has the prescribed signature of void return and void parameters. It can  include any code. In this example we needonly  the `TEST_CHECK` macro.

Remember the test macro must evaluate to nonzero in order to pass the unit test:

```c
void pi_accurate_to_7_digits(void)
{
    TEST_CHECK(PI == 3.1415927);
}
```

The expression is simple because the test is simple. Here's the completed test_area.c file looks like this:

```c
/* test_area.c */
#include "cutest.h"
#include "area.h"

/* 1. Add function prototype returning void with void params. */
void pi_accurate_to_7_digits(void);

/* 2. Add description + function name to TEST_LIST array. */
TEST_LIST = {
    { "PI accurate to 7 digits", pi_accurate_to_7_digits },
    { 0 }
};

/* 3. Implement the function */
void pi_accurate_to_7_digits(void)
{

/* 4. Include TEST_CHECK expression evaluating to boolean result. */
    TEST_CHECK(PI == 3.1415927f);
}
```

### 5. Compile and run the unit test

Let's see what happens with a successful unit test. Compile and run:

```bash
$ gcc -std=c99 area.c test_area.c -o test_area
$ ./test_area
```

The output is more interesting now:

```bash
Test PI accurate to 7 digits... [   OK   ]

Summary:
  SUCCESS: All unit tests have passed.
```

Congratulations! You have written your first unit test.

### Run the unit test with the --verbose flag

Remember how CUTest automatically creates a `main()` function for you? It has some command-line options. Let's see what happens when you run the test again, passing it a `--verbose` flag on the command line.

```bash
$ ./test_area --verbose
Test PI accurate to 7 digits:
  test_area.c:18: Check PI == 3.1415927f... ok
  All conditions have passed.


Summary:
  Count of all unit tests:        1
  Count of run unit tests:        1
  Count of failed unit tests:     0
  Count of skipped unit tests:    0
  SUCCESS: All unit tests have passed.
```

#### Notes

* You can use `-v` instead of `--verbose`
* You can get a list of all command-line options using `--help` or `-h`. Here's what the output looks like in verbose mode:

```bash
Run the specified unit tests; or if the option '--skip' is used, run all
tests in the suite but those listed.  By default, if no tests are specified
on the command line, all unit tests in the suite are run.

Options:
  -s, --skip            Execute all unit tests but the listed ones
      --no-exec         Do not execute unit tests as child processes
      --no-summary      Suppress printing of test results summary
  -l, --list            List unit tests in the suite and exit
  -v, --verbose         Enable more verbose output
      --verbose=LEVEL   Set verbose level to LEVEL:
                          0 ... Be silent
                          1 ... Output one line per test (and summary)
                          2 ... As 1 and failed conditions (this is default)
                          3 ... As 1 and all conditions (and extended summary)
      --color=WHEN      Enable colorized output (WHEN is one of 'auto', 'always', 'never')
  -h, --help            Display this help and exit

Unit tests:
  PI accurate to 7 digits
```

It can do more. We'll come back round to it later. First let's see what happens when a unit test fails.

## Creating a unit test that fails

Here's what it looks like when a unit test fails. In this case we'll force a failure by modifying the `PI` declaration without modifying its test.

### Change the PI declaration in area.h

* Modify the PI constant declaration in area.h. The only test so far ensures that PI is accurate to 7 digits, so let's just lop off the 7. 

```c
/* area.h */
#ifndef __AREA__
#define __AREA__
float area(float radius);
#define PI 3.141592f /* Was 3.1415927f */
#endif
```

* Compile and run:

```bash
$ gcc -std=c99 area.c test_area.c -o test_area
$ ./test_area
```

And the output changes:

```bash
Test PI accurate to 7 digits... [ FAILED ]
  test_area.c:18: Check PI == 3.1415927f... failed

Summary:
  FAILED: 1 of 1 unit tests have failed.
```

## Displaying more information with TEST_CHECK_

Suppose you need more information than the `TEST_CHECK` macro supplies. You can append an expression similar to a `printf()` format string with the similarly-named `TEST_CHECK_` macro. In this example, let's print both what we expect PI to be and what its actual value is. Change it as follows: 

```c
void pi_accurate_to_7_digits(void)
{
    TEST_CHECK_(PI == 3.1415927f, "PI expected: 3.1415927. Actual: %1.7f", PI);
}
```

When you compile and run, the output now looks like this.

```bash
Test PI accurate to 7 digits... [ FAILED ]
  test_area.c:18: Check PI expected: 3.1415927. Actual: 3.1415920... failed

Summary:
  FAILED: 1 of 1 unit tests have failed.
```

Now go back and restore PI to its rightful value of 3.1415927f in `area.h` before you forget.

