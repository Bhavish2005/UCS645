# UCS645 Lab 2: Parallel Programming Performance Analysis

---

## 1. Project Overview
This repository contains the source code and performance analysis for three distinct parallel programming problems implemented using **OpenMP**. The goal was to analyze the scalability, efficiency, and hardware bottlenecks of different parallelization strategies on a hybrid CPU architecture (Intel i5-12500H with P-cores and E-cores).

The three workloads analyzed are:
1.  **Molecular Dynamics:** An O(N²) particle interaction simulation (Compute-Bound).
2.  **DNA Sequence Alignment:** A Smith-Waterman wavefront algorithm (Dependency-Bound).
3.  **Heat Diffusion:** A 2D Finite Difference Method simulation (Memory-Bound).

---

## 2. Workload Analysis

### A. Molecular Dynamics (N-Body Simulation)
**Algorithm:** Computes pairwise forces between particles using Newton's laws.  
**Key Characteristic:** Highly Compute-Bound with minimal memory overhead.

#### Performance Metrics
| Threads | Execution Time (ms) | Speed Up | Throughput | Efficiency |
| :--- | :--- | :--- | :--- | :--- |
| **1 (Baseline)** | 744.92 | 1.00x | 13.42 | 1.00 |
| **2** | 310.75 | 2.40x | 32.18 | 1.20 |
| **6 (Peak)** | **90.31** | **8.25x** | **110.73** | **1.37** |
| **12** | 77.00 | 9.67x | 129.87 | 0.81 |

#### Hardware Insight (perf stat)
* **Super-Linear Scaling:** The implementation achieved a peak efficiency of **1.37** at 6 threads. This indicates that splitting the dataset allowed it to fit perfectly into the L1/L2 cache of the cores, drastically reducing access latency.
* **Instruction Efficiency:** The system maintained a solid IPC (Instructions Per Cycle) of **1.54** with phenomenal branch prediction (0.02% miss rate).
* **Conclusion:** This workload scales almost perfectly because the CPU spends most of its time doing math (ALU operations) rather than waiting for memory.

---

### B. DNA Sequence Alignment (Wavefront)
**Algorithm:** Smith-Waterman using a Wavefront (Diagonal) traversal strategy.  
**Key Characteristic:** Heavy data dependencies; requires strict synchronization.

#### Performance Metrics (Wavefront Optimized)
| Threads | Best Tile Size | Execution Time (ms) | Speed Up | Efficiency |
| :--- | :--- | :--- | :--- | :--- |
| **1** | N/A | 271.13 | 1.00x | 1.00 |
| **2** | 512x512 | 108.26 | 2.50x | 1.25 |
| **6** | 512x512 | **41.60** | **6.52x** | **1.09** |
| **10** | 192x192 | 42.39 | 6.40x | 0.64 |

#### Key Takeaways
* **Tile Size Criticality:** Larger tiles (512x512) significantly outperformed smaller ones (32x32). Small tiles caused threads to idle frequently at synchronization barriers, destroying performance.
* **Scaling Limit:** Performance peaks at **6 threads**. Beyond this, the overhead of managing wavefront dependencies and barrier synchronization outweighs the compute benefits.
* **Bottleneck:** The application suffers from **high page faults (8k/sec)**, indicating heavy memory initialization costs for the dynamic programming table.

---

### C. Heat Diffusion (2D Finite Difference)
**Algorithm:** Iterative stencil computation solving the heat equation over a 2D grid.  
**Key Characteristic:** Severely Memory-Bound; high bandwidth saturation.

#### Performance Metrics (OpenMP Scheduling)
| Threads | Schedule Type | Time (ms) | Speed Up | Efficiency |
| :--- | :--- | :--- | :--- | :--- |
| **1** | N/A | 2405.87 | 1.00x | 1.00 |
| **2** | **GUIDED** | **1527.85** | **1.57x** | **0.79** |
| **4** | STATIC | 1672.87 | 1.44x | 0.36 |
| **8** | STATIC | 2262.93 | 1.06x | 0.13 |
| **12** | STATIC | 2871.29 | 0.84x | 0.07 |

#### Hardware Insight (perf stat)
* **The Memory Wall:** The CPU achieved a catastrophic **IPC of 0.23**. Despite nearly perfect branch prediction (0.25% miss rate), the cores spent **~88% of their time stalled**.
* **Negative Scaling:** Parallel performance degraded immediately after 2 threads. By 12 threads, the application was slower than the single-threaded baseline.
* **Scheduling:** `GUIDED` scheduling was optimal for low thread counts (balancing P/E cores), but `STATIC` was more resilient (though still poor) at higher counts due to lower runtime overhead.

---

## 3. Overall Conclusion
The analysis highlights the importance of matching the parallelization strategy to the algorithm's characteristics:
1.  **Compute-Bound tasks (Molecular Dynamics)** benefit massively from multi-threading and can achieve super-linear speedups on hybrid architectures.
2.  **Dependency-Bound tasks (DNA Alignment)** require careful tuning of block sizes (tiling) to balance cache usage against synchronization overhead.
3.  **Memory-Bound tasks (Heat Diffusion)** hit a "Memory Wall" almost immediately. Adding more threads to a saturated memory bus degrades performance, proving that **more cores ≠ faster execution** when data transfer is the bottleneck.
