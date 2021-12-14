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
CGSCC
AlwaysInliner.h AlwaysInliner.cpp
Implement a custom inliner handling only functions that are marked as "always inline"
old: AlwaysInlinerLegacyPass
new: AlwaysInlinerPass

### AnnotationMetadataPass
Module
Annotation2Metadata.h Annotation2Metadata.cpp
convert @llvm.global.annotations to !annotation metadata
old:Annotation2MetadataLegacy
new:Annotation2MetadataPass

### ArgumentPromotion
CGSCC
ArgumentPromotion.h ArgumentPromotion.cpp
This pass promotes by reference arguments to be by value arguments. Means looking for internal functions that have pointer arguments。
If it can prove， through the use of alias analysis, an argument is only loaded, then it can pass the value into the function instead of the address of the value.
This can cause recursive simplification of code and lead to the elimination of allocas(c++ template code like STL)

This pass also handles aggregate arguments that are passed into a function, scalarizing them if the elements of the aggregate are only loaded.

Can also handled arguments which only store to if LLVM support multiple return values from functions.

old:
new:

### Attributor
ModulePass
Attributor.cpp Attributor.h
An inter procedural "attribute" deduction framework. It can deduce & propagate attributes.
old:
new:
