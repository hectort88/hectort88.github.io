Implementing a graph in C is significantly more complex than implementing a linked list because you are dealing with arbitrary connections.

For educational purposes and system efficiency, the **Adjacency List** approach is preferred over the Adjacency Matrix for sparse graphs (graphs where most nodes aren't connected).

Here is a complete example of an undirected graph using the Adjacency List method in C.

### 🏛️ Graph Implementation: Adjacency List

In this model, we use an array where each index represents a vertex ID. Each element of that array is a pointer to the head of a linked list, and that linked list contains all the vertices connected (adjacent) to the current vertex.

```c
#include <stdio.h>
#include <stdlib.h>

// ===============================================
// 1. STRUCTURE DEFINITIONS
// ===============================================

// Node structure for the Adjacency List (Represents an edge/connection)
typedef struct Node {
    int vertex;          // The ID of the neighboring vertex
    struct Node *next;  // Pointer to the next neighbor in the list
} Node;

// Graph structure (The array holding the heads of all adjacency lists)
typedef struct Graph {
    int num_vertices;
    Node **adj_list; // Array of pointers, each pointing to a linked list head
} Graph;


// ===============================================
// 2. CORE GRAPH OPERATIONS
// ===============================================

// Function to create and initialize the graph
Graph* createGraph(int vertices) {
    Graph* g = (Graph*)malloc(sizeof(Graph));
    if (g == NULL) return NULL; // Handle allocation failure

    g->num_vertices = vertices;
    
    // Allocate the array of adjacency lists
    g->adj_list = (Node**)malloc(vertices * sizeof(Node*));
    if (g->adj_list == NULL) { free(g); return NULL; } 

    // Initialize every list pointer to NULL
    for (int i = 0; i < vertices; i++) {
        g->adj_list[i] = NULL;
    }

    return g;
}


// Function to add an edge (connect two vertices) - Undirected Graph
void addEdge(Graph* g, int u, int v) {
    // 1. Add V to U's list
    Node* newNodeU = (Node*)malloc(sizeof(Node));
    if (newNodeU == NULL) return;

    newNodeU->vertex = v;
    newNodeU->next = g->adj_list[u]; // Point the new node to the old head
    g->adj_list[u] = newNodeU;       // Update U's list head to point to the new node

    // 2. Since it is an UNDIRECTED graph, also add U to V's list
    Node* newNodeV = (Node*)malloc(sizeof(Node));
    if (newNodeV == NULL) { free(newNodeU); return; } // Handle failure
    
    newNodeV->vertex = u;
    newNodeV->next = g->adj_list[v];
    g->adj_list[v] = newNodeV;

    printf("Edge added: %d <--> %d\n", u, v);
}


// Function to traverse the graph using Depth First Search (DFS)
void DFS(Graph* g, int vertex, int visited[]) {
    // Mark the current node as visited and print it
    visited[vertex] = 1;
    printf("%d ", vertex);

    // Iterate through all neighbors in the adjacency list of the current vertex
    Node* current_neighbor = g->adj_list[vertex];
    while (current_neighbor != NULL) {
        int neighbor = current_neighbor->vertex;
        
        // If a neighbor hasn't been visited, recursively call DFS on it
        if (!visited[neighbor]) {
            DFS(g, neighbor, visited);
        }
        // Move to the next node in the list for vertex 'vertex'
        current_neighbor = current_neighbor->next; 
    }
}


// Function to clean up all dynamically allocated memory (CRITICAL)
void freeGraph(Graph* g) {
    for (int i = 0; i < g->num_vertices; i++) {
        Node* current = g->adj_list[i];
        while (current != NULL) {
            Node* next = current->next; // Save the pointer to the next node
            free(current);             // Free the current node memory
            current = next;            // Move to the saved next node
        }
    }
    free(g->adj_list); // Free the array of list pointers
    free(g);           // Free the main graph structure
    printf("\n[Graph successfully cleaned up.]\n");
}


// ===============================================
// 3. MAIN EXECUTION
// ===============================================

int main() {
    // We define a graph with 5 vertices (IDs 0 through 4)
    const int NUM_VERTICES = 5;
    Graph* myGraph = createGraph(NUM_VERTICES);

    if (myGraph == NULL) return 1;

    // Build the connections:
    addEdge(myGraph, 0, 1); // 0 is connected to 1
    addEdge(myGraph, 0, 2); // 0 is connected to 2
    addEdge(myGraph, 1, 3); // 1 is connected to 3
    addEdge(myGraph, 2, 4); // 2 is connected to 4
    addEdge(myGraph, 3, 4); // 3 is connected to 4

    printf("\n--- Starting Depth First Search (DFS) from Vertex 0 ---\n");

    // Array to track visited nodes (to prevent infinite loops in cycles)
    int *visited = (int*)calloc(NUM_VERTICES, sizeof(int));

    // Start the traversal from vertex 0
    DFS(myGraph, 0, visited); 
    printf("\nTraversal finished.\n");


    // Cleanup memory to prevent leaks
    freeGraph(myGraph);
    free(visited);

    return 0;
}
```

### 🧠 Deep Dive & System Analysis

#### 1. Why Adjacency List? (The "Why")
*   **Space Complexity:** $O(V + E)$. Where $V$ is the number of vertices and $E$ is the number of edges. This means the memory used scales with the actual number of connections. For a sparse graph ($E \ll V^2$), this is vastly more efficient than an Adjacency Matrix, which always takes $O(V^2)$ space regardless of how few edges there are.
*   **Time Complexity (Edge Check):** Checking if two vertices are connected in the adjacency list requires traversing that vertex's linked list, taking $O(\text{Degree})$ time (where Degree is the number of neighbors).

#### 2. Time and Space Analysis of Operations:
| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **Graph Creation** | $O(V)$ | We must initialize an array pointer for every vertex. |
| **Adding Edge** | $O(1)$ (Amortized) | Since we are always inserting at the head of a linked list, it's instantaneous, regardless of how long the neighbor list gets. |
| **Traversal (DFS/BFS)** | $O(V + E)$ | Every vertex and every edge is examined exactly once during traversal. This is optimal for graph search. |

#### 3. The Critical C Concepts Used:
*   **Pointers to Pointers (`Node **adj_list`):** This is the most complex part of the memory management. `g->adj_list` is an array of pointers. Each element in that array holds a pointer (the head) to a dynamic linked list stored elsewhere on the heap.
*   **Dynamic Allocation (`malloc` / `free`):** Every single neighbor connection requires calling `malloc()` for a new `Node`. This must be perfectly mirrored by calling `freeGraph()` to prevent memory leaks—a critical skill in systems programming.
*   **Linked List Logic:** We use standard linked list mechanics (saving the `next` pointer before freeing/overwriting the current node) to manage the neighbors of each vertex.

#### 4. System-Level Application Examples
*   **Social Networks (Facebook):** Vertices are users; edges are friendships. Finding connections or finding mutual friends is a graph traversal problem.
*   **GPS/Road Maps:** Vertices are intersections/locations; edges are roads weighted by distance or time. Algorithms like Dijkstra's use this structure to find the shortest path.
*   **Network Routing:** Routers use graphs to determine the optimal path for data packets across an entire network topology.