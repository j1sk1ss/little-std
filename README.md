# simple-std

This repository is a collection of simple, self-contained memory allocators, structs and usefull stuff written in C. Also, this is a collection of some sort of my-most-used code in my projects. You can drop any of them into your project without (or with little) any additional modification.

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

### Usage

Each allocator is implemented in a **single `.h` file**.  
To use one:

```c
#include "balloc.h"

int main() {
    b_init();
    void* ptr = b_malloc(128);
    b_free(ptr);
}
```

Each allocator provides the following functions:
- `..._init()` — initializes internal buffer and structures.
- `..._malloc(size)` — allocates memory.
- `..._free(ptr)` — deallocates memory.

## Benchmark tools
Collection of benchmark-related tools

### Timer
```c
#include <timer.h>
int main() {
    ttimer_t optimer;
    reset_time_timer(&optimer);
    add_time_timer(MEASURE_TIME_US({
        int a = 10 - 11;
    }), &optimer);
    fprintf(stdout, "time=%.2f µs", get_avg_timer_and_reset(&optimer));
}
```

## Structs

- map
```c
#include <map.h>
int main() {
    map_t m;
    map_init(&m);
    map_put(&m, 1, (void*)100);

    int val;
    map_get(&m, 1, (void**)&val);

    map_iter_t it;
    map_iter_init(&m, &it);
    while (map_iter_next(&it, (void**)&val)) {

    }

    map_free(&m);
}
```

- set (based on map)
```c
#include <set.h>
int main() {
    set_t s;
    set_init(&s);
    set_put(&s, (void*)1);
    if (!set_has(&s, (void*)1)) return 1;

    int val;
    set_iter_t it;
    set_iter_init(&s, &it);
    while (set_iter_next(&it, (void**)&val)) {

    }

    set_free(&s);
}
```

- list
```c
#include <list.h>
int main() {
    list_t l;
    list_init(&l);

    list_add(&l, (void*)100);
    int val;
    list_iter_t it;
    list_iter_hinit(&l, &it);
    while (list_iter_next(&it, (void**)&val)) {

    }

    list_free(&l);
}
```