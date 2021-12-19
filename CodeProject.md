####[If [a1,a2,a3...,an,b1,b2...bn] is given input change this to [a1,b1,a2,b2.....an,bn] , solution should be in-place]()

_First swap elements in the middle pair
Next swap elements in the middle two pairs
Next swap elements in the middle three pairs
iterate n-1 steps._

Ex: with n = 4.
a1 a2 a3 a4 b1 b2 b3 b4
a1 a2 a3 b1 a4 b2 b3 b4
a1 a2 b1 a3 b2 a4 b3 b4
a1 b1 a2 b2 a3 b3 a4 b4

```cpp
int interleave(int arr[], int len) {
  int n = len/2;
  for (int i = 1; i < n; i++) { // my step no.
    for (int j = 0; j < i; j++) { // no. of swaps
      swap(arr, n-i+2*j, n-i+2*j+1);
    }
  }
}
```

---

####[Given an int array without repeated elements and a target, count the total number of subset that can be generated from the array such that min (subset) + max (subset) < target](https://careercup.com/question?id=5091778836299776)

```java
public int countSubSet(int[] nums, int target){

}
```

_Sort the array, then use two pointers to count subsets._

```java
public static int countSubSet(int[] nums, int target){
    if(nums.length==0)
        return 0;

    Arrays.sort(nums);
    int subsets=0;
    int start=0, end=nums.length-1;

    while(start<=end){
        if(2*nums[start]>=target)
            return subsets;
        if(nums[start]+nums[end]<target){
            if(start==end)
                subsets=subsets+1;
            else
                subsets= subsets+(int)Math.pow(2,end-start-1);

            start++;
        }
        else
            end--;
    }

    return subsets;
}
```

---

####[Find subsets]()

_// For a given vector of integers and integer K, find the number of non-empty subsets S such that min(S) + max(S) <= K
// For example, for K = 8 and vector [2, 4, 5, 7], the solution is 5 ([2], [4], [2, 4], [2, 4, 5], [2, 5])
The time complexity should be O(n2). Approach and code was asked_

Can be solved using 2 Pointers technique. Complexity - O(n logn) for sorting. If the data is sorted then O(n)

```cpp
int findSubsets(vector<int>& arr) {
    sort(arr.begin(), arr.end());
    int p1 = 0, p2 = arr.size()-1;
    int ans = 0;

    while(p1<=p2)
    {
        if(arr[p1] + arr[p2] > k)
        {
            p2--;
        }
        else
        {
            ans = ans + 1<<(p2 -p1);
            p1++;
        }
    }

    return ans;
}
```

---

####[Dictionary with access to last element]()

_Write a new data structure, "Dictionary with Last"
Methods:
set(key, value) - adds an element to the dictionary
get(key) - returns the element
delete(key) - removes the element
last() - returns the last key that was added or read.
In case a key was removed, last will return the previous key in order._

```sh
Use a HashMap and a Doubly Linked List, where Hashmap would have a pointer to the list Node such as Map<Key, ListNode> and List would have ListNode(Value, Left, Right)
and the head of the list would be the most recent element added or read.

HEAD -> _ Most recently added or read
TAIL -> _ Least recently read or added, "oldest" element

For set(Key, value) check if key exists in HashMap, if so get ListNode and set new value make it HEAD, set left and right pointers accordingly. If not create new ListNode, set it as new HEAD and make HEAD -> right point to old HEAD.

For get(Key) check if key exists in HashMap, if so get ListNode and make it HEAD, set left and right pointers accordingly. Return value. If it does not exits return null or throw exception.

For delete(Key) check if key exists in HashMap, if so get ListNode and set left and right pointers accordingly eliminate this node and remove it from hashmap . Return value. If it does not exits return null or throw exception.

For last() check if list is empty, if not return HEAD, If empty return null.

Other solution

Self-Balancing Binary Tree: Red-Black Tree I think is the best choice because it gives you the best worst case (log).
Other options might be: AVL tree and Hash Tables... but, depends on what you want to do with this structure.
```

---

####[Given a array of integers , find 3 indexes i,j,k such that, i<j<k and a[i] < a[j] < a[k]. Best possible is a O(n) algorithm.]()

```sh
a[i]<a[j]<a[k] s.t i<j<k
From the original array build two arrays.
i) LMin[i] contains index of the min element seen so far from the left including a[i].
ii) RMax[i] contains index of the max element seen so far from the right including a[i].
consider the following array:
a =4,7,5,1,3,8,9,6,2
LMin=0,0,0,3,3,3,3,3,3
RMax=6,6,6,6,6,6,6,7,8
```

```cpp
void print(vector<int>& arr) {
    for(int i=0;i<n;i++) left[i] = i > 0 ? min(left[i-1],arr[i]) : arr[i];
    for(int i=n-1;i>=0;i--) right[i] = ...;

    for(int i=0;i<n;i++) {
        if(left[i] < arr[i] && right[i] > arr[i]) {
            cout<<left[i]<<" "<<arr[i]<<right[i]<<endl;
        }
    }
}
```

---

#### [Find min number of jumps it will take to reach to the top in snake and ladder game using recursive function]()

```cpp
int minJumpsTest() {
    int n = 5, m = 6;
    int end = n*m;
    unordered_map<int> jumps;
    jumps[5] = 12;
    ....

    int start = 1;
    unordered_set<int> visited;
    auto res = minJumpsDFS(start, end, jumps,visited);
}

int minJumpsDFS(int start, int end, unordered_map<int> jumps,unordered_set<int>& visited)
{
    if(start == end) return 0;
    visited[start] = true;
    int mn = INT_MAX;
    for(int k=start+1;k<=end && k<start+6;k++) {
        int target = k;
        if(jumps.find(k) != jumps.end()) target = jumps[k];
        if(!visited[target])
            mn = min(mn, 1+minJumps(target, end, jumps));
    }

    return mn;
}

int minJumpsBFS(int n, unordered_map<int> jumps)
{
    queue<pair<int,int>> q;
    q.push({1, 0});
    unordered_set<int> visited;
    visited.insert(1);

    while(!q.empty()) {
        auto f = q.front(); q.pop();
        if(f.first == n) return f.second;

        for(int k=f.first+1;k<=n && k<f.first+6;k++) {
            int target = k;
            if(jumps.find(k) != jumps.end()) target = jumps[k];

            if(!visited[target]) {
                q.push({target, f.second+1});
                visited[target] = true;
            }
        }
    }
}
```

---

#### [Given an array of n integers, find the lexicographically smallest subsequence of length k.]()

I feel this question can be solved by stack. once we found previous element in the stack is larger than current element, than pop out

```cpp
// Exponential time
void smallestSubsequence(vector<int> nums, int index, string& smallest, string cur, int k)
{
    if(k == 0) {
        if(cur < smallest) smallest = cur;
        return;
    }
    if(index >= nums.size()) return;

    smallestSubsequence(nums, index+1, smallest, cur + to_string(nums[index]), k-1);

    smallestSubsequence(nums, index+1, smallest, cur, k);
}
```

_Another approach using stack._

```cpp
void smallestSubsequence(vector<int> nums, int k) {
    stack<char> st;

    int l = nums.size();
    int index = 0;
    for(auto num : nums) {
        char ch = tochar(num);
        if(st.size() < k) {
            st.push(ch);
        } else {
            int rem = l - index - 1;
            int needed = 1;
            while(!st.empty() && num < st.top() && rem >= needed) {
                st.pop();
                needed++;
            }
            st.push(ch);
        }

        index++;
    }
}
```

---

#### [Find if string is K-Palindrome or not | Set 1](https://www.geeksforgeeks.org/find-if-string-is-k-palindrome-or-not/)

Given a string, find out if the string is K-Palindrome or not. A k-palindrome string transforms into a palindrome on removing at most k characters from it.

```sh
Examples :

Input : String - abcdecba, k = 1
Output : Yes
String can become palindrome by remo-
-ving 1 character i.e. either d or e)


Input  : String - abcdeca, K = 2
Output : Yes
Can become palindrome by removing
2 characters b and e.

Input : String - acdcb, K = 1
Output : No
String can not become palindrome by
removing only one character.
```

1. Use lcs to determine longest common subsequence between it and its reverse.
   l = lcs(str, rev_of_str);
   d = n - l; // calculate the uncommon chars
   if k <= d, then its k palindromic, otherwise not

2. Calculate the k-palindrome as follow (reverse of lcs algorithm)

```cpp
int palinDist(string str, string rev) {
    int n = str.length();
    int m = rev.length();

    int dp[n+1][m+1];
    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if(i==0) dp[i][j] = j;
            else if(j==0) dp[i][j] = i;
            else if(str[i-1] == rev[j-1]) dp[i][j] = dp[i-1][j-1];
            else dp[i][j] = min(dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[n][m];
}

bool kPalin(string str, int k) {
    return palinDist(palinDist, str.reverse()) <= k;
}
```

---

#### [Look and Say Pattern ](https://practice.geeksforgeeks.org/problems/decode-the-pattern1138/1)

```sh
Given an integer n. Return the nth row of the following look-and-say pattern.
1
11
21
1211
111221
```

```cpp
string lookandsay(int n) {
    string res = std::to_string(1);
    int cnt = 0;
    string tmp;
    char prev;
    while(n-- > 1) {
        for(char ch : res) {
            if(cnt > 0 && ch != prev)
            {
                tmp += std::to_string(cnt);
                tmp += prev;
                cnt = 0;
            }
            prev = ch;
            cnt++;
        }
        if(cnt > 0) tmp += std::to_string(cnt) + prev;
        res = tmp;
        tmp = "";
        cnt = 0;
    }
    return res;
}
```

---

#### [Convert to Roman No ](https://practice.geeksforgeeks.org/problems/convert-to-roman-no/1)

```sh
Given an integer n, your task is to complete the function convertToRoman which prints the corresponding roman number of n. Various symbols and their values are given below.

I 1
V 5
X 10
L 50
C 100
D 500
M 1000
```

```cpp
string convertToRoman(int n) {
    map<int, string,std::greater<int>> map = { // reverse order
        {1000,"M"},
        {900,"CM"},
        {500, "D"},
        {400, "CD"},
        {100, "C"},
        {90, "XC"},
        {50,"L"},
        {40,"XL"},
        {10,"X"},
        {9,"IX"},
        {5,"V"},
        {4,"IV"},
        {1,"I"},
    };

    string result;
    while(n) {
        for(auto e : map) {
            if(n >= e.first)  {
                n -= e.first;
                result += e.second;
                break;
            }
        }
    }
    return result;
}
```

```cpp
int convertToInt(string str) {
    unordered_map<char, int> map = {
        {'M',1000},
        {'D', 500},
        {'C', 100},
        {'L',50},
        {'X',10},
        {'V',5},
        {'I',1},
    };
    int res = 0;
    for(int i=str.length()-1;i>=0;i--) {
        if(i < str.length()-1 && map[str[i]] < map[str[i+1]]) res -= map[str[i]];
        else res += map[str[i]];
    }

    return res;
}
```

---

#### [Convert Ternary Expression to Binary Tree ](https://practice.geeksforgeeks.org/problems/convert-ternary-expression-to-binary-tree/1)

```sh
Given a string that contains ternary expressions. The expressions may be nested. You need to convert the given ternary expression to a binary Tree and return the root.

Input: a?b:c
Output: a b c
Explanation:
string expression = a?b:c
So the binary tree for the above expression is:
       a
     /   \
    b     c
```

```cpp
Node *convertExpression(string str, int i)
{
    if(i == str.length()) return nullptr;

    Node *cur = new Node(str[i++]);
    if (i == str.length()) return cur;
    if (str[i] == '?') cur->left = convertExpression(str, i+1);
    else if(str[i] == ':') cur->right = convertExpression(str, i+1);

    return cur;
}
```

---

#### [Maximum Rectangular Area in a Histogram ](https://practice.geeksforgeeks.org/problems/maximum-rectangular-area-in-a-histogram-1587115620/1)

Find the largest rectangular area possible in a given histogram where the largest rectangle can be made of a number of contiguous bars. For simplicity, assume that all bars have the same width and the width is 1 unit, there will be N bars height of each bar will be given by the array arr.

```sh
Input:
N = 7
arr[] = {6,2,5,4,5,1,6}
Output: 12
```

[![N|Solid](http://d1hyf4ir1gqw6c.cloudfront.net/wp-content/uploads/histogram1.png)](https://practice.geeksforgeeks.org/problems/maximum-rectangular-area-in-a-histogram-1587115620/1#)

```cpp
int getMaxArea(int arr[], int n)
{
    stack<int> st;
    int area = 0;

    for(int i=0; i<n || !st.empty();) {
        if(st.empty() ||  (i<n && arr[i] >= arr[st.top()])) st.push(i++);
        else {
            int height = arr[st.top()]; st.pop();
            int width = st.empty() ? i : (i-st.top()-1);

            area = max(area, height * width);
        }
    }

    return area;
}
```

---

#### [K largest elements](https://practice.geeksforgeeks.org/problems/k-largest-elements3736/1)

Given an array of N positive integers, print k largest elements from the array.

```sh
N = 5, k = 2
arr[] = {12,5,787,1,23}
Output: 787 23
Explanation: First largest element in
the array is 787 and the second largest
is 23.
```

```cpp
vector<int> kLargest(int arr[], int n, int k)
{
    return kLargestUsingMinHeap(arr, n, k);
    //return kLargestUsingQuickPartitioning(arr, n, k);
}

vector<int> kLargestUsingMinHeap(int arr[], int n, int k) {
    priority_queue<int, vector<int>, greater<int>> pq;
    for(int i=0;i<n;i++) {
        if(pq.size() < k) pq.push(arr[i]);
        else {
            if(arr[i] > pq.top()) {
                pq.pop();
                pq.push(arr[i]);
            }
        }
    }
    vector<int> res;
    while(!pq.empty()) { res.push_back(pq.top()); pq.pop(); }

    sort(res.begin(), res.end(),[](const auto& f, const auto& s) {return f > s;});

    return res;
}

vector<int> kLargestUsingQuickPartitioning(int arr[], int n, int k)
{
    int p = kthLargestPos(arr, 0, n-1, k);
    vector<int> res;
    for(int i=p;i<n;i++) res.push_back(arr[i]);

    sort(res.begin(), res.end(),[](const auto& f, const auto& s) {return f > s;});
    return res;
}

int partition(int arr[], int l, int r) {
    int pivot = arr[r];
    int i = l;
    for(int j=l;j<r;j++) {
        if(arr[j] <= pivot) swap(arr[i++], arr[j]);
    }
    swap(arr[i],arr[r]);

    return i;
}

int kthLargestPos(int arr[], int l, int r, int k) {
    if(l > r || r-l+1 < k) return -1;
    int p = partition(arr, l, r);
    int q = r-p+1;
    if(q == k) return p;
    else if(k > q) return kthLargestPos(arr, l, p-1, k-q);
    else return kthLargestPos(arr, p+1, r, k);
}
```

---

#### [Maximum difference between node and its ancestor ](https://practice.geeksforgeeks.org/problems/maximum-difference-between-node-and-its-ancestor/1#)

Given a Binary Tree, you need to find the maximum value which you can get by subtracting the value of node B from the value of node A, where A and B are two nodes of the binary tree and A is an ancestor of B.

```sh
6 2 3 N N 4 1 N 5 N N 7 N
      6
    /    \
   2      3
         /  \
        4    1
          5
        7
Output: 6-1 = 5
```

```cpp
int maxDiff(Node *root, int& mxDiff) {
    if(!root) return INT_MAX;

    int mnChild = min(maxDiff(root->left, mxDiff), maxDiff(root->right, mxDiff));
    mxDiff = max(mxDiff, root->data -mnChild);

    return min(mnChild, root->data);
}

int maxDiff(Node* root)
{
    int mxDiff = INT_MIN;
    maxDiff(root, mxDiff);

    return mxDiff;
}
```

---

#### [Largest Number formed from an Array ](https://practice.geeksforgeeks.org/problems/largest-number-formed-from-an-array1117/1)

Given a list of non negative integers, arrange them in such a manner that they form the largest number possible.The result is going to be very large, hence return the result in the form of a string.

```sh
N = 5
Arr[] = {3, 30, 34, 5, 9}
Output: 9534330
Explanation: Given numbers are {3, 30, 34,
5, 9}, the arrangement 9534330 gives the
largest value.
```

**Append numbers and sort string lexographically.**

```cpp
string printLargest(vector<string> &arr) {
    sort(arr.begin(), arr.end(),[](const auto& f, const auto& s) {
        return (f+s) > (s+f);
    });
    string res;
    for(auto e : arr) res += e;

    return res;
}
```

---

#### [Remove loop in Linked List ](https://practice.geeksforgeeks.org/problems/remove-loop-in-linked-list/1#)

```cpp
LinkedListNode<int>* removeLoopInLinkedList(LinkedListNode<int>* head)
{
    LinkedListNode<int>* intersection = nullptr;
    bool res = hasLoop(head, intersection);
    if (!res) return head;

    cout << "Loop found at " << intersection->val << endl;
    LinkedListNode<int>* tmp = head;
    while (tmp && intersection && tmp->next != intersection->next) {
        tmp = tmp->next;
        intersection = intersection->next;
    }

    cout << "Removing loop at prev to loop node " << intersection->val << endl;
    intersection->next = nullptr;

    return head;
}

bool hasLoop(LinkedListNode<int>* head, LinkedListNode<int>*& intersection) {
    LinkedListNode<int>* slow = head, * fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) { intersection = slow; return true; }
    }

    return false;
}
```

---

#### [Find the number of islands ](https://practice.geeksforgeeks.org/problems/find-the-number-of-islands/1#)

Given a grid consisting of '0's(Water) and '1's(Land). Find the number of islands.
Note: An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically or diagonally i.e., in all 8 directions.

```cpp
int numIslands(vector<vector<char>>& grid) {
    int islands = 0;
    int n = grid.size();
    if(n == 0) return islands;

    int m = grid[0].size();
    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(grid[i][j] == '1') {
                dfs(grid, i, j, n, m);
                islands++;
            }
        }
    }

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(grid[i][j] == '2') grid[i][j] = '1';
        }
    }

    return islands;
}

void dfs(vector<vector<char>>& grid, int x, int y, int n, int m) {
    grid[x][y] = '2';

    for(int i=-1;i<=1;i++) {
        for(int j=-1;j<=1;j++) {
            if(i == 0 && j == 0) continue;
            int nx = x + i;
            int ny = y + j;
            if(nx < 0 || ny < 0 || nx >= n || ny >= m) continue;
            if(grid[nx][ny] != '1') continue;

            dfs(grid, nx, ny, n, m);
        }
    }
}
```

---

#### [Smallest window in a string containing all the characters of another string](https://practice.geeksforgeeks.org/problems/smallest-window-in-a-string-containing-all-the-characters-of-another-string-1587115621/1)

Given two strings S and P. Find the smallest window in the string S consisting of all the characters(including duplicates) of the string P. Return "-1" in case there is no such window present. In case there are multiple such windows of same length, return the one with the least starting index.

```cpp
string smallestWindow (string str, string pat)
{
    int n = str.length();
    int m = pat.length();
    vector<int> txtFreq(256, 0);
    vector<int> patFreq(256, 0);
    for(auto ch : pat) patFreq[ch]++;

    int cnt = 0;
    int smallestLen = INT_MAX;
    string smallest;
    for(int s=0,e=0;e<n;e++) {
        char ch = str[e];
        txtFreq[ch]++;
        if(txtFreq[ch] <= patFreq[ch]) cnt++;

        if(cnt == m) {
            while(s<n && txtFreq[str[s]] > patFreq[str[s]]) {
                txtFreq[str[s]]--;
                s++;
            }

            if(e-s+1 < smallestLen) {
                smallest = str.substr(s, e-s+1);
                smallestLen = e-s+1;
            }

            txtFreq[str[s]]--;
            s++;
            cnt--;
        }
    }

    return smallest;
}
```

---

#### [Reverse a Linked List in groups of given size. ](https://practice.geeksforgeeks.org/problems/reverse-a-linked-list-in-groups-of-given-size/1)

Given a linked list of size N. The task is to reverse every k nodes (where k is an input to the function) in the linked list. If the number of nodes is not a multiple of k then left-out nodes, in the end, should be considered as a group and must be reversed (See Example 2 for clarification).

```cpp
node *reverse (node *head, int k)
{
    if(!head) return head;

    node *prev = nullptr;
    node *tmp = head;
    for(int i=1;i<=k && tmp;i++) {
        node *next = tmp->next;
        tmp->next = prev;
        prev = tmp;
        tmp = next;
    }

    head->next = reverse(tmp, k);

    return prev;
}
```

---

#### [Next Greater Element](https://practice.geeksforgeeks.org/problems/next-larger-element-1587115620/1)

Given an array arr[ ] of size N having distinct elements, the task is to find the next greater element for each element of the array in order of their appearance in the array.
Next greater element of an element in the array is the nearest element on the right which is greater than the current element.
If there does not exist next greater of current element, then next greater element for current element is -1. For example, next greater of the last element is always -1.

```sh
N = 4, arr[] = [1 3 2 4]
Output:
3 4 4 -1
Explanation:
In the array, the next larger element
to 1 is 3 , 3 is 4 , 2 is 4 and for 4 ?
since it doesn't exist, it is -1.
```

```cpp
vector<long long> nextLargerElement(vector<long long> arr, int n){
    using ll = long long;
    vector<ll> res(n);
    if(n == 0) return res;

    stack<ll> st;
    for(int i=n-1;i>=0;i--) {
        while(!st.empty() && st.top() <= arr[i]) st.pop();

        if(st.empty()) res[i] = -1;
        else res[i] = st.top();

        st.push(arr[i]);
    }

    return res;
}
```

---

#### [Best Time to Buy and Sell Stock Only Once](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

```sh
Example 1:

Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

```cpp
int maxProfit(vector<int>& prices) {
    int mxProfit = 0;
    int mnPrice = INT_MAX;

    for(int i=0;i<prices.size();i++) {
        mnPrice = min(mnPrice, prices[i]);
        mxProfit = max(mxProfit, prices[i]-mnPrice);
    }

    return mx;
}
```

---

#### [Best Time to Buy and Sell Stock Multiple Times](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

You are given an integer array prices where prices[i] is the price of a given stock on the ith day.

On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can buy it then immediately sell it on the same day.

Find and return the maximum profit you can achieve.

```sh
Input: prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.
```

```cpp
int maxProfit(vector<int>& prices) {
    int i=0;
    int mxProfit = 0;
    int n = prices.size();

    while(i < n) {
        while(i<n-1 && prices[i] >= prices[i+1]) i++;
        if(i == n-1) break;

        int buy = prices[i];

        while(i<n-1 && prices[i+1] >= prices[i]) i++;

        int sell = prices[i];

        mxProfit += (sell - buy);
        i++;
    }

    return mxProfit;
}
```

---

#### [Stock span problem ](https://practice.geeksforgeeks.org/problems/stock-span-problem-1587115621/1)

The stock span problem is a financial problem where we have a series of n daily price quotes for a stock and we need to calculate the span of stock’s price for all n days.
The span Si of the stock’s price on a given day i is defined as the maximum number of consecutive days just before the given day, for which the price of the stock on the current day is less than or equal to its price on the given day.
For example, if an array of 7 days prices is given as {100, 80, 60, 70, 60, 75, 85}, then the span values for corresponding 7 days are {1, 1, 1, 2, 1, 4, 6}.

```cpp
vector <int> calculateSpan(int price[], int n)
{
    vector<int> span;
    stack<int> st;

    for(int i=0;i<=n;i++) {
        while(!st.empty() && i < n && price[st.top()] <= price[i]) st.pop();
        if(!st.empty()) span.push_back(i-st.top());
        else span.push_back(i+1);

        st.push(i);
    }

    return span;
}
```

---

#### [Binary Tree To Doubly Linked List](https://practice.geeksforgeeks.org/problems/binary-tree-to-dll/1)

Given a Binary Tree (BT), convert it to a Doubly Linked List(DLL) In-Place. The left and right pointers in nodes are to be used as previous and next pointers respectively in converted DLL. The order of nodes in DLL must be same as Inorder of the given Binary Tree. The first node of Inorder traversal (leftmost node in BT) must be the head node of the DLL.

- Solution 1: Using static next node
  - It normally works but doesn't incase of threads because static variable is class variable.

```cpp
Node * bToDLL(Node *root)
{
    if(!root) return root;
    static Node *next = nullptr;

    bToDLL(root->right);
    root->right = next;
    if(next) next->left = root;
    next = root;
    bToDLL(root->left);

    return next;
}
```

- Solution 2: Pass next variable by reference. This works for multithreaded program because the reference variable is per thread.

```cpp
Node * bToDLL(Node *root)
{
    Node *next = nullptr;
    return bToDLL(root, next);
}

Node * bToDLL(Node *root, Node *& next)
{
    if(!root) return root;

    bToDLL(root->right, next);

    root->right = next;
    if(next) next->left = root;
    next = root;

    bToDLL(root->left, next);

    return next;
}
```

---

#### [Add two numbers represented by linked lists ](https://practice.geeksforgeeks.org/problems/add-two-numbers-represented-by-linked-lists/1)

##### If numbers are represented in forward order. Also list can be of different size.

Given two numbers represented by two linked lists of size N and M. The task is to return a sum list.

The sum list is a linked list representation of the addition of two input numbers from the last.

```sh
Input:
N = 2
valueN[] = {4,5}
M = 3
valueM[] = {3,4,5}
Output: 3 9 0
Explanation: For the given two linked
list (4 5) and (3 4 5), after adding
the two linked list resultant linked
list will be (3 9 0).
```

```cpp
Node* addTwoLists(Node* first, Node* second)
{
    int l1 = length(first);
    int l2 = length(second);

    if(l1 != l2) {
        if(l1 < l2) first = padd(first, l2-l1);
        else second = padd(second, l1-l2);
    }

    int carry = 0;
    Node *s = addTwoLists(first, second, carry);
    if(carry) {
        Node *tmp = new Node(carry);
        tmp->next = s;
        s = tmp;
    }

    return s;
}

Node *addTwoLists(Node *f, Node *s, int &carry) {
    if(!f && !s) return nullptr;

    Node *next = addTwoLists(f->next, s->next, carry);
    int val = f->data + s->data + carry;
    Node *newNode = new Node(val%10);
    carry = val/10;
    newNode->next = next;

    return newNode;
}

int length(Node *node) {
    int l = 0;
    while(node) { node = node->next; l++; }

    return l;
}

Node *padd(Node *node, int l) {
    while(l--) {
        Node *tmp = new Node(0);
        tmp->next = node;

        node = tmp;
    }

    return node;
}
```

##### If numbers are represented in reverse order. Also list can be of different size.

```sh
Input:
l1 = [5 4 3]
l2 = [5 4]

s  = [0 9 3]
```

```cpp
Node *add(Node *first,  Node *second, int carry) {
    if(!first && !second) {
        if(carry) return new Node(carry);
        else return nullptr;
    }

    int val = carry;
    if(first) { val += first->val; first = first->next; }
    if(second) { val += second->val; second = second->next; }

    Node *newNode = new Node(val%10);
    newNode->next = add(first, second, val/10);

    return newNode;
}

```

---

#### [Majority Element ](https://practice.geeksforgeeks.org/problems/majority-element-1587115620/1)

##### Given an array A of N elements. Find the majority element in the array. A majority element in an array A of size N is an element that appears more than N/2 times in the array.

```cpp
int majorityElement(int a[], int size)
{
    int maj, cnt = 0;
    for(int i=0;i<size;i++) {
        if(cnt == 0) maj = a[i];
        if(a[i] == maj) cnt++;
        else cnt--;
    }

    cnt = 0;
    for(int i=0;i<size;i++) {
        if(a[i] == maj) cnt++;
    }

    return cnt > (size/2) ? maj : -1;
}
```

##### Given an array A of N elements. Find the majority elements in the array. A majority element in an array A of size N is an element that appears more than N/3 times in the array.

```cpp

```

---

#### [Reverse a linked list](https://practice.geeksforgeeks.org/problems/reverse-a-linked-list/1)

Given a linked list of N nodes. The task is to reverse this list.

- Reverse Linked List Iteratively

```cpp
Node* reverseList(Node *head)
{
    Node *prev = nullptr;
    while(head) {
        auto *next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }

    return prev;
}
```

- Reverse List Recursively

```cpp
Node* reverseList(Node *head)
{
    if(!head || !head->next) return head;

    Node *rest = reverseList(head->next);
    head->next->next = head;
    head->next = nullptr;

    return rest;
}
```

---

#### [Mirror Tree](https://practice.geeksforgeeks.org/problems/mirror-tree/1)

Given a Binary Tree, convert it into its mirror.

```cpp
void mirror(Node* node) {
    if(!node) return;

    swap(node->left, node->right);
    mirror(node->left);
    mirror(node->right);
}
```

---

#### [Remove every k'th node ](https://practice.geeksforgeeks.org/problems/remove-every-kth-node/1)

Given a singly linked list, your task is to remove every kth node from the linked list.

```cpp
Node* deleteK(Node *head,int K)
{
    Node *org = head; // store original head
    Node *prev = nullptr;
    while(head && K >= 1) { // if k == 0, don't remove anything
        for(int i=1;head && (i<K);i++) {
            prev = head;
            head = head->next;
        }
        if(head) { // handle case when K > list size
            auto *next = head->next;
            if(prev) prev->next = next; // handle case when K == 1
            else org = next;    // update head of list of k==-1
            delete head;
            head = next;
        }
    }

    return org;
}
```

---

#### [Longest Even Length Substring such that Sum of First and Second Half is same](https://www.geeksforgeeks.org/longest-even-length-substring-sum-first-second-half/)

```cpp
//Using cumulative sum method
int findLengthCumSum(string str, int n)
{
  int longest = 0;
  vector<int> sum(n+1); sum[0] = 0;
  for(int i=1;i<=n;i++) sum[i] = sum[i-1] + (str[i-1]-'0');

  for(int len=2;len<=n;len +=2) {
    for(int l=0;l<=n-len;l++) {
      int r = l+len;
      int m = l + (len/2);
      if(sum[m]-sum[l] == sum[r]-sum[m]) {
        longest = max(longest, len);
      }
    }
  }

  return longest;
}
```

```cpp
// Using expand around centers (we only need to expand around non-common center)
int findLength(string str, int n)
{
  int longest = 0;
  for(int i=0;i<n-1;i++) {
    int l = i, r = i+1;
    int lsum = 0, rsum = 0;
    while(l>=0 && r <n) {
      lsum += str[l] - '0';
      rsum += str[r] - '0';
      if(lsum == rsum) {
        longest = max(longest, r-l+1);
      }
      l--; r++;
    }
  }
  return longest;
}
```

---

#### [Longest Palindromic Substring](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/)

```cpp
int longestPalSubstr(string str)
{
  int n = str.length();
  int longest = 0;
  string longestStr;

  // expand around (mid,mid) center
  for(int i=0;i<n;i++) {
    int l = i, r = i;
    while(l>=0 && r<n && str[l] == str[r]) {l--; r++;}
    l++, r--;
    longest = max(longest , r-l+1);
    if(longest == r-l+1) longestStr = str.substr(l, r-l+1);
  }

  // expand around (mid,mid+1) center
  for(int i=0;i<n-1;i++) {
    int l = i, r = i+1;
    while(l>=0 && r<n && str[l] == str[r]) {l--; r++;}
    l++, r--;
    longest = max(longest , r-l+1);
    if(longest == r-l+1) longestStr = str.substr(l, r-l+1);
  }

  cout<<"longestStr = "<<longestStr<<endl;
  return longest;
}
```

---

#### [Missing number in array ](https://www.geeksforgeeks.org/find-the-missing-number/)

You are given a list of n-1 integers and these integers are in the range of 1 to n. There are no duplicates in the list. One of the integers is missing in the list. Write an efficient code to find the missing integer.

```cpp
int MissingNumber(vector<int>& array, int n) {
    unsigned long long sum = n*(n+1)/2;
    for(auto& val : array) sum -= val;
    return sum;
}
```

```cpp
// using xoring
int missingNumber(vector<int> arr) {
    int xor = 0;
    for(int i=0;i<arr.size();i++) {
        xor ^= (i+1);
        xor ^= arr[i];
    }

    return xor;
}
```

---

#### [Find the repeating and the missing](https://www.geeksforgeeks.org/find-a-repeating-and-a-missing-number/)

Given an unsorted array of size n. Array elements are in the range from 1 to n. One number from set {1, 2, …n} is missing and one number occurs twice in the array. Find these two numbers.

1. Using Sorting

```cpp
    sort(arr, arr+n);
    for i=0 to n-1:
        if(arr[i] != (i+1)) return (i+1);
```

---

2. Using array index

```cpp
// given, elements in range [1-n] and only one missing and one repeating
pair<int, int> find(vector<int>& arr) {
    int x, y;

    // Here, we set the element at the index position to negative;
    // If the index position is already negative, that mean that number is repititive.
    for(int i=0;i<arr.size();i++) {
        int idx = abs(arr[i]-1);
        if(arr[idx] > 0) arr[idx] *= -1;
        else y = abs(arr[i]);
    }

    // If there is one repeating, then one index is still not marked.
    for(int i=0;i<arr.size();i++) {
        if(arr[i] > 0) {
            x = (i+1);
            break;
        }
    }
    return {x,y};
}
```

3. Using Xoring

```cpp
pair<int, int> find(vector<int>& arr) {
    int x=0, y=0;
    int xorVal;
    for(int i=0;i<arr.size();i++) {
        xorVal ^= (i+1);
        xorVal ^= arr[i];
    }

    // last set bit
    xorVal &= ~(xorVal-1);

    for(int i=0;i<arr.size();i++) {
        if(arr[i] & xorVal) x ^= arr[i];
        else y ^= arr[i];
    }

    return {x,y};
}
```

---

### [Subset Sum](https://www.geeksforgeeks.org/subset-sum-problem-osum-space/)

Given an array of non-negative integers and a value sum, determine if there is a subset of the given set with sum equal to given sum.

- Using Recursion

```cpp
bool isSubset(vector<int> arr, int n, int s) {
    if(s == 0) return true;
    if(n <= 0 || s < 0) return false;

    return isSubset(arr, n-1, s-arr[n-1]) || isSubset(arr, n-1, s);
}
```

- Using O(sum\*n) space

```cpp
bool isSubset(vector<int> arr, int n, int sum) {
    vector<vector<bool>> memo(s+1, vector<bool>(n+1));

    for(int s=0;s<=sum;s++) {
        for(int i=0;i<=n;i++) {
            memo[s][i] = false;

            if(s == 0) memo[s][i] = true;
            else if(i == 0) memo[s][i] = false;
            else {
                memo[s][i] |= memo[s][i-1];
                if(s >= arr[i-1])
                    memo[s][i] |= memo[s-arr[i-1]][i-1];
            }
        }
    }

    return memo[sum][n];
}
```

- Using O(sum) space

```cpp
bool isSubset(vector<int> arr, int n, int sum) {
    vector<bool> memo(sum+1, false);
    memo[s] = true;

    for(int i=0;i<n;i++) {
        for(int s=sum;s>=arr[i];s--) {
            memo[s] |= memo[s-arr[i]];
        }
    }

    return memo[sum][n];
}
```

---

#### [Partition Equal Subset Sum ](https://practice.geeksforgeeks.org/problems/subset-sum-problem2014/1)

Given an array arr[] of size N, check if it can be partitioned into two parts such that the sum of elements in both parts is the same.

```sh
Input: N = 4
arr = {1, 5, 11, 5}
Output: YES
Explaination:
The two parts are {1, 5, 5} and {11}.
```

```cpp
int equalPartition(int N, int arr[])
{
    int s = std::accumulate(arr, arr+N, 0);
    if(s&1) return 0;

    int h = s/2;

    bool *table = new bool[h+1];
    memset(table, false, (h+1)*sizeof(bool));
    table[0] = true;
    for(int i=0;i<N;i++) {
        for(int j=h;j>=arr[i];j--) {
            table[j] |= table[j-arr[i]];
        }
    }

    return table[h];
}
```

---

#### [Sort an array of 0s, 1s and 2s ](https://practice.geeksforgeeks.org/problems/sort-an-array-of-0s-1s-and-2s4231/1#)

Given an array of size N containing only 0s, 1s, and 2s; sort the array in ascending order.

```cpp
void sort012(int a[], int n)
{
    for(int l=0,m=0,r=n-1;m<=r;) {
        if(a[m] == 0) swap(a[l++],a[m++]);
        else if(a[m] == 1) m++;
        else if(a[m] == 2) swap(a[m],a[r--]);
    }
}

void sort012(int a[], int n)
{
    int l = 0, m = 0, r = n-1;
    while(m <= r) {
        case 0:
            swap(a[l++],a[m++]); //  If m == 0, then swap with lower and then both l and m becomes at right place and hence increment both. l must be 1 at this point.
            break;
        case 1:
            m++; // If m is 1 then its already at right place
            break;
        case 2:
            swap(a[m],a[r--]); // if m == 2, then move it to end (r)
            break;
    }
}
```

---

#### [Minimum number of jumps ](https://practice.geeksforgeeks.org/problems/minimum-number-of-jumps-1587115620/1)

Given an array of N integers arr[] where each element represents the max number of steps that can be made forward from that element. Find the minimum number of jumps to reach the end of the array (starting from the first element). If an element is 0, then you cannot move through that element.
Note: Return -1 if you can't reach the end of the array.

```cpp
int minJumpsRec(int arr[], int index, int n){
    if(index >= n) return 0; // means, we can reach end so return 0

    // minimize number of jumps to reach end from current index
    int jumps = INT_MAX;
    for(int i=1;i<=arr[index];i++) {
        int cur = minJumpsRec(arr, index+i, n);
        jumps = min(jumps, 1+cur);
    }

    return jumps;
}
```

```cpp
int minJumpsGreedy(int arr[], int n) {
    int jumps = 0, newMax = 0, curMax = 0;

    for(int i=0;i<n-1;i++) {
        newMax = max(newMax, i + arr[i]);
        if(curMax == i) {
            jumps++;
            curMax = newMax;
        }
    }

    return curMax >= n-1 ? jumps: -1;
}
```

```cpp
int minJumpsDP(int arr[], int n) {
    vector<int> memo(n, INT_MAX);
    memo[0] = 0;

    // minimize jumps from each position to all reachable positions.
    for(int i=0;i<n;i++) {
        for(int j=i+1;j<=i+arr[i] && j<n;j++) {
            if(memo[i] != INT_MAX && 1+memo[i] < memo[j])
                memo[j] = 1+memo[i];
        }
    }

    return memo[n-1] == INT_MAX ? -1 : memo[n-1];
}
```

```cpp
int minJumpsDP2(int arr[], int n) {
    vector<int> memo(n);
    memo[0] = 0;

    // minimize to jumps to reach pos 'i' from all previous elements
    for(int i=1;i<n;i++) {
        memo[i] = INT_MAX;
        for(int j=0;j<i;j++) {
            if(i <= j+arr[j] && memo[j] != INT_MAX) {
                memo[i] = min(memo[i], 1+memo[j]);
            }
        }
    }

    return memo[n-1] == INT_MAX ? -1 : memo[n-1];
}
```

---

#### [Sum Tree ](https://practice.geeksforgeeks.org/problems/sum-tree/1)

Given a Binary Tree. Return 1 if, for every node X in the tree other than the leaves, its value is equal to the sum of its left subtree's value and its right subtree's value. Else return 0.

An empty tree is also a Sum Tree as the sum of an empty tree can be considered to be 0. A leaf node is also considered a Sum Tree.

```cpp
bool isSumTree(Node* root)
{
    int sum = 0;
    return isSumTree(root, sum);
}

bool isSumTree(Node* root, int& sum)
{
    if(!root) { sum = 0; return true; }
    if(!root->left && !root->right) { sum = root->data; return true; }

    int ls, rs;
    bool l = isSumTree(root->left, ls);
    if(!l) return false;

    bool r = isSumTree(root->right, rs);
    if(!r) return false;

    if(ls + rs != root->data) return false;

    sum = ls + rs + root->data;

    return true;
}
```

---

#### [Level order traversal in spiral form ](https://practice.geeksforgeeks.org/problems/level-order-traversal-in-spiral-form/1)

Complete the function to find spiral order traversal of a tree. For below tree, function should return 1, 2, 3, 4, 5, 6, 7.
![](https://contribute.geeksforgeeks.org/wp-content/uploads/level.jpg)

```cpp
vector<int> findSpiral(Node *root)
{
    vector<int> result;
    deque<Node *> q;
    if(root) q.push_back(root);
    bool rightToLeft = true;

    // here, we read each level by level either in forward or reverse direction which gives spiral order.
    // child elements are always insert in left -> right order
    while(!q.empty()) {
        int sz = q.size();
        while(sz--) {
            if(rightToLeft) { // If right the left, then read from back and insert in front.
                auto f = q.back(); q.pop_back();
                result.push_back(f->data);

                if(f->right) q.push_front(f->right);
                if(f->left) q.push_front(f->left);
            } else { // If left to right, read from from front and push in back
                auto f = q.front(); q.pop_front();
                result.push_back(f->data);

                if(f->left) q.push_back(f->left);
                if(f->right) q.push_back(f->right);
            }
        }
        rightToLeft = !rightToLeft;
    }

    return result;
}
```

---

#### [Remove duplicate element from sorted Linked List ](https://practice.geeksforgeeks.org/problems/remove-duplicate-element-from-sorted-linked-list/1)

Given a singly linked list consisting of N nodes. The task is to remove duplicates (nodes with duplicate values) from the given list (if exists).
Note: Try not to use extra space. Expected time complexity is O(N). The nodes are arranged in a sorted way.

```cpp
Node *getNext(Node *node, int data) {
    while(node && node->data == data) {
        auto next = node->next;
        delete node;
        node = next;
    }

    return node;
}

Node *removeDuplicates(Node *head)
{
    Node *tmp = head;
    while(tmp) {
        auto* next = getNext(tmp->next, tmp->data);
        tmp->next = next;
        tmp = tmp->next;
    }

    return head;
}
```

---

#### [Merge Sort for Linked List ](https://practice.geeksforgeeks.org/problems/sort-a-linked-list/1#)

Given Pointer/Reference to the head of the linked list, the task is to Sort the given linked list using Merge Sort.
Note: If the length of linked list is odd, then the extra node should go in the first list while splitting.

```cpp
Node* mergeSort(Node* head) {
    if(!head || !head->next) return head;

    Node *first = head;
    Node *second = split(head);

    first = mergeSort(first);
    second = mergeSort(second);

    return merge(first, second);
}

Node *split(Node *node) {
    Node *slow = node, *fast = node->next;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    auto next = slow->next;
    slow->next = nullptr;

    return next;
}

Node *merge(Node *first, Node *second) {
    if(!first) return second;
    if(!second) return first;

    Node *head = nullptr;
    if(first->data <= second->data) {
        head = first;
        head->next = merge(first->next, second);
    }
    else {
        head = second;
        head->next = merge(first, second->next);
    }

    return head;
}
```

---

#### [Number of 1 Bits ](https://practice.geeksforgeeks.org/problems/set-bits0143/1)

```cpp
int setBits(int N) {
    int cnt = 0 ;
    while(N) {
        cnt++;
        N &= (N-1);
    }

    return cnt;
}
```

---

#### [Power of 2 ](https://practice.geeksforgeeks.org/problems/power-of-2-1587115620/1)

```cpp
bool isPowerofTwo(long long n){
    if(n == 0) return false;
    return (n&(n-1)) == 0;
}
```

---

#### [Calculate Power](https://www.careercup.com/question?id=5733237978562560)

Write program to calculate power(x,n) in log(n) time

```cpp
bool pow(int x, int y){
    if(y == 0) return 1;

    int p = pow(x, y/2);

    return (y&1) ? p*p*x : p*p;
}
```

```cpp
// 2nd approach
// if n is even then power(x, n) = power(x, n / 2) * power(x, n / 2)
// else power(x, n) = x * power(x, n - 1)

int power(int x, int n) {
    if (n == 1) return 1;
    else if (n % 2 == 1) return x * power(x, n - 1);
    else {
        int b = power(x, n / 2);
        return b * b;
    }
}
```

---

#### [First Non Repeating Character ](https://practice.geeksforgeeks.org/problems/non-repeating-character-1587115620/1)

Given a string S consisting of lowercase Latin Letters. Find the first non-repeating character in S.

```cpp
char firstNonRepeatingCharacter(string S)
{
    list<char> uniqueChars;
    unordered_map<char, list<char>::iterator> map;

    for(auto ch : S) {
        if(map.find(ch) == map.end()) {
            uniqueChars.push_back(ch);
            map.insert({ch, std::prev(uniqueChars.end())});
        } else if(map[ch] != uniqueChars.end()) {
            auto iter = map[ch];
            uniqueChars.erase(iter);
            map[ch] = uniqueChars.end();
        }
    }

    return uniqueChars.empty()? '$' : uniqueChars.front();
}
```

```cpp
char FirstNonRepeating(string str){
    pair<int,int> arr[26] = {{-1,0}}; // <firstPos, count>
    for(int i=0;i<str.length();i++) {
        int idx = str[i] - 'a';
        if(arr[idx].second == 0) { // we see this character first time
            arr[idx] = {i,1}; // record its first index and count to 1
        } else { // already seen this char, increase cnt
            arr[idx].second++;
        }
    }

    int mnCharIndex = INT_MAX;
    for(int i=0;i<26;i++) {
        if(arr[i].second == 1 && arr[i].first < mnCharIndex) mnCharIndex = arr[i].first;
    }

    if (mnCharIndex == INT_MAX) return '#';
    return str[mnCharIndex];
}
```

---

#### [Find First Unique Url](https://www.careercup.com/question?id=11856466)

Given a very long list of URLs, find the first URL which is unique ( occurred exactly once ).
I gave a O(n) extra space and O(2n) time solution, but he was expecting O(n) time, one traversal.

```cpp
string findFirstUniqueUrl(vector<string> urls)
{
    list<string> urlList;
    unordered_map<string, list<string>::iterator> urlMap;

    for (auto& url : urls) {
        if (urlMap.find(url) != urlMap.end()) {
            auto val = urlMap[url];
            if (val != urlList.end()) {
                urlList.erase(val);
                urlMap[url] = urlList.end();
            }
        }
        else {
            urlList.push_back(url);
            urlMap[url] = std::prev(urlList.end());
        }
    }

    if (!urlList.empty()) {
        return urlList.front();
    }

    return "Not Found";
}
```

---

#### [First Repeating Element ](https://practice.geeksforgeeks.org/problems/first-repeating-element4018/1#)

Given an array arr[] of size n, find the first repeating element. The element should occurs more than once and the index of its first occurrence should be the smallest.

```cpp
int findFirstRepeatingElement(int arr[], int n) {
    unordered_map<int, pair<int,int>> m;
    for(int i=0;i<n;i++) {
        if(m.find(arr[i]) == m.end()) m[arr[i]] = {i, 1};
        else m[arr[i]].second++;
    }

    int idx = INT_MAX;
    for(auto e: m) {
        if(e.second.second > 1) {
            idx = min(idx, e.second.first+1);
        }
    }

    return idx == INT_MAX ? -1 : idx;
}
```

---

#### [0 - 1 Knapsack Problem ](https://practice.geeksforgeeks.org/problems/0-1-knapsack-problem0945/1#)

You are given weights and values of N items, put these items in a knapsack of capacity W to get the maximum total value in the knapsack. Note that we have only one quantity of each item.
In other words, given two integer arrays val[0..N-1] and wt[0..N-1] which represent values and weights associated with N items respectively. Also given an integer W which represents knapsack capacity, find out the maximum value subset of val[] such that sum of the weights of this subset is smaller than or equal to W. You cannot break an item, either pick the complete item or don’t pick it (0-1 property)

```cpp
int knapSack(int W, int wt[], int val[], int n)
{
    //return knapSackDPOptimized(W, wt, val, n);
    return knapSackDP(W, wt, val, n);
}

int knapSackDPOptimized(int W, int wt[], int val[], int n)
{
    vector<int> table(W+1, 0);
    for(int i=0;i<n;i++) {
        for(int w=W;w>=0;w--) {
            if(wt[i] <= w) {
                table[w] = max(table[w], val[i]+table[w-wt[i]]);
            }
        }
    }

    return table[W];
}

int knapSackDP(int W, int wt[], int val[], int n)
{
    vector<vector<int>> table(W+1,vector<int>(n+1, 0));
    for(int w=0;w<=W;w++) {
        for(int i=0;i<=n;i++) {
            if(w == 0 || i == 0) table[w][i] = 0;
            else if(wt[i-1]<=w) table[w][i] = max(table[w][i-1],val[i-1] + table[w-wt[i-1]][i-1]);
            else table[w][i] = table[w][i-1];
        }
    }

    return table[W][n];
}
```

---

#### [Sum of Middle Elements of two sorted arrays ](https://practice.geeksforgeeks.org/problems/sum-of-middle-elements-of-two-sorted-arrays2305/1)

Given 2 sorted arrays Ar1 and Ar2 of size N each. Merge the given arrays and find the sum of the two middle elements of the merged array.

```cpp
// O(logn)
int findMidSum(int arr1[], int arr2[], int n) {
    if(n <= 0) return 0;
    if(n == 1) {
        return arr1[0] + arr2[0];
    }
    if(n == 2) {
        return max(arr1[0],arr2[0]) + min(arr1[1],arr2[1]);
    }

    int m = (n-1)/2; // include mid element
    if(arr1[m] <= arr2[m]) return findMidSum(arr1+m, arr2, n-m);
    else return findMidSum(arr1, arr2+m, n-m);
}

// O(n)
int findMidSum1(int arr1[], int arr2[], int n) {
    int mid1 = 0, mid2 = 0;
    int m = n;
    int i = 0, j = 0;
    while(m-- >= 0) {
        mid1 = mid2;
        if(i < n && j < n && arr1[i] <= arr2[j]) {
            mid2 = arr1[i++];
        } else if(j < n) {
            mid2 = arr2[j++];
        } else if(i < n) {
            mid2 = arr1[i++];
        }
    }
    return mid1 + mid2;
}
```

---

#### [Word Boggle ](https://practice.geeksforgeeks.org/problems/word-boggle4143/1)

Given a dictionary of distinct words and an M x N board where every cell has one character. Find all possible words from the dictionary that can be formed by a sequence of adjacent characters on the board. We can move to any of 8 adjacent characters

```sh
N = 1
dictionary = {"CAT"}
R = 3, C = 3
board = {{C,A,P},{A,N,D},{T,I,E}}
Output:
CAT
Explanation:
C A P
A N D
T I E
Words we got is denoted using same color.
```

```cpp
[Incomplete]
using Board = vector<vector<char>>;
using CharPositionsMap = unordered_map<char, vector<pair<int,int>>>;
using Set = unordered_set<int> visited;
using Point = pair<int,int>;
vector<string> wordBoggle(Board& board, vector<string>& dictionary) {
    CharPositionsMap charPositions;
    for(int i=0;i<board.size();i++) {
        for(int j=0;j<board[i].size();j++) {
            charPositions[board[i][j]].push_back({i,j});
        }
    }

    unordered_set<int> visited;
    int n = board.size();
    int m = board[0].size();

    vector<string> result;
    for(auto& w : dictionary) {
        if(exists(board, charPositions, w)) {
            result.push_back(w);
        }
    }

    sort(result.begin(), result.end());

    return result;
}

bool exists(Board& board, CharPositionsMap& charPositions, string& word, Set& visited, int n, int m) {
    if(board.empty()) return false;
    if(word.empty()) return true;

    for(pair<int,int> pos : charPositions[word[0]]) {
        if(exists(board, pos, charPositions, word, 1, visited, n, m));
    }
}

bool exists(Board& board, Point curPos, CharPositionsMap& charPositions, string& word, int index, Set& visited, int n, int m)
{
    if(index == word.length()) return true;

    vector<Point> neighbours = getNeighbours(charPositions, curPos, word[index], visited, m);
    for(Point pos : neighbours) {
        int idx = pos.first * m + pos.second;

        visited.insert(idx);
        if(exists(board, charPositions, word, index+1, visited, n, m)) {
            return true;
        }
        visited.erase(idx);
    }

    return false;
}

vector<Point> getNeighbours(CharPositionsMap& charPositions, Point curPos, char curCh, Set& visited, int m) {
    vector<Point> positions = charPositions[curCh];

    vector<Point> result;
    for(auto pos : positions) {
        for(int i=-1;i<=1;i++) {
            for(int j=-1;j<=1;j++) {
                if(i == 0 && j == 0) continue;
                if(pos.first -i == curPos.first && pos.second-j == curPos.second) {
                    int idx = (pos.first -i) * m + (pos.second-j);
                    if(visited.find(idx) != visited.end()) continue;

                    result.push_back(pos);
                }
            }
        }
    }

    return result;
}
```

---

#### [Array Pair Sum Divisibility Problem ](https://practice.geeksforgeeks.org/problems/array-pair-sum-divisibility-problem3257/1)

Given an array of integers and a number k, write a function that returns true if given array can be divided into pairs such that sum of every pair is divisible by k.

```cpp
bool canPair(vector<int> nums, int k) {
    int n = nums.size();
    if(n&1) return false;

    unordered_map<int,int> map;
    for(auto e : nums) map[e%k]++;

    for(auto e : nums) {
        int r = e%k;
        if(r == 0) { if(map[r]&1) return false; }
        else if(map[r] != map[k-r])  { return false; }
    }

    return true;
}
```

---

#### [Max Sum without Adjacents ](https://practice.geeksforgeeks.org/problems/max-sum-without-adjacents2430/1)

Given an array Arr of size N containing positive integers. Find the maximum sum of a subsequence such that no two numbers in the sequence should be adjacent in the array.

```cpp
int findMaxSum(int *arr, int n) {
    return findMaxSumDP(arr, n);
}

int findMaxSumDP(int *arr, int n) {
    vector<int> memo(n+2);
    memo[0] = 0;
    memo[1] = 0;

    for(int i=2;i<=n+1;i++) {
        memo[i] = max(memo[i-2]+arr[i-2], memo[i-1]);
    }

    return memo[n+1];
}

int findMaxSumRec(int *arr, int n) {
    if(n <= 0) return 0;
    return max(arr[n-1]+findMaxSum(arr, n-2), findMaxSum(arr, n-1));
}
```

---

#### [Return two prime numbers ](https://practice.geeksforgeeks.org/problems/return-two-prime-numbers2509/1)

Given an even number N (greater than 2), return two prime numbers whose sum will be equal to given number. There are several combinations possible. Print only the pair whose minimum value is the smallest among all the minimum values of pairs.

NOTE: A solution will always exist, read Goldbach’s conjecture.

```sh
Input: N = 74
Output: 3 71
Explaination: There are several possibilities
like 37 37. But the minimum value of this pair
is 3 which is smallest among all possible
minimum values of all the pairs.
```

```cpp
vector<int> primeDivision(int N){
    vector<bool> primes(N, true);
    primes[0] = primes[1] = false;

    for(int i=2;i*i<=N;i++) {
        if(primes[i]) {
            for(int j=2;j*i<N;j++) primes[i*j] = false;
        }
    }

    vector<int> result;
    for(int i=0;i<N;i++) if(primes[i]) result.push_back(i);

    int l = 0, r = result.size()-1;
    while(l<=r) {
        if(result[l]+result[r] == N) return {result[l],result[r]};
        else if(result[l]+result[r]<N) l++;
        else r--;
    }
    return {};
}
```

```cpp
vector<int> primeDivision(int N){
    vector<bool> primes(N, true);
    primes[0] = primes[1] = false;
    for(int i = 2; i*i <= N; i++) {
        if (!primes[i]) continue;
        for (int j = i * i; j < N; j += i)
            primes[j] = false;
    }
    for(int i = 2; i < N; ++i) {
        if(primes[i] && primes[N - i]) {
            vector<int> ans;
            ans.push_back(i);
            ans.push_back(N - i);
            return ans;
        }
    }

    return {};
}
```

---
