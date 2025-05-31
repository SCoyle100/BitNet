# BitNet Preset Kernels Overview

## Purpose

The preset kernels directory contains pre-optimized computational kernels for specific BitNet model configurations. These kernels are designed to accelerate BitNet (1.58-bit neural network) inference by providing architecture-specific optimized code for different hardware platforms.

## Directory Structure

The preset kernels are organized by model type:
- `Llama3-8B-1.58-100B-tokens/` - Kernels for Llama3 8B model with 1.58-bit quantization
- `bitnet_b1_58-3B/` - Kernels for BitNet B1.58 3B parameter model
- `bitnet_b1_58-large/` - Kernels for larger BitNet B1.58 models

## File Types

Each model directory contains two types of files:

### Header Files (.h)

#### `bitnet-lut-kernels-tl1.h`
- **Target**: ARM NEON (TL1 = Target Level 1)
- **Purpose**: Contains optimized kernels for ARM-based processors
- **Key Functions**:
  - `per_tensor_quant()`: Per-tensor quantization using ARM NEON SIMD instructions
  - `aligned_malloc()/aligned_free()`: Memory allocation utilities for 64-byte alignment
  - Transpose operations for efficient matrix operations
- **Platform**: Uses ARM NEON intrinsics and falls back to AVX2 for x86

#### `bitnet-lut-kernels-tl2.h`
- **Target**: x86 AVX2 (TL2 = Target Level 2)
- **Purpose**: Contains highly optimized kernels for x86_64 processors with AVX2 support
- **Key Features**:
  - Advanced SIMD operations using AVX2 instructions
  - Complex transpose functions (`Transpose_8_8`)
  - Vector merge operations for efficient data reorganization
  - Look-up table (LUT) based computations for BitNet operations
- **Platform**: Specifically targets x86_64 with AVX2 instruction set

### Configuration Files (.ini)

#### `kernel_config_tl1.ini` and `kernel_config_tl2.ini`
- **Purpose**: Define kernel parameters for different layer sizes
- **Parameters**:
  - `m`, `k`: Matrix dimensions
  - `bm`, `bk`: Block sizes for tiled operations
  - `bmm`: Memory management block size
- **Usage**: These configurations correspond to different layers in the neural network with varying dimensions

## Technical Overview

### BitNet 1.58-bit Quantization
These kernels implement efficient computation for BitNet's 1.58-bit quantization scheme, where weights are quantized to {-1, 0, +1}. This extreme quantization requires specialized computational kernels to maintain performance.

### Look-Up Table (LUT) Approach
The kernels use look-up tables to accelerate computation by pre-computing results for the limited set of possible weight values, reducing the computational complexity during inference.

### SIMD Optimization
- **ARM NEON**: Optimized for mobile and embedded ARM processors
- **AVX2**: High-performance optimization for modern x86_64 processors
- Both implementations provide vectorized operations for parallel processing of multiple data elements

### Memory Management
All kernels include optimized memory allocation functions that ensure proper alignment (64-byte) for SIMD operations, which is crucial for performance on modern processors.

## Usage Context

These preset kernels are automatically selected based on:
1. The target model architecture and size
2. The target hardware platform (ARM vs x86_64)
3. Available instruction set extensions (NEON vs AVX2)

The kernel selection enables BitNet models to achieve efficient inference across different hardware platforms while maintaining the benefits of extreme quantization.