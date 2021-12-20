# A Deep Dive into the Interprocedural Optimization Infrastructure
https://www.youtube.com/watch?v=I4Iv-HefknA
## IPO
passes consider more than one function
### intra procedural
1. Analysis Pass
2. Loop Pass
3. Function Pass
### inter procedural
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
FunctionSpecialization.cpp
not a pass, can be called from other pass
specialise function with constant parameter, with a function specializatoin attribute for arguments, we could have a direct way to steer function specialization, avoiding the cost-model, and thus control compile-times/ code-size

have some limitation:
(1)can not handle recursive function
(2)can not handle integer range
(3)only 1 argument is specialised
(4)cost model not perfect
(5)not caching result

### GlobalDCE
ModulePass
eliminate unreachable internal globals from the program.
use an aggressive algorithm, searching out globals that are known to be alive. After find all of the globals which are needed, it delete whatever is left over.
GlobalIDCE.cpp GlobalIDCE.h

old:
new:

### GlobalOpt
ModulePass
transform simple global variables that never have their address taken. if true, marks read write globals as constant, delete variables only stored to
GlobalOpt.h GLobalOpt.cpp

old:
new:

### GlobalSplit
ModulePass
use inrange annotations on GEP indices to split globals where beneficial.
can optimize whole-program virtual call optimization and cfi
GlobalSplit.cpp GlobalSplit.h
old:
new:

### HotColdSplitting
ModulePass
HotColdSplitting.cpp HotColdSplitting.h
Help improve memory locality of code. identify cold blocks and move them into separate function.
When the splitting pass finds a cold block, it grows a maximal cold region around that block. the maximal region contains all blocks post-dominated by the sink. In theory, these blocks are as cold as the sink. Once a region is found, it's split out of the original function provided it's profitable to do so.

can improve:
use PM to get domtrees and preserve BFI&BPI
reorder outlined function

old:
new:
### InferFunctionAttrs
ModulePass
InferFunctionAttrs.h InferFunctionAttrs.cpp
infer implicit function attributes from the name and signature of function declaration.

old:
new

### Inliner
CGSCC
Inliner.h Inliner.cpp
implement the mechanic required to implement inline without missing any calls and updating the call graph. the decisions of which calls are profitable to inline are implemented eleswhere.
Base class for inline, provide helper function.
old:
new:
### InlinSimple
CGSCC 
InlineCost.cpp
bottom up inline of functions into callees
a simple inline method
### Internalize
ModulePass
loop over all of the function and variable in the input module.
if function or variable doesn't need to be preserved according to hte client supplied callback, it's marked as internal
if a function is only used from within this module, it can internalize a function with external linkage. 
Internalize.h Internalize.cpp
old:
new:
### IPO
IPO whole interface for initialize
### IROutliner
ModulePass
IROutliner.cpp IROutliner.h
Use IRSimilarityIdentifier to identify the similar region of code. It evaluate each set of IRSimilarityCandidates with an estimate of whether it will provide code size reduction. Each region is extracted using the code extractor. These extracted functions are consolidated into a single function and called the extracted call sites.
example
```
%1 = add i32 %a, %b
%2 = add i32 %b, %a
%3 = add i32 %b, %a
%4 = add i32 %a, %b
```

```
define internal void outlined_ir_function(i32 %0,i32 %1){
    %1 = add i32 %0,%1
    %2 = add i32 %1,%0
    ret void
}
call void outlined_ir_function(i32 %a, i32 %b)
call void outlined_ir_function(i32 %b, i32 %a)
```

old:
new:
### LoopExtractor
ModulePass
LoopExtractor.cpp LoopExtractor.h
extract each top-level loop into its own new function. if is the only loop in a given function, not change. 
most useful for debugging via bugpoint
old
new
### LowerTypeTests
ModulePass 
LowerTypeTests.cpp LowerTypeTests.h
A layout algorithm for globals referenced by bit sets that tries to keep members of small bit sets together. This can siginicantly reduce bit set sizes.
There is an algorithm here
old
new

### MergeFunctions
ModulePass
This pass looks for equivalent functions that are mergable and folds them
MergeFunctions.cpp MergeFunctions.h
old
new

future will consider virtual function
### OpenMPOpt
CGSCCPass
OpenMPOpt.cpp OpenMPOpt.h
deduplicate of runtime calls(omp_get_thread_num)
replace globalized device memory with stack memory or shared memory
parallel region merging
transform generic-mode device kernels to SPMD mode
specialize the state machine for generic-mode device kernels

old
new
### PartialInlining
ModulePass
PartialInline.h PartialInline.cpp
partial inline, typically by inlining an if statement that surrounds the body of the function
old:
new:
### PassManagerBuilder
PassManagerBuilder.cpp PassManagerBuilder.h
used to set up a standard optimization sequence suitable for language like C and C++
### PruneEH (exception handle)
CGSCCPass
walk call graph(bottom-up traversal of the call graph), turning invoke instructions into calls,iff the callee cannot throw an exception and mark function nounwind if they cannot throw.
PruneEH.cpp
old:
### SampleContextTracker
not a pass, used by CSSPGO
SampleContextTracker.cpp SampleContextTracker.h
### SampleProfile
ModulePass
SampleProfile.cpp SampleProfile.h
implement a sampleprofileloader transform. This pass read a profile file generated by a sampling profiler and generates IR metadata to reflect the profile information in the given profile.
This pass generate branch weight annotations on the IR.
old:
new:
### SampleProfileProbe
provide interface for the pseudo probe implementation for AutoFDO (Feedback directed optimization)
SampleProfileProbe.h SampleProfileProbe.cpp
new:

### SCCP
ModulePass
interprocedural sparse conditional constant propagation and merging 
Assume values are constant unless proven otherwise
Assume BasicBlocks are dead unless proven otherwise
Proves values to be constant, and replaces them with constants
Proves conditional branches to be unconditional
SCCP.h SCCP.cpp

Compile Time run with constants.

new:
old:
### StripDeadPrototypes
Module Pass
StripDeadPrototypes.h StripDeadPrototypes.cpp
loop over all of the functions in the input module, looking for dead declarations and remove them.
Dead declarations are declarations of functions for which no implementation is available (i.e. declarations for unused library functions)
old:
new:
### StripSymbols
Module Pass
implement code stripping. it can delete:
names for virtual registers
symbols for internal globals and functions
debug information
This will make code much less readable, so it should only be used in situations where the strip utility would be used, such as reductin code size or making it harder to reverse engineer code.
old:
new:
### SyntheticCountsPropagation
Synthesize entry counts for functions and attaches !prof metadata to functions with the synthesized counts.
The presence of !prof metadata with counter name set to synthesized_function_entry_count indicate that the value of the counter is an estimation of the likely execution count of the function. This transform is applied only in non PGO mode as functions get real profile-based function entry counts in the PGO mode.
SyntheticCountsPropagation.h SyntheticCountsPropagation.cpp
new
### ThinLTOBitcodeWriter
ModulePass
prepare a module containing type metadata for ThinLTO by splitting it into regular and thin LTO parts if possible, and writing both parts to a multi-module bitcode file. Modules that do not contain type metadata are written unmodified as a single module.
ThinLTOBitcodeWriter.cpp ThinLTOBitcodeWriter.h
old:
new:
### WholeProgramDevirt
whole program devirtualization???
WholeProgramDevirt.h WholeProgramDevirt.cpp
new:
