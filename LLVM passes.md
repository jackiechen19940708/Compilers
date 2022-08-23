# OptimizationLevel
include/llvm/Passes/OptimizationLevel.h

lib/Passes/OptimizationLevel.cpp

llvm提供了6种级别的优化,分别针对二进制size和编译speed大小进行优化
O0
  /// Disable as many optimizations as possible. This doesn't completely
  /// disable the optimizer in all cases, for example always_inline functions
  /// can be required to be inlined for correctness.
O1
/// Optimize quickly without destroying debuggability.
  ///
  /// This level is tuned to produce a result from the optimizer as quickly
  /// as possible and to avoid destroying debuggability. This tends to result
  /// in a very good development mode where the compiled code will be
  /// immediately executed as part of testing. As a consequence, where
  /// possible, we would like to produce efficient-to-execute code, but not
  /// if it significantly slows down compilation or would prevent even basic
  /// debugging of the resulting binary.
  ///
  /// As an example, complex loop transformations such as versioning,
  /// vectorization, or fusion don't make sense here due to the degree to
  /// which the executed code differs from the source code, and the compile
  /// time cost.
O2
/// Optimize for fast execution as much as possible without triggering
  /// significant incremental compile time or code size growth.
  ///
  /// The key idea is that optimizations at this level should "pay for
  /// themselves". So if an optimization increases compile time by 5% or
  /// increases code size by 5% for a particular benchmark, that benchmark
  /// should also be one which sees a 5% runtime improvement. If the compile
  /// time or code size penalties happen on average across a diverse range of
  /// LLVM users' benchmarks, then the improvements should as well.
  ///
  /// And no matter what, the compile time needs to not grow superlinearly
  /// with the size of input to LLVM so that users can control the runtime of
  /// the optimizer in this mode.
  ///
  /// This is expected to be a good default optimization level for the vast
  /// majority of users.
O3
/// Optimize for fast execution as much as possible.
  ///
  /// This mode is significantly more aggressive in trading off compile time
  /// and code size to get execution time improvements. The core idea is that
  /// this mode should include any optimization that helps execution time on
  /// balance across a diverse collection of benchmarks, even if it increases
  /// code size or compile time for some benchmarks without corresponding
  /// improvements to execution time.
  ///
  /// Despite being willing to trade more compile time off to get improved
  /// execution time, this mode still tries to avoid superlinear growth in
  /// order to make even significantly slower compile times at least scale
  /// reasonably. This does not preclude very substantial constant factor
  /// costs though.
Os
 /// Similar to \c O2 but tries to optimize for small code size instead of
  /// fast execution without triggering significant incremental execution
  /// time slowdowns.
  ///
  /// The logic here is exactly the same as \c O2, but with code size and
  /// execution time metrics swapped.
  ///
  /// A consequence of the different core goal is that this should in general
  /// produce substantially smaller executables that still run in
  /// a reasonable amount of time.
Oz
 /// A very specialized mode that will optimize for code size at any and all
  /// costs.
  ///
  /// This is useful primarily when there are absolute size limitations and
  /// any effort taken to reduce the size is worth it regardless of the
  /// execution time impact. You should expect this level to produce rather
  /// slow, but very small, code.
# Pass Tutorial
https://www.youtube.com/watch?v=ar7cJl2aBuU
## opt
LLVM's modular optimizer
### 1.optimize
input: llvm source file(input.ll)
output: 
(1) LLVM source files(output.ll)
(2) analysis result(stdout)
### 2.load plugins with custom passes
### 3.generate CFG files


## PassManager
### Legacy PassManager
### New Pass Manager
CRTP

Mixin Pattern
Concept-model

Pass operates on some units of IR
Transformation modify the pass, Analysis generate high-level information
Analysis results are produced lazily, and are cached; need to deal with non-trival cache invalidation problem
Transformation pass manager record what's preserved

clang -emit-llvm -S -O0 .c -o .ll

## GVN
global value numbering

### value numbering
separate values （data in variable） from variables（code） a = b+ c ; e = b + c;

### usage
reduncancy code elimination

copy propagation: “等式替换”  y = x; z = 3 + y; z = 3 + x

constant folding

### type
local value numbering:within basic block
global value numbering:across basic block
when having more than one parent basic block, need SSA

### 方法
Hash based tech
1. simplify expression
2. hash operations
3. find redundant values & replace OR add into value table


### Code


## SSA
每个变量只被赋值一次

使用phi节点聚合

### 如何插入phi节点
Dominace frontier：在CFG汇聚的basicblock，其parent basic block 变量被多次赋值

### phi节点的消除
codegen阶段消除，通过插入拷贝
