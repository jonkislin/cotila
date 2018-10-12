<div align="center">
  <img src="logo.svg" width="30%" height="auto">
</div>

[![Build Status](https://travis-ci.org/calebzulawski/cotila.svg?branch=master)](https://travis-ci.org/calebzulawski/cotila)

Copyright 2018 Caleb Zulawski

Licensed under the Apache License, Version 2.0

## Overview

<<<<<<< HEAD
Cotila (**co**mpile-**ti**me **l**inear **a**lgebra) provides a set of linear algebra functions in C++ intended for use during compile time.
All functions available in Cotila are ***constexpr***, meaning they can be used at compile-time to generate constants and lookup tables in a type-safe, transparent manner.
=======
Cotila (**co**mpile-**ti**me **l**inear **a**lgebra) is a header-only library that provides a set of linear algebra functions in C++ intended for use during compile time. 
All functions available in Cotila are ***constexpr***, meaning they can be used at compile-time to generate constants and lookup tables in a type-safe, readable, and maintainable manner.
>>>>>>> 54b95c6... Flatten logo font and clean up documentation and readme

## Installation

Cotila is a header-only library.  Simply point your compiler to include the `include/` directory.

Code must be compiled with at least C++17 support to use Cotila.

## Documentation

The documentation can be generated by running `make docs`, which requires Doxygen.  It can also be viewed [online](https://calebzulawski.github.io/cotila/).

## Quickstart Guide

To use Cotila, all you need to do is `#include <cotila/cotila.h>`.  This header will include all of the headers provided by Cotila.

The Cotila interface is designed around operations commonly found in BLAS or MATLAB and should be simple and predictable.

### Types

Cotila provides support for three types: **scalars**, **vectors**, and **matrices**.

**Scalars** are represented by fundamental types, such as `float` or `double`, as well as `std::complex`.  Cotila provides a variety of operations that manipulate scalar types.  In some cases, such as square roots, a standard library implementation already exists but it is not `constexpr`.  A simple example below:

```c++
constexpr double s = cotila::sqrt(4.);
static_assert(s == 2.); // this evaluates and passes at compile time
```

**Vectors** are represented by the `cotila::vector` class.  The `vector` class is a container for scalar types.  Additionally, `vector` is an aggregate class containing a single array and is constructed via [aggregate initialization](http://en.cppreference.com/w/cpp/language/aggregate_initialization).  If you are confused, some notes on aggregate initialization can be found in the next section.  A simple vector example:
```c++
constexpr cotila::vector<double, 3> v1 {{1., -2., 3.}}; // very explicit declaration
constexpr cotila::vector v2 {1., 2., 3.}; // deduces the type, omits the extra braces via uniform initialization
static_assert(v2 == cotila::abs(v1));
```

**Matrices** are represented by the `cotila::matrix` class.  Like the `vector` class, `matrix` is an aggregate class containing a single 2-dimensional array.  A `matrix` is initialized like a normal 2-dimensional array in C++ (i.e. row-major order).  A simple matrix example:
```c++
/*  m1 contains:        m2 contains:
 *  1 2 3               1 4
 *  4 5 6               2 5
 *                      3 6
 */
constexpr cotila::matrix<double, 2, 3> m1 {{{1., 2., 3.}, {4., 5., 6.}}}; // very explicit declaration
constexpr cotila::matrix m2 {{{1., 4.}, {2., 5.}, {3., 6.}}}; // deduces the type, but the extra braces are required
static_assert(m2 == cotila::transpose(m1));
```

**Complex values** are not handled any differently, other than initialization:
```c++
/*  m1 contains:        m2 contains:
 *  1 + 0i   2 + 1i     1 + 0i   3 + 1i
 *  3 - 1i   4 + 2i     2 - 1i   4 - 2i
 *
 */
constexpr cotila::matrix<std::complex<double>, 2, 2> m1 {{{{1., 0.}, {2., 1.}}, {{3., -1.}, {4., 2.}}}};
constexpr cotila::matrix m2 {{{{1., 0.}, {3., 1.}}, {{2., -1.}, {4., -2.}}}}; // complex types can be deduced, too!
static_assert(m2 = cotila::hermitian(m1));
```

### Aggregate Initialization

Aggregate objects can be initialized similarly to C structs by simply providing an initializer list with the values to initialize each member.  In C++, arrays can be initialized like so:
```c++
double arr[3] = {1., 2., 3.};
```
or
```c++
double arr[3] {1., 2., 3.};
```
The `cotila::vector` class contains a single array:
```c++
template<typename T, std::size_t N>
struct vector {
    T arr[N];
};
```
To initialize a `vector`, you must initialize the array member, which results in an extra set of braces:
```c++
vector<double, 3> v = {{1., 2., 3.}};
```
or
```c++
vector<double, 3> v {{1., 2., 3.}};
```

#### Uniform initialization

Aggregate objects with a single member can be initialized with uniform initialization, which allows you to omit the extra braces:
```c++
vector<double, 3> v {1., 2., 3.};
```

You may omit the extra braces on `cotila::matrix`, however this can get confusing.
You may not use nested initializer lists for uniform initialization, so the elements must be listed in row-major order:
```c++
matrix<double, 2, 2> m {1., 2., 3., 4.}; // first row contains [1, 2], second row contains [3, 4]
```

#### Class template argument deduction

Cotila's vectors and matrices support class template argument deduction.  This allows you to omit the template arguments entirely:
```c++
vector v1 {1., 2., 3.};   // uniform initialization
vector v2 {{1., 2., 3.}}; // normal aggregate initialization
```

Matrices do not support template argument deduction for uniform initialization, since it is impossible to deduce the shape of the matrix.
In this case, an extra set of braces is always required:
```c++
matrix m {{{1., 2.}, {3., 4.}}};
```
