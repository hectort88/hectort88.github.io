# ⚖️ AVL Tree Implementation in C

The core idea of an AVL tree is that for *every* node, the difference between the heights of its left and right subtrees (the **Balance Factor**) must be $-1$, $0$, or $+1$. If it ever exceeds this range, a rotation occurs to restructure the tree and restore balance.

```c
#include <stdio.h>
#include <stdlib.h>

// ================================================
// 1. NODE STRUCTURE AND HELPER FUNCTIONS
// ================================================

typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
    int height; // CRUCIAL: Store the height of the subtree rooted at this node
} Node;

// Helper function to get the height of a node (handles NULL gracefully)
int getHeight(Node* N) {
    if (N == NULL) 
        return -1; // Height of an empty tree is -1
    return N->height;
}

// Function to update the height after any change (insertion or rotation)
void updateHeight(Node* N) {
    if (N != NULL) {
        // The height is 1 + the maximum height of its two children
        int left_height = getHeight(N->left);
        int right_height = getHeight(N->right);
        N->height = 1 + (left_height > right_height ? left_height : right_height);
    }
}

// Function to get the Balance Factor
int getBalanceFactor(Node* N) {
    if (N == NULL) return 0;
    return getHeight(N->left) - getHeight(N->right);
}


// ================================================
// 2. ROTATION FUNCTIONS (The Balancing Act)
// ================================================

/* Right Rotation (Used when the imbalance is in a Left-Left case) */
Node* rightRotate(Node* y) {
    printf("\n--- Performing RIGHT Rotation ---\n");
    Node* x = y->left; // x becomes the new root of this subtree
    Node* T2 = x->right;

    // Perform rotation: x moves up, y moves down and right
    x->right = y; 
    y->left = T2;

    // Update heights (order matters: update child 'y' first, then parent 'x')
    updateHeight(y);
    updateHeight(x); 

    return x; // x is the new root of this balanced section
}

/* Left Rotation (Used when the imbalance is in a Right-Right case) */
Node* leftRotate(Node* x) {
    printf("\n--- Performing LEFT Rotation ---\n");
    Node* y = x->right; // y becomes the new root of this subtree
    Node* T2 = y->left;

    // Perform rotation: y moves up, x moves down and left
    y->left = x;
    x->right = T2;

    // Update heights (order matters)
    updateHeight(x); 
    updateHeight(y);

    return y; // y is the new root of this balanced section
}


// ================================================
// 3. INSERTION AND BALANCING LOGIC
// ================================================

Node* insert(Node* node, int key) {
    // Standard BST Insertion
    if (node == NULL) {
        printf("Inserting %d\n", key);
        Node* newNode = (Node*)malloc(sizeof(Node));
        newNode->data = key;
        newNode->left = NULL;
        newNode->right = NULL;
        // A new leaf node always starts with a height of 0
        newNode->height = 0; 
        return newNode;
    }

    if (key < node->data) {
        node->left = insert(node->left, key); // Recurse Left
    } else if (key > node->data) {
        node->right = insert(node->right, key); // Recurse Right
    } else {
        // Duplicate keys are not allowed in this simple implementation
        return node; 
    }

    // --- AVL Balancing Steps (The Magic Happens Here) ---

    // 1. Update the height of the current node *after* recursion returns
    updateHeight(node);

    // 2. Get the balance factor and check for imbalance
    int balance = getBalanceFactor(node);

    // Left-Left Case: Imbalance on the left side, further leaning left (Single Right Rotation)
    if (balance > 1 && key < node->left->data) {
        return rightRotate(node);
    }

    // Right-Right Case: Imbalance on the right side, further leaning right (Single Left Rotation)
    if (balance < -1 && key > node->right->data) {
        return leftRotate(node);
    }

    // Left-Right Case: Imbalanced left child, but leans right (Double Rotation needed)
    if (balance > 1 && key > node->left->data) {
        node->left = leftRotate(node->left); // First, rotate the left child
        return rightRotate(node);             // Then, perform the main rotation
    }

    // Right-Left Case: Imbalanced right child, but leans left (Double Rotation needed)
    if (balance < -1 && key < node->right->data) {
        node->right = rightRotate(node->right); // First, rotate the right child
        return leftRotate(node);                  // Then, perform the main rotation
    }

    // If balanced, return the unchanged root of this subtree
    return node; 
}

// Helper function for traversal (In-order print)
void inOrder(Node* root) {
    if (root != NULL) {
        inOrder(root->left);
        printf("%d (H:%d, BF:%d) ", root->data, root->height, getBalanceFactor(root));
        inOrder(root->right);
    }
}

// ================================================
// 4. MAIN EXECUTION AND TESTING
// ================================================

int main() {
    Node* root = NULL;

    /* Test Case 1: Simple Insertion (No rotation needed) */
    printf("--- Testing initial insertions ---\n");
    root = insert(root, 50); // Root
    insert(root, 25);       // Left child
    insert(root, 75);       // Right child
    inOrder(root); printf("\n\n");

    /* Test Case 2: Triggering an LL Rotation (Imbalance on the left) */
    printf("=============================================\n");
    root = insert(root, 10); // Causes imbalance at 25 (LL case)
    inOrder(root); printf("\n\n");

    /* Test Case 3: Triggering an LR Rotation (Double rotation) */
    // Current path: 50 -> 75. Now inserting 60 causes right-left imbalance at 75.
    printf("=============================================\n");
    root = insert(root, 80); // Imbalance is now on the right side of 50 (RR case)
    insert(root, 65);      // This insertion will trigger the LR rotation at node 75
    inOrder(root); printf("\n\n");

    return 0;
}
```

### 📚 Analysis: Why is this important? (The "Why" and "How")

#### 1. The Problem Solved (Why it matters)
*   **Standard BST Degradation:** In a basic, unbalanced BST, if you insert data that is strictly increasing or decreasing (e.g., 1, 2, 3, 4, 5...), the tree degenerates into a linear structure—it becomes just like an array linked list!
*   **The Performance Hit:** When the BST degrades to $O(N)$ complexity (linear), searching takes as long as traversing every single node. This defeats the purpose of using a tree.
*   **AVL Solution:** The AVL Tree guarantees that it will never degenerate too severely. By performing rotations, it ensures that the height difference between any two subtrees is minimal ($\pm 1$).

#### 2. The Mechanism (How the Rotations Work)
The rotations (`leftRotate` and `rightRotate`) are not just simple reassignments; they are structural surgery on the tree. They change which nodes are parent, child, or sibling, but they *preserve the BST property* (Left < Root < Right).

*   **Rotation Goal:** To move a heavy side of the tree up into the root position so that all elements remain reachable and the height difference is restored to 0 or $\pm 1$.
*   **Time Complexity:** Because AVL trees are guaranteed to be "bushy" (their height $h$ is proportional to $\log N$), every search, insertion, or deletion operation has a worst-case time complexity of **$O(\log N)$**. This is vastly superior to the linear $O(N)$ complexity of an unbalanced list.

#### 3. Key Low-Level Implementation Details
*   **Height Tracking:** Storing the `height` attribute in every node saves us from having to traverse down and recalculate height every time we check for balance, making the balancing checks $O(1)$.
*   **The Recursion Stack:** The entire process relies on recursion. Insertion happens *down* into the tree (building the path), but the **balancing logic only executes as the recursion unwinds (comes back up)**, allowing us to fix imbalances at the lowest affected node and bubble the balanced structure back up.
[Self-balancing binary search tree](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree)
[AVL tree Wikipedia](https://en.wikipedia.org/wiki/AVL_tree)