# Parallel Correlation Matrix Calculation 

High-performance implementation of Pearson Correlation Coefficient calculation in C/C++. This project benchmarks **Sequential vs. Parallel** execution strategies and compares memory access patterns (**Flat Array vs. 2D Heap Array**) to analyze cache efficiency and thread scaling.

##  Key Features
* **Multi-Threading:** Utilizes **OpenMP** to parallelize the computationally intensive $O(N^3)$ correlation logic.
* **Memory Optimization:** Compares **Contiguous (Flat)** vs. **Fragmented (2D Heap)** memory layouts to demonstrate cache locality effects.
* **Hardware Acceleration:** Optimized for modern CPUs using vectorization (AVX/AVX2).

---

## Importance of the Makefile
The included `Makefile` is not just for compilation; it is a critical component for achieving high performance. It applies specific compiler flags that unlock your hardware's full potential:

| Flag | Description |
| :--- | :--- |
| **`-O3`** | Enables aggressive compiler optimizations (e.g., loop unrolling, inlining) to maximize single-thread speed. |
| **`-march=native`** | Instructs the compiler to use instructions specific to your CPU architecture (like **AVX/AVX2**), allowing it to process multiple data points per cycle. |
| **`-fopenmp`** | Enables the **OpenMP** runtime, allowing the code to spawn threads and execute loops in parallel across all available CPU cores. |

---

##  Quick Start

### Prerequisites
* **GCC Compiler** (with OpenMP support)
* **Make** build system

### Commands To Run

-   To Compile
    ``` bash
    make
    ```
-   To Run Parallel
    ``` bash
    make run
    ```
-   To Run Parallel with custom matrix size
    ``` bash
    make run ARGS="<number of rows> <number of columns>"
    ```
-   To Run Sequential
    ``` bash
    make run-seq
    ```
-   To Run Sequential with custom matrix size
    ``` bash
    make run-seq ARGS="<number of rows> <number of columns>"
    ```
