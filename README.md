# LLVM IR Control Flow Analysis Project

## 📋 Overview

This comprehensive project analyzes how C++ control flow structures (conditionals, loops, switch statements) are represented in LLVM Intermediate Representation (IR). The project demonstrates the compilation process from high-level C++ code to low-level LLVM IR and provides detailed analysis of the resulting control flow patterns.

## 🎯 Learning Objectives

- Understand LLVM IR syntax and semantics
- Analyze control flow structures at the IR level
- Learn about basic blocks, branches, and PHI nodes
- Visualize control flow graphs (CFGs)
- Compare different control structures in compiled form
- Master LLVM toolchain usage

## 📁 Project Structure

```
project/
├── README.md               # This comprehensive guide
├── src/                    # C++ source files
│   ├── condition.cpp      # If/else conditional example
│   ├── loop.cpp          # For-loop with accumulator
│   └── switch.cpp        # Multi-case switch statement
├── ir/                    # Generated LLVM IR files
│   ├── condition.ll      # IR for conditional logic
│   ├── loop.ll           # IR for loop structure
│   └── switch.ll         # IR for switch statement
├── notes/
│   └── control-flow.md   # Complete analysis and findings
├── ._Z15check_conditioni.dot # CFG dot file for conditional function
├── ._Z3sumi.dot          # CFG dot file for loop function
├── ._Z6choosei.dot       # CFG dot file for switch function
├── condition_cfg.png     # Conditional control flow graph
├── loop_cfg.png          # Loop control flow graph visualization
└── switch_cfg.png        # Switch control flow graph
```

## 🔧 Prerequisites

### System Requirements
- **Operating System**: macOS 10.15+, Ubuntu 18.04+, or Windows 10+ (with WSL2)
- **Architecture**: x86_64 or ARM64 (Apple Silicon supported)
- **Memory**: Minimum 4GB RAM recommended
- **Storage**: At least 2GB free space for LLVM installation

### Required Software

#### 1. LLVM Toolchain
The LLVM toolchain provides the core compilation and analysis tools:

**Components needed:**
- `clang++`: C++ compiler with LLVM backend
- `opt`: LLVM optimizer and IR analysis tool
- `llvm-dis`: LLVM disassembler (automatically included)
- `llc`: LLVM static compiler (optional)

**Version Requirements:**
- LLVM 12.0 or newer (recommended: LLVM 15+)
- Compatible with C++17 standard

#### 2. Graphviz
Required for generating visual control flow graphs:
- `dot`: Graph rendering engine
- Support for PNG/SVG output formats

#### 3. Development Tools
- **Text Editor/IDE**: VS Code, Vim, Emacs, or any editor with syntax highlighting
- **Terminal/Shell**: Bash, Zsh, or PowerShell
- **Git** (optional): For version control

### Optional Tools
- **Image Viewer**: For viewing generated CFG images
- **PDF Viewer**: If generating PDF documentation
- **Hex Editor**: For low-level IR analysis (advanced users)

## 🚀 Installation Guide

### macOS Installation

#### Method 1: Homebrew (Recommended)
```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install LLVM and Graphviz
brew install llvm graphviz

# Add LLVM to PATH (add to ~/.zshrc or ~/.bash_profile)
export PATH="/opt/homebrew/opt/llvm/bin:$PATH"

# For Intel Macs, use:
# export PATH="/usr/local/opt/llvm/bin:$PATH"

# Reload shell configuration
source ~/.zshrc  # or source ~/.bash_profile
```

#### Method 2: Xcode Command Line Tools + Manual Install
```bash
# Install Xcode Command Line Tools
xcode-select --install

# Download LLVM from official website
# Follow manual installation instructions
```

### Ubuntu/Debian Installation
```bash
# Update package manager
sudo apt update

# Install LLVM and tools
sudo apt install -y clang llvm llvm-dev graphviz

# Install specific version (if needed)
sudo apt install -y clang-15 llvm-15 llvm-15-dev

# Verify installation
clang++ --version
opt --version
dot -V
```

### Windows Installation (WSL2 Recommended)
```bash
# Install WSL2 with Ubuntu
# Then follow Ubuntu installation steps above

# Alternative: Use Visual Studio with Clang/LLVM
# Download LLVM for Windows from official site
```

## ✅ Verification

**Step 1: Set up LLVM PATH (Critical for Homebrew installations)**
```bash
# Add LLVM to your current session PATH
export PATH="/opt/homebrew/opt/llvm/bin:$PATH"

# Make it permanent by adding to shell configuration
echo 'export PATH="/opt/homebrew/opt/llvm/bin:$PATH"' >> ~/.zshrc

# Reload your shell configuration
source ~/.zshrc

# For Intel Macs, use this path instead:
# export PATH="/usr/local/opt/llvm/bin:$PATH"
# echo 'export PATH="/usr/local/opt/llvm/bin:$PATH"' >> ~/.zshrc
```

**Step 2: Verify your installation**
```bash
# Check clang++ version and target
clang++ --version
clang++ -print-targets

# Check opt (LLVM optimizer) - this should work now
opt --version
which opt

# Check Graphviz
dot -V

# Test basic compilation
echo 'int main() { return 0; }' > test.cpp
clang++ -S -emit-llvm test.cpp -o test.ll
cat test.ll
rm test.cpp test.ll
```

Expected output should show:
- LLVM version 12.0 or higher
- Graphviz version 2.40 or higher
- Successful generation of LLVM IR

## 🔄 Usage Instructions

### Step 1: Project Setup
```bash
# Clone or download the project
cd /path/to/project

# Verify all source files exist
ls src/
# Should show: condition.cpp  loop.cpp  switch.cpp
```

### Step 2: Compile C++ to LLVM IR
```bash
# Generate IR for conditional statement
clang++ -O0 -S -emit-llvm src/condition.cpp -o ir/condition.ll

# Generate IR for loop structure
clang++ -O0 -S -emit-llvm src/loop.cpp -o ir/loop.ll

# Generate IR for switch statement
clang++ -O0 -S -emit-llvm src/switch.cpp -o ir/switch.ll

# Verify IR files were created
ls ir/
```

**Compilation flags explained:**
- `-O0`: No optimization (preserves original structure)
- `-S`: Generate assembly/IR instead of object code
- `-emit-llvm`: Output LLVM IR instead of target assembly

### Step 3: Generate Control Flow Graphs
```bash
# Generate CFG dot files for all examples
opt -passes=dot-cfg ir/condition.ll -disable-output  # Creates ._Z15check_conditioni.dot
opt -passes=dot-cfg ir/loop.ll -disable-output       # Creates ._Z3sumi.dot
opt -passes=dot-cfg ir/switch.ll -disable-output     # Creates ._Z6choosei.dot

# Convert dot files to PNG images
dot -Tpng ._Z15check_conditioni.dot -o condition_cfg.png
dot -Tpng ._Z3sumi.dot -o loop_cfg.png
dot -Tpng ._Z6choosei.dot -o switch_cfg.png

# View the generated images
open condition_cfg.png  # macOS
open loop_cfg.png       # macOS  
open switch_cfg.png     # macOS
# or: xdg-open *.png    # Linux
```

**Understanding the generated filenames:**
- `._Z15check_conditioni.dot` → CFG for `check_condition(int)` function
- `._Z3sumi.dot` → CFG for `sum(int)` function
- `._Z6choosei.dot` → CFG for `choose(int)` function

The names use C++ name mangling where `_Z` prefix indicates mangled names.

### Step 4: Analysis
```bash
# Examine IR files
cat ir/condition.ll
cat ir/loop.ll
cat ir/switch.ll

# Read comprehensive analysis
cat notes/control-flow.md

# Analyze the generated CFG images
open condition_cfg.png  # Shows diamond pattern for if/else
open loop_cfg.png       # Shows cyclic pattern with back edge
open switch_cfg.png     # Shows multi-way branching
```

**CFG Pattern Analysis:**
- **Conditional CFG** (`condition_cfg.png`): Diamond-shaped control flow
  - Entry block → Comparison → Two branches → Merge point
- **Loop CFG** (`loop_cfg.png`): Cyclic control flow
  - Entry → Loop condition → Body → Increment → Back to condition
- **Switch CFG** (`switch_cfg.png`): Multi-way branching
  - Entry → Switch evaluation → Multiple case blocks → Merge point

## 🔍 What You'll Learn

### Part 1: Conditional Analysis
- **icmp instruction**: Integer comparisons in LLVM IR
- **br instruction**: Conditional and unconditional branching
- **Basic blocks**: Fundamental units of control flow
- **Label management**: How LLVM organizes code sections

### Part 2: Loop Analysis
- **Loop structures**: Entry, body, increment, and exit blocks
- **Memory operations**: Load/store vs. register operations
- **Loop metadata**: LLVM loop annotations
- **PHI nodes**: Understanding SSA form (in optimized code)

### Part 3: CFG Visualization
- **Graph structure**: Visual representation of control flow
- **Back edges**: Identifying loop structures in graphs
- **Dominance**: Understanding block relationships
- **Conditional CFG**: Diamond-shaped patterns in if/else structures
- **Loop CFG**: Cyclic patterns with back edges
- **Switch CFG**: Multi-way branching patterns

### Bonus: Switch Analysis
- **Switch instruction**: Multi-way branching in LLVM
- **Jump tables**: Efficient implementation strategies
- **Default cases**: Handling unmatched values

## 🛠️ Troubleshooting

### Common Issues

#### 1. Command Not Found Errors
```bash
# If 'opt' not found (most common issue):
# Step 1: Add LLVM to PATH immediately
export PATH="/opt/homebrew/opt/llvm/bin:$PATH"

# Step 2: Test if opt works now
opt --version

# Step 3: Make it permanent
echo 'export PATH="/opt/homebrew/opt/llvm/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# If 'clang++' not found:
which clang++
# If empty, check PATH or reinstall LLVM

# Verify LLVM installation location:
brew --prefix llvm
ls -la $(brew --prefix llvm)/bin/
```

#### 2. Permission Errors
```bash
# Make sure you have write permissions
chmod 755 /path/to/project
```

#### 3. Missing Dependencies
```bash
# Ubuntu: Install missing packages
sudo apt install build-essential

# macOS: Install Xcode Command Line Tools
xcode-select --install
```

#### 4. CFG Generation Issues
```bash
# If opt fails with pass errors:
opt --help | grep cfg  # Check available passes
opt -passes=help | grep cfg  # List CFG-related passes

# If dot files are not generated:
ls -la ._Z*.dot  # Check for generated dot files

# If image conversion fails:
dot -V  # Verify Graphviz installation
dot -Tpng ._Z15check_conditioni.dot -v -o condition_cfg.png  # Verbose output

# Alternative image formats:
dot -Tsvg ._Z15check_conditioni.dot -o condition_cfg.svg  # SVG format
dot -Tpdf ._Z15check_conditioni.dot -o condition_cfg.pdf  # PDF format
```

### Advanced Debugging
```bash
# Verbose compilation
clang++ -v -S -emit-llvm src/loop.cpp -o ir/loop.ll

# Check LLVM pass information
opt -passes=print-cfg ir/loop.ll -disable-output

# Analyze with different optimization levels
clang++ -O1 -S -emit-llvm src/loop.cpp -o ir/loop_O1.ll
clang++ -O2 -S -emit-llvm src/loop.cpp -o ir/loop_O2.ll
```

## 📚 Additional Resources

### Documentation
- [LLVM Language Reference Manual](https://llvm.org/docs/LangRef.html)
- [LLVM Programmer's Manual](https://llvm.org/docs/ProgrammersManual.html)
- [Graphviz Documentation](https://graphviz.org/documentation/)

### Tutorials
- [LLVM Tutorial: Table of Contents](https://llvm.org/docs/tutorial/)
- [Getting Started with LLVM Core Libraries](https://llvm.org/docs/GettingStarted.html)

### Community
- [LLVM Discourse Forum](https://discourse.llvm.org/)
- [Stack Overflow LLVM Tag](https://stackoverflow.com/questions/tagged/llvm)

## 🤝 Contributing

To extend this project:
1. Add new C++ examples in `src/`
2. Update analysis in `notes/control-flow.md`
3. Generate corresponding IR files
4. Document findings and patterns

## 📝 License

This educational project is provided for learning purposes. LLVM is distributed under the Apache 2.0 License with LLVM Exceptions.

## 📊 Project Metrics

- **Source Files**: 3 C++ examples
- **Generated Files**: 3 LLVM IR files + CFG visualization
- **Documentation**: Comprehensive analysis with code annotations
- **Complexity**: Beginner to intermediate level
- **Time Investment**: 2-4 hours for complete analysis
