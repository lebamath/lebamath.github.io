---
layout: default
title: "天线阵列的射频波入射角公式 AoA: Angle of Arrival"
back_url: /chinese_version.html
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