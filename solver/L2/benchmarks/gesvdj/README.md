Singular Value Decomposition for symmetric matrix (GESVDJ)
==========================================================

GESVDJ example resides in ``L2/benchmarks/gesvdj`` directory. The tutorial provides a step-by-step guide that covers commands for building and running kernel.

Executable Usage
================

* **Work Directory(Step 1)**

The steps for library download and environment setup can be found in :ref:`l2_vitis_solver`. For getting the design,

```
   cd L2/benchmarks/gesvdj
```

* **Build kernel(Step 2)**

Run the following make command to build your XCLBIN and host binary targeting a specific device. Please be noticed that this process will take a long time, maybe couple of hours.

```
   source /opt/xilinx/Vitis/2022.2/settings64.sh
   source /opt/xilinx/xrt/setenv.sh
   export DEVICE=/opt/xilinx/platforms/xilinx_u250_xdma_201830_2/xilinx_u250_xdma_201830_2.xpfm
   export TARGET=hw
   make run 
```

* **Run kernel(Step 3)**

To get the benchmark results, please run the following command.

```
   ./build_dir.hw.xilinx_u250_xdma_201830_2/test_gesvdj.exe -xclbin build_dir.hw.xilinx_u250_xdma_201830_2/kernel_gesvdj.xclbin -runs 1 -M 512 -N 512 -seed 12 
```

GESVDJ Input Arguments:

```
   Usage: test_gesvdj.exe -[-xclbin -o -c -g]
          -xclbin     gesvdj binary;
          -runs       number of runs; 
          -M          size of input Matrix row; 
          -N          size of input Matrix column;
          -seed       seed for generating a random number;
```

Note: Default arguments are set in Makefile. The default configs are: -runs 1 -M 16 -N 16 -seed 12. 

* **Example output(Step 4)** 

  
``` 
   ---------------------GESVDJ Test----------------
   Found Platform
   Platform Name: Xilinx
   INFO: Found Device=xilinx_u250_xdma_201830_2
   INFO: Importing build_dir.hw.xilinx_u250_xdma_201830_2/wcc_kernel.xclbin
   Loading: 'build_dir.hw.xilinx_u250_xdma_201830_2/wcc_kernel.xclbin'
   INFO: kernel has been created
   INFO: kernel start------
   INFO: kernel end------
   INFO: Execution time 53.697ms
   INFO: Write DDR Execution time 0.11773ms
   INFO: Kernel Execution time 53.198ms
   INFO: Read DDR Execution time 0.049562ms
   INFO: Total Execution time 53.3653ms
   ============================================================
```

Profiling 
=========

The GESVDJ is validated on Xilinx Alveo U250 board. 
The accuracy of GESVDJ implementation has been verified with Lapack dgesvd (QR based SVD) and dgesvj (Jacobi SVD) functions. 
The hardware resources and performance for double datatype is listed in :numref:`tabgesvdjDouble`.
To describe the resource utilization, we separate the overall utilization into two parts, P stands for the resource usage in platform, that is those instantiated in static region of the FPGA card, K stands for those used in kernels(dynamic region).  
The Unroll factor means how many CUs are configured to calculate Matrix in parell.


##### Table double Type GESVDJ performance

    +-------------+--------+----------------+--------------+-------------+-------------+-----------+-----------+----------+
    | Matrix Size | Unroll | Frequency(MHz) | Latency(MHz) |     LUT     |     REG     |  BRAM     |  URAM     |  DSP     |
    +-------------+--------+----------------+--------------+-------------+-------------+-----------+-----------+----------+
    |  512x512    |    8   |     216        |              |  146722(P)  |  225353(P)  |   283(P)  |     0(P)  |    7(P)  |
    |             |        |                |              |   64493(K)  |   88013(K)  |   325(K)  |   128(K)  |  168(K)  |
    +-------------+--------+----------------+--------------+-------------+-------------+-----------+-----------+----------+


##### Note
```
    The unroll factor is limited by 2 factors, the matrix size and URAM port. The maximum unroll factor should be less than half of matrix size, and :math:`2 \times {Unroll}^{2}` should also be less than available URAM on board. Besides, unroll factor can only be the factorization of 2.
```

