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

# Use
表明哪个User使用了哪个Value，是一个类似边的概念
include/llvm/IR/Use.h
  Value *Val = nullptr;
  Use *Next = nullptr;
  Use **Prev = nullptr;
  User *Parent = nullptr;

Val - 指向被使用的Value对象.
Next - 下一个指向同一Value的边.
Prev - 上一个指向同一Value的边, 注意是双向链表的设计, 这里用的二层指针.
Parent - 使用该Value的User(边的源节点).

# User
继承自Value，用于管理Use，内部数据排布是Use1、Use2、...Usen、User

首先从抽象的角度User必须继承自Value, 因为从直观上来讲一个Value可以同时是一个Value的Usee且又是另一个Value的User.
另一方面Use是依赖于User存在的(只有User知道它需要多少依赖以及依赖指向哪个Value), 因此User负责对Use的管理(申请与释放).
由于User是一个通用的基础类, 而不同指令的操作数个数又不同, 导致Use不能作为User的成员存在(容器方式存储可以, 但是效率降低).
让我们来看下User类(defined in include/llvm/IR/User.h)的定义.

User重载了三个版本的new操作符, 我们先来看最下面的实现, 其在给定大小Size的基础上又增加了一个Use指针大小的空间进行内存申请. 然后将返回地址的起始作为Use指针并置空, 之后的地址作为User对象的地址初始化并返回. 即在每个User对象之前有一个Use指针大小的空间保存了一个默认为空的Use指针. 有点意思, huhhh ?
再来看下另外两个版本的实现均调用了void *User::allocateFixedOperandUser(size_t Size, unsigned Us, unsigned DescBytes). 有趣的是申请的内存空间是传入的Size加上Us个Use对象的空间(以及DescriptInfo的大小, 可选项, 暂时忽略). 而在初始化时又将DescriptInfo放在最前, 然后是Us个Use, 最后是User对象.
现在可以理解User是如何管理Use对象的了:

第一种是独立分配, 在构造User对象时额外分配一个指针用来保存Use数组, 这种情况下HasHungOffUses为true.
对于这种情况如需分配Use对象时可以调用void User::allocHungoffUses(unsigned N, bool IsPhi = false)与void User::growHungoffUses(unsigned N, bool IsPhi = false).
另一种是co-allocated, 在构造User对象时传入边的数量并分配连续内存同时保存User与Use, 这种情况下HasHungOffUses为false.

# Value
include/llvm/IR/value.h
include/llvm/IR/Value.def def定义了各个Value类型的预处理宏，可以由其他文件重定义以实现代码
定义了一个值，可以被其他User使用。

定义SubclassData，用于子类自定义状态使用。

定义ValueID，表征Value类型

use_begin() and use_end() 这两个方法可以让你遍历使用了此Value的User，即遍历def-use chain

RAUW ： replaceAllUsesWith(Value \*)这个方法可以帮助你快速把使用当前Value的User，替换为使用参数Value

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
include/llvm/IR/Instruction.def
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
