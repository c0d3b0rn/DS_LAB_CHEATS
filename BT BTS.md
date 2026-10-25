### **Program 2: Lab 9 Additional Exercises — Tree Equality, Mirror Image & Tree Copy**
* **Covers:** Checking equality of two binary trees (`isEqual`), verifying mirror image relationship (`isMirror`), and creating a deep duplicate copy of a tree (`copyTree`).

```c
// Lab9_Additional.c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
} Node;

Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;
    newNode->left = newNode->right = NULL;
    return newNode;
}

Node* createBinaryTree() {
    int data;
    printf("Enter data (-1 for no child): ");
    scanf("%d", &data);
    if (data == -1) return NULL;
    Node* root = createNode(data);
    printf("Left child of %d -> ", data);
    root->left = createBinaryTree();
    printf("Right child of %d -> ", data);
    root->right = createBinaryTree();
    return root;
}

void inorder(Node* root) {
    if (!root) return;
    inorder(root->left);
    printf("%d ", root->data);
    inorder(root->right);
}

// 1. Check Equality of Two Binary Trees
int isEqual(Node* r1, Node* r2) {
    if (r1 == NULL && r2 == NULL) return 1;
    if (r1 != NULL && r2 != NULL) {
        return (r1->data == r2->data) &&
               isEqual(r1->left, r2->left) &&
               isEqual(r1->right, r2->right);
    }
    return 0;
}

// 2. Check if one tree is a Mirror Image of another
int isMirror(Node* r1, Node* r2) {
    if (r1 == NULL && r2 == NULL) return 1;
    if (r1 == NULL || r2 == NULL) return 0;
    return (r1->data == r2->data) &&
           isMirror(r1->left, r2->right) &&
           isMirror(r1->right, r2->left);
}

// 3. Copy a Binary Tree
Node* copyTree(Node* root) {
    if (root == NULL) return NULL;
    Node* temp = createNode(root->data);
    temp->left = copyTree(root->left);
    temp->right = copyTree(root->right);
    return temp;
}

int main() {
    printf("=== Create Tree 1 ===\n");
    Node* tree1 = createBinaryTree();

    printf("\n=== Create Tree 2 ===\n");
    Node* tree2 = createBinaryTree();

    printf("\nTree 1 Inorder: "); inorder(tree1); printf("\n");
    printf("Tree 2 Inorder: "); inorder(tree2); printf("\n");

    if (isEqual(tree1, tree2)) {
        printf("\n[Result] Tree 1 and Tree 2 are EQUAL.\n");
    } else {
        printf("\n[Result] Tree 1 and Tree 2 are NOT EQUAL.\n");
    }

    if (isMirror(tree1, tree2)) {
        printf("[Result] Tree 1 is a MIRROR IMAGE of Tree 2.\n");
    } else {
        printf("[Result] Tree 1 is NOT a mirror image of Tree 2.\n");
    }

    Node* copiedTree = copyTree(tree1);
    printf("\n[Copy Test] Inorder traversal of copied Tree 1: ");
    inorder(copiedTree);
    printf("\n");

    return 0;
}
```

---


### **Program 4: Lab 10 Additional Exercises — Level Order Traversal & BST to Doubly Linked List**
* **Covers:** Breadth-First Level Order Traversal using a Queue, and in-place conversion of a BST into a sorted Doubly Linked List (DLL).

```c
// Lab10_Additional.c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
} Node;

Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;
    newNode->left = newNode->right = NULL;
    return newNode;
}

Node* insertBST(Node* root, int key) {
    if (!root) return createNode(key);
    if (key < root->data) root->left = insertBST(root->left, key);
    else if (key > root->data) root->right = insertBST(root->right, key);
    return root;
}

// 1. Level Order Traversal using Queue (BFS)
void levelOrderTraversal(Node* root) {
    if (root == NULL) return;

    Node* queue;
    int front = 0, rear = 0;

    queue[rear++] = root;

    while (front < rear) {
        Node* curr = queue[front++];
        printf("%d ", curr->data);

        if (curr->left != NULL) queue[rear++] = curr->left;
        if (curr->right != NULL) queue[rear++] = curr->right;
    }
}

// 2. Convert BST into Sorted Doubly Linked List (in-place)
void BSTToDLL(Node* root, Node** head, Node** prev) {
    if (root == NULL) return;

    BSTToDLL(root->left, head, prev);

    if (*prev == NULL) {
        *head = root;
    } else {
        root->left = *prev;
        (*prev)->right = root;
    }
    *prev = root;

    BSTToDLL(root->right, head, prev);
}

void displayDLL(Node* head) {
    Node* curr = head;
    printf("Forward Traversal  : ");
    while (curr != NULL) {
        printf("%d ", curr->data);
        if (curr->right == NULL) break;
        curr = curr->right;
    }
    printf("\nBackward Traversal : ");
    while (curr != NULL) {
        printf("%d ", curr->data);
        curr = curr->left;
    }
    printf("\n");
}

int main() {
    Node* root = NULL;
    int n, val, i;

    printf("Enter number of elements for BST: ");
    scanf("%d", &n);
    printf("Enter %d integers:\n", n);
    for (i = 0; i < n; i++) {
        scanf("%d", &val);
        root = insertBST(root, val);
    }

    printf("\n--- Level Order Traversal (Queue BFS) ---\n");
    levelOrderTraversal(root);
    printf("\n");

    printf("\n--- Convert BST to Sorted Doubly Linked List ---\n");
    Node* head = NULL;
    Node* prev = NULL;
    BSTToDLL(root, &head, &prev);
    displayDLL(head);

    return 0;
}
```

---


### **Program 6: Module 4 — Expression Tree Construction & Evaluation**
* **Covers:** Constructing an Expression Tree from a Postfix Expression string using a stack, printing Infix, Prefix, and Postfix notations via tree traversals, and evaluating the arithmetic tree result.

```c
// Module4_ExpressionTree.c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>

typedef struct ExpNode {
    char data;
    struct ExpNode *left;
    struct ExpNode *right;
} ExpNode;

ExpNode* createExpNode(char data) {
    ExpNode* newNode = (ExpNode*)malloc(sizeof(ExpNode));
    newNode->data = data;
    newNode->left = newNode->right = NULL;
    return newNode;
}

// Build Expression Tree from Postfix Expression
ExpNode* buildExpressionTree(char postfix[]) {
    ExpNode* stack;
    int top = -1;

    for (int i = 0; postfix[i] != '\0'; i++) {
        char ch = postfix[i];
        if (ch == ' ' || ch == '\t') continue;

        ExpNode* newNode = createExpNode(ch);

        if (isalnum(ch)) {
            stack[++top] = newNode;
        } else { // Operator
            newNode->right = stack[top--];
            newNode->left = stack[top--];
            stack[++top] = newNode;
        }
    }
    return stack[top];
}

// Evaluate Expression Tree
int evaluateExpTree(ExpNode* root) {
    if (!root) return 0;

    // Leaf node (Operand)
    if (!root->left && !root->right) {
        return root->data - '0';
    }

    int leftVal = evaluateExpTree(root->left);
    int rightVal = evaluateExpTree(root->right);

    switch (root->data) {
        case '+': return leftVal + rightVal;
        case '-': return leftVal - rightVal;
        case '*': return leftVal * rightVal;
        case '/': return leftVal / rightVal;
    }
    return 0;
}

void inorderExp(ExpNode* root) {
    if (!root) return;
    if (root->left || root->right) printf("(");
    inorderExp(root->left);
    printf("%c", root->data);
    inorderExp(root->right);
    if (root->left || root->right) printf(")");
}

void preorderExp(ExpNode* root) {
    if (!root) return;
    printf("%c ", root->data);
    preorderExp(root->left);
    preorderExp(root->right);
}

void postorderExp(ExpNode* root) {
    if (!root) return;
    postorderExp(root->left);
    postorderExp(root->right);
    printf("%c ", root->data);
}

int main() {
    char postfix;
    printf("Enter Postfix Expression (e.g., 53-4*9+): ");
    scanf("%s", postfix);

    ExpNode* root = buildExpressionTree(postfix);

    printf("\nInfix Expression (Inorder)   : ");
    inorderExp(root);
    printf("\nPrefix Expression (Preorder)  : ");
    preorderExp(root);
    printf("\nPostfix Expression (Postorder): ");
    postorderExp(root);

    printf("\n\nEvaluated Result of Expression Tree: %d\n", evaluateExpTree(root));

    return 0;
}
```

---

### **Program 7: Module 4 — Inorder Threaded Binary Tree**
* **Covers:** Inorder Threaded Binary Tree node representation with `leftThread` and `rightThread` flags, finding `insucc` (Inorder Successor), and performing stackless linear Inorder traversal (`tinorder`).

```c
// Module4_ThreadedBinaryTree.c
#include <stdio.h>
#include <stdlib.h>

typedef struct ThreadedNode {
    int data;
    struct ThreadedNode *lchild;
    struct ThreadedNode *rchild;
    int leftThread;  // 1 if lchild points to predecessor thread, 0 if pointer
    int rightThread; // 1 if rchild points to successor thread, 0 if pointer
} ThreadedNode;

ThreadedNode* createThreadedNode(int data) {
    ThreadedNode* temp = (ThreadedNode*)malloc(sizeof(ThreadedNode));
    temp->data = data;
    temp->lchild = temp->rchild = NULL;
    temp->leftThread = temp->rightThread = 1;
    return temp;
}

// Find Inorder Successor without using Stack or Recursion
ThreadedNode* insucc(ThreadedNode* node) {
    ThreadedNode* temp = node->rchild;
    if (node->rightThread == 0) {
        while (temp->leftThread == 0) {
            temp = temp->lchild;
        }
    }
    return temp;
}

// Inorder Traversal using Threads
void tinorder(ThreadedNode* treehead) {
    ThreadedNode* temp = treehead;
    while (1) {
        temp = insucc(temp);
        if (temp == treehead) break;
        printf("%d ", temp->data);
    }
    printf("\n");
}

int main() {
    // Create Dummy Head Node
    ThreadedNode* head = createThreadedNode(-999);
    head->leftThread = 0;
    head->rightThread = 0;
    head->rchild = head;

    // Constructing a sample Threaded Binary Tree manually
    ThreadedNode *root = createThreadedNode(6);
    ThreadedNode *n1 = createThreadedNode(3);
    ThreadedNode *n2 = createThreadedNode(8);
    ThreadedNode *n3 = createThreadedNode(1);
    ThreadedNode *n4 = createThreadedNode(5);

    head->lchild = root;

    // Attach Left Subtree
    root->leftThread = 0; root->lchild = n1;
    root->rightThread = 0; root->rchild = n2;

    n1->leftThread = 0; n1->lchild = n3;
    n1->rightThread = 0; n1->rchild = n4;

    // Setup Threads
    n3->lchild = head; n3->rchild = n1; // Threads
    n4->lchild = n1; n4->rchild = root; // Threads

    n2->lchild = root; n2->rchild = head; // Threads

    printf("=== Inorder Traversal of Threaded Binary Tree (No Stack used) ===\n");
    tinorder(head);

    return 0;
}
```

---

