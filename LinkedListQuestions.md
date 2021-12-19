#### [Find length of Loop](https://practice.geeksforgeeks.org/problems/find-length-of-loop/1)

Given a linked list of size N. The task is to complete the function countNodesinLoop() that checks whether a given Linked List contains a loop or not and if the loop is present then return the count of nodes in a loop or else return 0. C is the position of the node to which the last node is connected. If it is 0 then no loop.

```cpp
int countNodesinLoop(struct Node *head)
{
    Node *slow = head, *fast = head;
    while(fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if(slow == fast) break;
    }

    if(slow != fast) return 0;

    slow = head;
    while(slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }

    int cnt = 0;
    do {
        fast = fast->next;
        cnt++;
    } while(fast != slow);

    return cnt;
}
```

---

#### [Flattening a Linked List ](https://practice.geeksforgeeks.org/problems/flattening-a-linked-list/1#)

Given a Linked List of size N, where every node represents a sub-linked-list and contains two pointers:
(i) a next pointer to the next node,
(ii) a bottom pointer to a linked list where this node is head.
Each of the sub-linked-list is in sorted order.
Flatten the Link List such that all the nodes appear in a single level while maintaining the sorted order.
Note: The flattened list will be printed using the bottom pointer instead of next pointer.

```java
class FlattenList
{
    // We can easily transform this to merge on next pointer instead of bottom pointer.
    Node flatten(Node root)
    {
        if(root == null) return null;

        // merge the bottom list on next pointer and current pointer from back
        root.next = flatten(root.next);
        root = merge(root, root.next);

        return root;
    }

    // this method is simply merging two linked list using bottom pointers
    Node merge(Node root, Node next) {
        if(next == null) return root;
        if(root == null) return next;

        Node result = null;
        if(root.data <= next.data) {
            result = root;
            result.bottom = merge(root.bottom, next);
        } else {
            result = next;
            result.bottom = merge(root, next.bottom);
        }

        return result;
    }

    void print(Node root) {
        while(root != null) {
            System.out.print(root.data + " ");
            root = root.bottom;
        }
        System.out.println();
    }
}
```

---

#### [Convert Binary Search tree to sorted doubly linked list](https://www.careercup.com/question?id=4863668900593664)

Given a binary search tree (BST), write a mehtod that will convert this BST into a doubly linked list that is sorted (ascending or descending order) and returns the first element in this list. You may assume you are given following Node class:

```sh
public class Node {
    public Node left, right;
    public String val;
}
Example: The following BST
G
/ \
A T
can be converted into a list
A = G = T

Do it in place! Hnce the memory complexity of your algorithm shoul be O(1).
```

```cpp
ITNode *convertBSTToSortedList(ITNode *root) {
    if(!root) return root;

    static ITNode *next = nullptr;
    convertBSTToSortedList(root->right);

    root->right = next;
    if(next) next->left = root;
    next = root;

    convertBSTToSortedList(root->left);

    return next;
}
```

---

#### [Pairwise swap elements of a linked list ](https://practice.geeksforgeeks.org/problems/pairwise-swap-elements-of-a-linked-list-by-swapping-data/1)

Given a singly linked list of size N. The task is to swap elements in the linked list pairwise.
For example, if the input list is 1 2 3 4, the resulting list after swaps will be 2 1 4 3.
Note: You need to swap the nodes, not only the data. If only data is swapped then driver will print -1.

```cpp
Node* pairWiseSwap(struct Node* head)
{
    Node *prev = nullptr;
    Node *newHead = head;
    while(head && head->next) {
        // store next and nextnext pointers
        auto nextnext = head->next->next;
        auto next = head->next;

        // reverse link (1->2 to 2->1)
        head->next = nullptr;
        next->next = head;

        // connect reversed links to nextnext link
        head->next = nextnext;

        // correct head if required (its always need for n >= 2)
        if(!prev) newHead = next;
        else prev->next = next;

        // move to next iteration
        prev = head;
        head = nextnext;
    }

    return newHead;
}
```

---

#### [Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

Given the root of a binary tree, flatten the tree into a "linked list":

The "linked list" should use the same TreeNode class where the right child pointer points to the next node in the list and the left child pointer is always null.
The "linked list" should be in the same order as a pre-order traversal of the binary tree.

```sh
Input: root = [1,2,5,3,4,null,6]
Output: [1,null,2,null,3,null,4,null,5,null,6]
```

```cpp
void flattenBinaryTree(TreeNode* root) {
    TreeNode* prev = NULL;
    flattenBinaryTree(root, prev);
}

void flattenBinaryTree(TreeNode* root, TreeNode* &prev)
{
    if(!root) return;

    TreeNode* left = root->left;
    TreeNode* right = root->right;

    if(prev) {
        prev->right = root;
        prev->left = nullptr;
    }
    prev = root;

    flattenBinaryTree(left, curr);
    flattenBinaryTree(right, curr);
}
```

**_Flatten binary tree second method_**

```cpp
TreeNode* flattenInternal(TreeNode* node) {
    if (!node) return node;

    TreeNode* left = flattenInternal(node->left);
    TreeNode* right = flattenInternal(node->right);

    node->left = nullptr;
    node->right = left;

    getRightMostNode(node)->right = right;

    return node;
}

TreeNode* getRightMostNode(TreeNode* node) {
    while (node->right) {
        node = node->right;
    }
    return node;
}
```

---
