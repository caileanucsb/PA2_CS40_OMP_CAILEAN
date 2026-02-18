Last name of Student 1: FERNANDES
First name of Student 1: CAIILEAN
Email of Student 1: cailean@ucsb.edu


If CSIL is used for performance assessment instead of Expanse, make sure you evaluate when such a machine is lightly 
loaded using “uptime”. Please  indicate your evaluation is done on CSIL and list the uptime index of that CSIL machine.  

Report 
----------------------------------------------------------------------------
1. How is the code parallelized? Show your solution by listing the key computation parallelized with
  OpenMP and related code. 
The code is parallelized using shared memory architecture. The Omp "parallel for" directive is used which automatically breaks for loop iterations into chunks and assigns them to threads. The call to mv_compute (which operates on each row of the matrix) is effectively parallelized. There are different scheduling types in place, but these types are determined at run-time based on user-provided parameters. Block and Block Cyclic mapping using omp_static_schedule, while, dynamic uses omp_dynamic_schedule:

void parallel_itmv_mult(int threadcnt, int mappingtype, int chunksize) {
  /*Your solutuion with OpenMP*/
 omp_set_num_threads(threadcnt);
 omp_sched_t sched_type;
  if(mappingtype == BLOCK_MAPPING){
    sched_type = omp_sched_static;
    omp_set_schedule(sched_type, chunksize); 
  }else if(mappingtype == BLOCK_CYCLIC){
    sched_type = omp_sched_static;
    omp_set_schedule(sched_type, chunksize);
  }else {
    sched_type = omp_sched_dynamic;
    omp_set_schedule(sched_type, chunksize);
  }
  for (int k = 0; k < no_iterations; k++) {
    #pragma omp parallel for num_threads(threadcnt) schedule(runtime)
        for (int i = 0; i < matrix_dim; i++) {
          mv_compute(i);
        }
    #pragma omp parallel for num_threads(threadcnt) schedule(static)
        for (int i = 0; i < matrix_dim; i++) {
          vector_x[i] = vector_y[i];
        }
  }


----------------------------------------------------------------------------
2.  Report the parallel time, speedup, and efficiency with blocking mapping, block cyclic mapping with block size 1 
and block size 16 using  2 cores (2 threads), and 4 cores (4 threads) for parallelizing the code 
in handling a full dense matrix with n=4096 and t=1024. 

Note: in both blocking and cyclic mapping cases, to find a sequential time estimation, I ran my code with 1 thread and block size of 1.

1 Core (Blocking): (Sequential Time Estimation): 
Test 9: n=4K t=1K blockmapping (r=1): Latency = 16.35124 sec and 1.3451 GFLOPS with 1 threads. Matrix dimension 4096 
****************************************************************************
2 Cores (Blocking:)
Test 9: n=4K t=1K blockmapping: Latency = 8.186287 sec and 4.1972 GFLOPS with 2 threads. Matrix dimension 4096
***************************************************************************
4 Cores (Blocking)
Test 9: n=4K t=1K blockmapping: Latency = 4.223282 sec and 8.1358 GFLOPS with 4 threads. Matrix dimension 4096 
****************************************************************************

Speed-up (blocking, 2 cores): 16.35/8.19 = 1.996
Efficiency(blocking, 2 cores): 1.996/2 = 99.8%
Speed-up (blocking, 4 cores): 16.35/4.22 = 3.87
Efficiency(blocking, 4 cores): 96.8%

1 Core (Block Cyclic): (Sequential Time Estimation): 
Block size 1: 
Test 10: n=4K t=1K block cylic (r=1): Latency = 18.060271 sec and 1.2245 GFLOPS with 1 threads. Matrix dimension 4096 
****************************************************************************
2 Cores (Block Cyclic)
Block Size 1:  Test 10: n=4K t=1K block cylic (r=1): Latency = 9.847270 sec and 3.4893 GFLOPS with 2 threads. Matrix dimension 4096 
Block Size 16: Test 11: n=4K t=1K block cyclic (r=16): Latency = 9.191937 sec and 3.7380 GFLOPS with 2 threads. Matrix dimension 4096
***************************************************************************
4 Cores (Block Cyclic)
Block Size 1: 
Test 10: n=4K t=1K block cylic (r=1): Latency = 4.617162 sec and 7.4417 GFLOPS with 4 threads. Matrix dimension 4096 
Block Size 16:
Test 11: n=4K t=1K block cyclic (r=16): Latency = 4.557179 sec and 7.5397 GFLOPS with 4 threads. Matrix dimension 4096 
****************************************************************************
Speed-up(cyclic, 2 cores, 1 block size): 18.06/9.85 = 1.833
Efficiency(cylic, 2 cores, 1 block size): 1.833/2 = 91.6%
Speed-up(cyclic, 2 cores, 16 block size): 18.06/9.19 = 1.965
Efficiency(cylic, 2 cores, 16 block size): 1.965/2 = 98.3%
Speed-up(cyclic, 4 cores, 1 block size): 18.06/4.62 = 3.909
Efficiency(cylic, 4 cores, 1 block size): 3.909/4 = 97.7%
Speed-up(cyclic, 4 cores, 16 block size): 18.06/4.557 = 3.963
Efficiency(cylic, 4 cores, 16 block size): 3.963/4 = 99.1%

----------------------------------------------------------------------------
3.  Report the parallel time, speedup, and efficiency with blocking mapping, block cyclic mapping with block size 1 
and block size 16 using  2 cores (2 threads), and 4 cores (4 threads) for parallelizing the code 
in handling an upper triangular matrix (n=4096 and t=1024).

Note: in both blocking and cyclic mapping cases, to find a sequential time estimation, I ran my code with 1 thread and block size of 1.

1 Core (Blocking): (Sequential Time Estimation): 
Test 12: n=4K t=1K upper block mapping: Latency = 12.831815 sec and 1.3392 GFLOPS with 1 threads. Matrix dimension 4096 
****************************************************************************
2 Cores (Blocking:)
Test 12: n=4K t=1K upper block mapping: Latency = 7.263520 sec and 2.3658 GFLOPS with 2 threads. Matrix dimension 4096
***************************************************************************
4 Cores (Blocking)
Test 12: n=4K t=1K upper block mapping: Latency = 3.997325 sec and 4.2989 GFLOPS with 4 threads. Matrix dimension 4096
****************************************************************************

Speed-up (blocking, 2 cores): 12.83/7.26 = 1.767
Efficiency(blocking, 2 cores): 1.767/2 = 88.3%
Speed-up (blocking, 4 cores): 12.83/3.99 = 3.21
Efficiency(blocking, 4 cores): 80.3%

1 Core (Block Cyclic): (Sequential Time Estimation): 
Block size 1: 
Test 13: n=4K t=1K upper block cylic (r=1): Latency = 12.164866 sec and 1.4126 GFLOPS with 1 threads. Matrix dimension 4096 
****************************************************************************
2 Cores (Block Cyclic)
Block Size 1: Test 13: n=4K t=1K upper block cylic (r=1): Latency = 7.407912 sec and 2.3197 GFLOPS with 2 threads. Matrix dimension 4096
Block Size 16: Test 14: n=4K t=1K upper block cyclic(r=16): Latency = 7.545751 sec and 2.2773 GFLOPS with 2 threads. Matrix dimension 4096
***************************************************************************
4 Cores (Block Cyclic)
Block Size 1:  n=4K t=1K upper block cylic (r=1): Latency = 3.686543 sec and 4.6613 GFLOPS with 4 threads. Matrix dimension 4096
Block Size 16:  n=4K t=1K upper block cyclic(r=16): Latency = 2.709819 sec and 6.3414 GFLOPS with 4 threads. Matrix dimension 4096
****************************************************************************
Speed-up(cyclic, 2 cores, 1 block size): 12.17/7.41 = 1.642
Efficiency(cylic, 2 cores, 1 block size): 1.642/2 = 82.1%
Speed-up(cyclic, 2 cores, 16 block size): 12.17/7.55 = 1.612
Efficiency(cylic, 2 cores, 16 block size): 1.612/2 = 80.5%
Speed-up(cyclic, 4 cores, 1 block size): 12.17/3.69 = 3.29
Efficiency(cylic, 4 cores, 1 block size): 3.29/4 = 82.4%
Speed-up(cyclic, 4 cores, 16 block size): 12.17/2.71 = 4.49
Efficiency(cylic, 4 cores, 16 block size):4.49/4 = 112% Most Likely a Discrepancy or, drop in a constant latency from previous tests in the suite.


4. Write a short explanation on why one mapping method is significantly faster than or similar to another.
For Dense Matrices, Block Mapping performs the best as it consistently maps continuous blocks of matrix A to each thread, this maximizes cache locality, caching large blocks of the matrix that will be quickly accessed in the next iteration. Further both block mapping and block cylic mapping were statically scheduled which bolsters efficiency due to even workloads and computations. 

For Upper Triangular matrices, we see similar trends as in PA1. More threads are spawned that quickly exit out due to being below the matrix diagonal. These threads waste computation resources and simply spawn with large overheads to eventually contribute nothing to the end-result. Here block-mapping with 2-cores performs most efficiently, a comprimise between spawning less wasted threads, as well as preserving cache locality.  
