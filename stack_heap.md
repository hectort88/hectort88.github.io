The memory structure of a running process in Linux is divided into several logical segments. These segments, which form the **Process Address Space**, control how the process's code, data, and execution state are organized in memory.

Here are the main parts of a process in Linux, often referred to as its "text and data" segments:

---

## 1. Code (Text) Segment
* **What it is:** This segment contains the actual **executable machine code** of the program.
* **Allocation:** Read-only and often shared.
* **Details:**
    * It is typically marked as **read-only** to prevent the process from accidentally modifying its own instructions.
    * For commonly used programs (like system utilities), the kernel can map the same code segment to multiple running processes to **save physical memory**. This is called shared text.
    * This is generally the **lowest memory address** section of the process address space.

---

## 2. Data Segments
The data segments store the application's global and static variables.

### A. Initialized Data Segment ($\texttt{.data}$)
* **What it is:** This holds **global and static variables** that have been explicitly initialized with a non-zero value in the source code.
* **Allocation:** Read/Write.
* **Details:** The size of this segment is determined at compile time.

### B. Uninitialized Data Segment ($\texttt{.bss}$ - Block Started by Symbol)
* **What it is:** This holds **global and static variables** that are uninitialized or initialized to zero.
* **Allocation:** Read/Write.
* **Details:** To save space in the executable file, this segment only records its size; the kernel initializes this memory area to zero when the process is loaded.

---

## 3. Heap
* **What it is:** The segment used for **dynamic memory allocation**.
* **Allocation:** Read/Write.
* **Details:**
    * Memory in the Heap is allocated during runtime using functions like $\texttt{malloc()}$ in C or $\texttt{new}$ in C++.
    * It **grows upward** (towards higher memory addresses).
    * The kernel manages the Heap, extending it typically via the $\texttt{brk}$ system call or mapping new memory pages via $\texttt{mmap}$ for large allocations.
    * The Heap is often managed by a user-space library (like $\texttt{glibc}$'s memory allocator).

---

## 4. Stack
* **What it is:** The segment used for **local variables, function parameters, and return addresses** during function calls.
* **Allocation:** Read/Write.
* **Details:**
    * The Stack **grows downward** (towards lower memory addresses) from a high memory address.
    * It is managed automatically by the CPU and the compiler.
    * Each time a function is called, a new **Stack Frame** is pushed onto the stack, and when the function returns, the frame is popped off.
    * The Stack typically starts near the top of the process's address space.

---

## 5. Shared Memory / Memory Mapping (mmap)
* **What it is:** An area for memory mapped files, dynamically loaded libraries (like $\texttt{libc.so}$), and explicit shared memory segments.
* **Allocation:** Read/Write or Read-only, depending on the mapping.
* **Details:** This region sits between the Stack and the Heap and grows as required, allowing the process to map files directly into memory or share memory with other processes.

### 



The general layout is designed to allow the Stack and the Heap to grow towards each other, maximizing the available space before a collision (called a "stack overflow" or running out of heap space) occurs.

[![Memory structure](/images/memparts.png)](/images/memparts.png)

### Example with arrays:

If you declare an array normally (without malloc), the answer depends entirely on where in your code you declare it. In C, it breaks down into two main scenarios:
1. Inside a Function (Local Variable) → The Stack

If you declare the array inside a function (including main), it is allocated on the Stack.

```c
void myFunction() {
    int scores[100]; // ALLOCATED ON THE STACK
    // ...
} // Memory is automatically reclaimed here!
```

- Behavior: The memory is automatically "pushed" onto the stack when the function starts and "popped" off when the function ends.

- Risk: If you make this array too large (e.g., int big[1000000]), you might cause a Stack Overflow because the stack is usually quite small (often 1MB to 8MB).

2. Outside a Function (Global/Static) → Data Segment

If you declare the array outside of any function, it is not on the Stack and not on the Heap. It lives in a special area called the Data Segment (or BSS).

```c
int globalArray[100]; // ALLOCATED IN THE DATA SEGMENT

int main() {
    static int staticArray[50]; // ALSO IN THE DATA SEGMENT
    return 0;
}
```

- Behavior: This memory is allocated when the program starts and stays there until the program exits.

- Difference: Unlike the Heap, you cannot resize this, and unlike the Stack, it doesn't disappear when a function ends.

### The "Gotcha": Returning a Stack Array

A very common mistake for beginners is trying to return a stack-allocated array from a function. Don't do this!

```c
int* getScores() {
    int scores[3] = {1, 2, 3}; 
    return scores; // DANGER!
}
```

When getScores finishes, the Stack memory for scores is deleted. The pointer you return now points to "garbage" memory. If you want to return an array from a function, you must use malloc to put it on the Heap.