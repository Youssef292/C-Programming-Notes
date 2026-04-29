# Pointers in C

---

## Introduction

C supports two categories of data types:

- **Primitive data types** — built-in types provided by C: `int`, `char`, `float`, etc.
- **User-defined data types** — custom types defined by the programmer (e.g. `struct`, `union`, `enum`).

Every variable in memory has:

- A **value** — the data it stores.
- An **address** — its location in memory.

```c
char y = 10;
// size: 1 byte | name: y | value: 10
```

Pointers give us the ability to access and manipulate variables **through their memory addresses**, which enables powerful patterns like cross-scope variable access.

**Problem without pointers:**

```c
int main() {
    int z = 10; // z is a local variable, only accessible within main()
}

int sum() {
    z = 10; // ERROR: undefined variable — z is out of scope
}
```

> With pointers, we can work around scope limitations and access variables across functions.

---

## Pointer Syntax & Declaration

```c
int* ptr; // declares a pointer to int using the dereference (*) operator
// pointe Datatype : int | astrics operator : (*) | name : ptr
```

**Key rules:**

1. An uninitialized pointer holds a **garbage (wild) value** — always initialize before use.
2. A pointer is a **special variable that holds a memory address**, not a regular value.
3. Pointers are treated like variables — they have their own address in memory too.

**Declaration shorthand:**

```c
int x = 5;
int *ptr = &x; // declare and initialize in one line
```

---

## How Pointers Work in Memory

```c
int x = 7;
int *ptr;
ptr = &x;   // ptr stores the address of x (e.g. 0x30)

int y;
y = *ptr;   // dereference: reads the value stored at the address in ptr
```

|Expression|Description|Example Output|
|---|---|---|
|`x`|Value of `x`|`7`|
|`y`|Value copied from `*ptr` (same as `x`)|`7`|
|`ptr`|Address stored in `ptr` (address of `x`)|`0x30`|
|`&x`|Address of `x` directly|`0x30`|
|`*ptr`|Value at the address stored in `ptr` (indirect access to `x`)|`7`|
|`&ptr`|Address of the pointer variable itself in memory|`0x100`|

> `*ptr` is like saying: _"go to address `0x30` and give me the value stored there."_

---

## Pointer Operations — Examples

### Example 1 — Pointer Assignment & Value Modification

```c
int x = 5;
int y = 10;

int *ptr1;
ptr1 = &x;      // ptr1 = 0x30 (address of x)

int *ptr2;
ptr2 = ptr1;    // ptr2 = 0x30 (same address as ptr1)

*ptr2 = y;      // *(0x30) = 10 → modifies x through ptr2
```

**Memory state after execution:**

|Symbol|Value|
|---|---|
|`x`|`10`|
|`y`|`10`|
|`ptr1`|`0x30`|
|`ptr2`|`0x30`|
|`*ptr1`|`10`|
|`*ptr2`|`10`|
|`&ptr1`|`0x100` (address of ptr1 itself)|

---

### Example 2 — Modifying Values Through Pointers

```c
int x = 5;
char y = 10;

int  *ptr1 = &x;
char *ptr2 = &y;

*ptr1 = 15;   // x is now 15
*ptr2 = 40;   // y is now 40

printf(x);           // 15
printf(y);           // 40

printf(sizeof(x));    // 4 bytes
printf(sizeof(y));    // 1 byte

printf(sizeof(ptr1)); // 4 bytes (compiler-dependent, fixed)
printf(sizeof(ptr2)); // 4 bytes (same as ptr1)
```

> **Important:** All pointers have the **same fixed size** regardless of their data type, because they store memory addresses — and address size is compiler-dependent (typically 4 bytes on 32-bit, 8 bytes on 64-bit systems).

---

### Example 3 — Overwriting a Value via Pointer

```c
int x = 20;       // 4 bytes reserved in memory
int *ptr = &x;    // ptr holds address of x (e.g. 0x30)

*ptr = 15;        // goes to address 0x30 and overwrites the value → x is now 15
```

---

## Common Pointer Errors

```c
// ❌ Error: assigning a raw value directly to a pointer
int *ptr = 0x30;
// The compiler treats pointers as a special type — they cannot store raw values, only addresses.

// ❌ Error: assigning a variable's value (not address) to a pointer
int x = 0x30;
int *ptr;
ptr = x;  // ERROR: x holds a value, not an address — use ptr = &x instead

// ✅ Correct
int *ptr = &x;
```

---

## Pointer Size & Data Types

- Each memory location has a **fixed-size address**.
- Pointers exist to **hold addresses**, so their size is fixed and compiler-dependent.
- The **data type** of a pointer (`int*`, `char*`, etc.) determines **how many bytes are read/written** when dereferencing — not the size of the pointer itself.

|Variable|Size|
|---|---|
|`int x`|4 bytes|
|`char y`|1 byte|
|`int *ptr`|4 bytes (compiler-dependent)|
|`char *ptr`|4 bytes (same)|

---

## Interview Question — Byte-Level Modification

### Problem

```c
int x = 276;
char *ptr = &x;  // pointer to char — points to the FIRST (lowest) byte of x
*ptr = 0;        // modifies only Byte 0
printf(x);       // What does this print?
```

### Answer: `256`

### Why?

`276` stored in memory (little-endian, 4 bytes):

|Byte 3|Byte 2|Byte 1|Byte 0|
|---|---|---|---|
|`00000000`|`00000000`|`00000001`|`00010100`|
|0|0|256|20|

- `char *ptr` points to **Byte 0** (the lowest byte).
- `*ptr = 0` zeroes out **only Byte 0** (was `00010100` = 20).
- **Byte 1** remains unchanged: `00000001` = 256.
- Result: `x = 256 + 0 = 256`.

### Key Takeaway

> A `char*` pointer can be used to manipulate **individual bytes** of a multi-byte integer. This is because `char` is always 1 byte, so dereferencing a `char*` reads/writes exactly one byte at a time.

```c
// Important Interview Question 
// you have an integer x swap byte(0) with byte(3) using pointer

#include <stdio.h>

  

void swap_bytes(int* x)

{
    unsigned char* p = (unsigned char *) x;
    unsigned char temp = p[0];


    p[0] = p[3];
    p[3] = temp;

}

  

int main()

{
  
    int x = 5;
    swap_bytes(&x);
    printf("%d",x);


    return 0;

}
```

### Operations on Pointers
```c
char x = 5;

char y = 10;

char * ptr = &x;

ptr++; // --> it will increament the ptr with 1 not as a number but increament by the datatype size in this case as ptr is character so it increament the pointer by one byte in the momry 

printf(*ptr) // it'll print print the data inside the next location after increaminting by one

// But what if it's a pointer to integer 
int x = 5;

int y = 10;

int *ptr = &x;

ptr++; //in this case it'll increament with 4 bytes which is the size of the pointers datatype so it'll jump 4bytes in memory 
// and why this happens beacuse it's a special case from variables it depends on the step size of the pointer which is depending on the datatype size


//Final notes operations on pointer is possible but it's alawys with the step size which the size of the pointers datatype
```
