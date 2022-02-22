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

#### [Construct Binary Search Tree from given Preorder traversal](https://www.careercup.com/question?id=14419694)

Given preorder traversal array of a BST, recontruct the BST.

This can be solved in O(n) only. The approach am using is, keep a separate stack of node pointers. Push on the 1st node. Keep on traversing the preorder traversal

1. if the value of stack top is more than the current node value, then make the current node left pointer of stack top.
2. if the value of stack top is less that current node value, keep popping from the stack till value of stack top is more than current node. Then make current node the right child of last popped element.
   Push the current node on stack in both the cases.

It may seem like an O(n^2) algo, but we are pushing and popping every element on stack only once so this is O(n) time and space.

```cpp
static Node* constructBST(vector<int>& preorder) {
    if (preorder.empty()) return nullptr;
    Node* root = new Node(preorder[0]);

    stack<Node*> st;
    st.push(root);

    for (int i = 1; i < preorder.size(); i++) {
        auto newNode = new Node(preorder[i]);
        if (newNode->val < st.top()->val) {
            st.top()->left = newNode;
        }
        else {
            Node* prev = nullptr;
            while (!st.empty() && st.top()->val < newNode->val) {
                prev = st.top(); st.pop();
            }
            prev->right = newNode;
        }
        st.push(newNode);
    }

    return root;
}
```

---

#### [Serialize/Deserialize Binary Tree]()

**_Serialize Tree Using Preorder Traversal_**

```cpp
string serialize(ITNode* node)
{
    if (!node) return "#";

    string result = std::to_string(node->val);
    result += " " + serialize(node->left);
    result += " " + serialize(node->right);

    return result;
}
```

```cpp
ITNode* deserialize(string serialized)
{
    vector<string> items;
    string word;
    stringstream ss(serialized);
    while (ss >> word) items.push_back(word);

    int index = 0;
    return deserialize(items, index, "#");
}

ITNode* deserialize(vector<string>& items, int& index, string marker)
{
    if (index >= items.size()) return nullptr;
    if (items[index] == marker) {
        index++;  return nullptr;
    }

    ITNode* root = new ITNode(stoi(items[index++]));
    root->left = deserialize(items, index, marker);
    root->right = deserialize(items, index, marker);

    return root;
}
```

```cpp
ITNode* deserialize2(string serialized)
{
    stringstream ss(serialized);

    int index = 0;
    return deserialize2(ss, "#");
}

ITNode* deserialize2(stringstream& ss, string marker)
{
    string word;
    if (!(ss >> word)) return nullptr;
    if (word == marker) return nullptr;

    ITNode* root = new ITNode(stoi(word));

    root->left = deserialize2(ss, marker);
    root->right = deserialize2(ss, marker);

    return root;
}
```

```cpp
ITNode* deserialize3(string& serialized) {
    stringstream ss(serialized);
    char delim = ',';

    string marker = "#";
    ITNode* root = deserialize3(ss, delim, marker);
    return root;
}

ITNode* deserialize3(stringstream& ss, char delim, string& marker) {
    string cur;
    if (!getline(ss, cur, delim)) return nullptr;
    if (cur == marker) return nullptr;

    ITNode* node = new ITNode(stoi(cur));
    node->left = deserialize3(ss, delim, marker);
    node->right = deserialize3(ss, delim, marker);

    return node;
}
```

**_Serialize Tree Using Level Order Traversal_**

```cpp
string serialize(ITNode* root) {
    string serialized;

    queue<ITNode*> q;
    q.push(root);

    while (!q.empty()) {
        auto cur = q.front(); q.pop();
        if (cur == nullptr) serialized = serialized + "," + "#";
        else serialized += "," + std::to_string(cur->val);

        if (cur) {
            q.push(cur->left);
            q.push(cur->right);
        }
    }

    if (!serialized.empty()) serialized.erase(0, 1);

    return serialized;
}

ITNode* deserialize(string& serialized) {
    stringstream ss(serialized);
    char delim = ',';
    int index = 0;

    string cur;
    if (!getline(ss, cur, delim) || cur == "#") return nullptr;

    queue<ITNode*> q;
    q.push(new ITNode(stoi(cur)));
    ITNode* root = q.front();

    while (!q.empty()) {
        auto curNode = q.front(); q.pop();

        if (getline(ss, cur, delim) && cur != "#") curNode->left = new ITNode(stoi(cur));
        if (getline(ss, cur, delim) && cur != "#") curNode->right = new ITNode(stoi(cur));

        if (curNode->left) q.push(curNode->left);
        if (curNode->right) q.push(curNode->right);
    }

    return root;
}
```

---

#### [Serialize/Deserialize Binary Search Tree]()

```cpp
string serialize(ITNode* node)
{
    if (!node) return "";

    string result = std::to_string(node->val);
    string left = serialize(node->left);
    if (!left.empty()) result += " " + left;

    string right = serialize(node->right);
    if (!right.empty()) result += " " + right;

    return result;
}
```

```cpp
ITNode* deserialize(string serialized)
{
    stringstream ss(serialized);
    int val;
    vector<int> items;
    while (ss >> val) { items.push_back(val); }
    int index = 0;
    return deserialize(items, index, INT_MIN, INT_MAX);
}

ITNode* deserialize(vector<int> items, int& index, int mn, int mx)
{
    if (index >= items.size()) return nullptr;
    if (items[index] <= mn || items[index] >= mx) return nullptr;

    ITNode* root = new ITNode(items[index++]);
    root->left = deserialize(items, index, mn, root->val);
    root->right = deserialize(items, index, root->val, mx);

    return root;
}
```

```cpp
ITNode* deserialize2(string serialized)
{
    stringstream ss(serialized);
    int val;
    if (!(ss >> val)) return nullptr;

    return deserialize2(ss, val, INT_MIN, INT_MAX);
}

ITNode* deserialize2(stringstream& ss, int& val, int mn, int mx)
{
    if (val < mn || val > mx) return nullptr;

    ITNode* root = new ITNode(val);
    if (!(ss >> val)) return root;

    root->left = deserialize2(ss, val, mn, root->val);
    root->right = deserialize2(ss, val, root->val, mx);

    return root;
}
```

---

#### [Serialize/Deserialize Nary Tree]()

```cpp
void Serialize(NaryTreeNode* node, vector<char>& serializedTree)
{
    if (node == nullptr) {
        serializedTree.push_back('\0');
        return;
    }

    serializedTree.push_back(node->val + '0');
    for (auto& child : node->children) {
        Serialize(child, serializedTree);
    }
}

NaryTreeNode* Deserialize(vector<char>& serializedTree, int& index)
{
    if (index >= serializedTree.size()) return nullptr;
    if (serializedTree[index] == '\0') { index++; return nullptr; }

    NaryTreeNode* curRoot = new NaryTreeNode({ serializedTree[index++] - '0' });
    while (true) {
        auto nextChild = Deserialize(serializedTree, index);
        if (nextChild == nullptr) break;
        curRoot->children.push_back(nextChild);
    }

    return curRoot;
}
```

---

#### [Replace Nodes values with greater value]()

In a BST, I want to replace all nodes with value which is the sum of all the nodes which are greater than equal to the current node.
5
2 10

Output -->
15
17 10

```cpp
static void updateTree(ITNode* root, int& sum) {
    if (root != nullptr) {
        updateTree(root->right, sum);
        sum += root->val;
        root->val = sum;
        updateTree(root->left, sum);
    }
}
```

---

#### [Mirror Binary Tree](https://practice.geeksforgeeks.org/problems/mirror-tree/1)

Given a Binary Tree, convert it into its mirror.

**_Mirror tree with a copy_**

```cpp
ITNode* mirror(ITNode* node)
{
    if (node == nullptr) return nullptr;
    ITNode* newNode = new ITNode(node->val);
    newNode->right = mirror(node->left);
    newNode->left = mirror(node->right);

    return newNode;
}
```

**_Inplace mirror_**

```cpp
void inplaceMirror(ITNode*& node)
{
    if (node == nullptr) return;
    swap(node->left, node->right);
    inplaceMirror(node->left);
    inplaceMirror(node->right);
}
```

**_Check if a tree is mirror of other_**

```cpp
bool isMirror(ITNode* originalNode, ITNode* mirrorNode)
{
    if (originalNode == nullptr && mirrorNode == nullptr) return true;
    if (originalNode == nullptr || mirrorNode == nullptr) return false;

    return originalNode->val == mirrorNode->val &&
        isMirror(originalNode->left, mirrorNode->right) &&
        isMirror(originalNode->right, mirrorNode->left);
}
```

---

#### [Binry Search Tree Iterator]()

**TODO**

```cpp
class BinarySearchTreeIterator {
    BinaryTreeNode<int> *root = nullptr;

    class Iterator {

    };

    Iterator begin() {
        return Iterator(root);
    }

    Iterator end() {
        return Iterator(nullptr);
    }
};
```

---

#### [Copy Tree]()

```cpp
Node *copyTree(Node *root) {
    if(!root) return root;

    Node *newRoot = new Node(root);
    newRoot->left = copyTree(root->left);
    newRoot->right = copyTree(root->right);

    return newRoot;
}

Node *deleteTree(Node *root) {
    if(!root) return root;

    root->left = deleteTree(root->left);
    root->right = deleteTree(root->right);

    delete root;
    root = nullptr;

    return root;
}
```

---

#### [Diameter of Binary tree](https://www.geeksforgeeks.org/diameter-n-ary-tree/)

```cpp
pair<int, int> diameter(Node *root) {
    if(!root) return {0, 0}; // for end node, both {diameter=0, height = 0}

    auto left = diameter(root->left);
    auto right = diameter(root->right);

    // max diameter is max of existing diameter or cur diameter which is sum of left height + right height + root node
    int curDiameter = left->second + right->second + 1;
    int mxDiameter = max(max(left->first, right->second), curDiameter);

    // return {mxDiameter, mxHeight including this node}
    return {mxDiameter, 1 + max(left->second, right->second)};
}
```

---

#### [Check if tree is valid]()

Given a list of tree nodes, validate following:

1. List contains all nodes associated with a tree.
2. This is indeed a tree. (not a graph)
3. There are no cycles

```cpp
// input can come in any order
bool isValidTree(vector<Node *> nodes) {
    unordered_set<Node *> set;
    for(auto node : nodes) set.insert(node);

    // check if all tree nodes are provided in the list. If missing node found, return false.
    // also populate the indegree for each vertex
    unordered_map<Node *, vector<Node *>> indegreeVertices;
    for(auto node : nodes) {
        if(node->left) {
            if(set.find(node->left) == set.end()) {
                cout<<"Missing node in list"<<endl;
                return false;
            }
            indegreeVertices[node->left].push_back(node);
        }

        if(node->right) {
            if(set.find(node->right) == set.end()) {
                cout<<"Missing node in list"<<endl;
                return false;
            }
            indegreeVertices[node->right].push_back(node);
        }
    }

    // check if there are no more than one root node
    int rootCount = 0;
    for(auto node : nodes) {
        // there must be only one root, i.e. only one vertices which can't be reached(has no incoming edges)
        if(indegreeVertices.find(node) == indegreeVertices.end()) {
            rootCount++;
        }

        if (rootCount > 1) {
            cout << "Dangling Node" << endl;
            return false;
        }
    }

    // check that any vertex doesn't has more than 1 incoming edge (i.e. doesn't form cycle)
    for(auto e : indegreeVertices) {
        if(e.size() > 1) {
            cout << "Found circle" << endl;
            return false;
        }
    }

    // its a valid tree
    return true;
}
```

---

#### [Rank of an element in Binary Search Tree]()

```cpp
BstNode* insertInternal(BstNode* cur, int val) {
    if (cur == nullptr) return new BstNode(val);

    cur->size++;
    if (val <= cur->val) cur->left = insertInternal(cur->left, val);
    else cur->right = insertInternal(cur->right, val);

    return cur;
}

int rank(BstNode* cur, int elem) {
    if (cur == nullptr) return -1;
    if (cur->val == elem) return 1 + size(cur->left);
    if (elem < cur->val) return rank(cur->left, elem);
    else {
        int right = rank(cur->right, elem);
        if (right == -1) return right;
        return 1 + size(cur->left) + right;
    }
}
```

---

#### [Count paths with given sum in tree](https://leetcode.com/problems/path-sum-iii/submissions/)

Given the root of a binary tree and an integer targetSum, return the number of paths where the sum of the values along the path equals targetSum.

The path does not need to start or end at the root or a leaf, but it must go downwards (i.e., traveling only from parent nodes to child nodes).

```cpp
int pathSum(TreeNode* root, int targetSum) {
    unordered_map<int, int> freq;
    return countPaths(root, targetSum, 0, freq);
}

int countPaths(TreeNode* root, int sum, int cur, unordered_map<int, int>& freq) {
    if (!root) return 0;

    cur += root->val;
    int result = freq[cur - sum];
    if (cur == sum) result++;
    freq[cur]++;

    result += countPaths(root->left, sum, cur, freq);
    result += countPaths(root->right, sum, cur, freq);

    freq[cur]--;

    return result;
}
```

---

#### [Least Common Ancestor]()

**_Doesn't handle non existing case_**

```cpp
BinaryTreeNode<int>* findSimpleLCA(BinaryTreeNode<int>* root, int node1, int node2) {
    if (!root) return nullptr;

    if (root->val == node1 || root->val == node2) return root;

    auto left = findSimpleLCA(root->left, node1, node2);
    auto right = findSimpleLCA(root->right, node1, node2);

    if (left && right) return root;

    return left ? left : right;
}
```

**_handles the existing case_**

```cpp
BinaryTreeNode<int>* findLCA(BinaryTreeNode<int>* root, int node1, int node2) {
    BinaryTreeNode<int>* left = nullptr;
    BinaryTreeNode<int>* right = nullptr;

    auto* node = findLCA(root, node1, node2, left, right);
    if (left && right) return node;

    return nullptr;
}

BinaryTreeNode<int>* findLCA(BinaryTreeNode<int>* root, int node1, int node2, BinaryTreeNode<int>*& node1Node, BinaryTreeNode<int>*& node2Node) {
    if (root == nullptr) return root;

    bool found = false;
    if (root->val == node1) {
        node1Node = root;
        found = true;
    }
    if (root->val == node2) {
        node2Node = root;
        found = true;
    }

    auto* left = findLCA(root->left, node1, node2, node1Node, node2Node);
    auto* right = findLCA(root->right, node1, node2, node1Node, node2Node);

    if (left && right || found) return root;
    return left ? left : right;
}
```

---

#### [Kth Smallest Element in Binary Search Tree]()

```cpp
int kthSmallestElement(Node *root, int& k) {
    if(!root) return INT_MIN;

    int left = kthSmallestElement(root->left, k);
    if(left != INT_MIN) return left;

    k--;
    if(k == 0) return root->val;

    return kthSmallestElement(root->right, k);
}
```

**_Kth Smallest element using ranking_**

```cpp
int kthSmallestElement(Node *root, int k) {
    if(!root) return INT_MIN;

    int sz = 1 + size(root->left);
    if(sz == k) return root->val;

    if(k < sz) return kthSmallestElement(root->left, k);
    else return kthSmallestElement(root->right, k-sz);
}
```

**_Get kth smallest element using bst iterator_**

```cpp
int kthSmallestElement(Node *root, int k) {
    stack<Node *> st;

    while(root || !st.empty()) {
        while(root) {
            st.push(root);
            root = root->left;
        }
        root = st.top(); st.pop();

        k--;
        if(k == 0) return root->val;

        root = root->right;
    }

    return INT_MAX;
}
```

---

#### [Flatten a tree into its preorder format](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

Given the root of a binary tree, flatten the tree into a "linked list":

The "linked list" should use the same TreeNode class where the right child pointer points to the next node in the list and the left child pointer is always null.
The "linked list" should be in the same order as a pre-order traversal of the binary tree.

```cpp
static ITNode* flatten1(ITNode* root) {
    ITNode* curr = nullptr;
    flatten1Internal(root, curr);

    return root;
}

static void flatten1Internal(ITNode* root, ITNode*& curr) {
    if (!root) return;

    auto left = root->left;
    auto right = root->right;

    if (curr) {
        curr->right = root;
        curr->left = nullptr;
    }
    curr = root;

    flatten1Internal(left, curr);
    flatten1Internal(right, curr);
}
```

```cpp
static ITNode* flatten2(ITNode* root) {
    if (!root) return nullptr;

    auto left = flatten2(root->left);
    auto right = flatten2(root->right);

    root->left = nullptr;
    root->right = left;
    getRightMostNode(root)->right = right;

    return root;
}
```

```cpp
static ITNode* flatten3(ITNode* root) {
    if (!root) return root;

    ITNode* left = flatten3(root->left);
    ITNode* next = root->right;

    root->right = left;
    root->left = nullptr;

    ITNode* right = flatten3(next);
    getRightMostNode(root)->right = right;

    return root;
}

static ITNode* getRightMostNode(ITNode* root) {
    while (root && root->right) root = root->right;
    return root;
}
```

---

#### [Kth Smallest Element in BST/Rank of BST]()

```cpp
struct Node {
    int val;
    Node *left, *right;
    int sz;
    Node(int val) : val(val), left(nullptr), right(nullptr), sz(1) {}
};
Node *root = nullptr;

void insert(int val) {
    root = insert(root, val);
}

Node *insert(Node *node, int val) {
    if(!node) return new Node(val);

    node->sz++;
    if(val < node->val) node->left = insert(node->left, val);
    else node->right = insert(node->right, val);

    return node;
}

int findKthSmallestElement(int k) {
    return findKthSmallestElement(root, k);
}

int findKthSmallestElement(Node *node, int k) {
    if(!node) return -1;

    int curSize = size(left) + 1;
    if(curSize == k) return node->val;

    if(k < curSize) return findKthSmallestElement(node->left, k);
    else return findKthSmallestElement(node->right, k-curSize);
}

int size(Node *node) { return node ? node->sz : 0; };

int kthSmallestElementUsingInorderCounting(Node *node, int& k) {
    if(!node) return -1;

    auto left = kthSmallestElementUsingInorderCounting(node->left, k);
    if(left != -1) return left;

    k--;
    if(k == 0) return node-val;

    return kthSmallestElementUsingInorderCounting(node->right, k);
}
```

---
