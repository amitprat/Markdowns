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
