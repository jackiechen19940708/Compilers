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
