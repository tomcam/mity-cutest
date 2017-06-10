# mity-cutest tutorial
Tutorial for cutest.h C unit testing framework by mity

This step-by-step tutorial shows how to create a unit test suite for modules written in C.

* Create a tiny library written in C consisting of a single source and single header file. This is a library file so it doesn't have a `main()`.
* Compile it using the command line (Unix/MacOS)
* Add simple unit tests by including [cutest.h](https://github.com/mity/cutest) by Martin Mitáš, aka [mity](https://github.com/mity) on GitHub
* Run the unit tests. See how both success and failure look.

# MISSING
* How to get cutest.h

## Create the library files

The library will consist of a simple function to compute the area of a circle. It has some error checking

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
#ifndef __AREA__
#define __AREA__
float area(float radius);
#define PI 3.1415927
#endif
```

* Create the library file area.c:

```c
#include "area.h"
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

If you're not used to compiling with the command line, some notes.

* `gcc` is the name of the compiler. On MacOS you may need to download [Xcode](https://developer.apple.com/xcode/downloads/). If you haven't done so before you will be required to create a free developer's account for the privilege.
* The command-line flag `std=c99` forces a compile using the [C99 standard](https://en.wikipedia.org/wiki/C99) version of the language. It is useful here primarily because this code uses `//` comments instead of the older `/*  */` style.
* The command-line flag `-c` means compile but do not make an executable. Compile and correct your source as many times as is necessary until `gcc` yields no output, which means success. There's no `main` in this code so it wouldn't work anyway.
