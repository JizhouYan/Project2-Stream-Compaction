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

	![Optimize the block size](img/T1.png)

	From above chart, for each implementation, the optimized block_size is around 128. Small block size (less than 16) will cause serious performance penalties. When block size is higher than 128, performance cannot continue obtaining any improvement whith the increase of block size, and performance will drop off for large block size.

* Compare all of these GPU Scan implementations (Naive, Work-Efficient, and Thrust) to the serial CPU version of Scan. Plot a graph of the comparison (with array size on the independent axis).

	![Performance Comparison of Scan](img/T2.png)

	An unexpected thing about the above images are thrust(the blue line in the middle of the image) taking much more time when it's first time running. I guess that's because the implementation of THRUST, it might require some initialization operations during the first time running. And once after that, the thrust execution time become much faster than my implementation.

	With the increase of array size, thrust achives the slowest increase of execution time. CPU and GPU implementations' performance will suffer from the increase of array size. 

	In my computer, GPU implementation is slower than CPU impelmentation. Work-efficient GPU implemenetation is much faster than naive GPU scan.

	Non-power-of-two array size will introduce some decrease of performance for GPU implementation, but very slightly.

	![Performance Comparison for Compaction](img/T3.png)

	Non-power-of-tow array size have trivial influence on both gpu and cpu.

	GPU implementation is slower than cpu without scan implementation, but much faster than cpu implementation with scan.

	![Performance Comparison for Radix Sort and Std::sort](img/T4.png)

	For extra, I implementated Radix Sort, compared with std::sort, radix sort is generally slower than std::sort.

	Execution time increases almost linearly for Radix Sort.

* Can you find the performance bottlenecks? Is it memory I/O? Computation? Is it different for each implementation?

	I think the bottleneck is memory I/O. Without using shared-memory, it's very inefficient for adjacent threads accessing memory with huge physical distance. Especially when this project is all about memory I/O. Even using work-efficient scan implementation, GPU still cannot beat CPU implemetation. For GPU scan and compaction implementations, array size of power of two can obtain better performance than non-power-of-two array size. On CPU, non-power-of-tow doesn't seem to have huge influence. For this type of memory I/O-intensive computation tasks, we need some better solution about CUDA to improve the efficience of memory IO.

## Example Output

```
****************
** SCAN TESTS **
****************
    [   5  42  47  27  16   0   5  26   2  32  37  11  31 ...  25   0 ]
==== cpu scan, power-of-two ====
   elapsed time: 68.0948ms    (std::chrono Measured)
    [   0   5  47  94 121 137 137 142 168 170 202 239 250 ... 410888135 410888160 ]
==== cpu scan, non-power-of-two ====
   elapsed time: 32.0523ms    (std::chrono Measured)
    [   0   5  47  94 121 137 137 142 168 170 202 239 250 ... 410888062 410888084 ]
    passed
==== naive scan, power-of-two ====
   elapsed time: 13.1426ms    (CUDA Measured)
    passed
==== naive scan, non-power-of-two ====
   elapsed time: 13.0404ms    (CUDA Measured)
    passed
==== work-efficient scan, power-of-two ====
   elapsed time: 6.33571ms    (CUDA Measured)
    passed
==== work-efficient scan, non-power-of-two ====
   elapsed time: 6.27824ms    (CUDA Measured)
    passed
==== thrust scan, power-of-two ====
   elapsed time: 1.22403ms    (CUDA Measured)
    passed
==== thrust scan, non-power-of-two ====
   elapsed time: 1.12288ms    (CUDA Measured)
    passed

*****************************
** STREAM COMPACTION TESTS **
*****************************
    [   1   3   1   3   0   0   3   2   2   2   3   3   0 ...   0   0 ]
==== cpu compact without scan, power-of-two ====
   elapsed time: 36.9479ms    (std::chrono Measured)
    [   1   3   1   3   3   2   2   2   3   3   1   1   3 ...   3   3 ]
    passed
==== cpu compact without scan, non-power-of-two ====
   elapsed time: 38.7113ms    (std::chrono Measured)
    [   1   3   1   3   3   2   2   2   3   3   1   1   3 ...   2   3 ]
    passed
==== cpu compact with scan ====
   elapsed time: 148.285ms    (std::chrono Measured)
    [   1   3   1   3   3   2   2   2   3   3   1   1   3 ...   3   3 ]
    passed
==== work-efficient compact, power-of-two ====
   elapsed time: 8.14077ms    (CUDA Measured)
    passed
==== work-efficient compact, non-power-of-two ====
   elapsed time: 8.2161ms    (CUDA Measured)
    passed

*****************************
****** RADIX SORT TESTS *****
*****************************
    [ 16812 29839 8257 3018 2627 25803 18380 10671 31557 6386 24591 25599 24391 ... 30548 16146 ]
==== std::sort ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ... 32767 32767 ]
   elapsed time: 8.2161ms    (std::chrono Measured)

==== GPU::RadixSort ====
    passed
   elapsed time: 8.2161ms    (CUDA Measured)

Press any key to continue . . .
```
