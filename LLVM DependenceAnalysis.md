https://www.youtube.com/watch?v=1e5y6WDbXCQ


# DependenceAnalysis
why?
in reordering transformations, preserve the dependences and the semantics
what?
gather information about the dependences of a program

## Dependence
is an order between two operations which need to preserve

## DependenceAnalysis analyzes dependences between pairs of memory access
unique_ptr<Dependence> DependenceInfo::depends(Instruction* Src, Instruction* Dst, bool PossiblyLoopIndependent)
  
  Loop Independent
  for i
     A[i] = A[i] + 1
  Loop Carried
  for i
     A[i+1] = A[i]
  此时有依赖距离（Dependence Distance）的概念，通常是常量; 可能出现多维距离





# MemorySSA
替代MemoryDependenceAnalysis （https://digitalcommons.macalester.edu/mathcs_honors/8/）

一切皆value， instruction也是value
Value的 user，可以查看其消费者
Instruction 的operands 可以查看其生产者

## 对于memory api，可能会有问题,例如下面不知道a和c是否一样
store %v, i32* %a
%z = load i32* %c
## 需要alias分析=》clobber
依赖AliasAnalysis
如果store发生在load前，并且这两个指针alias，则称store是load的一个clobber

## SSA on versioned Memory (内存版本)
1. liveOnEntry —— function entry memory state
2. x = MemoryDef(y) —— modify memory version y produce x(store)
3. MemoryUse(x) —— read memory version x (load)
4. MemoryPhi(x,y) —— merge incoming memory version at block entry
### 根据内存版本图可以看是否可以调整指令顺序
### 目前一些缺陷在于MemorySSA依赖图画不准，有可能会依赖过于严格
MemorySSA->getWalker 可以通过这种方式来解决，获取准确的memory版本依赖分析
