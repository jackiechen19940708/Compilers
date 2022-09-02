跟着AsmWriter.cpp学习IR


# DataLayout
include/llvm/IR/DataLayout.h
描述大小端、ELF mangling、ABI对齐、Native integer宽度等信息，被Module持有


# Module
一个编译模块，例如一个cpp文件即对应一个Module，包含多个：
1.全局符号表GlobalSymbols
1.1Function声明
1.2Function定义 
1.3GlobalVariable
2.目标信息Target Info
3.其他
include/llvm/IR/module.h
lib/IR/module.cpp

# Attributes
给Module、Function、Function参数Argument、Instruction等打的属性，用于后续分析优化。
谁打的Attribute？
如何使用这些Attribute？
include/llvm/IR/Attributes.h
include/llvm/IR/Attributes.td tablegen文件，定义了所有内置Attribute

# Argument
Function的形参
include/llvm/IR/Argument.h
里面有很多判断参数属性的方法，比如是否NonNull、ByVal、ByRef、对齐等等

# Function
include/llvm/IR/Function.h
是一个函数块，包含参数和多个BasicBlock
Argument
Entry Basic Block
Basic Block


# BasicBlock
include/llvm/IR/BasicBlock.h
多个连续无跳转指令的集合，首指令为唯一entry指令，包含如下部分
label
[phi Instruction]
[Instruction]
Terminator Instruction
# CFG
是BasicBlock作为Node的图，Function也是一个CFG；
llvm中用BasicBlock特化GraphTraits模板，并定义图起始节点、child的begin/end即可实现CFG
include/llvm/IR/CFG.h

# Type
llvm使用的是一种强类型IR，Type即定义了各种各样的类型
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
include/llvm/IR/Instruction.def 定义了一些宏，用于其他文件重定义宏，生成代码
lib/IR/Instruction.cpp 
include/llvm/IR/InstrTypes.h 一些大类指令
include/llvm/IR/Instructions.h 具体的指令
lib/IR/Instructions.cpp 
## nuw nsw
nuw and nsw stand for “No Unsigned Wrap” and “No Signed Wrap”, respectively. If the nuw and/or nsw keywords are present, the result value of the add is a poison value if unsigned and/or signed overflow, respectively, occurs.

# Intrinsic



include/llvm/IR/IntrinsicInst.h
include/llvm/IR/IntrinsicsAArch64.td
include/llvm/IR/IntrinsicsAMDGPU.td
include/llvm/IR/IntrinsicsARM.td
include/llvm/IR/IntrinsicsBPF.td
include/llvm/IR/Intrinsics.h
include/llvm/IR/IntrinsicsHexagonDep.td
include/llvm/IR/IntrinsicsHexagon.td
include/llvm/IR/IntrinsicsMips.td
include/llvm/IR/IntrinsicsNVVM.td
include/llvm/IR/IntrinsicsPowerPC.td
include/llvm/IR/IntrinsicsRISCV.td
include/llvm/IR/IntrinsicsSPIRV.td
include/llvm/IR/IntrinsicsSystemZ.td
include/llvm/IR/Intrinsics.td
include/llvm/IR/IntrinsicsVE.td
include/llvm/IR/IntrinsicsVEVL.gen.td
include/llvm/IR/IntrinsicsWebAssembly.td
include/llvm/IR/IntrinsicsX86.td
include/llvm/IR/IntrinsicsXCore.td



# AbstractCallSite
一个封装，可以使得直接、间接、callback调用行为一致。
include/llvm/IR/AbstractCallSite.h
[youtube](https://www.youtube.com/watch?v=zfiHaPaoQPc)
[llvm talk](https://llvm.org/devmtg/2018-10/slides/Doerfert-Johannes-Optimizing-Indirections-Slides-LLVM-2018.pdf)
[thesis](http://compilers.cs.uni-saarland.de/people/doerfert/par_opt_lcpc18.pdf)
# InstVisitor
辅助visit类，Visitor模式，解耦数据和操作，继承此类可以定义自己的访问指令行为
include/llvm/IR/InstVisitor.h
To define your own visitor, inherit from this class, specifying your
/// new type for the 'SubClass' template parameter, and "override" visitXXX
/// functions in your class. I say "override" because this class is defined
/// in terms of statically resolved overloading, not virtual functions.
///
/// For example, here is a visitor that counts the number of malloc
/// instructions processed:
///
///  /// Declare the class.  Note that we derive from InstVisitor instantiated
///  /// with _our new subclasses_ type.
///  ///
///  struct CountAllocaVisitor : public InstVisitor<CountAllocaVisitor> {
///    unsigned Count;
///    CountAllocaVisitor() : Count(0) {}
///
///    void visitAllocaInst(AllocaInst &AI) { ++Count; }
///  };
///
///  And this class would be used like this:
///    CountAllocaVisitor CAV;
///    CAV.visit(function);
///    NumAllocas = CAV.Count;
///
/// The defined has 'visit' methods for Instruction, and also for BasicBlock,
/// Function, and Module, which recursively process all contained instructions.


# AbstractAttribute
add new attribute

# GlobalVariable
