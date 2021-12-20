# InstructionSimplify
InstructionSimplify.h InstructionSimplify.cpp

Combine instructions to form fewer, simple instructions. This pass does not modify the CFG. This pass is where algebraic simplification happens.

This pass combines things like:

%Y = add i32 %X, 1
%Z = add i32 %Y, 1
into:

%Z = add i32 %X, 2
This is a simple worklist driven algorithm.

This pass guarantees that the following canonicalizations are performed on the program:

If a binary operator has a constant operand, it is moved to the right-hand side.
Bitwise operators with constant operands are always grouped so that shifts are performed first, then ors, then ands, then xors.
Compare instructions are converted from <, >, ≤, or ≥ to = or ≠ if possible.
All cmp instructions on boolean values are replaced with logical operations.
add X, X is represented as mul X, 2 ⇒ shl X, 1
Multiplies with a constant power-of-two argument are transformed into shifts.
… etc.
This pass can also simplify calls to specific well-known function calls (e.g. runtime library functions). For example, a call exit(3) that occurs within the main() function can be transformed into simply return 3. Whether or not library calls are simplified is controlled by the -function-attrs pass and LLVM’s knowledge of library calls on different targets.

InstCombine文件夹下包含如下组合


### InstCombineAddSub

### InstCombineAndOrXor

### InstCombineAtomicRMW

### InstCombineCalls

### InstCombineCasts

### InstCombineCompares

### InstCombineInternal

### InstCombineLoadStoreAlloca

### InstCombineMulDivRem

### InstCombineNegator

### InstCombinePHI

### InstCombineSelect

### InstCombineShifts

### InstCombineVectorOps

### InstructionCombining

