# UCS645: Parallel and Distributed Computing

This repository contains C++ implementations for the UCS645 course, focusing on high-performance computing using the OpenMP (Open Multi-Processing) API.

##  Quick Start: Compilation

The code in this repository utilizes features from **C++23**. To ensure proper performance and parallel execution, use the following compilation command:

```bash
g++ -std=c++23 -Wall -Wextra -Wpedantic -fopenmp -O3 -o <output_file> <input_file>