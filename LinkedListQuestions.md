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
