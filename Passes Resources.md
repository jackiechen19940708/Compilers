|  Pass   | 说明  | 资源 |
|  ----  | ----  |----  |
| AggressiveInstCombine  | LTO pass for TruncInstCombine; 用低位宽的表达式替换高位宽的表达式 |lib/Transforms/AggressiveInstCombine/* |
| CFGuard  | add Microsoft's Control Flow Guard checks on Windows targets |lib/Transforms/CFGuard/* [CFG](https://docs.microsoft.com/en-us/windows/win32/secbp/control-flow-guard)| 
|  CoroEarly  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroSplit  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroElide  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  CoroCleanup  | ----  | lib/Transforms/Coroutines/* [LLVMCoroutine](https://llvm.org/docs/Coroutines.html#coroutine-transformation) |
|  InstCombine  | 用更快的指令替代慢的指令,非常复杂，将近2w行代码  |lib/Transforms/InstCombine/* [blog1](https://icode.best/i/13010341321431) [LLVM Essentials](https://www.oreilly.com/library/view/llvm-essentials/9781785280801/ch04s05.html)  |
|  AddressSanitizer  | ----  |lib/Transforms/Instrumentation/AddressSantizer.cpp [Google Address Sanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizerAlgorithm) [llvm Talk](https://llvm.org/devmtg/2011-11/Serebryany_FindingRacesMemoryErrors.pdf) |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
|  ----  | ----  |----  |
