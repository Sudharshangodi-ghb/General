## What is a Circular Linked List?
A circular linked list is a linked list in which the last node’s next pointer points back to the head (or any node within the list), forming a loop.

### Types
Singly Circular Linked List (SCLL): Each node has data and next. The last node’s next points to the head.
<img width="707" height="135" alt="image" src="https://github.com/user-attachments/assets/54a19fcf-deb2-4d14-ba6c-10dc1887514b" />

Doubly Circular Linked List (DCLL): Each node has prev and next; prev of head points to tail and next of tail points to head.
<img width="707" height="135" alt="image" src="https://github.com/user-attachments/assets/dca157db-dc2d-4454-85ae-0fe1e8bf4a18" />

---------------------------------------------------------------------------------------
## How do you detect a loop in a circular or singly linked list (Floyd's algorithm)?
Use two pointers:
slow moves 1 step at a time.
fast moves 2 steps at a time.

If the list has a cycle, the two pointers will eventually meet inside the loop.
If fast reaches NULL, the list is acyclic (no loop).
### Why It Works
In a loop, fast gains on slow by 1 node per iteration.
Eventually, fast will catch up to slow inside the cycle.

### Snippet 

#include <stdbool.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

bool has_loop(Node *head) {
    Node *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;           // move 1 step
        fast = fast->next->next;     // move 2 steps
        if (slow == fast) return true; // loop detected
    }
    return false; // no loop
}

--------------------------------------------------
## How do you remove a loop from a linked list?
### Snippet


#include <stdbool.h>

static Node* floyd_meet(Node *head) {
    Node *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;           // 1 step
        fast = fast->next->next;     // 2 steps
        if (slow == fast) return slow; // meeting point inside loop
    }
    return NULL; // no loop
}

void remove_loop(Node *head) {
    if (!head) return;
    
    Node *meet = floyd_meet(head);
    if (!meet) return; // no loop

    // Find start of loop
    Node *slow = head;
    Node *fast = meet;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    // Now 'slow' (== fast) is the start of the loop

    // Find the tail of the cycle (node whose next points to start)
    Node *tail = slow;
    while (tail->next != slow) {
        tail = tail->next;
    }

    // Break the loop
    tail->next = NULL;
}

----------------------------------------------------------------------
## How do you convert a circular linked list into a singly linked list?
Start from head.
Traverse until you find the node whose next points back to head (the tail of the circular list).
Set that node’s next = NULL.

head → A → B → C
head → A → B → C → NULL

## What causes a stack overflow?

A stack overflow occurs when a program uses more stack memory than is available. The stack is a region of memory used for:
Function call frames (return addresses, local variables)
Parameter passing
Saved registers during context switches

Common Causes:
###Deep or infinite recursion
Each recursive call pushes a new frame onto the stack.
If the recursion never terminates or goes too deep, the stack fills up.
Example:
Cvoid recurse() {    recurse(); // no base case → infinite recursion}Show more lines

### Large local variables
Declaring big arrays or structures inside a function consumes stack space.
Example:
Cvoid func() {    int arr[100000]; // ~400 KB on stack → overflow likely}Show more lines

### Excessive function nesting
Many nested calls without returning can exhaust stack space.

### Small stack size
Embedded systems or threads often have limited stack (e.g., 1 KB or 4 KB).
Complex functions or RTOS tasks can overflow easily.

### Corrupted stack pointer 
Bugs like writing beyond array bounds can overwrite the stack pointer, causing unpredictable overflow.

## concept of volatile 
### What volatile means
volatile tells the compiler: “This object may change unexpectedly—don’t optimize away reads/writes.”
So every time your code reads a volatile object, it must perform an actual memory read; every write must be emitted to memory, in program order (not re-ordered across sequence points by the compiler).
### When to use volatile
Memory-mapped hardware registers
Variables modified by an ISR (interrupt)
Shared flags accessed by hardware/another execution context
    DMA status registers, watchdog kick counters, flags updated by bootloader, etc.
Special memory with side effects
    Reading a register triggers hardware behavior (e.g., clears an interrupt). Marking it volatile ensures the read happens exactly as written.
### When not to use volatile
    General multi-threaded synchronization:
        volatile does not guarantee atomicity or ordering across threads/cores. Use mutexes/semaphores, or C11 atomics (e.g., atomic_int with memory_order_*), or RTOS synchronization primitives (FreeRTOS: xSemaphore, xQueue, xTaskNotify).
    To “fix” data races:
        It only prevents certain compiler optimizations; it does not prevent races.
    For large buffers where you only need occasional reads: avoid global volatile arrays—use volatile pointers to access boundaries or control registers, keep normal buffers non-volatile for performance.

## Steps to Calculate Structure Size
Start with member sizes
    Each member has its own size (e.g., char = 1 byte, int = 4 bytes).
Apply alignment requirements
    Each member must start at an address that is a multiple of its alignment (usually equal to its size or compiler-defined).
    Compiler inserts padding bytes between members to satisfy alignment.
Round up to largest alignment
    The structure’s total size is rounded up to a multiple of the largest member’s alignment.
    Extra padding may be added at the end.
### Example 
struct Example {
    char  c;    // 1 byte
    int   i;    // 4 bytes
    short s;    // 2 bytes
};

c at offset 0 → size 1
i needs 4-byte alignment → add 3 bytes padding → i at offset 4
s needs 2-byte alignment → placed at offset 8
Total so far = 10 bytes
Largest alignment = 4 → round up to multiple of 4 → final size = 12 bytes


## What is the compilation process?
Source Code (.c)
   ↓ Preprocessor
Expanded Source (.i)
   ↓ Compiler
Assembly (.s)
   ↓ Assembler
Object File (.o)
   ↓ Linker
Executable (a.out / .exe)

Preprocessing
    Handles directives like #include, #define, #ifdef.
    Expands macros, includes header files, removes comments.
    Output: a pure C source file with all macros expanded.
Compilation
    Converts the preprocessed C code into assembly language for the target architecture.
    Performs syntax and semantic checks.
    Output: an assembly file (.s).
Assembly
    Translates assembly code into machine code (binary instructions).
    Produces an object file (.o or .obj) containing machine code and relocation info.
    Output: object file.
Linking
    Combines multiple object files and libraries into a single executable.
    Resolves external symbols (functions, global variables).
    Adds startup code and runtime support.
    Output: final executable (.exe or a.out).

## Explain how memory fragmentation occurs.
Memory fragmentation happens when free memory is split into small, non-contiguous chunks, making it hard to satisfy large allocation requests even though the total free memory is sufficient. It occurs in dynamic memory allocation systems (like malloc/free) due to allocation and deallocation patterns.

Types of Fragmentation
External Fragmentation
    Free memory is scattered in small blocks between allocated blocks.
    Example:
    [Alloc 10][Free 5][Alloc 20][Free 8][Alloc 15]
    Total free = 13 bytes, but cannot allocate a single 12-byte block because free blocks are not contiguous.
Internal Fragmentation
    Memory allocated is larger than requested due to alignment or fixed block sizes.
    Example:
    Request 30 bytes, allocator gives 32 bytes (aligned to 8).
    Wasted 2 bytes inside the block.