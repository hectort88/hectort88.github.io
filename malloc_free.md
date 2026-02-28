# Dynamic Memory Allocation

Dynamic Memory Allocation is the ability of a program to determine how much memory it needs while it is running (at "runtime"), rather than when the code is being written or compiled.

To understand why this is a big deal, we have to look at the two ways C handles memory: Static vs. Dynamic.

### 1. Static (Fixed) Allocation

When you declare an array like int scores[100];, the size is "baked in." This happens on the Stack.

The Problem: What if you only have 5 scores? You’ve wasted memory for 95 integers. What if you have 101 scores? Your program crashes or behaves unpredictably because the array is too small.

The Analogy: It’s like booking a wedding venue for exactly 100 people months in advance. If 200 show up, you’re in trouble; if 10 show up, you’ve wasted a lot of money.

### 2. Dynamic Allocation

Dynamic allocation happens on the Heap. You don't decide the size until the program is actually executing.

The Benefit: You can ask the user "How many items do you have?" and then tell the computer, "Okay, give me exactly enough space for that many."

The Analogy: It’s like a restaurant with modular tables. As the party walks through the door, the host pushes tables together to fit the exact number of people currently standing there.

## 1. The Stack: The "Automatic" Organized Pile

The Stack is managed by the CPU and follows a LIFO (Last-In, First-Out) structure. When you call a function, the computer "pushes" its variables onto the stack. When the function ends, it "pops" them off, and that memory is instantly gone.

- Analogy: A stack of dinner plates. You can only add or remove the top plate.

- Speed: Blazing fast. The CPU has a special register (the stack pointer) to track it.

- Lifetime: Temporary. Controlled by the scope of your functions { ... }.

## 2. The Heap: The "Manual" Open Field

The Heap is a large, unstructured pool of memory. It doesn't clean up after itself. You must go out into the field, find a spot, and plant a flag (malloc). When you're done, you must manually remove the flag (free).

- Analogy: A massive parking lot. You can park anywhere there's an empty space, but you have to remember where you parked and tell the attendant when you're leaving.

- Speed: Slower. The OS has to search for a big enough gap to fit your request.

- Lifetime: Permanent (until you say otherwise).

```c
void myFunction() {
    // STACK: 
    // This is managed for you. It's fast, but disappears
    // as soon as we hit the closing '}' of this function.
    int stackVar = 10; 

    // HEAP: 
    // We are requesting 40 bytes on the heap. 
    // 'heapPtr' is a variable on the STACK that 
    // holds the address of the memory on the HEAP.
    int *heapPtr = (int*)malloc(10 * sizeof(int));

    // Use it...
    
    // If we don't do this, the memory is lost forever (Leak!)
    free(heapPtr); 
}
```

## Heap Memory

Every byte of memory in a program’s memory space has an associated address. Everything the program needs to run is in its memory space, and different types of entities reside in different parts of a program’s memory space. For example, the code region contains the program’s instructions, global variables reside in the data region, local variables and parameters occupy the stack, and dynamically allocated memory comes from the heap. Because the stack and the heap grow at runtime (as functions are called and return and as dynamic memory is allocated and freed), they are typically far apart in a program’s address space to leave a large amount of space for each to grow into as the program runs.

Dynamically allocated memory occupies the heap memory region of a program’s address space. When a program dynamically requests memory at runtime, the heap provides a chunk of memory whose address must be assigned to a pointer variable.

## malloc and free

malloc and free are functions in the standard C library (stdlib) that a program can call to allocate and deallocate memory in the heap. Heap memory must be explicitly allocated (malloc’ed) and deallocated (freed) by a C program.

To allocate heap memory, call malloc, passing in the total number of bytes of contiguous heap memory to allocate. Use the sizeof operator to compute the number of bytes to request.

```c
// Determine the size of an integer and allocate that much heap space.
malloc(sizeof(int));
```

Think of malloc and free as the "manual labor" of memory management in C. Unlike local variables that are automatically cleaned up when a function ends, these tools allow you to request a specific amount of memory that lives until you explicitly decide to get rid of it.

In C, memory is typically divided into the Stack (automatic, small, managed by the compiler) and the Heap (large, manual, managed by you).

malloc (Memory Allocation): Requests a block of memory from the heap. It returns a void* pointer to the first byte of that block.

free: Tells the operating system that you're done with a previously allocated block of memory, allowing it to be reused.

```c
void* malloc(size_t size);
void free(void* ptr);
```
When a program no longer needs the heap memory it dynamically allocated with malloc, it should explicitly deallocate the memory by calling the free function. It’s also a good idea to set the pointer’s value to NULL after calling free, so that if an error in the program causes it to be accidentally dereferenced after the call to free, the program will crash rather than modify parts of heap memory that have been reallocated by subsequent calls to malloc. Such unintended memory references can result in undefined program behavior that is often very difficult to debug, whereas a null pointer dereference will fail immediately, making it a relatively easy bug to find and to fix.

```c
free(p);
p = NULL;
```

### When will malloc fail?

malloc isn't magic; it can fail. When it does, it returns NULL. This typically happens if:

- Insufficient Memory: The most common reason is that the system (or the process) has run out of available memory (RAM and swap space) or virtual address space to fulfill the request.

- Memory Fragmentation: Even if the total available memory is enough, it may be too fragmented into small, non-contiguous blocks to satisfy a request for a single, large, contiguous block.

- Requesting a Zero Size: If the size of the requested memory space is zero, the behavior is implementation-defined; the function may return a NULL pointer or a unique pointer that cannot be dereferenced but can be passed to free().

- System-Imposed Limits: The operating system may impose limits on the amount of memory a single process can use, which can cause malloc to fail even if the machine has free memory.

- Heap Corruption: Errors in a program, such as writing past the end of an allocated block or freeing the same block multiple times, can corrupt the internal structures of the memory allocator, leading to unpredictable behavior including malloc returning NULL.
- Implementation Specifics: Some embedded systems or specialized C library implementations may not support dynamic allocation at all and thus malloc will always return NULL, regardless of the request size. 

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int n = 5;
    
    // 1. Request memory for 5 integers
    int *arr = (int*)malloc(n * sizeof(int));

    // 2. ALWAYS check if malloc failed
    if (arr == NULL) {
        fprintf(stderr, "Memory allocation failed!\n");
        return 1; 
    }

    // 3. Use the memory
    for (int i = 0; i < n; i++) {
        arr[i] = i * 10;
        printf("%d ", arr[i]);
    }

    // 4. Free the memory when done
    free(arr);
    
    // 5. Good practice: set pointer to NULL so you don't use it accidentally
    arr = NULL;

    return 0;
}
```

### The "Borrower" Rule

Treat heap memory like a library book.

- Check it out (malloc).

- Make sure you actually got it (Check NULL).

- Read/Write in it (Use the pointer).

- Return it (free) so others can use it.

- Forget the call number (Set to NULL) so you don't try to go back to the shelf for a book that isn't there.