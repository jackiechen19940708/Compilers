|  Pass   | 说明  | 资源 |
|  ----  | ----  |----  |
| AggressiveInstCombine  | LTO pass for TruncInstCombine; 用低位宽的表达式替换高位宽的表达式 |lib/Transforms/AggressiveInstCombine/* |
| CFGuard  | add Microsoft's Control Flow Guard checks on Windows targets |lib/Transforms/CFGuard/* [CFG](https://docs.microsoft.com/en-us/windows/win32/secbp/control-flow-guard)| 
|  CoroEarly  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroSplit  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroElide  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroCleanup  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  InstCombine  | 用更快的指令替代慢的指令,非常复杂，将近2w行代码  |lib/Transforms/InstCombine/* [blog1](https://icode.best/i/13010341321431) [LLVM Essentials](https://www.oreilly.com/library/view/llvm-essentials/9781785280801/ch04s05.html)  |
|  AddressSanitizer  | 编译期检查内存访问是否  |lib/Transforms/Instrumentation/AddressSantizer.cpp [Google Address Sanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizerAlgorithm) [AddressSanitizer算法及源码解析]([https://llvm.org/devmtg/2011-11/Serebryany_FindingRacesMemoryErrors.pdf](https://cloud.tencent.com/developer/article/1518165)) |
|  BoundsChecking  | 检查memory访问是否越界  |lib/Transforms/Instrumentation/BoundsChecking.cpp  |
|  CFGMST  | CFG最小生成树,用在GCOVProfiling和PGOInstrrumentation中  |lib/Transforms/Instrumentation/CFGMST.h  |
|  CGProfile  | call graph profile  |lib/Transforms/Instrumentation/CGProfile.cpp  |
|  ControlHeightReduction  | 属于PGO。根据profiling 的hot path合并条件Block，减少分支跳转  | lib/Transforms/Instrumentation/ControlHeightReduction.cpp  |
|  DataFlowSanitizer  | Dynamic Dataflow Analysis  |[clang dfsan design](https://clang.llvm.org/docs/DataFlowSanitizerDesign.html)  |
|  GCOVProfiling  | 产生类似gcc的profiling数据  | lib/Transforms/Instrumentation/GCOVProfiling.cpp  |
|  HWAddressSanitizer  | ----  |lib/Transforms/Instrumentation/HWAddressSanitizer.cpp [clang HWASAN](https://clang.llvm.org/docs/HardwareAssistedAddressSanitizerDesign.html)  |
|  IndirectCallPromotion  | 在profiling data的辅助下，convert indirect call(函数指针) to conditional direct call （if else到固定函数），用于减少分支预测开销  |lib/Transforms/Instrumentation/IndirectCallPromotion.cpp  [高通 IndirectCallPromotion介绍](https://llvm.org/devmtg/2015-10/slides/Baev-IndirectCallPromotion.pdf)|
|  InstrOrderFile  | 记录函数执行顺序  |lib/Transforms/Instrumentation/InstrOrderFile  |
|  InstrProfiling  | ----  |lib/Transforms/Instrumentation/InstrProfiling.cpp  |
|  MemorySanitizer  | 运行时，检测读取未初始化内存  | lib/Transforms/Instrumentation/MemorySanitizer.cpp [论文](http://goo.gl/QKbem)  [clang MemSanitizier](https://clang.llvm.org/docs/MemorySanitizer.html) [llvm conf msan](https://llvm.org/devmtg/2012-11/Serebryany_TSan-MSan.pdf) |
|  MemProfiler  | 记录内存访问信息  |lib/Transforms/Instrumentation/MemProfiler.cpp  |
|  PGOInstrumentation  | PGO 运行时打标记，分析程序热点  |lib/Transforms/Instrumentation/PGOInstrumentation.cpp [llvm conf PGOInstrumentation](https://llvm.org/devmtg/2020-09/slides/PGO_Instrumentation.pdf)  |
|  PGOMemOPSizeOpt  | 根据profiling data优化memory intrinsic ，把变量size 特化 |lib/Transforms/Instrumentation/PGOMemOPSizeOpt.cpp  |
|  PoisonChecking  | 检测LLVM IR中不合理的部分  |lib/Transforms/Instrumentation/PoisonChecking.cpp  |
|  SanitizerCoverage  | 检测覆盖率  |lib/Transforms/Instrumentation/SanitizerCoverage.cpp [clang scov](https://clang.llvm.org/docs/SanitizerCoverage.html)  |
|  ThreadSanitizer  | 为function call和内存访问添加一些标记,检测data race  |lib/Transforms/Instrumentation/ThreadSanitizer.cpp [clang tsan](https://clang.llvm.org/docs/ThreadSanitizer.html) |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
