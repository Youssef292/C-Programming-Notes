# C Build Process

> **Definition:** The complete pipeline of transforming a human-readable `.c` source file into an executable binary (machine language) that a processor can run.

```
File.c  ──►  ToolChain  ──►  File.exe / .hex / .elf
```

The final output format (`.exe`, `.hex`, `.elf`) depends on the target platform. Reaching that output is called the **build process**.

---

## Overview: The Four Stages


<div style="background: #1f6feb; border-radius: 8px; padding: 14px 18px; text-align: center; min-width: 90px;">
  <div style="font-size: 11px; color: #79c0ff; text-transform: uppercase; letter-spacing: 1px;">Input</div>
  <div style="font-size: 16px; font-weight: bold; margin-top: 4px;">file.c</div>
</div>

<div style="display: flex; flex-direction: column; align-items: center; margin: 0 4px;">
  <div style="color: #f0883e; font-size: 20px;">↓</div>
</div>

<div style="background: #388bfd22; border: 1px solid #388bfd; border-radius: 8px; padding: 14px 18px; text-align: center; min-width: 110px;">
  <div style="font-size: 11px; color: #79c0ff; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 4px;">Stage 1</div>
  <div style="font-weight: bold; color: #79c0ff;">Preprocessor</div>
  <div style="font-size: 11px; color: #8b949e; margin-top: 4px;">↓ file.i</div>
</div>

<div style="display: flex; flex-direction: column; align-items: center; margin: 0 4px;">
  <div style="color: #f0883e; font-size: 20px;">↓</div>
</div>

<div style="background: #3fb95022; border: 1px solid #3fb950; border-radius: 8px; padding: 14px 18px; text-align: center; min-width: 110px;">
  <div style="font-size: 11px; color: #7ee787; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 4px;">Stage 2</div>
  <div style="font-weight: bold; color: #7ee787;">Compiler</div>
  <div style="font-size: 11px; color: #8b949e; margin-top: 4px;">→ file.asm</div>
</div>

<div style="display: flex; flex-direction: column; align-items: center; margin: 0 4px;">
  <div style="color: #f0883e; font-size: 20px;">↓</div>
</div>

<div style="background: #bc8cff22; border: 1px solid #bc8cff; border-radius: 8px; padding: 14px 18px; text-align: center; min-width: 110px;">
  <div style="font-size: 11px; color: #d2a8ff; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 4px;">Stage 3</div>
  <div style="font-weight: bold; color: #d2a8ff;">Assembler</div>
  <div style="font-size: 11px; color: #8b949e; margin-top: 4px;">→ file.o</div>
</div>

<div style="display: flex; flex-direction: column; align-items: center; margin: 0 4px;">
  <div style="color: #f0883e; font-size: 20px;">↓</div>
</div>

<div style="background: #f0883e22; border: 1px solid #f0883e; border-radius: 8px; padding: 14px 18px; text-align: center; min-width: 110px;">
  <div style="font-size: 11px; color: #f0883e; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 4px;">Stage 4</div>
  <div style="font-weight: bold; color: #f0883e;">Linker</div>
  <div style="font-size: 11px; color: #8b949e; margin-top: 4px;">→ file.exe</div>
</div>





---

## Stage 1 — Preprocessor

The preprocessor is the **first tool** in the toolchain. It processes **preprocessor directives** — commands that begin with `#`.

### Preprocessor Directives (11 Total)

```c
#include
#define
#undef
#if
#ifdef
#ifndef
#else
#elif
#endif
#error
#line
```

> ⚠️ **Important:** `#pragma` is **NOT** a preprocessor directive. It is a compiler directive that passes special instructions directly to the compiler.

### What the Preprocessor Does

- Expands `#include` files (copies header content inline)
- Expands `#define` macros
- Evaluates `#if` / `#elif` / `#else` / `#endif` conditional blocks
- Strips all `#` directives from the code (except `#pragma`)

### Output

|Input|Output|
|---|---|
|`file.c`|`file.i` _(intermediate file)_|

- The `.i` file is still **valid C code**
- Contains **no `#` directives** (except `#pragma`, which passes through to the compiler)

---

## Stage 2 — Compiler

The compiler takes the preprocessed `.i` file and translates **C code** into **processor-specific assembly instructions**.

### What the Compiler Does

- Reads C-language constructs (loops, conditions, expressions)
- Maps them to the **instruction set architecture (ISA)** of the target processor
- Performs syntax/semantic checking and optimization

### Output

|Input|Output|
|---|---|
|`file.i`|`file.asm` / `file.s` _(assembly file)_|

> 🔴 **Target Dependent** — Different processors have different instruction sets (x86, ARM, RISC-V, etc.). The compiler must know the target CPU.

---

## Stage 3 — Assembler

The assembler translates **human-readable assembly mnemonics** into **binary opcodes** (machine code) that the processor directly executes.

### What the Assembler Does

- Reads each assembly instruction (e.g., `MOV`, `ADD`, `LDR`)
- Replaces it with its binary opcode defined by the processor's ISA
- Generates a relocatable binary object file

### Output

|Input|Output|
|---|---|
|`file.asm`|`file.o` _(object file)_|

> 🔴 **Target Dependent** — Each processor has its own unique opcode table.

---

## Stage 4 — Linker

The linker **collects all object files** from the project and combines them into a single executable.

### Multi-File Project Flow

<div style="font-family: 'Courier New', monospace; background: #0d1117; color: #c9d1d9; border-radius: 12px; padding: 28px; margin: 24px 0; border: 1px solid #30363d; overflow-x: auto;"> <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-bottom: 16px;"> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #60a5fa;">file1.c</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #60a5fa;">file2.c</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #60a5fa;">file3.c</div> </div> <div style="text-align: center; color: #f0883e; margin: 4px 0;">↓ Preprocessor ↓</div> <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin: 8px 0;"> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #a5f3fc;">file1.i</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #a5f3fc;">file2.i</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #a5f3fc;">file3.i</div> </div> <div style="text-align: center; color: #f0883e; margin: 4px 0;">↓ Compiler ↓</div> <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin: 8px 0;"> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #86efac;">file1.asm</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #86efac;">file2.asm</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #86efac;">file3.asm</div> </div> <div style="text-align: center; color: #f0883e; margin: 4px 0;">↓ Assembler ↓</div> <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin: 8px 0;"> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #d8b4fe;">file1.o</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #d8b4fe;">file2.o</div> <div style="background: #1f2937; border: 1px solid #374151; border-radius: 6px; padding: 10px; text-align: center; color: #d8b4fe;">file3.o</div> </div> <div style="text-align: center; color: #f0883e; margin: 8px 0; font-size: 20px;">↘ &nbsp;&nbsp; ↓ &nbsp;&nbsp; ↙</div> <div style="text-align: center; color: #f0883e; margin: 4px 0;">Linker</div> <div style="text-align: center; color: #f0883e; margin: 4px 0;">↓</div> <div style="background: #f0883e22; border: 2px solid #f0883e; border-radius: 8px; padding: 14px; text-align: center; color: #f0883e; font-weight: bold; margin-top: 8px;"> program.exe / .elf / .hex </div> </div>

---

## Target Dependency Analysis

A key question in embedded development: **which tools change when you change the target CPU?**

<div style="font-family: sans-serif; background: #0d1117; border-radius: 12px; padding: 24px; margin: 24px 0; border: 1px solid #30363d;"> <table style="width: 100%; border-collapse: collapse;"> <thead> <tr style="border-bottom: 2px solid #30363d;"> <th style="padding: 12px 16px; text-align: left; color: #8b949e; font-size: 12px; text-transform: uppercase; letter-spacing: 1px;">Tool</th> <th style="padding: 12px 16px; text-align: center; color: #8b949e; font-size: 12px; text-transform: uppercase; letter-spacing: 1px;">Target Dependent?</th> <th style="padding: 12px 16px; text-align: left; color: #8b949e; font-size: 12px; text-transform: uppercase; letter-spacing: 1px;">Reason</th> </tr> </thead> <tbody> <tr style="border-bottom: 1px solid #21262d;"> <td style="padding: 12px 16px; color: #79c0ff; font-weight: bold;">Preprocessor</td> <td style="padding: 12px 16px; text-align: center;"> <span style="background: #3fb95033; color: #3fb950; border-radius: 20px; padding: 4px 12px; font-size: 13px; font-weight: bold;">Independent</span> </td> <td style="padding: 12px 16px; color: #8b949e;">Only processes text substitution and directive expansion — no CPU knowledge needed.</td> </tr> <tr style="border-bottom: 1px solid #21262d;"> <td style="padding: 12px 16px; color: #7ee787; font-weight: bold;">Compiler</td> <td style="padding: 12px 16px; text-align: center;"> <span style="background: #f8514922; color: #f85149; border-radius: 20px; padding: 4px 12px; font-size: 13px; font-weight: bold;">Dependent</span> </td> <td style="padding: 12px 16px; color: #8b949e;">Must know the target's instruction set (ARM ISA ≠ x86 ISA ≠ RISC-V ISA).</td> </tr> <tr style="border-bottom: 1px solid #21262d;"> <td style="padding: 12px 16px; color: #d2a8ff; font-weight: bold;">Assembler</td> <td style="padding: 12px 16px; text-align: center;"> <span style="background: #f8514922; color: #f85149; border-radius: 20px; padding: 4px 12px; font-size: 13px; font-weight: bold;">Dependent</span> </td> <td style="padding: 12px 16px; color: #8b949e;">Each processor has a unique opcode table. ARM opcodes differ from x86 opcodes.</td> </tr> <tr> <td style="padding: 12px 16px; color: #f0883e; font-weight: bold;">Linker</td> <td style="padding: 12px 16px; text-align: center;"> <span style="background: #e3b34122; color: #e3b341; border-radius: 20px; padding: 4px 12px; font-size: 13px; font-weight: bold;">Partially</span> </td> <td style="padding: 12px 16px; color: #8b949e;">The linker tool itself is independent, but it reads a <strong style="color: #f0883e;">Linker Script</strong> that is target-specific (defines flash/RAM sizes and memory map).</td> </tr> </tbody> </table> </div>

### The Linker Script

The linker itself is a generic tool — it simply combines object files. However, it consults a **Linker Script** (a configuration file) that contains:

- Flash memory size of the target microcontroller
- RAM size and layout
- Memory region addresses

If the final executable **exceeds the flash memory size**, the linker **refuses to link** and throws an _"insufficient memory"_ error. This constraint is encoded in the linker script, making the **script** target-dependent, not the linker binary itself.

---

## How the Linker Works — Deep Dive

The linker performs two distinct steps:

### Step 1 — Symbol Table Verification (Object Verification)

The compiler processes **one file at a time**. It has no knowledge of symbols defined in other files. To handle cross-file references, the compiler generates a **Symbol Table** for every object file.

**Example: Cross-file variable sharing**

```c
// file1.c
int x = 10;
```

```c
// file2.c
extern int x;       // Tells the compiler: "x is defined somewhere else"

void func(void)
{
    x = 50;         // Compiler accepts this due to extern declaration
}
```

**Generated Symbol Tables:**

<div style="font-family: 'Courier New', monospace; background: #0d1117; border-radius: 12px; padding: 24px; margin: 24px 0; border: 1px solid #30363d; display: grid; grid-template-columns: 1fr 1fr; gap: 16px;"> <div style="background: #161b22; border-radius: 8px; border: 1px solid #388bfd; padding: 16px;"> <div style="color: #388bfd; font-weight: bold; margin-bottom: 12px; font-size: 14px;">📄 file1.o — Symbol Table</div> <table style="width:100%; border-collapse: collapse; font-size: 13px;"> <tr style="color: #8b949e; border-bottom: 1px solid #30363d;"> <th style="padding: 4px 8px; text-align:left;">Object</th> <th style="padding: 4px 8px; text-align:left;">Type</th> <th style="padding: 4px 8px; text-align:left;">State</th> </tr> <tr style="color: #3fb950;"> <td style="padding: 6px 8px;">x</td> <td style="padding: 6px 8px;">int</td> <td style="padding: 6px 8px; font-weight: bold;">PROVIDED ✓</td> </tr> </table> </div> <div style="background: #161b22; border-radius: 8px; border: 1px solid #f85149; padding: 16px;"> <div style="color: #f85149; font-weight: bold; margin-bottom: 12px; font-size: 14px;">📄 file2.o — Symbol Table</div> <table style="width:100%; border-collapse: collapse; font-size: 13px;"> <tr style="color: #8b949e; border-bottom: 1px solid #30363d;"> <th style="padding: 4px 8px; text-align:left;">Object</th> <th style="padding: 4px 8px; text-align:left;">Type</th> <th style="padding: 4px 8px; text-align:left;">State</th> </tr> <tr style="color: #f85149;"> <td style="padding: 6px 8px;">x</td> <td style="padding: 6px 8px;">int</td> <td style="padding: 6px 8px; font-weight: bold;">NEEDED ←</td> </tr> <tr style="color: #3fb950;"> <td style="padding: 6px 8px;">func()</td> <td style="padding: 6px 8px;">void</td> <td style="padding: 6px 8px; font-weight: bold;">PROVIDED ✓</td> </tr> </table> </div> </div>

The linker performs **two verification checks** on all symbol tables:

**Check 1 — Every NEEDED symbol must be PROVIDED somewhere:**

> If `file2` needs `x` but no file provides `x` → **Linker Error:** `undefined reference to 'x'`

**Check 2 — Every PROVIDED symbol must appear in exactly ONE symbol table:**

> If both `file1` and `file2` provide `x` → **Linker Error:** `multiple definition of 'x'`

Only when both checks pass does the linker proceed to output generation.

### Step 2 — Output Generation

The linker opens the output file (`.exe`, `.elf`, etc.) and writes all resolved object files into a single unified binary.

---

## The `static` Keyword and the Linker

The `static` keyword has special significance at **link time**.

### Effect on Variables (File Scope)

A `static` variable at file scope is **not added to the symbol table**. This means:

- It **cannot be `extern`-ed** by another file
- It is **invisible to the linker** — private to its translation unit

### Effect on Functions

A `static` function is **not exported** from its translation unit:

```c
// file1.c
static void helper(void)   // Cannot be called from file2.c
{
    // ...
}

void public_func(void);    // Can be called from any file
```

> 💡 **Rule:** `static` local variables and `static` file-scope variables/functions **do not appear in the symbol table** and therefore **cannot be linked across files**.

---

## Complete Worked Example — Multi-File Linking

This example traces the exact behavior of a 3-file project through the linker.

### Source Files

```c
// file1.c
int x = 50;

int main(void)
{
    printf("%d\n", x);   // Print 1 → outputs: 50
    func();
    printf("%d\n", x);   // Print 2 → outputs: 7  (func() set x = 7)
    func2();              // Print 3 → outputs: 9  (func2 sets its own static x = 9)
    printf("%d\n", x);   // Print 4 → outputs: 7  (file1's x unchanged by func2)
}
```

```c
// file2.c
extern int x;            // Links to x defined in file1.c

void func(void)
{
    x = 7;               // Modifies file1's x directly
}
```

```c
// file3.c
static int x = 8;        // Private to file3.c — NOT in symbol table

void func2(void)
{
    x = 9;               // Modifies file3's own private x
    printf("%d\n", x);   // Outputs: 9
}
```

### Symbol Tables

<div style="font-family: 'Courier New', monospace; background: #0d1117; border-radius: 12px; padding: 24px; margin: 24px 0; border: 1px solid #30363d; display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px;"> <div style="background: #161b22; border-radius: 8px; border: 1px solid #388bfd; padding: 14px;"> <div style="color: #388bfd; font-weight: bold; margin-bottom: 10px; font-size: 13px;">📄 file1.o</div> <div style="font-size: 12px;"> <div style="color: #3fb950; padding: 3px 0;">✓ x : int [PROVIDED]</div> <div style="color: #3fb950; padding: 3px 0;">✓ main : fn [PROVIDED]</div> </div> </div> <div style="background: #161b22; border-radius: 8px; border: 1px solid #7ee787; padding: 14px;"> <div style="color: #7ee787; font-weight: bold; margin-bottom: 10px; font-size: 13px;">📄 file2.o</div> <div style="font-size: 12px;"> <div style="color: #f85149; padding: 3px 0;">← x : int [NEEDED]</div> <div style="color: #3fb950; padding: 3px 0;">✓ func : fn [PROVIDED]</div> </div> </div> <div style="background: #161b22; border-radius: 8px; border: 1px solid #d2a8ff; padding: 14px;"> <div style="color: #d2a8ff; font-weight: bold; margin-bottom: 10px; font-size: 13px;">📄 file3.o</div> <div style="font-size: 12px;"> <div style="color: #6e7681; padding: 3px 0;">⊘ x : static [HIDDEN]</div> <div style="color: #3fb950; padding: 3px 0;">✓ func2 : fn [PROVIDED]</div> </div> </div> </div>

### Expected Output

```
50   ← main: printf before func()       (file1's x = 50)
7    ← main: printf after func()        (func set file1's x = 7)
9    ← func2: internal printf           (file3's static x = 9)
7    ← main: printf after func2()       (file1's x still = 7, unchanged)
```

### Scenario Analysis

**Scenario A — What if `file1.c` declares `static int x = 50`?**

- `x` in `file1` is removed from the symbol table (hidden)
- `file2` has `x` as NEEDED but no file PROVIDEs `x`
- **Linker Error:** `undefined reference to 'x'`

**Scenario B — What if `file2.c` replaces `extern int x` with `static int x`?**

- `file2` now has its own private `x` — does not touch `file1`'s `x`
- Both files compile and link successfully
- Expected output becomes:
    
    ```
    50   ← file1's x = 50 (unchanged by func)50   ← file1's x still 509    ← file3's static x = 950   ← file1's x unchanged
    ```
    

---

## Linker Errors Reference

|Error|Cause|Fix|
|---|---|---|
|`undefined reference to 'symbol'`|A NEEDED symbol is not PROVIDED by any object file|Define the symbol in one file, or add the correct `extern` declaration|
|`multiple definition of 'symbol'`|A symbol is PROVIDED in more than one object file|Remove duplicate definitions; use `extern` in all but one file|
|`region 'FLASH' overflowed by N bytes`|Output binary exceeds flash size defined in linker script|Reduce code size or update linker script for a larger device|

---

## Quick Reference Summary

```
.c  ──► [Preprocessor] ──► .i   (C code, no # directives)
.i  ──► [Compiler]     ──► .asm (Assembly for target CPU)
.asm──► [Assembler]    ──► .o   (Binary object file)
.o  ──► [Linker]       ──► .exe (Final executable)
```

|Stage|Tool|Input|Output|Target Dependent|
|---|---|---|---|---|
|1|Preprocessor|`.c`|`.i`|❌ No|
|2|Compiler|`.i`|`.asm`|✅ Yes|
|3|Assembler|`.asm`|`.o`|✅ Yes|
|4|Linker|`.o` files|`.exe`|⚠️ Script only|

> The **Linker Script** is target-dependent (defines memory map). The linker binary itself is not.
