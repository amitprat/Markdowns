#### [Delete a node from BST](https://practice.geeksforgeeks.org/problems/delete-a-node-from-bst/1)

Given a Binary Search Tree and a node value X. Delete the node with the given value X from the BST. If no node with value x exists, then do not make any change.

```cpp
Node *minNode(Node *root) {
    while(root->left) {
        root = root->left;
    }

    return root;
}

Node *deleteNode(Node *root, int X) {
    if(!root) return root;
    if(root->data == X) {
        if(!root->left || !root->right) {
            auto next = root->left ? root->left :root->right;
            delete root;
            root = next;
        } else {
            auto suc = minNode(root->right);
            root->data = suc->data;

            root->right = deleteNode(root->right, suc->data);
        }
    } else if(X < root->data) {
        root->left = deleteNode(root->left, X);
    } else {
        root->right = deleteNode(root->right, X);
    }

    return root;
}
```

---

#### [Longest consecutive sequence in Binary tree](https://practice.geeksforgeeks.org/problems/longest-consecutive-sequence-in-binary-tree/1)

Given a Binary Tree find the length of the longest path which comprises of connected nodes with consecutive values in increasing order.

**NOTE** : This only considers the paths starting from root.

```cpp
int longestConsecutiveSeq(Node *root) {
    int longest = 0;
    findLongest(root, INT_MIN, 0, longest);

    return longest;
}

void findLongest(Node *root, int prev, int curLongest, int& longest) {
    if(!root) return;

    if(prev+1 == root->data) curLongest++;
    else curLongest = 1;

    longest = max(longest, curLongest);

    findLongest(root->left, cur->data, curLongest, longest);
    findLongest(root->right, cur->data, curLongest, longest);
}
```

#### [Maximum Path Sum Between 2 Leaf Nodes](https://practice.geeksforgeeks.org/problems/maximum-path-sum/1)

Given a binary tree in which each node element contains a number. Find the maximum possible path sum from one leaf node to another leaf node.

Note: Here Leaf node is a node which is connected to exactly one different node.

```cpp
int maxPathSum(Node* root)
{
    if(!root) return 0;

    int mxSum = INT_MIN;
    int res = maxPathSum(root, mxSum);

    // we need to consider this because mx has't been updated yet
    if(!root->left || !root->right) {
        mxSum = max(mxSum, res);
    }

    return mxSum;
}

int maxPathSum(Node *root, int& sum) {
    if(!root) return 0;

    int ls = maxPathSum(root->left, sum);
    int rs = maxPathSum(root->right, sum);

    if(root->left && root->right) {
        sum = max(sum, root->data + ls + rs);
        return root->data + max(ls, rs);
    }

    return root->data + (ls ? ls : rs);
}
```

---

#### [Check if tree is red-black balanced]()

```cpp
bool isRedBlackBalanced(Node *root) {
    int mxH = 0, mnH = 0;

    return isRedBlackBalanced(root, mnH, mxH);
}

bool isRedBlackBalanced(Node *root, int& mnH, int& mxH) {
    if(!root) {mnH = mxH = 0; return true;}

    int leftMin, leftMax, rightMin, rightMax;

    if(!isRedBlackBalanced(root->left, leftMin, leftMax)) return false;
    if(!isRedBlackBalanced(root->right, rightMin, rightMax)) return false;

    mxH = 1 + max(leftMax, rightMax);
    mnH = 1 + min(leftMin, rightMin);

    if(mxH >= 2*mnH) return false;

    return true;
}
```

#### [Construct Tree from Preorder and Inorder traversal]()

```cpp
    static void test(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int, int> map;
        for (int i = 0; i < inorder.size(); i++) {
            map[inorder[i]] = i;
        }

        ITNode* cur = constructTree(preorder, inorder, 0, inorder.size() - 1, map);
        cout << to_string(cur) << endl;
    }

    ITNode* constructTree(vector<int>& preorder, vector<int>& inorder, int l, int r, unordered_map<int, int>& map)
    {
        if (l > r) return nullptr;

        static int index = 0;
        ITNode* root = new ITNode(preorder[index++]);
        if (l == r) return root;

        int pos = map[root->val];

        root->left = constructTree(preorder, inorder, l, pos - 1, map);
        root->right = constructTree(preorder, inorder, pos + 1, r, map);

        return root;
    }
```

---

#### [Kth smallest/largest element in a bst]()

**Finding kth smallest/largest element using recursion**

```cpp
int getKthSmallest(Tree<int>::Node* root, int& k) {
    if (root == nullptr) return INT_MIN;
    auto left = getKthSmallest(root->left, k);
    if (left != INT_MIN) return left;
    k--;
    if (k == 0) return root->val;
    return getKthSmallest(root->right, k);
}
```

**Finding kth smallest/largest element using recursion with tree having size**

```cpp
int getKthSmallestOrder(Tree<int>::Node* root, int k) {
    if (root == nullptr) return INT_MIN;
    int cur = size(root->left) + 1;
    if (cur == k) return root->val;
    if (k < cur) return getKthSmallestOrder(root->left, k);
    return getKthSmallestOrder(root->right, k - cur);
}

int size(Tree<int>::Node* root) {
    if (root == nullptr) return 0;
    return root->sz;
}
```

**Finding kth smallest/largest element iteratively**

```cpp
int getKthSmallestIter(Tree<int>::Node* root, int k) {
    stack< Tree<int>::Node*> st;
    while (root || !st.empty()) {
        while (root) {
            st.push(root);
            root = root->left;
        }
        root = st.top(); st.pop();
        k--;
        if (k == 0) return root->val;
        root = root->right;
    }
    return -1;
}
```

---

#### [Closest value in binary search tree(BST)](https://www.careercup.com/question?id=12697664)

Given (i) a non-empty binary search tree with double values (e.g. 3.5) in each node and (ii) a key value K.
Write a method to find the closest value to K.

```cpp
double kClosest(ITNode* node, double val) {
    if (!node) return 0;

    auto left = kClosest(node->left, val);
    auto right = kClosest(node->right, val);

    auto leftDiff = abs(val - left);
    auto rightDiff = abs(val - right);
    auto curDiff = abs(val - node->val);

    if (curDiff <= leftDiff && curDiff <= rightDiff) return node->val;
    else if (leftDiff <= curDiff && leftDiff <= rightDiff) return left;
    else return right;
}
```

---

#### [Find pairs with given sum in BST]()

Given a BST and a value x. Find two nodes in the tree whose sum is equal x. Additional space: O(height of the tree). It is not allowed to modify the tree

**_Using Set Method_**

```cpp
vector<pair<int, int>> findPairUsingSet(Node* root, int target) {
    unordered_set<int> set;
    vector<pair<int, int>> res;

    findPairUsingSet(root, target, set, res);

    return res;
}
void findPairUsingSet(Node* root, int target, unordered_set<int>& set, vector<pair<int, int>>& res) {
    if (root) {
        findPairUsingSet(root->left, target, set, res);
        if (set.find(target - root->val) != set.end()) {
            res.push_back({ target - root->val, root->val });
        }
        set.insert(root->val);
        findPairUsingSet(root->right, target, set, res);
    }
}
```

**_Convert BST to DLL and search using 2 pointers_**

```cpp
vector<pair<int, int>> findPairUsingDLL(Node* root, int target) {
    auto res = convertToDLL(root);
    vector<pair<int, int>> result;
    while (res.first != res.second && res.first->val < res.second->val) {
        auto cur = res.first->val + res.second->val;
        if (cur == target) {
            result.push_back({ res.first->val , res.second->val });
            res.first = res.first->right;
            res.second = res.second->left;
        }
        else if (cur < target) res.first = res.first->right;
        else res.second = res.second->left;
    }
    return result;
}

    pair<Node*, Node*> convertToDLL(Node* root) {
    Node* prev = nullptr;
    Node* first = nullptr, * last = nullptr;
    convertToDLL(root, prev, first, last);

    return { first, last };
}

void convertToDLL(Node* root, Node*& prev, Node*& first, Node*& last) {
    if (root) {
        convertToDLL(root->left, prev, first, last);

        Node* dllNode = new Node(root->val);
        if (prev == nullptr) first = dllNode;
        else {
            dllNode->left = prev;
            prev->right = dllNode;
        }
        prev = last = dllNode;

        convertToDLL(root->right, prev, first, last);
    }
}
```

**_Using 2 stacks to iterate using 2 pointers_**

```cpp
vector<pair<int, int>> findPairsUsingStack(Node* root, int target) {
    stack<Node *> leftSt, rightSt;
    Node* leftNode = root, * rightNode = root;

    vector<pair<int, int>> result;
    while (true) {
        while (leftNode) { leftSt.push(leftNode); leftNode = leftNode->left; }
        while (rightNode) { rightSt.push(rightNode); rightNode = rightNode->right; }
        if (leftSt.empty() || rightSt.empty()) break;

        leftNode = leftSt.top(); leftSt.pop();
        rightNode = rightSt.top(); rightSt.pop();

        if (leftNode->val > rightNode->val || leftNode == rightNode) break;

        int sum = leftNode->val + rightNode->val;
        if (sum == target) {
            result.push_back({ leftNode->val , rightNode->val });
            leftNode = leftNode->right;
            rightNode = rightNode->left;
        }
        else if (sum < target) {
            leftNode = leftNode->right;
            rightSt.push(rightNode);
            rightNode = nullptr;
        }
        else {
            rightNode = rightNode->left;
            leftSt.push(leftNode);
            leftNode = nullptr;
        }
    }

    return result;
}
```

---

#### [Huffman Encoding Decoding]()

Huffman coding is a lossless data compression algorithm. The idea is to assign variable-length codes to input characters, lengths of the assigned codes are based on the frequencies of corresponding characters. The most frequent character gets the smallest code and the least frequent character gets the largest code.
The variable-length codes assigned to input characters are Prefix Codes, means the codes (bit sequences) are assigned in such a way that the code assigned to one character is not the prefix of code assigned to any other character. This is how Huffman Coding makes sure that there is no ambiguity when decoding the generated bitstream.
Let us understand prefix codes with a counter example. Let there be four characters a, b, c and d, and their corresponding variable length codes be 00, 01, 0 and 1. This coding leads to ambiguity because code assigned to c is the prefix of codes assigned to a and b. If the compressed bit stream is 0001, the de-compressed output may be “cccd” or “ccb” or “acd” or “ab”.
See this for applications of Huffman Coding.
There are mainly two major parts in Huffman Coding

Build a Huffman Tree from input characters.
Traverse the Huffman Tree and assign codes to characters.

**_Data structure_**

```cpp
struct TreeNode {
    char ch;
    TreeNode *left, *right;

    TreeNode(char ch): ch(ch) {}
};
```

**_Encode_**

```cpp
string encode(string str, unordered_map<char, string>& codes) {
    unordered_map<char, int> freq;
    for(auto ch : str) freq[ch]++;

    using Node = pair<int,TreeNode *>;
    priority_queue<Node, vector<Node>, greater<Node>> pq;
    for(auto e : freq) {
        pq.push({e.second, new TreeNode(e.first)});
    }

    while(pq.size() > 1) {
        auto left = pq.top(); pq.pop();
        auto right = pq.top(); pq.top();

        int freq = left.first + right.first;
        TreeNode *parent = new TreeNode('$');
        parent->left = left.second;
        parent->right = right;

        pq.push({freq, parent});
    }

    auto root = pq.top(); pq.pop();

    traverseTree(root, "", codes);

    string encoded;
    for(auto ch : str) {
        encoded += codes[ch];
    }

    return encoded;
}

void traverseTree(TreeNode *root, string cur, unordered_map<char, string>& codes) {
    if(!root) return;
    if(isLeaf(root)) {
        codes[root->ch] = cur;
        return;
    }

    traverseTree(root->left, cur + "0", codes);
    traverseTree(root->right, cur + "1", codes);
}
```

**_Decode_**

```cpp
string decode(TreeNode *root, string encodedStr) {
    string result;

    TreeNode *cur = root;
    int pos = 0;
    while(pos < encodedStr.length() && cur) {
        if(isLeaf(cur)) {
            result += cur->ch;
            cur = root;
        }

        char ch = encodedStr[pos]++;
        cur = (ch == '0' ? cur->left : cur->right);
    }

    return result;
}
```

```cpp
string decode(unordered_map<char, string> codes, string encodedStr) {
    string result;

    unordered_map<string, char> revCodeMap;
    for (auto e : codes) revCodeMap[e.second] = e.first;

    string cur;
    for (char ch : encodedStr) {
        cur += ch;
        if (revCodeMap.find(cur) != revCodeMap.end()) {
            result += revCodeMap[cur];
            cur = "";
        }
    }

    return result;
}
```

---

#### [Minimum distance to leaf node from given node](https://www.careercup.com/question?id=6040159610994688)

The node can be in down path or anywhere on its parent node.

```cpp
static pair<int, int> getMinimumDistanceToLeafNode(ITNode* root, ITNode* target, int& minDistance)
{
    if (!root) return { INT_MAX, 0 }; // {minDistanceToTargetDownwards, minDistanceToLeafNodeDownwards}

    auto left = getMinimumDistanceToLeafNode(root->left, target, minDistance);
    auto right = getMinimumDistanceToLeafNode(root->right, target, minDistance);

    bool isOnLeft = false, isOnRight = false;
    if (left.first != INT_MAX) isOnLeft = true;
    else if (right.first != INT_MAX) isOnRight = true;

    int distanceToTarget = INT_MAX;
    if (root == target) {
        // if the node is same as target node, then the minimum distance must be min(leftDistance, rightDistance)
        int curMin = 1 + min(left.second, right.second);
        minDistance = min(minDistance, curMin);
        distanceToTarget = 1;
    }
    else if (isOnLeft && left.first + 1 + right.second < minDistance) {
        // if the targetNode is on left, the minDistance is min of old min distance and (distance from current node to target node on left + 1(root) + distance on right)
        int curMin = left.first + 1 + right.second;
        distanceToTarget = left.first + 1;
        minDistance = min(minDistance, curMin);
    }
    else if (isOnRight && right.first + 1 + left.second < minDistance) {
        int curMin = right.first + 1 + left.second;
        distanceToTarget = right.first + 1;
        minDistance = min(minDistance, curMin);
    }

    return { distanceToTarget, 1 + min(left.second, right.second) };
}
```

---
