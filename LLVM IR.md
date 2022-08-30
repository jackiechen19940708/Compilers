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

# Instruction

## nuw nsw
nuw and nsw stand for “No Unsigned Wrap” and “No Signed Wrap”, respectively. If the nuw and/or nsw keywords are present, the result value of the add is a poison value if unsigned and/or signed overflow, respectively, occurs.

# CFG


# AbstractAttribute
add new attribute

# GlobalVariable
