### ➡️ Linear Data Structures
*These structures arrange elements sequentially, where each element is connected to its previous and next element.*

#### 1. Array
**Definition:** A collection of items stored at contiguous memory locations. Elements are accessed directly using an index (position number), allowing for extremely fast, constant-time access ($O(1)$). Arrays have a fixed size that must be defined when they are created in most languages.

#### 2. Linked List
**Definition:** A sequential collection of elements called *nodes*. Each node contains two parts: the data itself and a pointer (a reference) to the next node in the sequence. Elements do not need to be stored contiguously in memory, allowing the list to grow or shrink dynamically during runtime.

#### 3. Stack
**Definition:** A linear structure that operates based on the **LIFO (Last-In, First-Out)** principle. Data is added at one end (the "push" operation) and removed from the same end (the "pop" operation). It behaves like a pile of plates—you access the top item last.

#### 4. Queue
**Definition:** A linear structure that operates based on the **FIFO (First-In, First-Out)** principle. Elements are added to the back end ("enqueue") and removed from the front end ("dequeue"). It behaves like a line of people waiting for service.

---

### 🌳 Non-Linear Data Structures
*These structures organize data hierarchically or in complex relationships, allowing an element to be connected to multiple other elements.*

#### 5. Tree
**Definition:** A hierarchical structure composed of nodes, starting from a single node called the "root." Each node can have zero or more child nodes. Trees model real-world hierarchies (like file systems).

*   **Binary Tree (Specific Type):** A type of tree where every node has at most two children: a left child and a right child.
*   **Binary Search Tree (BST) (Specific Type):** A specialized binary tree where for every parent node, all data values in its left subtree are less than the parent's value, and all values in its right subtree are greater than the parent's value. This ordering allows for fast searching ($O(\log N)$).

#### 6. Graph
**Definition:** A collection of elements called "vertices" (or nodes) connected by links called "edges." Graphs are the most flexible structure, as they can represent complex relationships, such as social networks or road maps. Edges can be directional (directed graph) or bidirectional (undirected graph).

#### 7. Hash Table (Hash Map / Dictionary)
**Definition:** A data structure that implements an associative array, allowing for very fast lookups of a value based on a key. It uses a **hash function** to compute an index into an underlying array from the input key. This allows retrieval operations to approach constant time ($O(1)$).

---

### 🧩 Advanced/Hybrid Structures (Often Built Using The Basics)
*These structures combine elements of the above for specialized performance.*

#### 8. Heap
**Definition:** A specialized tree-based data structure that satisfies the **heap property**: every parent node is either greater than or equal to its children (**Max Heap**) or less than or equal to its children (**Min Heap**). Heaps are typically used to implement priority queues, where the highest (or lowest) priority item can be retrieved very quickly.

#### 9. Trie (Prefix Tree)
**Definition:** A specialized tree structure used to store a dynamic set of strings efficiently. Nodes in a Trie represent common prefixes, meaning that words sharing the same beginning characters share the same path through the tree. This is ideal for auto-complete and dictionary applications.