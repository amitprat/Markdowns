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

#### [Flattening a linked list with child pointers](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)

```cpp
Node* flatten(Node* head) {
    if(!head) return head;

    auto next = flatten(head->next);
    auto child = flatten(head->child);

    if(child) {
        auto end = getEndNode(child);
        end->next = next;
        if(next) next->prev = end;
        next = child;
    }

    head->next = next;
    if(next) next->prev = head;
    head->child = nullptr;

    return head;
}

Node* getEndNode(Node *head) {
    while(head && head->next) head = head->next;
    return head;
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

#### [Cancel out list](https://careercup.com/question?id=5717797377146880)

Given some resources in the form of linked list you have to canceled out all the resources whose sum up to 0(Zero) and return the remaining list.

```sh
E.g-->> 6 -6 8 4 -12 9 8 -8

the above example lists which gets canceled :
6 -6
8 4 -12
8 -8
o/p : 9
case 3 : 4 6 -10 8 9 10 -19 10 -18 20 25
O/P : 20 25
```

```cpp
static void cancelOutList() {
    LinkedListNode<int>* node = new LinkedListNode<int>(6);
    node->next = new LinkedListNode<int>(-6);
    node->next->next = new LinkedListNode<int>(8);
    node->next->next->next = new LinkedListNode<int>(4);
    node->next->next->next->next = new LinkedListNode<int>(-12);
    node->next->next->next->next->next = new LinkedListNode<int>(9);
    node->next->next->next->next->next->next = new LinkedListNode<int>(8);
    node->next->next->next->next->next->next->next = new LinkedListNode<int>(-8);

    auto newHead = cancelOutList(node);
    cout<<to_string(newHead)<<endl;
}

static void cancelOutList(LinkedListNode<int>* node) {
    unordered_set<int> visited;
    LinkedListNode<int>* newHead = node;
    stack<LinkedListNode<int>*> st;

    int curSum = 0;
    visited.insert(curSum);

    while (node) {
        curSum += node->val;
        if (visited.find(curSum) == visited.end()) {
            visited.insert(curSum);
            st.push(node);
        } else {
            int tmp = curSum - node->val;
            do {
                if (tmp != 0) visited.erase(tmp);
                auto cur = st.top(); st.pop();
                tmp -= cur->val;
            } while (tmp != curSum);

            if (st.empty()) newHead = node->next;
            else st.top()->next = node->next;
        }
        node = node->next;
    }

    return newHead;
}
```

---

#### [Swap Kth Node from start and end in linked list](https://www.careercup.com/question?id=13575664)

Given a singly link list and a number 'K', swap the Kth node from the start with the Kth node from the last. Check all the edge cases.

Sample Input: 1->2->3->4->5->6->7->8 and K = 3
Sample Output : 1->2->6->4->5->3->7->8

Sample Input: 1->2->3->4->5->6->7->8 and K = 10
Sample Output: print error "LIST IS OF LESSER SIZE".

```cpp
void swapKThNodeFromStartEnd(Node *root, int k) {
    Node *start = root, *root = end;
    Node *prev1 = nullptr, *end = nullptr;

    for(int i=1;i<k;i++) {
        prev1 = start;
        start = start->next;

        if(!start) {
            cout<<"K is outside list range"<<endl;
            return root;
        }
    }

    Node *tmp = start->next;
    while(tmp) {
        tmp = tmp->next;
        prev2 = end;
        end = end->next;
    }

    if(prev1) prev1->next = end;
    else root = end;

    if(prev2) prev2->next = start;
    else root = start;

    swap(start->next, end->next);

    return root;
}
```

---

#### [Remove Duplicate Nodes from Sorted LinkedList]()

**_Remove All copies of duplicates if duplicates present_**

```cpp
Node *removeDups(Node *root) {
    Node *newNode = new Node(INT_MIN);
    newNode->next = root;

    Node *tmp = newNode;
    while(tmp && tmp->next) {
        if(tmp->next->next && tmp->next->val == tmp->next->next->val) {
            auto curVal = tmp->next->val;
            while(tmp->next && tmp->next->val == curVal) {
                auto next = tmp->next->next;
                delete tmp->next;
                tmp->next = next;
            }
        } else {
            tmp = tmp->next;
        }
    }

    return newNode->next;
}
```

**_Remove duplicates if present. Only keep one copy._**

```cpp
Node *removeDups(Node *root) {
    Node *tmp = root;

    while(tmp && tmp->next) {
        if(tmp->val == tmp->next->val) {
            auto next = tmp->next->next;
            delete tmp->next;
            tmp->next = next;
        } else {
            tmp = tmp->next;
        }
    }

    return root;
}
```

---

#### [Remove Duplicate Nodes from Unsorted LinkedList]()

**_Remove All copies of duplicates if duplicates present_**

```cpp
Node *removeAllFromUnsortedList(Node *root) {
    Node *newNode = new Node(INT_MIN);
    newNode->next = root;

    Node *tmp = newNode;
    while(tmp->next && tmp->next->next) {
        bool found = false;
        auto cur = tmp->next->val;

        auto runner = tmp->next;
        while(runner && runner->next) {
            if(runner->next->val == cur) {
                found = true;
                auto next = runner->next->next;
                delete runner->next;
                runner->next = next;
            } else {
                runner = runner->next;
            }
        }

        if(found) {
            auto next = tmp->next->next;
            delete tmp->next;
            tmp->next = next;
        } else {
            tmp = tmp->next;
        }
    }

    return newNode->next;
}
```

**_Remove duplicates if present. Only keep one copy._**

```cpp
Node *removeDupsFromUnsortedList(Node *root) {
    Node *tmp = root;
    while(tmp && tmp->next) {
        int candidateVal = tmp->val;

        auto* runner = tmp;
        while(runner->next) {
            if(runner->next->val == candidateVal) {
                auto next = runner->next->next;
                delete runner->next;
                runner->next = next;
            } else {
                runner = runner->next;
            }
        }

        tmp = tmp->next;
    }

    return root;
}
```

**_Remove Duplicated from list using extra space(set). Keep single copy._**

```cpp
Node *removeDupsFromUnsortedListUsingSet(Node *root) {
    if(!root || !root->next) return root;

    unordered_set<int> set;
    set.insert(root->val);
    Node *tmp = root;

    while(tmp->next) {
        if(set.find(tmp->next->val) == set.end()) {
            set.insert(tmp->next->val);
            tmp = tmp->next;
        } else {
            auto next = tmp->next->next;
            delete tmp->next;
            tmp->next = next;
        }
    }

    return root;
}
```

---

#### [Partition a Linked List]()

```cpp
void partitionList(Node *head, Node&first, Node &second) {
    if(!head) return;

    int val = head->val; // it could be custom value

    partitionList(head, first, second, val);
}

void partitionList(Node *head, Node&first, Node &second, int val) {
    first = new Node(INT_MIN), second = new Node(INT_MIN);
    Node *firstHead = first, *secondHead = second;

    Node *tmp = node;

    while(tmp) {
        if(tmp->val <= val) {
            first->next = tmp;
            first = first->next;
        } else {
            second->next = tmp;
            second = second->next;
        }

        tmp = tmp->next;
    }

    first->next = second->next = nullptr;
    first = firstHead->next, second = secondHead->next;

    delete firstHead;
    delete secondHead;
}
```

---

#### [Check if linked list is palindromic]()

```cpp
bool isPalin(Node *root) {
    Node *rev = reverse(root);

    while(root && rev && root->val == rev->val) {
        root = root->next;
        rev = rev->next;
    }

    return !root && !rev;
}
```

```cpp
// 1->2->3(s)->4->null(f)
// 1->2(s)->3(f)->null
bool isPalin(Node *root) {
    if(!root) return true;

    stack<Node*> st;
    st.push(root);

    Node *slow = root, *fast = root;
    while(fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;

        if(fast) st.push(slow);
    }

    while(slow) {
        if(st.empty() || slow->val != st.top().val) return false;
        slow = slow->next;
        st.pop();
    }

    return !slow && st.empty();
}
```

---

#### [Copy LinkedList With Random Pointers]()

```cpp
Node *copyList(Node *root) {
    unordered_map<Node *, Node *> map;
    Node *cur = root;
    Node *newCur = new Node(root->val);
    Node *newRoot = newCur;

    cur = cur->next;
    while(cur) {
        newCur->next = new Node(cur->val);
        newCur = newCur->next;

        map[cur] = newCur;

        cur = cur->next;
    }

    cur  = root;
    newCur = newRoot;

    while(cur) {
        newCur->random = map[cur->random];
        cur = cur->next;
        newCur = newCur->next;
    }

    return newRoot;
}
```

---
