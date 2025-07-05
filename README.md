# LLVM Control Flow Graph Analysis Project

This project demonstrates control flow analysis using LLVM IR (Intermediate Representation) for different C++ control structures including conditionals, loops, and switch statements. The project generates both LLVM IR and visual Control Flow Graphs (CFGs) to analyze how high-level code constructs are translated into lower-level representations.

## Project Structure

```
project/
├── README.md                    # This file
├── src/                        # C++ source files
│   ├── condition.cpp           # Conditional statement example
│   ├── loop.cpp               # Loop example
│   └── switch.cpp             # Switch statement example
├── ir/                        # Generated LLVM IR files
│   ├── condition.ll           # LLVM IR for condition.cpp
│   ├── loop.ll               # LLVM IR for loop.cpp
│   └── switch.ll             # LLVM IR for switch.cpp
├── output/                    # Generated output files (images, graphs)
│   ├── condition_cfg.png     # Raw CFG for condition function
│   ├── condition_clean_cfg.png # Cleaned CFG for condition function
│   ├── sum_cfg.png           # Raw CFG for sum function
│   ├── sum_clean_cfg.png     # Cleaned CFG for sum function
│   ├── switch_cfg.png        # Raw CFG for switch function
│   └── switch_clean_cfg.png  # Cleaned CFG for switch function
├── notes/                     # Analysis documentation
│   ├── cfg-analysis.md       # Detailed CFG analysis
│   └── control-flow.md       # Control flow documentation
├── condition_clean.dot        # DOT file for condition CFG
├── sum_clean.dot             # DOT file for sum CFG
└── switch_clean.dot          # DOT file for switch CFG
```

## Prerequisites

Before working with this project, ensure you have the following installed:

### macOS Installation

1. **Install Homebrew** (if not already installed):
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install LLVM/Clang**:
   ```bash
   brew install llvm
   ```

3. **Install Graphviz** (for CFG visualization):
   ```bash
   brew install graphviz
   ```

4. **Add LLVM to PATH** (add to your `~/.zshrc`):
   ```bash
   echo 'export PATH="/opt/homebrew/opt/llvm/bin:$PATH"' >> ~/.zshrc
   source ~/.zshrc
   ```

### Verify Installation

```bash
# Check clang version
clang++ --version

# Check LLVM tools
opt --version
llc --version

# Check Graphviz
dot -V
```

## Getting Started

### 1. Clone or Download the Project

```bash
# If using git
git clone <repository-url>
cd llvm-cfg-analysis

# Or create the project structure manually
mkdir llvm-cfg-analysis
cd llvm-cfg-analysis
```

### 2. Compile C++ to LLVM IR

Generate LLVM IR files from the C++ source code:

```bash
# Create ir directory if it doesn't exist
mkdir -p ir

# Compile condition.cpp to LLVM IR
clang++ -O0 -S -emit-llvm src/condition.cpp -o ir/condition.ll

# Compile loop.cpp to LLVM IR
clang++ -O0 -S -emit-llvm src/loop.cpp -o ir/loop.ll

# Compile switch.cpp to LLVM IR
clang++ -O0 -S -emit-llvm src/switch.cpp -o ir/switch.ll
```

**Note**: The `-O0` flag disables optimizations to make the IR more readable and closer to the original source structure.

### 3. Generate Control Flow Graphs

Create visual representations of the control flow:

```bash
# Generate CFG from LLVM IR (raw format)
opt -passes=dot-cfg ir/condition.ll -disable-output
opt -passes=dot-cfg ir/loop.ll -disable-output
opt -passes=dot-cfg ir/switch.ll -disable-output

# Create output directory if it doesn't exist
mkdir -p output

# Convert DOT files to PNG images
dot -Tpng cfg.check_condition.dot -o output/condition_cfg.png
dot -Tpng cfg._Z3sumi.dot -o output/sum_cfg.png
dot -Tpng cfg._Z6choosei.dot -o output/switch_cfg.png

# Generate clean/simplified CFG images from custom DOT files
dot -Tpng condition_clean.dot -o output/condition_clean_cfg.png
dot -Tpng sum_clean.dot -o output/sum_clean_cfg.png
dot -Tpng switch_clean.dot -o output/switch_clean_cfg.png

# Clean up temporary DOT files
rm cfg.*.dot
```

### 4. View and Analyze Results

```bash
# View the generated LLVM IR
cat ir/condition.ll
cat ir/loop.ll
cat ir/switch.ll

# Open CFG images (on macOS)
open output/condition_cfg.png
open output/sum_cfg.png
open output/switch_cfg.png

# View clean CFG images
open output/condition_clean_cfg.png
open output/sum_clean_cfg.png
open output/switch_clean_cfg.png
```

## Source Code Examples

### Conditional Example (`src/condition.cpp`)
```cpp
int check_condition(int x) {
    if (x > 5) {
        return 1;
    } else {
        return 0;
    }
}
```

### Loop Example (`src/loop.cpp`)
```cpp
int sum(int n) {
    int s = 0;
    for (int i = 0; i < n; ++i)
        s += i;
    return s;
}
```

### Switch Example (`src/switch.cpp`)
```cpp
int choose(int x) {
  switch (x) {
    case 1: return 100;
    case 2: return 200;
    default: return -1;
  }
}
```

## Key LLVM IR Concepts Demonstrated

### 1. Conditional Statements
- **icmp**: Integer comparison instruction (`icmp sgt` for signed greater than)
- **br**: Conditional and unconditional branching
- **Basic Blocks**: Separate blocks for if/else branches

### 2. Loops
- **PHI nodes**: Handle variable values from different predecessors
- **Loop structures**: Entry, condition, body, increment, and exit blocks
- **Back edges**: Branches that create loops in the CFG

### 3. Switch Statements
- **switch instruction**: Multi-way branching
- **Jump tables**: Efficient implementation for dense case values
- **Default cases**: Fallback handling

## Analysis Commands

### Analyze LLVM IR Structure
```bash
# Count basic blocks
grep -c "^[0-9].*:" ir/condition.ll

# Find all branch instructions
grep "br " ir/loop.ll

# Locate PHI nodes
grep "phi " ir/loop.ll

# View function signatures
grep "define " ir/*.ll
```

### CFG Analysis
```bash
# Analyze DOT files
grep "label=" *.dot

# Count nodes and edges in CFG
grep -c "node\|edge" condition_clean.dot
```

## Working with the Project

### Modifying Source Code
1. Edit files in the `src/` directory
2. Recompile to LLVM IR using the commands in step 2
3. Regenerate CFGs using the commands in step 3
4. Analyze the changes in the generated files

### Adding New Examples
1. Create new `.cpp` files in `src/`
2. Follow the compilation process
3. Update this README with the new examples

### Optimization Analysis
Compare optimized vs unoptimized IR:
```bash
# Generate optimized IR
clang++ -O2 -S -emit-llvm src/condition.cpp -o ir/condition_opt.ll

# Compare with unoptimized version
diff ir/condition.ll ir/condition_opt.ll
```

## Educational Objectives

This project helps understand:
- How high-level language constructs translate to LLVM IR
- Control flow representation in compiler intermediate forms
- Basic block structure and CFG generation
- The relationship between source code and low-level representations
- LLVM toolchain usage for code analysis

## Git Workflow

### Repository Setup

The project includes a comprehensive `.gitignore` file that excludes:
- Generated LLVM IR files (`*.ll`)
- Generated CFG images (`*.png`, `*.svg`, `*.pdf`)
- Temporary DOT files from LLVM opt
- System files and IDE configurations
- Build artifacts and temporary files

### What to Track in Git

**Included in version control:**
- Source code files (`src/*.cpp`)
- Documentation (`notes/*.md`, `README.md`)
- Clean DOT files (`*_clean.dot`) - manually created for documentation
- Project configuration files

**Excluded from version control:**
- Generated IR files - can be recreated with `clang++` commands
- CFG images - can be regenerated from DOT files
- Temporary files and build artifacts

### Initial Git Setup

```bash
# Initialize git repository
git init

# Add all source files and documentation
git add src/ notes/ README.md .gitignore *_clean.dot

# Initial commit
git commit -m "Initial commit: LLVM CFG analysis project"

# Add remote origin (replace with your repository URL)
git remote add origin <your-repository-url>
git push -u origin main
```

### Regular Workflow

```bash
# After modifying source code
git add src/
git commit -m "Update source code examples"

# After updating documentation
git add notes/ README.md
git commit -m "Update analysis documentation"

# Push changes
git push origin main
```

### Regenerating Project Files

When cloning the repository, regenerate the excluded files:

```bash
# Clone the repository
git clone <repository-url>
cd llvm-cfg-analysis

# Regenerate LLVM IR files
mkdir -p ir
clang++ -O0 -S -emit-llvm src/condition.cpp -o ir/condition.ll
clang++ -O0 -S -emit-llvm src/loop.cpp -o ir/loop.ll
clang++ -O0 -S -emit-llvm src/switch.cpp -o ir/switch.ll

# Regenerate CFG images
opt -passes=dot-cfg ir/*.ll -disable-output
mkdir -p output
dot -Tpng cfg.check_condition.dot -o output/condition_cfg.png
dot -Tpng cfg._Z3sumi.dot -o output/sum_cfg.png
dot -Tpng cfg._Z6choosei.dot -o output/switch_cfg.png
dot -Tpng condition_clean.dot -o output/condition_clean_cfg.png
dot -Tpng sum_clean.dot -o output/sum_clean_cfg.png
dot -Tpng switch_clean.dot -o output/switch_clean_cfg.png
rm cfg.*.dot
```

## Troubleshooting

### Common Issues

1. **Command not found errors**: Ensure LLVM is properly installed and in PATH
2. **Permission errors**: Use `chmod +x` on script files if needed
3. **Missing images**: Ensure Graphviz is installed for DOT file processing
4. **IR generation fails**: Check that source files are syntactically correct

### Getting Help

- Check LLVM documentation: https://llvm.org/docs/
- Verify tool versions with `--version` flags
- Ensure all prerequisites are properly installed

## License

This project is for educational purposes. Please refer to your institution's guidelines for academic use.
