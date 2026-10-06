---
layout: default
title: "天线阵列的射频波入射角公式 AoA: Angle of Arrival"
back_url: /index.html?lang=zh
---

## (一) GPU 编程初步介绍

[录制的视频在 B 站](https://www.bilibili.com/cheese/play/ep2481485)

本文从比较底层/接近硬件的角度，来讨论一下 GPU 的内核编程，主要是讨论多线程多核心是如何在硬件层面进行调度的，文章以 AMD MI300X 为例来讲解，其它型号的 GPU 也类似， NVidia 的 GPU 也是类似的，只是有些名词稍微有些不同。

我们先来看一个最简单的代码：

```
#include <hip/hip_runtime.h>
	#include <stdio.h>
	
	// GPU 核函数：逐元素相加 C[i] = A[i] + B[i]
	__global__ void vec_add(const float* A, const float* B, float* C, int N)
	{
		int i = blockIdx.x * blockDim.x + threadIdx.x;
		if (i < N) C[i] = A[i] + B[i];
	}
	
	int main()
	{
		const int N = 1024;
		size_t sz = N * sizeof(float);
		
		// 主机端分配并初始化数据
		float *h_A = new float[N];
		float *h_B = new float[N];
		float *h_C = new float[N];
		for (int i = 0; i < N; i++) { h_A[i] = (float)i; h_B[i] = (float)(N - i); }
		
		// GPU 端分配显存并拷贝数据
		float *d_A, *d_B, *d_C;
		hipMalloc(&d_A, sz);
		hipMalloc(&d_B, sz);
		hipMalloc(&d_C, sz);
		hipMemcpy(d_A, h_A, sz, hipMemcpyHostToDevice);
		hipMemcpy(d_B, h_B, sz, hipMemcpyHostToDevice);
		
		// 每个 block 256 个线程，grid 覆盖全部 N 个元素
		dim3 block(256);
		dim3 grid((N + 255) / 256);
		vec_add<<<grid, block>>>(d_A, d_B, d_C, N);
		
		// 结果拷回主机并验证
		hipMemcpy(h_C, d_C, sz, hipMemcpyDeviceToHost);
		printf("C[0]=%g  C[512]=%g  C[1023]=%g\n", h_C[0], h_C[512], h_C[1023]);
		
		// 释放资源
		hipFree(d_A); hipFree(d_B); hipFree(d_C);
		delete[] h_A; delete[] h_B; delete[] h_C;
		return 0;
	}
```

这段代码很简单，就是把两个 1024 维的数组，即每个数组有 1024 个元素，逐元素相加，放到第三个数组中。

其中，函数  vec\_add 是要在 GPU 中执行的，但是我们可以看到，这个函数内部是没有一个 for 循环来把所有元素相加，这个函数只对一个位置的元素对进行相加，把结果放到第三个数组的对应位置上，即 $$C[i] = A[i] + B[i]$$，也就是说，这个函数只做一次加法（除了前面计算位置 i 的以外 ).

简单地说， 总体上来讲，GPU 是把这样一个简单的函数，同时发给数百个甚至数千个实体同时运行，每个实体负责运行一个位置的计算。

那么， GPU 是如何来分配和调度呢？

我们先来看看图1的架构:

![图1：AMD GPU MI300X 架构](/figure//GPU//AMD-MI300X-CU-structure.png)

*图1：AMD GPU MI300X 架构*

我们先从最底层网上看，理解了底层，然后才比较容易理解上面的层次是如何来安排和调度底层的。

### SIMD 层面
最底层执行具体代码的，即执行一个线程(执行一次vec\_add这个函数), 是 VALU 这个模块 (注意：实际上不是百分之百 由 VALU 执行，一些分支跳转指令由 SALU 执行，这个后面再讲)。可以看到，框图中有 16 个 VALU，这 16 个 VALU 会同时执行相同的指令，但是指令的操作寄存器不同，例如指令:
```
v_add_f16  v0, v1, v2
```

16 个线程同时执行，但是 v0,v1,v2 是在上面的 64 个VGPR 中 16 个v0, v1, v2.

例如:

第一个周期，16 个线程用 VGPR 中 0--15 的 v0, v1, v2;

第二个周期，16 个线程用 VGPR 中 16--31 的 v0, v1, v2;

第三个周期，16 个线程用 VGPR 中 32--47 的 v0, v1, v2;

第四个周期，16 个线程用 VGPR 中 48--63 的 v0, v1, v2;

这 64 个 thread 要都执行一遍，才会切换到另外的 64 个 thread。这 64 个 thread 是步调完全一致的，在 AMD 中称之为 wavefront，或者简称为 wave，在 CUDA 系统中称之为一个 warp.

可以看到，一共有 4 个 SIMD 并排，每个  SIMD 上有 64 个 thread 去执行，在不同的 SIMD 上的 thread，其步调不是要求一致的，只有在一个 SIMD 内部的 64 个 thread 才是步调一致。

如果某个 SIMD 上的 64 个 thread，由于在等数据等原因需要等待，那么再上一层的调度会把当前这 64 个 thread 挂起，立即调度就绪的另外 64 个 thread，即调度另外一个 wavefront.

在 AMD 中， thread 有时也称之为 Lane.

### CU 层面

从架构图中可以看到，一个 CU ( Compute Unit) 管理 4 个 SIMD，所以，一个 CU 同时可以运行 64 * 4 个 thread.

当一个 SIMD 中的 wavefront( 64 个 threads) 卡住了，即在等待其它条件就绪，那么 CU 负责无缝零延时切换到另外一个就绪的 wavefront. 每个 SIMD 中可以支持最多 8 个 wavefront 实现零延时切换，其中每一个被称之为一个槽位 Slot，所以，一个 SIMD 有 8 个槽位，CU 向这 8 个槽位发射wavefront 时，还需要考虑资源的情况。

要实现这种零延时切换，wavefront 用到的寄存器需要已经保存在 VGPR 中。根据每个线程用多少寄存器，可以判断有最多可以使用几个槽位.

**运行时的“动态划拨”与占用率（Occupancy）**
当内核启动，CU 调度器准备向 SIMD 的 8 个槽位发射 Wavefront 时，它会根据编译器报告的单线程 VGPR 需求量，从寄存器池中动态切出一块连续的空间分配给这个 Wavefront。

这会导致三种不同的占用（Occupancy）场景：

**场景 A：寄存器受限（Register Bound）—— 槽位填不满**

假设编译器报告：该内核极其复杂，每个线程需要 128 个 VGPR。
此时，一个 Wavefront中的 thread(Lane) 会占用 128个物理寄存器。
512/128 = 最多只能容纳 4 个 Wavefront。
结果： SIMD 上的 8 个控制槽位，只有 4 个被填满，剩下 4 个槽位只能空着。此时虽然有空槽位，但因为没寄存器了，调度器无法再塞入新的 Wavefront。

**场景 B：完美平衡**

假设内核优化得很好，每个线程正好使用 64 个 VGPR。
寄存器池总容量 512 /64 = 8。
结果： 寄存器池刚好够分给 8 个 Wavefront。此时 8 个槽位全部用满，物理寄存器也全部用满，达到 100% 的理论 Occupancy。

**场景 C：槽位受限（Wave/Slot Bound）—— 寄存器闲置**

假设内核极简，每个线程只需 32 个 VGPR。
按照寄存器容量算：512 / 32  = 可以放得下 16 个 Wavefront。
结果： 尽管物理寄存器还有一大半是空闲的，但因为 SIMD 硬件设计上只有 8 个 Wavefront 槽位（控制逻辑的硬限制），所以最多也只能跑 8 个 Wavefront。剩下的寄存器空间就被白白浪费了。


### 名词对照

| **CUDA** | **HIP** | **OpenCL™** |
|---|---|---|
| grid | grid | NDRange |
| block | block | work group |
| thread | thread(lane) | work item |
| warp | wavefront | sub-group (?) |


### rocminfo

下面这段信息，是在一个 MI300X 上使用 rocminfo 命令得到的。

其中这三个是与我们前面的讨论有关：
```
SIMDs per CU:            4
	Wavefront Size:          64(0x40)                           
	Max Waves Per CU:        32(0x20)                           
	Max Work-item Per CU:    2048(0x800)
```

SIMDs per CU: 一个 CU 中有  4 个 SIMD.

Wavefront Size: 就是同时在一个 SIMD 中执行的thread(lane, work-item) 数量.

Max Waves Per CU: 一个 CU 中最多可以驻留的 wavefront(warp) 的数量，一个 CU 有 4 个 SIMD，所以，据此可以推断一个 SIMD 可以驻留 32/4=8 个 wavefront.

Max Work-item Per CU: 一个 CU 中驻留的最大的线程数量为 8*4*64 = 2048.


```
ROCk module version 6.16.13 is loaded
	=====================    
	HSA System Attributes    
	=====================    
	Runtime Version:         1.18
	Runtime Ext Version:     1.15
	System Timestamp Freq.:  1000.000000MHz
	Sig. Max Wait Duration:  18446744073709551615 (0xFFFFFFFFFFFFFFFF) (timestamp count)
	Machine Model:           LARGE                              
	System Endianness:       LITTLE                             
	Mwaitx:                  DISABLED
	XNACK enabled:           NO
	DMAbuf Support:          YES
	VMM Support:             YES
	
	==========               
	HSA Agents               
	==========               
	*******                  
	Agent 1                  
	*******                  
	Name:                    INTEL(R) XEON(R) PLATINUM 8568Y+   
	Uuid:                    CPU-XX                             
	Marketing Name:          INTEL(R) XEON(R) PLATINUM 8568Y+   
	Vendor Name:             CPU                                
	Feature:                 None specified                     
	Profile:                 FULL_PROFILE                       
	Float Round Mode:        NEAR                               
	Max Queue Number:        0(0x0)                             
	Queue Min Size:          0(0x0)                             
	Queue Max Size:          0(0x0)                             
	Queue Type:              MULTI                              
	Node:                    0                                  
	Device Type:             CPU                                
	Cache Info:              
	L1:                      32768(0x8000) KB                   
	Chip ID:                 0(0x0)                             
	ASIC Revision:           0(0x0)                             
	Cacheline Size:          64(0x40)                           
	Max Clock Freq. (MHz):   0                                  
	BDFID:                   0                                  
	Internal Node ID:        0                                  
	Compute Unit:            20                                 
	SIMDs per CU:            0                                  
	Shader Engines:          0                                  
	Shader Arrs. per Eng.:   0                                  
	WatchPts on Addr. Ranges:1                                  
	Memory Properties:       
	Features:                None
	Pool Info:               
	Pool 1                   
	Segment:                 GLOBAL; FLAGS: FINE GRAINED        
	Size:                    247409304(0xebf2a98) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:4KB                                
	Alloc Alignment:         4KB                                
	Accessible by all:       TRUE                               
	Pool 2                   
	Segment:                 GLOBAL; FLAGS: EXTENDED FINE GRAINED
	Size:                    247409304(0xebf2a98) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:4KB                                
	Alloc Alignment:         4KB                                
	Accessible by all:       TRUE                               
	Pool 3                   
	Segment:                 GLOBAL; FLAGS: KERNARG, FINE GRAINED
	Size:                    247409304(0xebf2a98) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:4KB                                
	Alloc Alignment:         4KB                                
	Accessible by all:       TRUE                               
	Pool 4                   
	Segment:                 GLOBAL; FLAGS: COARSE GRAINED      
	Size:                    247409304(0xebf2a98) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:4KB                                
	Alloc Alignment:         4KB                                
	Accessible by all:       TRUE                               
	ISA Info:                
	*******                  
	Agent 2                  
	*******                  
	Name:                    gfx942                             
	Uuid:                    GPU-45d0c32dfea52974               
	Marketing Name:          AMD Instinct MI300X VF             
	Vendor Name:             AMD                                
	Feature:                 KERNEL_DISPATCH                    
	Profile:                 BASE_PROFILE                       
	Float Round Mode:        NEAR                               
	Max Queue Number:        128(0x80)                          
	Queue Min Size:          64(0x40)                           
	Queue Max Size:          131072(0x20000)                    
	Queue Type:              MULTI                              
	Node:                    1                                  
	Device Type:             GPU                                
	Cache Info:              
	L1:                      32(0x20) KB                        
	L2:                      4096(0x1000) KB                    
	L3:                      262144(0x40000) KB                 
	Chip ID:                 29877(0x74b5)                      
	ASIC Revision:           1(0x1)                             
	Cacheline Size:          128(0x80)                          
	Max Clock Freq. (MHz):   2100                               
	BDFID:                   33536                              
	Internal Node ID:        1                                  
	Compute Unit:            304                                
	SIMDs per CU:            4                                  
	Shader Engines:          32                                 
	Shader Arrs. per Eng.:   1                                  
	WatchPts on Addr. Ranges:4                                  
	Coherent Host Access:    FALSE                              
	Memory Properties:       
	Features:                KERNEL_DISPATCH 
	Fast F16 Operation:      TRUE                               
	Wavefront Size:          64(0x40)                           
	Workgroup Max Size:      1024(0x400)                        
	Workgroup Max Size per Dimension:
	x                        1024(0x400)                        
	y                        1024(0x400)                        
	z                        1024(0x400)                        
	Max Waves Per CU:        32(0x20)                           
	Max Work-item Per CU:    2048(0x800)                        
	Grid Max Size:           4294967295(0xffffffff)             
	Grid Max Size per Dimension:
	x                        2147483647(0x7fffffff)             
	y                        65535(0xffff)                      
	z                        65535(0xffff)                      
	Max fbarriers/Workgrp:   32                                 
	Packet Processor uCode:: 189                                
	SDMA engine uCode::      24                                 
	IOMMU Support::          None                               
	Pool Info:               
	Pool 1                   
	Segment:                 GLOBAL; FLAGS: COARSE GRAINED      
	Size:                    200998912(0xbfb0000) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:2048KB                             
	Alloc Alignment:         4KB                                
	Accessible by all:       FALSE                              
	Pool 2                   
	Segment:                 GLOBAL; FLAGS: EXTENDED FINE GRAINED
	Size:                    200998912(0xbfb0000) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:2048KB                             
	Alloc Alignment:         4KB                                
	Accessible by all:       FALSE                              
	Pool 3                   
	Segment:                 GLOBAL; FLAGS: FINE GRAINED        
	Size:                    200998912(0xbfb0000) KB            
	Allocatable:             TRUE                               
	Alloc Granule:           4KB                                
	Alloc Recommended Granule:2048KB                             
	Alloc Alignment:         4KB                                
	Accessible by all:       FALSE                              
	Pool 4                   
	Segment:                 GROUP                              
	Size:                    64(0x40) KB                        
	Allocatable:             FALSE                              
	Alloc Granule:           0KB                                
	Alloc Recommended Granule:0KB                                
	Alloc Alignment:         0KB                                
	Accessible by all:       FALSE                              
	ISA Info:                
	ISA 1                    
	Name:                    amdgcn-amd-amdhsa--gfx942:sramecc+:xnack-
	Machine Models:          HSA_MACHINE_MODEL_LARGE            
	Profiles:                HSA_PROFILE_BASE                   
	Default Rounding Mode:   NEAR                               
	Default Rounding Mode:   NEAR                               
	Fast f16:                TRUE                               
	Workgroup Max Size:      1024(0x400)                        
	Workgroup Max Size per Dimension:
	x                        1024(0x400)                        
	y                        1024(0x400)                        
	z                        1024(0x400)                        
	Grid Max Size:           4294967295(0xffffffff)             
	Grid Max Size per Dimension:
	x                        2147483647(0x7fffffff)             
	y                        65535(0xffff)                      
	z                        65535(0xffff)                      
	FBarrier Max Size:       32                                 
	ISA 2                    
	Name:                    amdgcn-amd-amdhsa--gfx9-4-generic:sramecc+:xnack-
	Machine Models:          HSA_MACHINE_MODEL_LARGE            
	Profiles:                HSA_PROFILE_BASE                   
	Default Rounding Mode:   NEAR                               
	Default Rounding Mode:   NEAR                               
	Fast f16:                TRUE                               
	Workgroup Max Size:      1024(0x400)                        
	Workgroup Max Size per Dimension:
	x                        1024(0x400)                        
	y                        1024(0x400)                        
	z                        1024(0x400)                        
	Grid Max Size:           4294967295(0xffffffff)             
	Grid Max Size per Dimension:
	x                        2147483647(0x7fffffff)             
	y                        65535(0xffff)                      
	z                        65535(0xffff)                      
	FBarrier Max Size:       32                                 
	*** Done ***
```


## 四 软件层面来配置 Block 

我们已经知道（前面几个文章的讨论），给 Compute Unit 分派 thread，是按照 Block 来进行的，一个 block 可以含有很多个 thread。

GPU 为了方便软件设计和开发，对 block 中的 thread 又进行了一定的分组，这个分组只是软件层面上的，对于 GPU 硬件来讲，block 中的 thread 没有分组或者三维/二维信息，都是平等的。

block 的分组，是为了适应 "输出的数据结构"， thread 输出的结果是要保存到一个一维数组，二维数组还是一个三维数组，虽然软件可以给定一个一维的 index 索引，软件再去考虑这是在输出的三维数组中各个维度的下标，或者用来寻找输入数据的位置等，都不是很直观，因此，根据输出数据的数据结构， block 可以适当分组，最多可以分三维。

另外，还需要告知 GPU 一共有多少个 block，在代码中一般称之为 grid(网格)，网格的维度也是与 block 的维度相同。下面举例详细说明一下。

下面我们分三种情况来讨论。

第一种情况：输出数据是一维的，例如前面讨论的两个一维数组对应元素相加。

```
dim3 block(256);
	dim3 grid((N + 255) / 256);
	vec_add<<<grid, block>>>(d_A, d_B, d_C, N)
```

这里的 block 变量，就只是告诉 GPU， block 中含有 256 个 thread，是一维的。grid 变量是告知有多少个  block，变量 N 是总的需要计算的输出元素的数量，即 thread 的总数。

在单个 thread 内，确定是对哪个元素进行操作时，用下面的逻辑来确定 i:
```
int i = blockIdx.x * blockDim.x + threadIdx.x;
```

其中 

blockIdx.x 表示这是第几个 block，其取值范围是从 0  到 (N + 255) / 256 - 1.

blockDim.x 表示 block 中 thread 的数量，是 256.

threadIdx.x 表示在指定的 block 中是第几个 thread，取值范围是从 0 到 256-1=255.

这三个变量，是 GPU 中的调度模块在调度时自动计算好并把这三个变量赋值，以便在 thread 内的软件代码可以使用。

下面这个代码是对输入的一维数组（偶数个元素），对每两个输入找一个最大值，然后输出出去，类似于神经网络中的 pooling.


```
#include <hip/hip_runtime.h>
	#include <stdio.h>
	
	// GPU kernel: output[i] = max(input[2*i], input[2*i+1])
	__global__ void pairwise_max(const float* input, float* output, int N_out)
	{
		int i = blockIdx.x * blockDim.x + threadIdx.x;
		if (i < N_out) output[i] = fmaxf(input[2 * i], input[2 * i + 1]);
	}
	
	int main()
	{
		const int N = 1024;        // input size (must be even)
		const int N_out = N / 2;   // output size
		size_t sz_in  = N * sizeof(float);
		size_t sz_out = N_out * sizeof(float);
		
		// Host: allocate and initialize
		float *h_in  = new float[N];
		float *h_out = new float[N_out];
		for (int i = 0; i < N; i++) h_in[i] = (float)i;
		
		// Device: allocate and copy
		float *d_in, *d_out;
		hipMalloc(&d_in, sz_in);
		hipMalloc(&d_out, sz_out);
		hipMemcpy(d_in, h_in, sz_in, hipMemcpyHostToDevice);
		
		// Launch: one thread per output element
		dim3 block(256);
		dim3 grid((N_out + 255) / 256);
		pairwise_max<<<grid, block>>>(d_in, d_out, N_out);
		
		// Copy back and verify
		hipMemcpy(h_out, d_out, sz_out, hipMemcpyDeviceToHost);
		printf("out[0]=%g  out[256]=%g  out[511]=%g\n", h_out[0], h_out[256], h_out[511]);
		
		// Cleanup
		hipFree(d_in); hipFree(d_out);
		delete[] h_in; delete[] h_out;
		return 0;
	}
```

其中： 
```
dim3 block(256);
	dim3 grid((N_out + 255) / 256);
	pairwise_max<<<grid, block>>>(d_in, d_out, N_out);
```

N\_out 是输出元素的数量，因为每个输出元素用一个 thread 来计算，所以，也就是 thread 的数量为 N\_out.


第二种情况，输出是一个二维数组，在 2x2 的范围内找最大值输出，类似于 2x2 max pooling.

```
#include <hip/hip_runtime.h>
	#include <stdio.h>
	
	// GPU kernel: 2x2 max pooling on a 2D array
	// output[r][c] = max(input[2r][2c], input[2r][2c+1], input[2r+1][2c], input[2r+1][2c+1])
	__global__ void max_pool_2x2(const float* input, float* output,
	int rows_out, int cols_out, int cols_in)
	{
		int c = blockIdx.x * blockDim.x + threadIdx.x;
		int r = blockIdx.y * blockDim.y + threadIdx.y;
		if (r < rows_out && c < cols_out) {
			int r2 = 2 * r, c2 = 2 * c;
			float v0 = input[r2       * cols_in + c2];
			float v1 = input[r2       * cols_in + c2 + 1];
			float v2 = input[(r2 + 1) * cols_in + c2];
			float v3 = input[(r2 + 1) * cols_in + c2 + 1];
			output[r * cols_out + c] = fmaxf(fmaxf(v0, v1), fmaxf(v2, v3));
		}
	}
	
	int main()
	{
		const int ROWS = 32, COLS = 64;         // input dimensions (both even)
		const int ROWS_OUT = ROWS / 2, COLS_OUT = COLS / 2;
		size_t sz_in  = ROWS * COLS * sizeof(float);
		size_t sz_out = ROWS_OUT * COLS_OUT * sizeof(float);
		
		// Host: allocate and initialize
		float *h_in  = new float[ROWS * COLS];
		float *h_out = new float[ROWS_OUT * COLS_OUT];
		for (int i = 0; i < ROWS * COLS; i++) h_in[i] = (float)i;
		
		// Device: allocate and copy
		float *d_in, *d_out;
		hipMalloc(&d_in, sz_in);
		hipMalloc(&d_out, sz_out);
		hipMemcpy(d_in, h_in, sz_in, hipMemcpyHostToDevice);
		
		// Launch: one thread per output element, 2D grid
		dim3 block(16, 16);
		dim3 grid((COLS_OUT + 15) / 16, (ROWS_OUT + 15) / 16);
		max_pool_2x2<<<grid, block>>>(d_in, d_out, ROWS_OUT, COLS_OUT, COLS);
		
		// Copy back and verify
		hipMemcpy(h_out, d_out, sz_out, hipMemcpyDeviceToHost);
		printf("out[0][0]=%g  out[0][1]=%g  out[15][31]=%g\n",
		h_out[0], h_out[1], h_out[ROWS_OUT * COLS_OUT - 1]);
		
		// Cleanup
		hipFree(d_in); hipFree(d_out);
		delete[] h_in; delete[] h_out;
		return 0;
	}
```

首先，dim3 block(16, 16);  一个 block 是含有 16x16= 256 的 thread，这些 thread 被划分成了 16 列，16 行。

包含有多少个 block，也是定义成了一个二维的结构，dim3 grid((COLS\_OUT + 15) / 16, (ROWS\_OUT + 15) / 16);

内核代码中，计算在第几行，第几列：
```
int c = blockIdx.x * blockDim.x + threadIdx.x;
	int r = blockIdx.y * blockDim.y + threadIdx.y;
``` 

其中 

blockIdx.x 表示 列方向看是第几个 block

blockDim.x 表示列方向上，一个 block 含有的 thread 数量

threadIdx.x 表示列方向上，当前这个 block 中的第几个 thread.

同理，

blockIdx.y 表示 行方向看是第几个 block

blockDim.y 表示行方向上，一个 block 含有的 thread 数量

threadIdx.y 表示行方向上，当前这个 block 中的第几个 thread.


输入和输出的内存，还是被看成是一个连续的内存，即看成了一个一维数组，因此，需要计算在一维数组中的 index

output[r * cols\_out + c]

输入数据，也需要按照一维数组来计算索引： 

input[r2 * cols\_in + c2]


第三种情况，输出是一个三维数组。

```
#include <hip/hip_runtime.h>
	#include <stdio.h>
	
	const int D = 16, H = 32, W = 64;                  // input dimensions (all even)
	const int D_OUT = D / 2, H_OUT = H / 2, W_OUT = W / 2;
	
	// GPU kernel: 2x2x2 max pooling on a 3D array
	__global__ void max_pool_2x2x2(const float input[][H][W],
	float output[][H_OUT][W_OUT],
	int d_out, int h_out, int w_out)
	{
		int w = blockIdx.x * blockDim.x + threadIdx.x;
		int h = blockIdx.y * blockDim.y + threadIdx.y;
		int d = blockIdx.z * blockDim.z + threadIdx.z;
		if (d < d_out && h < h_out && w < w_out) {
			int d2 = 2 * d, h2 = 2 * h, w2 = 2 * w;
			float m = input[d2][h2][w2];
			m = fmaxf(m, input[d2  ][h2  ][w2+1]);
			m = fmaxf(m, input[d2  ][h2+1][w2  ]);
			m = fmaxf(m, input[d2  ][h2+1][w2+1]);
			m = fmaxf(m, input[d2+1][h2  ][w2  ]);
			m = fmaxf(m, input[d2+1][h2  ][w2+1]);
			m = fmaxf(m, input[d2+1][h2+1][w2  ]);
			m = fmaxf(m, input[d2+1][h2+1][w2+1]);
			output[d][h][w] = m;
		}
	}
	
	int main()
	{
		size_t sz_in  = D * H * W * sizeof(float);
		size_t sz_out = D_OUT * H_OUT * W_OUT * sizeof(float);
		
		// Host: allocate and initialize
		float *h_in  = new float[D * H * W];
		float *h_out = new float[D_OUT * H_OUT * W_OUT];
		for (int i = 0; i < D * H * W; i++) h_in[i] = (float)i;
		
		// Device: allocate and copy
		float *d_in, *d_out;
		hipMalloc(&d_in, sz_in);
		hipMalloc(&d_out, sz_out);
		hipMemcpy(d_in, h_in, sz_in, hipMemcpyHostToDevice);
		
		// Launch: 3D grid, one thread per output element
		dim3 block(16, 8, 2);
		dim3 grid((W_OUT + 15) / 16, (H_OUT + 7) / 8, (D_OUT + 1) / 2);
		max_pool_2x2x2<<<grid, block>>>(
		(const float (*)[H][W]) d_in,
		(float (*)[H_OUT][W_OUT]) d_out,
		D_OUT, H_OUT, W_OUT);
		
		// Copy back and verify
		hipMemcpy(h_out, d_out, sz_out, hipMemcpyDeviceToHost);
		printf("out[0][0][0]=%g  out[7][15][31]=%g\n",
		h_out[0], h_out[D_OUT * H_OUT * W_OUT - 1]);
		
		// Cleanup
		hipFree(d_in); hipFree(d_out);
		delete[] h_in; delete[] h_out;
		return 0;
	}
```

这个代码也是类似的，稍微不同的是，这个例子中假定三维的长度都是确定的，因此，在内核函数的定义上，直接传入的是const float input[][H][W]，这样在内核函数中，输入和输出的变量都是有清晰的结构的，方便编程，前提是这些维度在编译前就是确定的。

在 AMD 提供的云平台 https://amd.digitalocean.com/  上可以租用 MI300x 的服务器，一个小时不到 2 美元，新开户送  100 美元的信用额度可用。

## 简单介绍一下 Nvidia GB202

下面简单介绍一下 Nvida GB202 的内部架构以及相应的编程。我们还是从底层开始讨论。

### sub-partition 和 SM 



如图 2 所示，这是一个 SM(Streaming Multiprocessor)，一个软件上定义的 block，只能分派给一个 SM，不能跨 SM，所以这个 SM 就类似于 AMD 的 CU(Compute Unit).

图 2 中的 SM 内部又含有 4 个相同的模块，绿色的部分，这个一般称之为 Sub-Partition,或者 partition, 或者 processing block,或者 quadrant，好像没有一个统一的称谓。这个就是类似于 AMD 中的 SIMD 模块，一个 sub-partition 同时推进多个线程，这些线程被称之为一个 warp,类似于 AMD 中的 wavefront.  在 BG202 中，一个 warp 是 32 个 thread，一个 sub-partition 中有 32 个计算单元，因此，这里与 AMD MI300x 不同的地方在于，BG202 的 一个sub-partition 用一个 cycle 就执行了 32 个 thread.



图 2 中的其它模块，例如 Tex ( Texture 纹理处理模块 ), RT core ( Race Tracking 光线追踪 ) 模块等，是给游戏或者 AI 物理建模使用的模块，不算是通用计算模块，这里就暂时忽略不讲了。

### GPC 和  TPC


GPC：Graphics Processing Cluster

TPC： Texture Processing Cluster

这两个名称都是继承与显卡时代的名称。

SM 之上，就是 TPC，一个 TPC 只含有一个 SM。再往上就是 GPC,如图 3所示。从软件层面上来讲，这些都是比较透明的，硬件会自动处理。

GPC 负责管理多个 TPC ，知道各个 TPC 的繁忙与空闲状态， GPC 负责给各个 TPC 分派任务。

这些 GPC 和 TPC 的划分，是为了一些存储共享，例如 cache 共享，或者是在芯片设计时方便裁剪，例如 GPC 模块在芯片设计阶段，可以根据芯片的高低端定位，放置不同数量的 GPC，如图 4 所示时整个芯片的一个局部。如图 5 是 GB202 的整体。

![图1：AMD GPU MI300X 架构](/figure/GPU/AMD-MI300X-CU-structure.png)

*图1：AMD GPU MI300X 架构*

![图2：nvidia BG202 sub partition 示意图](/figure/GPU/nvidia_GB202_c.png)

*图2：nvidia BG202 sub partition 示意图*

![图3：nvidia BG202 GPC 示意图](/figure/GPU/nvidia_GB202_b.png)

*图3：nvidia BG202 GPC 示意图*

![图4：nvidia BG202 局部 示意图](/figure/GPU/nvidia_GB202_a.png)

*图4：nvidia BG202 局部 示意图*

![图5：nvidia BG202 整体 示意图](/figure/GPU/nvidia_GB202_0.png)

*图5：nvidia BG202 整体 示意图*


下面这个是基于 Nvidia GB202 的简单代码，基本上与之前看到的 AMD 的代码结构是一样的，唯一区别是函数前缀是 cuda.

```
#include <cuda_runtime.h>
	#include <stdio.h>
	
	__global__ void vec_add(const float* A, const float* B,
	float* C, int N)
	{
		int i = blockIdx.x * blockDim.x + threadIdx.x;
		if (i < N) C[i] = A[i] + B[i];
	}
	
	int main()
	{
		const int N = 1024;
		size_t sz = N * sizeof(float);
		
		// Host allocation and initialization
		float *h_A = new float[N];
		float *h_B = new float[N];
		float *h_C = new float[N];
		for (int i = 0; i < N; i++) { h_A[i] = (float)i; h_B[i] = (float)(N - i); }
		
		// Device allocation and copy
		float *d_A, *d_B, *d_C;
		cudaMalloc(&d_A, sz);
		cudaMalloc(&d_B, sz);
		cudaMalloc(&d_C, sz);
		cudaMemcpy(d_A, h_A, sz, cudaMemcpyHostToDevice);
		cudaMemcpy(d_B, h_B, sz, cudaMemcpyHostToDevice);
		
		// Launch kernel: 256 threads per block
		dim3 block(256);
		dim3 grid((N + 255) / 256);
		vec_add<<<grid, block>>>(d_A, d_B, d_C, N);
		
		// Copy result back and verify
		cudaMemcpy(h_C, d_C, sz, cudaMemcpyDeviceToHost);
		printf("C[0]=%g  C[512]=%g  C[1023]=%g\n", h_C[0], h_C[512], h_C[1023]);
		// Expected: C[i] = i + (1024-i) = 1024 for all i
		
		cudaFree(d_A); cudaFree(d_B); cudaFree(d_C);
		delete[] h_A; delete[] h_B; delete[] h_C;
		return 0;
	}
```

## GPU 矩阵加速-以 MI300X 为例

类似于标量计算模块 vALU，在每个 SIMD 内部还有矩阵运算的加速器。从硬件角度来看，加速器的最小单元是一个外积计算，这个计算是做两个向量相乘， A x B，其中 A 是一个 4个元素的列向量， B 是一个4个元素的行向量，所以， A*B 得到的是一个 4x4
的矩阵， 如图 6所示,我们把这个计算称为 4x1x4外积。


![图6：矩阵加速的最小单元](/figure/GPU/MFMA-prim.png)

*图6：矩阵加速的最小单元*


在前面几个文章讲到的 thread，在这里就是对应一个 4x1x4 的向量外积计算，即一个 thread 负责一个 4x1x4的外积计算。那么如何使用这个外积的原语呢？因为矩阵都是累乘加运算，所以，需要对原始的矩阵乘法做一点数学变换，这里我们假定矩阵式 A 是
16x4，B 是 4x16的。

首先， 我们把矩阵表示成列向量的形式：

$$
\mathbf{ A } = \begin{bmatrix}
		\mathbf a_1 & \mathbf a_2 & \mathbf a_3 & \mathbf a_4 
	\end{bmatrix}
$$

其中 $$\mathbf a_i$$ 是 16x1 的列向量。

类似地，对 矩阵 B 的转置，写成列向量形式：

$$
\mathbf{ B }' = \begin{bmatrix}
		\mathbf b_1 & \mathbf b_2 & \mathbf b_3 & \mathbf b_4 
	\end{bmatrix}
$$

则 B 矩阵本身可以写成：

$$
\mathbf{ B } = \begin{bmatrix}
		\mathbf b_1' \\
		\mathbf b_2' \\
		\mathbf b_3' \\
		\mathbf b_4' 
	\end{bmatrix}
$$

其中 $$\mathbf B'$$ 表示 B 的转置矩阵。$$\mathbf b_i$$ 表示 $$\mathbf B'$$  的列向量，也就是 矩阵$$\mathbf B$$本身的行向量。

则：

$$
\mathbf A \times \mathbf B = 
	\begin{bmatrix}
		\mathbf a_1 & \mathbf a_2 & \mathbf a_3 & \mathbf a_4 
	\end{bmatrix}
	\times
	\begin{bmatrix}
		\mathbf b_1' \\
		\mathbf b_2' \\
		\mathbf b_3' \\
		\mathbf b_4' 
	\end{bmatrix}
	= a_1 \times \mathbf b_1' + a_2 \times \mathbf b_2' + a_3 \times \mathbf b_3' + a_4 \times \mathbf b_4'
\tag{1}
$$

上面公式中的任何一项例如$$\mathbf a_3 \times \mathbf b_3'$$，就是一个列向量16x1乘以一个行向量1x16，这就是两个向量的外积,得到一个 16x16 的矩阵,四个这样的矩阵累加在一起就是最终的结果。

对上面公式中的任何一项例如$$\mathbf a_3 \times \mathbf b_3'$$ 做进一步的分块化，就可以得到可以使用最小硬件原语的表达式：

例如 

$$
\mathbf a_3 = \begin{bmatrix}
		\mathbf a_3^{(0)}  \\
		\mathbf a_3^{(1)} \\
		\mathbf a_3^{(2)}  \\
		\mathbf a_3^{(3)}
	\end{bmatrix}
$$

其中 $$\mathbf a_3^{(1)}$$ 表示是4个元素的列向量。同理:

$$
\mathbf b_3 = \begin{bmatrix}
		\mathbf b_3^{(0)}  \\
		\mathbf b_3^{(1)} \\
		\mathbf b_3^{(2)}  \\
		\mathbf b_3^{(3)}
	\end{bmatrix}
$$

则：

$$
\mathbf b_3' = \begin{bmatrix}
		\mathbf b_3^{(0)'}  & \mathbf b_3^{(1)'}  & \mathbf b_3^{(2)'}  & \mathbf b_3^{(3)'}
	\end{bmatrix}
$$

最终我们得到：

$$
\mathbf a_3 \times \mathbf b_3' = 
	\begin{bmatrix}
		a_3^{(0)} \times b_3^{(0)'} & a_3^{(0)} \times b_3^{(1)'} & a_3^{(0)} \times b_3^{(2)'} & a_3^{(0)} \times b_3^{(3)'}  \\
		a_3^{(1)} \times b_3^{(0)'} & a_3^{(1)} \times b_3^{(1)'} & a_3^{(1)} \times b_3^{(2)'} & a_3^{(1)} \times b_3^{(3)'}  \\     
		a_3^{(2)} \times b_3^{(0)'} & a_3^{(2)} \times b_3^{(1)'} & a_3^{(2)} \times b_3^{(2)'} & a_3^{(2)} \times b_3^{(3)'}  \\     
		a_3^{(3)} \times b_3^{(0)'} & a_3^{(3)} \times b_3^{(1)'} & a_3^{(3)} \times b_3^{(2)'} & a_3^{(3)} \times b_3^{(3)'}  
	\end{bmatrix}
$$

其中每个 $$a_3^{(i)} \times b_3^{(j)'}$$  是一个 4x1 的列向量与 1x4 的行向量相乘，得到的是一个 4x4 的矩阵。因此，每个 $$a_3^{(i)} \times b_3^{(j)'}$$ 可以用一个矩阵的硬件原语来执行。

则公式 (1) 就变成：

$$
\begin{aligned}
		\mathbf A \times \mathbf B = 
		&\begin{bmatrix}
			a_0^{(0)} \times b_0^{(0)'} & a_0^{(0)} \times b_0^{(1)'} & a_0^{(0)} \times b_0^{(2)'} & a_0^{(0)} \times b_0^{(3)'}  \\
			a_0^{(1)} \times b_0^{(0)'} & a_0^{(1)} \times b_0^{(1)'} & a_0^{(1)} \times b_0^{(2)'} & a_0^{(1)} \times b_0^{(3)'}  \\     
			a_0^{(2)} \times b_0^{(0)'} & a_0^{(2)} \times b_0^{(1)'} & a_0^{(2)} \times b_0^{(2)'} & a_0^{(2)} \times b_0^{(3)'}  \\     
			a_0^{(3)} \times b_0^{(0)'} & a_0^{(3)} \times b_0^{(1)'} & a_0^{(3)} \times b_0^{(2)'} & a_0^{(3)} \times b_0^{(3)'}  
		\end{bmatrix} + \\ \\
		& \begin{bmatrix}
			a_1^{(0)} \times b_1^{(0)'} & a_1^{(0)} \times b_1^{(1)'} & a_1^{(0)} \times b_1^{(2)'} & a_1^{(0)} \times b_1^{(3)'}  \\
			a_1^{(1)} \times b_1^{(0)'} & a_1^{(1)} \times b_1^{(1)'} & a_1^{(1)} \times b_1^{(2)'} & a_1^{(1)} \times b_1^{(3)'}  \\     
			a_1^{(2)} \times b_1^{(0)'} & a_1^{(2)} \times b_1^{(1)'} & a_1^{(2)} \times b_1^{(2)'} & a_1^{(2)} \times b_1^{(3)'}  \\     
			a_1^{(3)} \times b_1^{(0)'} & a_1^{(3)} \times b_1^{(1)'} & a_1^{(3)} \times b_1^{(2)'} & a_1^{(3)} \times b_1^{(3)'}  
		\end{bmatrix} + \\ \\
		& \begin{bmatrix}
			a_2^{(0)} \times b_2^{(0)'} & a_2^{(0)} \times b_2^{(1)'} & a_2^{(0)} \times b_2^{(2)'} & a_2^{(0)} \times b_2^{(3)'}  \\
			a_2^{(1)} \times b_2^{(0)'} & a_2^{(1)} \times b_2^{(1)'} & a_2^{(1)} \times b_2^{(2)'} & a_2^{(1)} \times b_2^{(3)'}  \\     
			a_2^{(2)} \times b_2^{(0)'} & a_2^{(2)} \times b_2^{(1)'} & a_2^{(2)} \times b_2^{(2)'} & a_2^{(2)} \times b_2^{(3)'}  \\     
			a_2^{(3)} \times b_2^{(0)'} & a_2^{(3)} \times b_2^{(1)'} & a_2^{(3)} \times b_2^{(2)'} & a_2^{(3)} \times b_2^{(3)'}  
		\end{bmatrix} + \\ \\
		&\begin{bmatrix}
			a_3^{(0)} \times b_3^{(0)'} & a_3^{(0)} \times b_3^{(1)'} & a_3^{(0)} \times b_3^{(2)'} & a_3^{(0)} \times b_3^{(3)'}  \\
			a_3^{(1)} \times b_3^{(0)'} & a_3^{(1)} \times b_3^{(1)'} & a_3^{(1)} \times b_3^{(2)'} & a_3^{(1)} \times b_3^{(3)'}  \\     
			a_3^{(2)} \times b_3^{(0)'} & a_3^{(2)} \times b_3^{(1)'} & a_3^{(2)} \times b_3^{(2)'} & a_3^{(2)} \times b_3^{(3)'}  \\     
			a_3^{(3)} \times b_3^{(0)'} & a_3^{(3)} \times b_3^{(1)'} & a_3^{(3)} \times b_3^{(2)'} & a_3^{(3)} \times b_3^{(3)'}  
		\end{bmatrix}
	\end{aligned}
\tag{2}
$$

可以看到上面一共有 4x4x4=64 个向量外积，每个都用一次硬件矩阵乘法原语，每个就对应一个 thread，所以，一共是 64 个 thread. 因为上面有 4 次加法，因此，这 64 个 thread 又被分成 4 组来执行，每组 16 个 thread.

我们知道 AMD MI300X 中一个 wavefront 是 64， 这 64 thread 刚好就构成一个 wavefront，一个 wavefront 被分配到一个 SIMD 中。

AMD MI300X 中一个 SIMD 含有 4 个矩阵原语，因此需要 16 个周期来完成上面的 64 个原语计算。

### 数据的输入

因为每个原语需要 4 + 4 = 8 个输入数据，并且这些数据是被重复使用的，但是，我们看到每 16 个原语计算中，输入数据是不同的， 每 16 个线程用到的数据刚好是 A 中的一列和 B 中的一行，总共是 32 个数据，因此，在硬件上，设置了一个约定：每 16
个thread 中的每个 thread，负责输入矩阵A 的一个元素和一个矩阵 B 的元素，具体对应规则如图7 所示。 thread 编号是 0 到 63，图 7 中的数字对应的 thread
编号，图中的每个小方框对应矩阵中的一个元素。

![图7：64个 thread 对应的输入数据](/figure/GPU/MFMA-thread_maping_to_AB.png)

*图7：64个 thread 对应的输入数据*

我们来看一下代码：


```
[language=C++]
	#define M 16
	#define N 16
	#define K 4
	__global__ void sgemm_16x16x4(
	const float *A, const float *B, float *D)
	{
		// 1. 将 0~63 的线性 ID 还原为二维坐标
		int tid = threadIdx.x; // 现在 block 是 (64, 1, 1)，所以只用 x
		int x = tid % 16;      
		int y = tid / 16;      
		
		using float4 = 
		__attribute__( (__vector_size__(K * sizeof(float)) )) float;
		float4 dmn = {0};
		
		// 2. 使用坐标计算索引
		int mk = y + K * x;
		int kn = x + N * y;
		
		float amk = A[mk];
		float bkn = B[kn];
		
		dmn = __builtin_amdgcn_mfma_f32_16x16x4f32(amk, bkn, dmn, 0, 0, 0);
		
		// 3. 使用坐标写回
		for (int i = 0; i < 4; ++i) {
			const int idx = x + i * N + y * 4 * N;
			D[idx] = dmn[i];
		}
	}
	
	
	int main() {
		
		.....
		
		dim3 block(64,1,1);
		dim3 grid(1,1,1);  
		
		// Execution configuration call
		sgemm_16x16x4<<<grid, block>>>(d_A, d_B, d_D);
		
		.....
		
		return 0;
	}
```

可以看到，代码中，每个线程输入进来的 thread ID ( tid） 用  tid/16 和 tid\%16，分别计算出对应的行和列坐标，由于在 A 和 B 中行和列的坐标刚好互换，因此，计算输入数据的索引时，用的是：

矩阵A的索引：int mk = y + K * x;

矩阵B的索引：int kn = x + N * y;


### 数据的输出

在前一节的代码中，已经看到了输出的操作，这里详细解释一下。AMD MI300X 的设计思路是一个 thread 负责一个4x1向量的输出，结果矩阵是 16x16 = 256 个元素，按照前面的讨论，我们是有 64 个 thread，因此，每个 thread 负责输出 4
个元素，如图8所示：

![图8：64个 thread 对应的输出](/figure/GPU/MFMA-thread_maping_to_D.png)

*图8：64个 thread 对应的输出*

从图中可以看到，其影射方式类似于矩阵 B 的，只是每个 thread 需要输出竖向的 4 个元素：
```
[language=C++]
	#define M 16
	#define N 16
	#define K 4
	__global__ void sgemm_16x16x4(
	const float *A, const float *B, float *D)
	{
		// 1. 将 0~63 的线性 ID 还原为二维坐标
		int tid = threadIdx.x; // 现在 block 是 (64, 1, 1)，所以只用 x
		int x = tid % 16;      
		int y = tid / 16;      
		
		......
		
		dmn = __builtin_amdgcn_mfma_f32_16x16x4f32(amk, bkn, dmn, 0, 0, 0);
		
		// 3. 使用坐标写回
		for (int i = 0; i < 4; ++i) {
			const int idx = x + i * N + y * 4 * N;
			D[idx] = dmn[i];
		}
	}
```

y * 4 * N  这个表示在哪个颜色区域，即哪 16 个线程中。

x 表示 在同一个颜色区域内，第几个线程。

i * N 表示在同一个颜色区域内的第几行。

则 idx = x + i * N + y * 4 * N 表示在内存中的 index.

### 与 C 矩阵累加

如果要计算的是如 D =  A * B + C， 那么

```
[language=C++]
	__global__ void sgemm_16x16x4(const float *A, const float *B, const float *C, float *D){
		// 1. Flatten the 3D thread block into a 1D linear ID (0~63)
		int tid = threadIdx.x + threadIdx.y * blockDim.x + 
		threadIdx.z * blockDim.x * blockDim.y;
		
		// Map linear ID to MFMA-specific 2D coordinates
		int x = tid % 16;      // 0~15
		int y = tid / 16;      // 0~3
		
		using float4 = __attribute__( (__vector_size__(4 * sizeof(float)) )) float;
		float4 dmn;
		
		// 2. Determine the index for C and load it into the accumulator (dmn)
		// Since C shares the same layout as D, the index 
		// calculation is exactly the same.
		for (int i = 0; i < 4; ++i) {
			const int c_idx = x + i * 16 + y * 4 * 16; // N = 16
			dmn[i] = C[c_idx];
		}
		
		// 3. Calculate indices for A and B
		int mk = y + 4 * x;   // K = 4
		int kn = x + 16 * y;  // N = 16
		
		float amk = A[mk];
		float bkn = B[kn];
		
		// 4. MFMA instruction now computes: dmn = amk * bkn + dmn (which holds C)
		dmn = __builtin_amdgcn_mfma_f32_16x16x4f32(amk, bkn, dmn, 0, 0, 0);
		
		// 5. Write back the final result to D
		for (int i = 0; i < 4; ++i) {
			const int d_idx = x + i * 16 + y * 4 * 16;
			D[d_idx] = dmn[i];
		}
	}
```

C 中元素与thread 的对应关系，和 D 一样，先读入到 dmn 中，作为一个参数传入到函数中, 矩阵加速器会自动做累加。

### 内部累加
内部运算时，每 16 个thread计算完的结果，都会与接下来的 16 个 thread 计算的结果累加，即公式 (2)  中的加法。

公式 (2) 可以写成：

$$
\mathbf A \times \mathbf B = 
	\sum_{k=0}^3 \left \{
	\begin{bmatrix}
		a_k^{(0)} \times b_k^{(0)'} & a_k^{(0)} \times b_k^{(1)'} & a_k^{(0)} \times b_k^{(2)'} & a_k^{(0)} \times b_k^{(3)'}  \\
		a_k^{(1)} \times b_k^{(0)'} & a_k^{(1)} \times b_k^{(1)'} & a_k^{(1)} \times b_k^{(2)'} & a_k^{(1)} \times b_k^{(3)'}  \\     
		a_k^{(2)} \times b_k^{(0)'} & a_k^{(2)} \times b_k^{(1)'} & a_k^{(2)} \times b_k^{(2)'} & a_k^{(2)} \times b_k^{(3)'}  \\     
		a_k^{(3)} \times b_k^{(0)'} & a_k^{(3)} \times b_k^{(1)'} & a_k^{(3)} \times b_k^{(2)'} & a_k^{(3)} \times b_k^{(3)'}  
	\end{bmatrix} 
	\right \}
$$

每 16 个 thread 计算求和公式中矩阵，并与前面计算的结果累加，这是硬件自动完成的。

### V\_MFMA\_F32\_16x16x1F32       

这个函数（编译器内置的函数原语）处理的是 16x1 的列向量乘以一个 1x16 的行向量，得到 16x16 的矩阵。

从输入的角度看，与前面的例子是一样的，只是因为一个 wavefront 有 64 thread，而 16x1 的列向量乘以一个 1x16 的行向量只需要 16 threads，因此，在一个 wavefront 中，可以做四个这样的“ 16x1 的列向量乘以一个 1x16 的行向量”。

只是累加矩阵 C 以及输出矩阵 D 这里，与 thread 的对应关系需要特别注意一下。


每个线程负责 所有四个16x16 矩阵中的一个 4x1 向量，如图9 所示。

![图9：16x16x1 情况下的 C/D 矩阵 mapping](/figure/GPU/MFMA-thread_maping_to_DC_16x16x1.png)

*图9：16x16x1 情况下的 C/D 矩阵 mapping*

### block的二维定义

如果一个 block 被定义成是二维 thread,  那么：

tid = threadIdx.x + threadIdx.y * threadDim.x

也就是 threadIdx.x 对应 B 矩阵的第几列，  threadIdx.y 对应 B 矩阵的第几行。



```
[language=C++]
	#define M 16
	#define N 16
	#define K 4
	
	
	__global__ void sgemm_16x16x4(const float *A, const float *B, float *D)
	{
		using float4 = __attribute__( (__vector_size__(K * sizeof(float)) )) float;
		float4 dmn = {0};
		
		int mk = threadIdx.y + K * threadIdx.x;
		int kn = threadIdx.x + N * threadIdx.y;
		
		float amk = A[mk];
		float bkn = B[kn];
		dmn = __builtin_amdgcn_mfma_f32_16x16x4f32(amk, bkn, dmn, 0, 0, 0);
		
		for (int i = 0; i < 4; ++i) {
			const int idx = threadIdx.x + i * N + threadIdx.y * 4 * N;
			D[idx] = dmn[i];
		}
	}
	
	
	dim3 grid (1, 1, 1);
	dim3 block(16, 4, 1);
	
	sgemm_16x16x4 <<< grid, block >>> (d_A, d_B, d_D);
```

![图10：Enter Caption](/figure/GPU/MFMA-thread_maping_to_AB_2D_Block.png)

*图10：Enter Caption*

![图10：Enter Caption](/figure/GPU/MFMA-thread_maping_to_D_2D_Block.png)

*图10：Enter Caption*