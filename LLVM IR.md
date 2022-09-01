跟着AsmWriter.cpp学习IR

# Module
include/llvm/IR/module.h
lib/IR/module.cpp

# Type
include/llvm/IR/type.h
include/llvm/IR/DerivedTypes.h
lib/IR/type.cpp

# Value

# Function
1. A function consists of a list of basic blocks, a list of arguments and symbol table.
# BasicBlock
1. A basic block is a container of instructions that execute sequentially.
2. BasicBlock type is LabelTy
3. Also Value, refered by instructions such as branches and switch tables
4. well formed BB is formed of a list of non-terminating instructions followed by a single terminator instruction
5. ill formed BB is allowed in intermediate stage(constructing or modifying a program)

## important method
1. Function\* getParent();
2. BasicBlock \*Create(LLVMContext& Context, const Twine &Name = "", Function \*Parent, BasicBlock \*InsertBefore=nullptr)
3. Module \*getModule();
4. Instruction \*getTerminator();
5. Instruction \*getFirstNonPHI();
6. BasicBlock \* getSinglePredecessor()
7. begin() & end(), used for Instruction iterate

# User
继承自Value，内部数据排布是Use1、Use2、...Usen、User
# Value
定义了一个值，可以被其他User使用。

定义SubclassData，用于子类自定义状态使用。

定义ValueID，表征Value类型

use_begin() and use_end() 这两个方法可以让你遍历使用了此Value的User，即遍历def-use chain

replaceAllUsesWith(Value \*)这个方法可以帮助你快速把使用当前Value的User，替换为使用参数Value

# Instruction
继承自User，继承自带parent的node，表征LLVM IR指令。

属性，所属BasicBlock、Debug信息、相对位置Order；可能还有metadata

## 类型
Terminator
## UnaryOp
BinaryOp
IntDivRem
Shift
LogicalShift
ArithmeticShift
Cast
FuncletPad
ExceptionalTerminator
IndirectTerminator
BitwiseLogicOp

很多操作Instruction的函数
增、删、改

里面有很多表征IR特征的函数:
hasMetadata
hasNoUnsignedWrap
hasNoSignedWrap
HasPoisonGeneratingFlags
isExact
isFast
hasAllowReassoc
hasNoNaNs
hasNoInfs
hasNoSignedZeros
hasAllowReciprocal
hasAllowContract
hasApproxFunc
isAssociative
isCommutative
isIdempotent
isNilpotent
mayWriteToMemory
mayReadFromMemory
mayReadOrWriteMemory
isAtomic
hasAtomicLoad
hasAtomicStore
isVolatile
mayThrow
isFenceLike
mayHaveSideEffects
isSafeToRemove
willReturn
isEHPad
isLifetimeStartOrEnd
isLaunderOrStripInvariantGroup
isDebugOrPseudoInst




isOnlyUserOfAnyOperand,判断是否是某个操作数的唯一使用者
1.
include/llvm/IR/Instruction.h
lib/IR/Instruction.cpp 
include/llvm/IR/InstrTypes.h 一些大类指令
include/llvm/IR/Instructions.h 具体的指令
lib/IR/Instructions.cpp 
## nuw nsw
nuw and nsw stand for “No Unsigned Wrap” and “No Signed Wrap”, respectively. If the nuw and/or nsw keywords are present, the result value of the add is a poison value if unsigned and/or signed overflow, respectively, occurs.

# CFG


# AbstractAttribute
add new attribute

# GlobalVariable
