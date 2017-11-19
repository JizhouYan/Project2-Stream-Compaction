CUDA Stream Compaction
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 2**

* Jizhou Yan
* Tested on: Windows 10, i7-4789K @ 4.00GHz 32GB, GTX 1080Ti(Personal Desktop)

## Features
* Scan
  * CPU Scan
  * GPU Naive Scan
  * GPU Work efficient Scan
* Compaction
  * CPU Compaction with/without Scan
  * GPU Work efficient Compaction
* GPU Radix Sort

## Questions

* Roughly optimize the block sizes of each of your implementations for minimal run time on your GPU.
![Optimize the block size](img/1.png)

	From above chart, for each implementation, the optimized block_size is around 128. Small block size (less than 16) will cause serious performance penalties. When block size is higher than 128, performance cannot continue obtaining any improvement whith the increase of block size, and performance will drop off for large block size.

* Compare all of these GPU Scan implementations (Naive, Work-Efficient, and Thrust) to the serial CPU version of Scan. Plot a graph of the comparison (with array size on the independent axis).

![Performance Comparison of Scan](img/3.png)

	An unexpected thing about the above images are thrust(the blue line in the middle of the image) taking much more time when it's first time running. I guess that's because the implementation of THRUST, it might require some initialization operations during the first time running. And once after that, the thrust execution time become much faster than my implementation.

	With the increase of array size, thrust achives the slowest increase of execution time. CPU and GPU implementations' performance will suffer from the increase of array size. 

	In my computer, GPU implementation is slower than CPU impelmentation. Work-efficient GPU implemenetation is much faster than naive GPU scan.

	Non-power-of-two array size will introduce some decrease of performance for GPU implementation, but very slightly.

![Performance Comparison for Compaction](img/4.png)

	Non-power-of-tow array size have trivial influence on both gpu and cpu.

	GPU implementation is slower than cpu without scan implementation, but much faster than cpu implementation with scan.

![Performance Comparison for Radix Sort and Std::sort](img/5.png)

	For extra, I implementated Radix Sort, compared with std::sort, radix sort is generally slower than std::sort.

	Execution time increases almost linearly for Radix Sort.

* Can you find the performance bottlenecks? Is it memory I/O? Computation? Is it different for each implementation?

	* I think the bottleneck is memory I/O. Without using shared-memory, it's very inefficient for adjacent threads accessing memory with huge physical distance. Especially when this project is all about memory I/O. Even using work-efficient scan implementation, GPU still cannot beat CPU implemetation. For GPU scan and compaction implementations, array size of power of two can obtain better performance than non-power-of-two array size. On CPU, non-power-of-tow doesn't seem to have huge influence. Following image is a profiling image captured by NSight:
	![profiling](img/5.png)
	From above image, we can see that `cudaMemcpy` and `cudaMalloc` almost took half of the entire execution time. For this type of memory I/O-intensive computation tasks, we need some better solution about CUDA to improve the efficience of memory IO.

	* optimized block size of each implementation, refer to **Find an Optimized BlockSize for Each Implementation**.
	* comparison of different implementations of scan and compaction, refer to **Performance Analysis**.
	* output of test-program, refer to **Example Output**.

## Build
Modified `CMakeLists.txt` for adding new files `radixsort.h` and `radixsort.cu`. 

## Example Output
Copy and paste from one execution: