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
