# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a C++ high-frequency trading (HFT) project focused on low-latency data structures and memory management. The project implements optimized object pools and skip lists for high-performance trading systems.

# common

用中文

## Build and Development Commands

### CMake Build (Recommended)

```bash
# Configure and build using CMake
mkdir -p cmake-build-debug
cd cmake-build-debug
cmake ..
make

# Run main application
./hft

# Run specific tests
./test_object_pool
./test_skip_list

# Run examples
./usage_example
./skip_list_example

# Run performance benchmarks
./pool_comparison
```

### Quick Build and Test

```bash
# Use the convenience script for rapid development
./build_and_run.sh
```

### Direct Compilation (for individual files)

```bash
# Object pool examples
c++ -std=c++20 -I. -O2 -o usage_example usage_example.cpp
c++ -std=c++20 -I. -O2 -o test_object_pool test_object_pool.cpp -pthread

# Skip list examples  
c++ -std=c++20 -I. -O2 -o test_skip_list test_skip_list.cpp
c++ -std=c++20 -I. -O2 -o skip_list_example skip_list_example.cpp
```

## Architecture and Code Structure

### Core Data Structures

**Object Pool System** (已实现):
- `object_pool.h`: Thread-safe object pool with RAII smart pointers
- `fast_object_pool.h`: High-performance variants including:
  - `FastObjectPool`: Lock-free single-threaded version
  - `ThreadSafeObjectPool`: Atomic operations for concurrent access
  - `PooledObject`: RAII wrapper for automatic resource management

**Skip List Implementation** (已实现):
- `skip_list.h`: Template-based probabilistic data structure
- O(log n) average time complexity for search/insert/delete
- Configurable maximum levels and probability parameters

### Performance Optimizations

1. **Memory Management**:
   - Pre-allocated object pools to eliminate malloc/free overhead
   - RAII patterns for automatic resource cleanup
   - Lock-free algorithms where possible

2. **Concurrency Models**:
   - Single-threaded fast paths for latency-critical operations
   - Thread-safe variants using atomic operations
   - Lock-based thread-safe pool for general use

3. **Compiler Optimizations**:
   - C++20 standard with `constexpr` and concepts
   - Release builds with `-O3` optimization
   - Template specialization for type-specific optimizations

### Testing Strategy

The project includes comprehensive test programs:
- `test_object_pool.cpp`: Multi-threaded object pool stress tests
- `pool_comparison.cpp`: Performance benchmarks comparing different pool implementations
- `usage_example.cpp`: Realistic HFT trading order examples
- `test_skip_list.cpp`: Skip list correctness and performance tests

### Dependency Management

Uses vcpkg for C++ package management:
- `fmt`: High-performance formatting library
- `spdlog`: Fast logging library
- Manifest mode configuration in `vcpkg.json`

## Architecture Patterns

### Object Pool Design

Three distinct implementations for different use cases:

1. **ObjectPool**: General-purpose thread-safe pool using `std::mutex`
2. **FastObjectPool**: Single-threaded optimized for minimum latency
3. **ThreadSafeObjectPool**: Lock-free concurrent pool using atomic operations

All pools support:
- Automatic object reset/cleanup
- RAII-based resource management  
- Dynamic expansion when capacity exceeded
- Configurable initial pool sizes

### Skip List Design

Probabilistic data structure optimized for:
- Fast search operations in sorted data
- Concurrent read access patterns
- Memory-efficient node allocation
- Template-based key-value storage

## Development Focus

1. **Ultra-Low Latency**: Nanosecond-level optimizations for trading systems
2. **Memory Efficiency**: Zero-allocation fast paths using object pools
3. **Scalability**: Lock-free algorithms for high-throughput scenarios
4. **Correctness**: Comprehensive testing with realistic trading workloads

## 重要开发指导原则

当使用Claude Code处理此项目时，请遵循以下原则：

### 代码风格和质量
- 严格遵循C++20标准，使用现代C++特性
- 优先使用模板和constexpr以实现编译时优化
- 保持代码的可读性和可维护性，同时不牺牲性能
- 使用RAII模式进行资源管理
- 避免动态内存分配在关键路径上

### 性能优化原则
- **时延优先**: 在设计任何新功能时，首先考虑对时延的影响
- **内存效率**: 使用对象池和预分配内存避免malloc/free
- **Cache友好**: 考虑数据结构的内存布局对CPU缓存的影响
- **分支预测**: 减少条件分支，使用likely/unlikely标记
- **编译器优化**: 充分利用`-O3`优化和模板特化

### 并发和线程安全
- 提供单线程和多线程两种实现版本
- 使用原子操作和内存屏障而非锁机制
- 明确标识线程安全性和使用场景
- 考虑NUMA架构对性能的影响

### 测试和验证
- 每个新功能必须包含相应的性能测试
- 使用压力测试验证多线程安全性
- 提供使用示例和最佳实践代码
- 测量和记录性能基准数据

### 构建和部署
- 保持CMake配置的简洁和可移植性
- 使用vcpkg管理依赖项
- 提供便捷的构建脚本用于快速开发
- 支持Debug和Release两种构建模式

## 禁止事项

- **不要**在关键路径添加日志输出
- **不要**使用异常处理机制（影响性能）
- **不要**使用标准库容器在高频操作中
- **不要**添加不必要的虚函数调用
- **不要**使用动态内存分配在时延敏感代码中

## 项目特定最佳实践

1. **对象池使用**:
   - 优先使用FastObjectPool用于单线程场景
   - 多线程场景使用ThreadSafeObjectPool
   - 合理设置池的初始大小以避免动态扩展

2. **跳表应用**:
   - 用于维护有序的价格-订单映射
   - 配置合适的最大层数平衡内存和性能
   - 考虑使用内存池分配节点

3. **编译优化**:
   - Release模式使用-O3 -DNDEBUG
   - 考虑使用PGO（Profile-Guided Optimization）
   - 针对目标CPU架构启用特定优化

# important-instruction-reminders
Do what has been asked; nothing more, nothing less.
NEVER create files unless they're absolutely necessary for achieving your goal.
ALWAYS prefer editing an existing file to creating a new one.
NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.