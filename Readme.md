# OpenMP Parallelism Lab

This repository contains C++ implementations demonstrating shared-memory parallelism using the OpenMP API. The project benchmarks the performance difference between serial and parallel execution for Linear Algebra kernels and Numerical Integration.

## Project Structure

* `daxpy_loop.cpp`: Implements the DAXPY linear algebra operation ($Y = aX + Y$) testing thread scalability from 2 to $N$ threads.
* `matrix_multiplication.cpp`: Compares different OpenMP loop scheduling strategies (1D, 2D, Collapsed) against a serial baseline for Matrix Multiplication.
* `pi.cpp`: Calculates the value of $\pi$ using numerical integration, demonstrating the use of the OpenMP `reduction` clause to handle race conditions.

## Prerequisites

* **Compiler:** GCC (`g++`) or any Clang-based compiler with OpenMP support.
* **OS:** Linux/Unix (recommended for easy terminal usage).

## Compilation Instructions

OpenMP requires the `-fopenmp` flag to enable parallel regions. We also recommend using `-O3` for compiler-level optimizations.

### 1. Compile DAXPY
```bash
g++ -std=c++23 -Wall -Wextra -Wpedantic -fopenmp -O3 -o daxpy_loop daxpy_loop.cpp