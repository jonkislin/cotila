# Cotila - a compile-time linear algebra system for C++

[![Build Status](https://travis-ci.org/calebzulawski/cotila.svg?branch=master)](https://travis-ci.org/calebzulawski/cotila)

Copyright 2018 Caleb Zulawski

## Overview

Cotila (**co**mpile-**ti**me **l**inear **a**lgebra) provides a set of linear algebra functions in C++ intended for use during compile time. 
All functions available in Cotila are ***constexpr***, meaning they can be used at compile-time to generate constants and lookup tables in a type-safe, transparent manner.

### Installation

Cotila is a header-only library.  Simply point your compiler to include the `include/` directory.

Code must be compiled with at least C++17 support to use Cotila.