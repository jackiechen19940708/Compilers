# A Deep Dive into the Interprocedural Optimization Infrastructure
https://www.youtube.com/watch?v=I4Iv-HefknA
## IPO
### intra procedural
1. Analysis Pass
2. Loop Pass
3. Function Pass
### intra procedural
1. CGSCC Pass
input is SCC; applied post order(bottom up)
Allow modify current SCC; add or remove globals
Disallow modify any other SCC; disallow add or remove SCC
2. Module Pass
input is a module, coarsed 

## classes
1. Inliner
AlwaysInliner, Inliner, InlineAdvisor
benefit:
expose local optimization opportunity
save jumps; save register stores and loads(calling convention)
improve instruction locality

2. Propagation between caller and callee
Attributor,IP-SCCP,InferFunctionAttrs,ArgumentPromotion,DeadArgumentElimination
benefit:
other passes would benefit from the propagated information

3. Linkage and Globals
GlobalDCE,GlobalOpt,GlobalSplit,ConstantMerge
benefit:
exploit the fact all uses of internal values are known
remove ununsed internal global
cooperate with LTO

4. Others
MergeFunction,OpenMPOpt,HotColdSplitting,Devirtualization


### AlwaysInliner
CGSCC pass
AlwaysInliner.h AlwaysInliner.cpp
Implement a custom inliner handling only functions that are marked as "always inline"
old: AlwaysInlinerLegacyPass
new: AlwaysInlinerPass

### AnnotationMetadataPass
Module pass
Annotation2Metadata.h Annotation2Metadata.cpp
convert @llvm.global.annotations to !annotation metadata
old:Annotation2MetadataLegacy
new:Annotation2MetadataPass

### ArgumentPromotion
CGSCC pass
ArgumentPromotion.h ArgumentPromotion.cpp
This pass promotes by reference arguments to be by value arguments. Means looking for internal functions that have pointer arguments。
If it can prove， through the use of alias analysis, an argument is only loaded, then it can pass the value into the function instead of the address of the value.
This can cause recursive simplification of code and lead to the elimination of allocas(c++ template code like STL)

This pass also handles aggregate arguments that are passed into a function, scalarizing them if the elements of the aggregate are only loaded.

Can also handled arguments which only store to if LLVM support multiple return values from functions.

old:
new:

### Attributor & AttributorAttributes
Module Pass
CGSCC pass
Attributor.cpp Attributor.h AttributorAttributes.cpp
An inter procedural "attribute" deduction framework. It can deduce & propagate attributes.
old:
new:

### BarrierNoopPass
ModulePass
A nounce pass, can manipulate implicitly nesting pass manager. For example can be used to cause a CGSCC pass manager to be closed prior to running a new collection of function passes.
BarrierNoopPass.cpp

old:

### BlockExtractor
ModulePass
BlockExtractor.h BlockExtractor.cpp
extract specified basic block from the module into their own function.

old:
new:

### CalledValuePropagatoin
ModulePass
CalledValuePropagation.h CalledValuePropagation.cpp
A transformation that attaches !callees metadata to indirect call site. Can indicate the set of functions the call site could possibly target at run-time
old:
new:
### ConstantMerge
ModulePass
ConstantMerge.cpp ConstantMerge.h
merge duplicate global constants together into a single constant that is shared.This is useful because some passes(TraceValues) insert a lot of string constants into the program, regardless of whether or not an existing string is available
old：
new:
### CrossDSOCFI
ModulePass
https://clang.llvm.org/docs/ControlFlowIntegrity.html
CrossDSOCFI.h CrossDSOCFI.cpp
export all llvm.bitset's found in the module in the form of \_\_cfi_check function, which can be used to verify cross-DSO call targets
old:
new:

### DeadArgumentElimination
ModulePass
delete dead arguments from internal functions. often useful as a cleanup pass to run after aggressive interprocedural passes,which add possibly-dead arguments or return values
DeadArgumentElimination.h DeadArgumentElimination.cpp
old:
new:

### ElimAvailExtern
ModulePass
eliminate available external global definitions from the program, turning them into declaration
ElimAvailExtern.cpp ElimAvailExtern.h
old:
new:

### ExtractGV
ModulePass
extract global value
ExtractGV.cpp
old:

### ForceFunctionAttrs
ModulePass
add attribute from commandline to function for debuging
ForceFunctionAttrs.cpp ForceFunctionAttrs.h
old:
new:

### FunctionAttrs
ModulePass CGSCCPass
walk the call graph deduce or propagate function attribute
FunctionAttrs.h FunctionAttrs.cpp
old:
new:

### FunctionImport
ModulePass
import function based on summary
FunctionImport.cpp FunctionImport.h
old:
new:

### FunctionSpecialization
not a pass, can be called from other pass
specialise function with constant parameter, with a function specializatoin attribute for arguments, we could have a direct way to steer function specialization, avoiding the cost-model, and thus control compile-times/ code-size

have some limitation:
(1)can not handle recursive function
(2)can not handle integer range
(3)only 1 argument is specialised
(4)cost model not perfect
(5)not caching result


