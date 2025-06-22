# Mallocators

This repository is a collection of simple, self-contained memory allocators written in C.  
The goal is to provide **easy-to-integrate**, **header-only**, and **user-space** memory allocators that require **no external dependencies**.

You can drop any of them into your project and have basic `malloc`/`free` functionality without relying on the system heap — useful for embedded systems, custom runtime environments, or low-level projects.

---

## Allocators

### 1. Linked List Allocator

A minimal allocator based on a **singly-linked list** of memory blocks.

- **Strategy**: First-fit (scans the list for the first suitable free block).
- **Splitting**: Yes — large blocks can be split into smaller ones.
- **Coalescing**: Partial — only when explicitly re-requested.
- **Best for**: Simplicity, small memory models, and minimal overhead.

**Pros**:
- Simple to understand and modify.
- Works in linear memory space.

**Cons**:
- Fragmentation builds up over time.
- Slower allocation for many small blocks.

---

### 2. Binary Tree Allocator

A tree-based allocator where blocks are recursively **split in half**, forming a binary tree.

- **Strategy**: Recursive tree traversal.
- **Splitting**: Yes — down to a minimal block size.
- **Coalescing**: Done lazily when malloc fails.
- **Best for**: Structured memory layouts with hierarchical splits.

**Pros**:
- Easy to visualize.
- Less fragmentation than linked lists in some scenarios.
- Faster allocation than in linked list.

**Cons**:
- Custom block structure overhead.
- Memory overhead from tree metadata.

---

### 3. Buddy Allocator

A proper **buddy system allocator** using block sizes as powers of two.

- **Strategy**: Maintains free lists by block size (levels).
- **Splitting**: To the smallest power-of-two block that fits.
- **Coalescing**: Automatic — merges buddies on `free()` using XOR.
- **Best for**: Fast, low-fragmentation allocation in power-of-two sizes.

**Pros**:
- Fast allocation and deallocation (logarithmic time).
- Minimal fragmentation due to buddy merging.
- Suitable for kernel, games, or real-time systems.

**Cons**:
- Slight internal fragmentation (due to rounding up to power-of-two).
- More complex than other allocators.

---

## Usage

Each allocator is implemented in a **single `.h` file**.  
To use one:

```c
#include "balloc.h"

int main() {
    b_init();                     // initialize allocator
    void* ptr = b_malloc(128);    // allocate memory
    b_free(ptr);                  // free memory
}
```

Each allocator provides the following functions:
- `..._init()` — initializes internal buffer and structures.
- `..._malloc(size)` — allocates memory.
- `..._free(ptr)` — deallocates memory.