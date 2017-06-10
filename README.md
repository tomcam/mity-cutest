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

### Create the file area.h

First create a header file named area.h:

```c
#ifndef __AREA__
#define __AREA__
float area(float radius);
#define PI 3.1415927
#endif
```

Next, create the library file area.c:

```c
float area(float radius)
{
    if (radius <= 0)
        return -1;
        
    return PI * radius * radius;
}
```

