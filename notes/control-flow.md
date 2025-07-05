# LLVM IR Control Flow Analysis

## Part 1: Conditional IR Analysis

### Task 1.1: Compilation
Compiled `src/condition.cpp` to `ir/condition.ll` using:
```bash
clang++ -O0 -S -emit-llvm src/condition.cpp -o ir/condition.ll
```

### Task 1.2: IR Analysis for Conditional Code

#### Key IR Instructions Identified:

**1. icmp instruction (comparison):**
```llvm
%5 = icmp sgt i32 %4, 5
```
- This implements the `x > 5` comparison
- `icmp sgt` means "integer compare signed greater than"
- Returns an i1 (boolean) value

**2. br instruction (conditional branch):**
```llvm
br i1 %5, label %6, label %7
```
- Takes the boolean result from icmp
- Branches to label %6 if true (x > 5), label %7 if false

**3. Basic blocks identified:**
- **Entry block (label %1)**: Sets up variables and performs comparison
- **True block (label %6)**: Stores return value 1
- **False block (label %7)**: Stores return value 0  
- **Exit block (label %8)**: Loads return value and returns

#### Answers to Questions:

**Q: How is x > 5 checked?**
A: Using the `icmp sgt i32 %4, 5` instruction, which performs signed integer comparison (greater than) between the loaded value of x and the constant 5.

**Q: How is the if/else structure implemented?**
A: Through conditional branching with `br i1 %5, label %6, label %7`. The boolean result determines which basic block to execute next - %6 for the if-branch, %7 for the else-branch.

**Q: How does LLVM determine which return value to use?**
A: Each branch stores its return value (1 or 0) in the same memory location (%2), then both branches jump to the common exit block %8, which loads and returns the stored value.

---

## Part 2: Loop and PHI Node Analysis

### Task 2.1: Loop Implementation
Created `src/loop.cpp` with the exact code:
```cpp
int sum(int n) {
    int s = 0;
    for (int i = 0; i < n; ++i)
        s += i;
    return s;
}
```

### Task 2.2: Compilation
Compiled using:
```bash
clang++ -O0 -S -emit-llvm src/loop.cpp -o ir/loop.ll
```

### Task 2.3: Loop IR Analysis

#### Key Components Identified:

**1. Loop entry block (label %5):**
```llvm
5:                                                ; preds = %13, %1
  %6 = load i32, ptr %4, align 4     ; Load i
  %7 = load i32, ptr %2, align 4     ; Load n
  %8 = icmp slt i32 %6, %7           ; Compare i < n
  br i1 %8, label %9, label %16      ; Branch to body or exit
```

**2. Loop body block (label %9):**
```llvm
9:                                                ; preds = %5
  %10 = load i32, ptr %4, align 4    ; Load i
  %11 = load i32, ptr %3, align 4    ; Load s
  %12 = add nsw i32 %11, %10         ; s = s + i
  store i32 %12, ptr %3, align 4     ; Store new s
  br label %13                       ; Jump to increment
```

**3. Loop increment block (label %13):**
```llvm
13:                                               ; preds = %9
  %14 = load i32, ptr %4, align 4    ; Load i
  %15 = add nsw i32 %14, 1           ; i = i + 1
  store i32 %15, ptr %4, align 4     ; Store new i
  br label %5, !llvm.loop !6         ; Jump back to condition
```

**Note about PHI nodes:** In this unoptimized IR (-O0), LLVM uses explicit memory operations (alloca/load/store) instead of PHI nodes. PHI nodes would appear in optimized IR where variables are kept in registers.

#### Answers to Questions:

**Q: What role does the phi node play?**
A: In this unoptimized IR, there are no PHI nodes. However, PHI nodes would normally merge values from different incoming paths (like merging the initial value 0 with updated values from loop iterations).

**Q: How does LLVM remember the loop variable i across iterations?**
A: Through memory operations - `i` is stored in allocated memory (%4), loaded before each use, incremented, and stored back. The memory location persists across iterations.

**Q: How is the loop exit condition implemented?**
A: Using `icmp slt i32 %6, %7` (i < n) followed by `br i1 %8, label %9, label %16` - continues to loop body if true, exits to %16 if false.

---

## Part 3: Control Flow Graph Visualization

### Task 3.1: CFG Generation
Generated CFG using:
```bash
opt -passes=dot-cfg ir/loop.ll -disable-output
```
This created `._Z3sumi.dot`

### Task 3.2: PNG Conversion
Converted to image using:
```bash
dot -Tpng ._Z3sumi.dot -o loop_cfg.png
```

### CFG Analysis
The generated control flow graph shows:
- **Entry block**: Function entry and variable initialization
- **Loop condition block**: Where `i < n` is evaluated
- **Loop body block**: Where `s += i` computation happens
- **Loop increment block**: Where `i++` occurs
- **Exit block**: Function return

The graph clearly shows the cyclic nature of the loop with the back-edge from increment to condition.

---

## Bonus: Switch Statement Analysis

### Implementation
Created `src/switch.cpp`:
```cpp
int choose(int x) {
  switch (x) {
    case 1: return 100;
    case 2: return 200;
    default: return -1;
  }
}
```

### IR Analysis
Compiled using:
```bash
clang++ -O0 -S -emit-llvm src/switch.cpp -o ir/switch.ll
```

#### Key Finding:
LLVM uses the `switch` instruction:
```llvm
switch i32 %4, label %7 [
  i32 1, label %5
  i32 2, label %6
]
```

**Analysis:**
- LLVM uses a dedicated `switch` instruction rather than multiple `br` instructions
- The switch instruction takes the discriminator value (%4) and jumps to the corresponding label
- `label %7` is the default case
- Each case has its own basic block (labels %5, %6, %7)
- All cases converge to a common exit block (label %8)

---

## Summary: Key LLVM IR Instructions

### icmp (Integer Compare)
- Performs integer comparisons (sgt, slt, eq, etc.)
- Returns i1 (boolean) type
- Used in conditional statements and loops

### br (Branch)
- Conditional: `br i1 %cond, label %true, label %false`
- Unconditional: `br label %target`
- Implements control flow decisions

### switch
- Multi-way branch instruction
- More efficient than multiple conditional branches for switch statements
- Syntax: `switch <type> <value>, label <default> [ <cases> ]`

### PHI Nodes (in optimized code)
- Merge values from different control flow paths
- Essential for SSA (Static Single Assignment) form
- Would appear as: `%result = phi i32 [ %val1, %block1 ], [ %val2, %block2 ]`

---

## Tools Used
- **clang++**: Compilation to LLVM IR
- **opt**: LLVM optimizer and analysis passes
- **dot (Graphviz)**: Graph visualization

## Files Generated
- `ir/condition.ll` - Conditional statement IR
- `ir/loop.ll` - Loop structure IR  
- `ir/switch.ll` - Switch statement IR
- `._Z3sumi.dot` - CFG dot file
- `loop_cfg.png` - Control flow graph image
