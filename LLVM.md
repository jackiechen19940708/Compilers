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
