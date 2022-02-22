#### [Consecutive 1's not allowed ](https://practice.geeksforgeeks.org/problems/consecutive-1s-not-allowed1912/1)

Given a positive integer N, count all possible distinct binary strings of length N such that there are no consecutive 1’s.

```cpp
int countStrings(int n) {
    int *a = new int[n]; // ending in 0
    int *b = new int[n]; // ending in 1
    a[0] = b[0] = 1; // {0}, {1}

    for(int i=1;i<n;i++) {
        a[i] = a[i-1] + b[i-1];// {0,0} {1,0} , it can take both previos value from a and b
        b[i] = a[i-1]; // {0,1}, this has to end in 1 so it can only consider previos value from a (ending in zeros)
    }

    return a[n-1]+b[n-1];
}
```

---

#### [Maximum Index ](https://practice.geeksforgeeks.org/problems/maximum-index-1587115620/1)

Given an array A[] of N positive integers. The task is to find the maximum of j - i subjected to the constraint of A[i] < A[j] and i < j.

**_Only using one extra array_**

```cpp
int maxIndexDiffSpaceOptimized(int A[], int N)
{
    if(N == 0) return 0;
    int *left = new int[N];

    left[0] = A[0];
    for(int i=1;i<N;i++) left[i] = min(left[i-1],A[i]);

    int mx = INT_MIN;
    int i=N-1,j=N-1;
    while(i>=0 && j>=0) {
        if(left[i] <= A[j]) {
            mx = max(mx, j-i);
            i--;
        }
        else j--;
    }

    return mx;
}
```

**_Using 2 extra arrays from leftMin and rightMax_**

```cpp
int maxIndexDiffWith2Arrays(int A[], int N)
{
    vector<int> left(N);
    vector<int> right(N);

    for(int i=0;i<N;i++) {
        if(i == 0) left[i] = A[i];
        else left[i] = min(left[i-1], A[i]);
    }

    for(int i=N-1;i>=0;i--) {
        if(i == N-1) right[i] = A[i];
        else right[i] = max(right[i+1], A[i]);
    }

    int i=0,j=0;
    int mx = INT_MIN;
    while(i<N && j<N) {
        if(left[i] <= right[j])  {
            mx = max(mx, j-i);
            j++;
        } else {
            i++;
        }
    }

    return mx;
}
```

---

#### [Count Inversions](https://practice.geeksforgeeks.org/problems/inversion-of-array-1587115620/1)

Given an array of integers. Find the Inversion Count in the array.

Inversion Count: For an array, inversion count indicates how far (or close) the array is from being sorted. If array is already sorted then the inversion count is 0. If an array is sorted in the reverse order then the inversion count is the maximum.
Formally, two elements a[i] and a[j] form an inversion if a[i] > a[j] and i < j.

```sh
Input: N = 5, arr[] = {2, 4, 1, 3, 5}
Output: 3
Explanation: The sequence 2, 4, 1, 3, 5
has three inversions (2, 1), (4, 1), (4, 3).
```

```cpp
int int inversionCount(int arr[], int N)
{
    int *tmp = new int[N];
    return inversionCount(arr, 0, N-1, tmp);
}

int inversionCount(int arr[], int l, int r, int tmp[]) {
    int inv = 0;
    if(l < r) {
        int m = (l+r)/2;
        inv += inversionCount(arr, l, m, tmp);
        inv += inversionCount(arr, m+1, r, tmp);
        inv += merge(arr, l, m, r, tmp);
    }
    return inv;
}

int merge(int arr[], int l, int m, int r, int tmp[]) {
    int inv = 0;

    int i = l, j = m+1;
    int k = l;
    while(i <= m && j <= r) {
        if(arr[i] <= arr[j]) tmp[k++] = arr[i++];
        else {
            inv += m-i+1; // number of elements in left array including the self which is getting compared
            tmp[k++] = arr[j++];
        }
    }
    while(i <= m) tmp[k++] = arr[i++];
    while(j <= r) tmp[k++] = arr[j++];

    i = l;
    while(i <= r) arr[i] = tmp[i++];

    return inv;
}

```

---

#### [Iterative Quick Sort]()

```cpp
void quickSort(vector<int>& v)
{
    stack<pair<int, int>> st;
    st.push({ 0,v.size() - 1 });

    while (!st.empty()) {
        auto tmp = st.top(); st.pop();
        int l = tmp.first;
        int r = tmp.second;

        int p = partition(v, l, r);
        if (p > l) st.push({ l, p - 1 });
        if (p < r) st.push({ p + 1, r });
    }
}

int partition(vector<int>& v, int l, int r)
{
    int pivot = v[r];
    int i = l;
    for (int j = l; j < r; j++) {
        if (v[j] < pivot) swap(v[j], v[i++]);
    }
    swap(v[i], v[r]);

    return i;
}
```

---

#### [Arrange numbers by their value]()

```sh
Input: [1,3]
Output:[3 1 2 1 3 2]

Input: [1,7]
Output:[1 4 1 5 6 7 4 2 3 5 2 6 3 7]
```

```cpp
bool canBePlaced(int startNum, int endNum, int pos, vector<int>& result, unordered_set<int>& set) {
    if (set.size() == endNum) return true;
    if (pos >= result.size()) return false;
    if (result[pos] != 0) return canBePlaced(startNum, endNum, pos + 1, result, set);

    for (int i = startNum; i <= endNum; i++) { // try out all the numbers at all positions
        if (set.find(i) != set.end() || pos + i + 1 >= result.size()) continue;
        if (result[pos + i + 1] != 0) continue;

        result[pos] = result[pos + i + 1] = i;
        set.insert(i);
        if (canBePlaced(startNum, endNum, pos + 1, result, set)) return true;
        result[pos] = result[pos + i + 1] = 0;
        set.erase(i);
    }

    return false;
}

bool canBePlaced(int num, vector<int>& result) {
    result.resize(num * 2, 0);
    unordered_set<int> set;
    auto res = canBePlaced(1, num, 0, result, set);
    return res;
}
```

---

#### [Print First N Prime Numbers]()

```cpp
bool isPrime(int num) {
    for(int d=2;d*d<=num;d++) {
        if(num%d == 0) return false;
    }
    return true;
}

vector<int> getFirstNPrimeNumbers(int n) {
    int num = 2;
    vector<int> result;
    for (int cnt=0;cnt<n;) {
        if (isPrime(num)) {
            result.push_back(num);
            cnt++;
        }
        num++;
    }
    return result;
}
```

```cpp
vector<int> getFirstNPrimeNumbersUsingSeiveMethod(int n) {
    int mxValue = 1000;
    vector<bool> primes(mxValue, true);
    primes[0] = primes[1] = false;

    vector<int> result;
    int cnt = 0;
    for (int i=2;i<mxValue && cnt<n;i++) {
        if (primes[i]) {
            result.push_back(i);
            markItsFactors(primes, i, mxValue);
            cnt++;
        }
    }

    return result;
}

void markItsFactors(vector<bool>& primes, int i, int mxValue) {
    for (int j=i*2;j<=mxValue;j+=i) {
        primes[j] = false;
    }
}
```

---

#### [Arrange Numbers in alternative orders](https://www.careercup.com/question?id=5684901156225024)

```sh
Arrange the numbers in an array in alternating order.
For example if the array is [a1, a2, a3, a4.. ]arrange the array such that b1<=b2>=b3<=b4 and so on.
Sampe Input: 3 5 7 8 4 9
Sample Output: 3 < 5 > 4 < 8 >7 < 9
```

```cpp
void arrangeNumbers(vector<int>& nums) {
    sort(nums.begin(), nums.end());

    for(int i=0;i<nums.size()-1;i+=2) {
        swap(nums[i], nums[i+1]);
    }
}
```

```cpp
void arrangeNumbers(vector<int>& nums) {
    for(int i=0;i<nums.size();i+=2) {
        if(i > 0 && nums[i-1] > nums[i]) swap(nums[i-1], nums[i]);
        if(i < n-1 && nums[i+1] > nums[i]) swap(nums[i+1], nums[i]);
    }
}
```

---

#### [Assign A or B Without Conditional Operator](https://www.careercup.com/question?id=5135296679116800)

You are given four integers 'a', 'b', 'y' and 'x', where 'x' can only be either zero or one. Your task is as follows:

If 'x' is zero assign value 'a' to the variable 'y', if 'x' is one assign value 'b' to the variable 'y'.

You are not allowed to use any conditional operator (including ternary operator).
Follow up: Solve the problem without utilizing arithmetic operators '+ - \* /'.

```cpp
void fun(int a, int b, int x, int y)
{
    int arr[2];
    arr[0] = a;
    arr[1] = b;
    y = arr[x]; // assign to y based on value of x in range [0,1]
}
```

```cpp
void fun2(int a, int b, int x, int y)
{
    int y = (1 - x) * a + x * b;
}
```

```cpp
void fun3(int a, int b, int x, int y)
{
    int y = (x && b) || a;
}
```

```cpp
void fun4(int a, int b, int x, int y)
{
    int arr[] = { a, b };
    y = arr[x];
}
```

---

#### [Check if array is in arithmetic prgression (AP)](https://www.careercup.com/question?id=5724684365594624)

Check if an integer array is arithmetic sequence.

Example: 1, 2, 3, 4, 5, 6, 7, 8 => true
1, 3, 5, 7, 9 => true

Array may not be sorted.

```cpp
bool isAP(vector<int>& arr) {
    int mn = INT_MAX, mx = INT_MIN;
    int xorVal = 0;
    int n = arr.size();

    for (auto e : arr) {
        mn = min(mn, e);
        mx = max(mx, e);

        xorVal ^= e;
    }
    int d = (mx - mn) / (n - 1);

    for (int s = mn; s <= mx; s += d) {
        xorVal ^= s;
    }

    return xorVal == 0;
}
```

---

#### [Kadane's Algorithm (Largest Sum Contiguous Subarray)](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)

Write an efficient program to find the sum of contiguous subarray within a one-dimensional array of numbers that has the largest sum.

```cpp
int kadane(int arr[], int n) {
    int curSum = 0, mxSum = INT_MIN;
    for(int i=0;i<n;i++) {
        curSum += arr[i];
        mxSum = max(mxSum, curSum);
        if(curSum < 0) curSum = 0;
    }
    return mxSum;
}
```

---

#### [Find continuous sequence with given sum](https://www.careercup.com/question?id=6305076727513088)

Question: Given a sequence of positive integers A and an integer T, return whether there is a continuous sequence of A that sums up to exactly T.

```sh
Example
[23, 5, 4, 7, 2, 11], 20. Return True because 7 + 2 + 11 = 20
[1, 3, 5, 23, 2], 8. Return True because 3 + 5 = 8
[1, 3, 5, 23, 2], 7 Return False because no sequence in this array adds up to 7
```

```cpp
bool findSum(vector<int>& arr, int sum) {
    int curSum = 0;
    for (int j = 0, i = 0; i <= arr.size();) {
        if (i < arr.size() && curSum < sum) curSum += arr[i++];
        if (curSum == sum) return true;
        if (i == arr.size() && curSum < sum) return false;

        if (curSum > sum) curSum -= arr[j++];
    }
    return false;
}
```

---

#### [Find first missing positive number in an array]()

Given an array of integers (positive or negative) find the lowest positive integer NOT present in that array.

```sh
Step 1:
Disregard all negatives (put all the positives in the front of the array)

Step 2:
Say the number of positive numbers is: N

we can divide the array into two parts: <=N and >N (the part <=N is first, the >N is second)

Step 3:
For every number x in the array <=N, swap it with the number at index x. Do not swap repeats.

Step 4:
Scan through the numbers and find the first number that does not match its index.
```

```cpp
int firstMissingPositive(vector<int>& arr)
{
    for (int i = 0; i < arr.size();)
    {
        if (i + 1 == arr[i]) i++;
        else if (arr[i] <= 0 || arr[i] >= arr.size()) i++;
        else swap(arr[i], arr[arr[i] - 1]);
    }

    for (int i = 0; i < arr.size(); i++) {
        if (i + 1 != arr[i]) return i + 1;
    }

    return arr.size() + 1;
}
```

---

#### [Find pairs with equal sum](https://www.careercup.com/question?id=5652354158297088)

You're given an array of integers(eg [3,4,7,1,2,9,8]). Find the index of values that satisfy A+B = C+D, where A,B,C & D are integers values in the array.

```sh
Eg: Given [3,4,7,1,2,9,8] array
The following
3+7 = 1+9 satisfies A+B=C+D
so print (0,2,3,5)
```

```cpp
pair<pair<int, int>, pair<int, int>> fourSum3(vector<int> arr) {
    unordered_map<int, pair<int, int>> pairSumMap;

    for (int i = 0; i < arr.size(); i++) {
        for (int j = i + 1; j < arr.size(); j++) {
            int s = arr[i] + arr[j];
            if (pairSumMap.find(s) == pairSumMap.end()) {
                pairSumMap[s] = { i,j };
            }
            else {
                auto old = pairSumMap[s];
                if (old.first != i && old.first != j && old.second != i && old.second != j) {
                    return { {arr[old.first],arr[old.second]},{arr[i],arr[j] } };
                }
            }
        }
    }

    return {};
}
```

---

#### [Minimum swaps required to sort an array](https://www.geeksforgeeks.org/minimum-number-of-swaps-required-to-sort-an-array-set-2/?ref=lbp)

**_If elements are in range from 1-N_**

```cpp
int minimumSwaps(vector<int> arr) {
    int cnt = 0;
    for(int i=0;i<arr.size();) {
        if(arr[i] == i+1) { i++; continue; }
        swap(arr[i], arr[arr[i]-1]);
        cnt++;
    }
    return cnt;
}
```

**_If elements aren't in range 0 to N or 1 to N_**

```cpp
int minimumSwaps(vector<int> arr) {
    vector<pair<int,int>> tmp;
    for(int i=0;i<arr.size();i++) {
        tmp.push_back({arr[i],i});
    }
    sort(tmp.begin(), tmp.end());

    int swaps = 0;
    for(int i=0;i<tmp.size();) {
        if(i == tmp[i].second) i++;
        else {
            swap(tmp[i], tmp[tmp[i].second]);
            swaps++;
        }
    }

    return swaps;
}
```

---

#### [Find indices i, j and k such that a[i]<a[j]<a[k]](https://www.careercup.com/question?id=14960662)

Given a array of integers , find 3 indexes i,j,k such that, i<j<k and a[i] < a[j] < a[k]. Best possible is a O(n) algorithm.

```cpp
void printOrder()
{
    vector<int> arr = { 4, 7, 5, 1, 3, 8, 9, 6, 2 };
    int n = arr.size();
    vector<int> left(arr.size());
    int right = n - 1;

    left[0] = 0;
    for (int i = 1; i < arr.size(); i++) {
        if (arr[i] < arr[left[i - 1]]) left[i] = i;
    }

    for (int i = n - 2; i >= 0; i--) {
        if (arr[left[i]] < arr[i] && arr[i] < arr[right]) {
            cout << left[i] << ", " << i << ", " << right << endl;
            cout << arr[left[i]] << ", " << arr[i] << ", " << arr[right] << endl;
        }
        if (arr[i] > arr[i + 1]) right = i;
    }

    cout << endl << endl;
}
```

---

#### [Find maximum repeated element in an array]()

```sh
Following is the O(n) time and O(1) extra space approach. Let us understand the approach with a simple example where arr[] = {2, 3, 3, 5, 3, 4, 1, 7}, k = 8, n = 8 (number of elements in arr[]).

1) Iterate though input array arr[], for every element arr[i], increment arr[arr[i]%k] by k (arr[] becomes {2, 11, 11, 29, 11, 12, 1, 15 })

2) Find the maximum value in the modified array (maximum value is 29). Index of the maximum value is the maximum repeating element (index of 29 is 3).

3) If we want to get the original array back, we can iterate through the array one more time and do arr[i] = arr[i] % k where i varies from 0 to n-1.
```

```cpp
int findMaxRepeatElem(vector<int> v, int k, int n) {
    for (auto e : v) {
        v[e % k] += k;
    }

    int mxIndex = -1;
    for (int i = 0; i < n; i++) {
        if (mxIndex == -1) mxIndex = i;
        else if (v[i] > v[mxIndex]) mxIndex = i;
    }

    for (int i = 0; i < n; i++) {
        v[i] = v[i] % k;
    }

    return mxIndex;
}
```

---

#### [Length of longest substring without repeating characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

Given a string s, find the length of the longest substring without repeating characters.

```sh
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

```cpp
string lengthOfLongestSubstring(string str) {
    int indices[256];
    for (int i = 0; i < 256; i++) indices[i] = -1;

    string mxStr;
    int start = 0;
    for (int cur = 0; cur < str.length(); cur++) {
        char ch = str[cur];
        if (indices[ch] >= 0) {
            int old = indices[ch];
            if (cur - start > mxStr.size()) mxStr = str.substr(start, cur - start);
            for (; start <= old; start++) indices[str[start]] = -1;
        }
        indices[ch] = cur;
    }
    if (str.length() - start > mxStr.size()) mxStr = str.substr(start, str.length() - start);

    return mxStr;
}
```

---

#### [Find smallest range which contains atleast one integer from each array](https://www.careercup.com/question?id=5103437989543936)

There are many sorted arrays. Find a minimum range, so that in each array there's at least one integer within this range.

```sh
{
    {1,2,4,5,9},
    {3,4,5,6,7},
    {2,3,5,6,7},
    {1,5,8,9,9},
    {10,20,50,60,70}
}

Result: {7,10}
```

```cpp
pair<int, int> findRangeUsingSTL(vector<vector<int>>& input)
{
    using P = pair<int, int>;
    priority_queue<P, vector<P>, greater<P>> minHeap;
    pair<int, int> range = { INT_MAX, INT_MIN };
    vector<int> indices;

    // insert first element of each array
    for (int index = 0; index < input.size(); index++) {
        if (input[index].empty()) return range; // if any of array is empty, no point of moving forward

        minHeap.push({ input[index][0], index });
        indices.push_back(0);
    }

    while (true) {
        auto elem = minHeap.top(); minHeap.pop();
        int arrIdx = elem.second;

        auto newRange = getRange(input, indices);

        if (range.first == INT_MAX || newRange.second - newRange.first < range.second - range.first) {
            range = newRange;
        }

        indices[arrIdx]++; // consider next element

        if (indices[arrIdx] >= input[arrIdx].size()) break; // if any of array gets empty, no point of moving forward
        else {
            int next = input[arrIdx][indices[arrIdx]];
            minHeap.push({ next, arrIdx });
        }
    }

    return range;
}

pair<int, int> getRange(vector<vector<int>>& input, vector<int>& indices)
{
    int mn = INT_MAX, mx = INT_MIN;
    for (int j = 0; j < input.size(); j++) {
        if (input[j][indices[j]] < mn) mn = input[j][indices[j]];
        if (input[j][indices[j]] > mx) mx = input[j][indices[j]];
    }

    return { mn, mx };
}
```

```cpp
pair<int, int> findRangeUsingSTLOptimized(vector<vector<int>>& arrs) {
    using  p = pair<int, pair<int, int>>;
    using v = vector<pair<int, pair<int, int>>>;
    priority_queue<p, v, std::greater<p>> pq;

    int mn = INT_MAX, mx = INT_MIN;

    for (int i = 0; i < arrs.size(); i++) {
        pq.push({ arrs[i][0], {i,0} });
        mn = min(mn, arrs[i][0]);
        mx = max(mx, arrs[i][0]);
    }

    pair<int, int> result = { mn, mx };

    while (true) {
        auto f = pq.top(); pq.pop();
        mn = f.first; // this is the smallest element of range

        if (mx - mn < result.second - result.first) {
            result = { mn, mx };
        }

        int arrIdx = f.second.first;
        int arrPos = f.second.second;
        if (arrPos + 1 < arrs[arrIdx].size()) {
            arrPos++;
            pq.push({ arrs[arrIdx][arrPos], {arrIdx, arrPos} });

            mx = max(arrs[arrIdx][arrPos], mx); // keep updating mx value always
        }
        else {
            // if any of the arrays end, exit it.
            break;
        }
    }

    return result;
}
```

---

#### [Print missing numbers or range from given array and range]()

```sh
{ 1,4,6,7,8,20,50,99 }

Result:
0
2, 3
5
9, 19
21, 49
51, 98
```

```cpp
void printMissingNumbers(vector<int> nums) {
    vector<int> range = { 0,99 };

    for (auto num : nums) {
        auto start = range[0];
        auto end = num - 1;

        if (start <= end) {
            cout << start;
            if (start != end) {
                cout << ", " << end;
            }
            cout << endl;
        }

        range[0] = num + 1;
    }

    if (range[0] <= range[1]) {
        cout << range[0];
        if (range[0] != range[1]) {
            cout << ", " << range[1];
        }
        cout << endl;
    }
}
```

---

#### [Find nearest bike from an employee in 2d campus]()

```sh
{
    {'.', '.', '.', '.', '.', '#'},
    {'.','.','E', '.', '.', '#'},
    {'#', '#', '#' ,'#', '.', '#'},
    {'.','B', '.', '.', '.', '.'},
    {'.', '.', '.', '.','.','B'}
};
Employee Position = (1, 2)
Bike Position = (4, 5)

Result:
Point = (4, 5)
Path = [(1, 2), (1, 3), (1, 4), (2, 4), (3, 4), (3, 5), (4, 5)]
Distance = 7
```

```cpp
Node findNearestBike(vector<vector<char>>& matrix, Point start) {
    int n = matrix.size();
    queue<Node> q;
    q.push(Node(start, { start }));
    unordered_set<int> visited;
    visited.insert(start.x * n + start.y);

    while (!q.empty()) {
        auto f = q.front(); q.pop();
        if (isBike(f.point, matrix)) return f;

        auto nbs = neighbours(f.point, visited, matrix);
        for (auto nb : nbs) {
            auto tmp = f.path;
            tmp.push_back(nb);
            q.push(Node(nb, tmp));
            visited.insert(nb.x * n + nb.y);
        }
    }

    return Node();
}

bool isBike(Point& p, vector<vector<char>>& matrix) {
    return matrix[p.x][p.y] == 'B';
}

vector<Point> neighbours(Point& p, unordered_set<int>& visited, vector<vector<char>>& matrix) {
    vector<Point> result;
    for (int i = -1; i <= 1; i++) {
        for (int j = -1; j <= 1; j++) {
            if (abs(i) != abs(j)) {
                int posx = p.x + i, posy = p.y + j;
                if (!valid(posx, posy, matrix, visited)) continue;
                result.push_back(Point(posx, posy));
            }
        }
    }
    return result;
}

bool valid(int posx, int posy, vector<vector<char>>& matrix, unordered_set<int>& visited) {
    int n = matrix.size();
    int m = matrix[0].size();

    if (posx < 0 || posy < 0 || posx >= n || posy >= m) return false;
    if (visited.find(posx * n + posy) != visited.end()) return false;
    if (matrix[posx][posy] == '#') return false;

    return true;
}
```

---

#### [Number of elements occurring more than n/k times](https://www.careercup.com/question?id=14099679)

Design an algorithm that, given a list of n elements in an array, finds all the elements that appear more than n/3 times in the list. The algorithm should run in linear time ( n >=0 )
You are expected to use comparisons and achieve linear time. No hashing/excessive space/ and don't use standard linear time deterministic selection algo

```sh
{ 4,4,4,1,2,3,5,5,5,5,1,2,3,6,4,5,4 }

output:

Numbers appearing more than (n/k) times, where n=17, k=3
5 occurred 5 times in array
4 occurred 5 times in array
```

```cpp
unordered_map<int, int> findOverNth(int arr[], int n, int k)
{
    unordered_map<int, int> map;
    for (int i = 0; i < n; i++)
    {
        map[arr[i]]++;
        if (map.size() == k)
        {
            for (auto it = map.begin(); it != map.end();)
            {
                it->second--;
                if (it->second == 0) map.erase(it++);
                else  it++;
            }
        }
    }

    for (auto it = map.begin(); it != map.end(); it++)
    {
        it->second = 0;
    }

    for (int i = 0; i < n; i++)
    {
        if (map.find(arr[i]) != map.end())
        {
            map[arr[i]]++;
        }
    }

    for (auto it = map.begin(); it != map.end();)
    {
        if (it->second < n / k) map.erase(it++);
        else it++;
    }

    return map;
}
```

---

#### [Find max repeating element in sorted array](https://www.careercup.com/question?id=5104572540387328)

Given a sorted array of integers, write a function that will return the number with the biggest number of repetitions.
(Asked to refine the solution to be more efficient)

**_Using linear search_**

```cpp
int findMaximumRepeatingElement(int* a, int n)
{
    int count = 1, maxcount = 1, maxelement = a[0];
    int element = a[0];
    int i = 1;
    while (i < n)
    {
        if (a[i] == element)
        {
            count++;
            if (count > maxcount)
            {
                maxcount = count;
                maxelement = a[i];
            }
        }
        else
        {
            element = a[i];
            count = 1;

        }
        i++;
    }
    return maxelement;
}
```

**_Using binary search_**

```cpp
pair<int, int> maxRepeatingElementBinarySearch(int arr[], int n)
{
    int s = 0;
    int mxElem = INT_MIN;
    int mxCnt = 0;

    while (s < n)
    {
        int e = maxRepeatingElementBinarySearch(arr, s, n - 1, arr[s]);
        if (e - s + 1 > mxCnt)
        {
            mxCnt = e - s + 1;
            mxElem = arr[s];
        }
        s = e + 1;
    }

    return { mxElem, mxCnt };
}

int maxRepeatingElementBinarySearch(int arr[], int s, int e, int k)
{
    if (s == e) return arr[s] == k ? s : -1;
    if (s > e) return -1;

    int m = (s + e + 1) / 2; // use upper mid
    if (k < arr[m]) return maxRepeatingElementBinarySearch(arr, s, m - 1, k);
    return maxRepeatingElementBinarySearch(arr, m, e, k);
}
```

---

#### [Get maximum possible number by performing atmost k swaps]()

```sh
for example:
input: 7899, k = 2
maximum possible number: 9987
explanation: 7899 -> 7989 -> 9987
```

\*\*\*Using backup to generate all possible permutation of numbers in vector and see which makes the largest number

```cpp
static int getMaximumNum(int num) {
    auto numVector = getNumVector(num);
    int mx = INT_MIN;
    getMaximum(numVector, 0, numVector.size(), mx, 2);

    return mx;
}

static void getMaximum(vector<int>& nums, int s, int n, int& mx, int k) {
    if (s == n || k == 0) {
        int cur = getNumFromVector(nums);
        mx = max(mx, cur);
        return;
    }

    for (int i = s; i < n; i++) {
        swap(nums[i], nums[s]);
        getMaximum(nums, s + 1, n, mx, k - (i != s));
        swap(nums[i], nums[s]);
    }
}

static vector<int> getNumVector(int num) {
    vector<int> res;
    while (num) {
        res.push_back(num % 10);
        num /= 10;
    }
    reverse(res.begin(), res.end());

    return res;
}

static int getNumFromVector(vector<int> nums) {
    int res = 0;
    for (auto num : nums) {
        res = res * 10 + num;
    }

    return res;
}
```

---

#### [Largest number from concatenation of array elements](https://www.careercup.com/question?id=7781671)

Given an integer array, sort the integer array such that the concatenated integer of the result array is max. e.g. [4, 94, 9, 14, 1] will be sorted to
[9,94,4,14,1] where the result integer is 9944141

```cpp
void largestConcatenation() {
    vector<int> arr = { 4, 94, 9, 14, 1 };
    sort(arr.begin(), arr.end(), [](const auto& f, const auto& s) {
        return stoi(to_string(f) + to_string(s)) > stoi(to_string(s) + to_string(f));
        });

    cout << to_string(arr) << endl;
}
```

---

#### [Least number of perfect sqaures to reach N](https://www.careercup.com/question?id=5725584103571456)

Given a number "n", find the least number of perfect square numbers sum needed to get "n"

```sh
n=12, return 3 (4 + 4 + 4) = (2^2 + 2^2 + 2^2) NOT (3^2 + 1 + 1 + 1)
n = 6, return 3 (4 + 1 + 1) = (2^2 + 1^2 + 1^2)
```

**_Using recursion_**

```cpp
int leastNumberOfPerfectSquares(int n) {
    if(n == 0) return 0;

    int p = sqrt(n);
    int cnt = 999;
    for(int i=p;i>=1;i--) {
        cnt = min(cnt, 1 + leastNumberOfPerfectSquares(n-i*i));
    }

    return cnt;
}
```

**_Using recursion with memoization_**

```cpp
int leastNumberOfPerfectSquares(int n) {
    vector<int> memo(n+1, -1);

    return leastNumberOfPerfectSquares(n, memo);
}

int leastNumberOfPerfectSquares(int n, vector<int>& memo) {
    if(n == 0) return 0;
    if(n < 0) return INT_MAX;
    if(memo[n] != -1) return memo[n];

    int mnVal = INT_MAX;
    for(int i=1;i*i<=n;i++) {
        int val = leastNumberOfPerfectSquares(n-i*i, memo);
        if(val != INT_MAX) mnVal = min(mnVal, val+1);
    }

    memo[n] = mnVal;

    return memo[n];
}
```

**_Using Dynamic Programming_**

```cpp
int leastNumberOfPerfectSquares(int n) {
    vector<int> table(n+1);
    for(int i=0;i<=n;i++) table[i] = i;

    for(int i=1;i*i<=n;i++) {
        for(int j=i*i;j<=n;j++) {
            table[j] = min(table[j], 1 + table[j-i*i]);
        }
    }

    return table[n];
}
```

---

#### [Maximum value of array by applying operators at different places]()

```sh
Your input is a double array, and you can use *, +, -, and () parenthesis anywhere to create and output the maximum possible value.

Ex:
input is {3,4,5,1} --> output: 72
input is {1,1,1,5} --> output: 15

Follow up, if there are numbers <0
```

```cpp
int maxValue(vector<int>& arr) {
    int n = arr.size();
    vector<vector<int>> memo(n, vector<int>(n, INT_MIN));
    vector<char> operators = {'+','*','-'};

    return mxValue(arr, 0, n-1, operators, memo);
}

int mxValue(vector<int>& arr, int l, int r, vector<char>& operators, vector<vector<int>>& memo) {
    if(memo[l][r] != INT_MIN) return memo[l][r];
    if(l == r) return arr[l];

    int mxValue = INT_MIN;
    for(int m=l;m<=r;m++) {
        auto left = mxValue(arr, l, m, r, operators, memo);
        auto right = mxValue(arr, m+1, r, operators, memo);

        for(auto op : operators) {
            mxValue = max(mxValue, apply(left, right, op));
        }
    }
    memo[l][r] = mxValue;

    return memo[l][r];
}

double apply(double left, double right, char ch) {
    switch (ch) {
    case '+': return left + right;
    case '-': return left - right;
    case '*': return left * right;
    }
    return 0;
}
```

---

#### [Maximum product subsequence]()

Given a sequence of non-negative integers find a subsequence of length 3 having maximum product with the numbers of the subsequence
being in ascending order.
Example:
Input: 6 7 8 1 2 3 9 10
Ouput: 8 9 10

```cpp
static pair<int, vector<int>> findSubsequenceWithMaxProduct(vector<int>& arr) {
    int mxProduct = 0;
    vector<int> result;

    for (int i = 1; i < arr.size() - 1; i++) {
        int leftHighestLower = 0;
        for (int j = i - 1; j >= 0; j--) {
            if (arr[j] < arr[i] && arr[j] > leftHighestLower) {
                leftHighestLower = arr[j];
            }
        }

        int rightHighestHigher = 0;
        for (int j = i + 1; j < arr.size(); j--) {
            if (arr[j] > arr[i] && arr[j] > rightHighestHigher) {
                rightHighestHigher = arr[j];
            }
        }

        int curMax = leftHighestLower * arr[i] * rightHighestHigher;
        if (curMax > mxProduct) {
            mxProduct = curMax;
            result = { leftHighestLower , arr[i], rightHighestHigher };
        }
    }

    return { mxProduct, result };
}
```

---

#### [Minimum length subsequence in array with sum greater than given sum](https://www.careercup.com/question?id=5653018213089280)

Determine minimum sequence of adjacent values in the input parameter array that is greater than input parameter sum.

Eg
Array 2,1,1,4,3,6. and Sum is 8
Answer is 2, because 3,6 is minimum sequence greater than 8.

```cpp
vector<int> getMinSeq(vector<int> arr, int sum)
{
    int s = 0, e = 0, curSum = 0;
    int n = arr.size();
    pair<int, int> p = { 0,-1 };
    while (s <= e && e <= n) {
        if (curSum < sum) curSum += arr[e++];
        else curSum -= arr[s++];

        if (curSum > sum) {
            p = { s, e };
        }

        if (e >= n && curSum <= 0) break;
    }

    vector<int> res;
    for (auto i = p.first; i <= p.second; i++) res.push_back(arr[i]);

    return res;
}
```

---

#### [Multiply array elements except itself]()

Multiply array elements except self, before [2, 3, 1, 4] , after [12, 8, 24, 6]

```cpp
vector<int> multiplyExceptSelf(vector<int> arr) {
    int l = 1, r = 1;
    int n = arr.size();
    vector<int> res(n, 1);

    for(int i=0;i<n;i++) {
        res[i] *= l;
        res[n-i-1] *= r;

        l *= arr[i];
        r *= arr[n-i-1];
    }

    return res;
}
```

---

#### [Arrange elements by distance between them]()

Input: 3
Output: [3 1 2 1 3 2]

```cpp
void permutationDistance(int n) {
    vector<int> arr(2 * n, 0);
    unordered_set<int> visited;

    bool res = generatePermutation(arr, visited, n);
    if (res) {
        cout << "Permutations: ";
        for (int i = 0; i < 2 * n; i++) cout << arr[i] << " ";
        cout << endl;
    }
    else {
        cout << "Not possible" << endl;
    }
}

bool generatePermutation(vector<int>& arr, unordered_set<int> visited, int n)
{
    if (visited.size() == n) return true;

    for (int i = 1; i <= n; i++) {
        if (visited.find(i) != visited.end()) continue;
        for (int j = 0; j < arr.size() - i - 1; j++) {
            if (arr[j] == 0 && arr[j + i + 1] == 0) {
                arr[j] = arr[j + i + 1] = i;
                visited.insert(i);
                if (generatePermutation(arr, visited, n)) return true;
                arr[j] = arr[j + i + 1] = 0;
                visited.erase(i);
            }
        }
    }

    return false;
}
```

---

#### [Number of subarrays with product less than K]()

Number of subarrays in [1, 2, 3, 4] with product less than 20 are 8

```cpp
int numSubarrayProductLessThanK(vector<int>& arr, int k) {
    int prod = 1;
    int s = 0, e = 0;
    int res = 0;
    while (s <= e && e <= arr.size()) {
        if (prod <= k) {
            if (prod <= k) res += (e - s); // this is correct because we need to consider all subarrays
            if (e < arr.size()) prod *= arr[e];
            e++;
        }
        else {
            prod /= arr[s++];
        }
    }

    return res;
}
```

---

#### [Print array elements in order]()

Implement iterator over sorted arrays/vectors to print array elements in order.

```cpp
class PrintArrayElementsInOrder {
    using T = tuple<int, int, int>;
    vector<vector<int>> input;
    priority_queue<T, vector<T>, greater<T>> pq;

public:
    PrintArrayElementsInOrder(vector<vector<int>> input) : input(input) {
        for (int i = 0; i < input.size(); i++) {
            if (!input[i].empty()) pq.push({ input[i][0] , i, 0 }); // compare by first element in tuple
        }
    }

    bool hasNext() {
        return !pq.empty();
    }

    int next() {
        auto item = pq.top(); pq.pop();
        auto curVal = std::get<0>(item);
        auto arrIdx = std::get<1>(item);
        auto nextPos = std::get<2>(item) + 1; // next position in this array

        if (nextPos < input[arrIdx].size()) { // not equalTo because that is end of array
            pq.push({ input[arrIdx][nextPos], arrIdx, nextPos });
        }

        return curVal;
    }

public:
    static void test() {
        vector<vector<int>> input = { {1, 5, 7}, {2, 3, 10}, {4, 6, 9 } };

        PrintArrayElementsInOrder obj(input);
        while (obj.hasNext()) {
            cout << obj.next() << " ";
        }
    }
};
```

---

#### [Can arrange numbers divisible by K](https://leetcode.com/problems/check-if-array-pairs-are-divisible-by-k/submissions/)

Given an array of integers arr of even length n and an integer k.

We want to divide the array into exactly n / 2 pairs such that the sum of each pair is divisible by k.

Return true If you can find a way to do that or false otherwise.

```cpp
bool canArrange(vector<int>& arr, int k)
{
    int n = arr.size();
    if (n & 1) return false;
    unordered_map<int,int> count;
    for (auto& e : arr) count[(e%k+k) % k]++; // (e%k+k)%k is required to eliminate negative numbers

    for (int i = 0; i <= k / 2; i++) {
        if(i == 0) {
            if(count[i]&1) return false;
            continue;
        }
        if (count[i] != count[k - i]) return false;
    }

    return true;
}
```

---

#### [Find the number which occurs only once and all the other number occurs exactly 3 times]()

Number occursing only once in [2, 1, 4, 5, 1, 4, 2, 2, 4, 1] is 5
Number occursing only once in [1, 2, 3, 1, 3, 3, 1] is 2
Number occursing only once in [1, 1, 1, 2, 2, 2, 3] is 3
Number occursing only once in [7, 1, 2, 3, 2, 3, 2, 1, 1, 4, 3, 7, 7] is 4

**_Using bitwise add mod 3_**

```cpp
static int numberOccurringOnlyOnceUsingBitwiseAddMod3(int arr[], int n)
{
    int bitsize = 0;
    for (int i = 0; i < n; i++) {
        bitsize = max(bitsize, (int)log2(arr[i]) + 1);
    }
    auto res = addBitwiseMod3(arr, n, bitsize);

    return res;
}

static int addBitwiseMod3(int arr[], int n, int bitsize) {
    if (bitsize == 0) return 0;
    int cur = addBitwiseMod3(arr, n, bitsize - 1);

    int s = 0;
    for (int j = 0; j < n; j++) {
        int v = (arr[j] >> bitsize - 1) & 1;
        s = (s + v) % 3;
    }
    cur = s * pow(2, bitsize - 1) + cur;

    return cur;
}
```

**_Using XORing and ANDing_**

```cpp
int numberOccurringOnlyOnceUsingBitwiseOper(int arr[], int n)
{
    int ones = 0;
    int twos = 0;
    for (int i = 0; i < n; i++)
    {
        int x = arr[i];
        twos |= ones & x;
        ones ^= x;
        int not_threes = ~(ones & twos);
        ones &= not_threes;
        twos &= not_threes;
    }

    return ones;
}
```

---

#### [Maximum possible n such that the array consists at least n values greater than or equals to n](https://www.careercup.com/question?id=5094709806497792)

Given an unsorted array of integers, you need to return maximum possible n such that the array consists at least n values greater
than or equals to n.
Array can contain duplicate values.
Sample input : [1, 2, 3, 4] -- output : 2
Sample input : [900, 2, 901, 3, 1000] -- output: 3

Maximum possible n=2 in array=[1, 2, 3, 4]
Maximum possible n=3 in array=[900, 2, 901, 3, 1000]
Maximum possible n=5 in array=[900, 950, 901, 900, 1000]

```cpp
int getMaxN(vector<int>& arr)
{
    int n = arr.size();
    vector<int> cnt(n + 1, 0);
    for (auto e : arr) {
        if (e < 0) continue;
        if (e >= n) cnt[n]++;
        else cnt[e]++;
    }

    int right = 0;
    for (int i = n; i >= 1; i--) {
        right += cnt[i];
        if (right >= i) return i;
    }

    return 0;
}
```

---

#### [Reverse array in group of size k]()

```cpp
void reverseInGroupsOfSizeK(vector<int>& arr, int k) {
    int len = arr.size();
    for(int i=0;i<len;i+=k) {
        reverse(arr, i, min(i+k-1, len-1));
    }
}

void reverse(vector<int>& arr, int l, int r) {
    while(l<r) {swap(arr[l++], arr[r--]);}
}
```

---

#### [Longest subarray of positive numbers in an array]()

```cpp
static void printLongestPositiveSequence() {
    vector<int> arr = { 1,2,-3,2,3,4,-6,1,2,3,4,5,-8,5,6 };

    int startIndex = -1;
    int mxStartIndex = -1;
    int mxLength = 0;
    int currentCnt = 0;
    for (int i = 0; i < arr.size(); i++) {
        if (arr[i] > 0) {
            if (!currentCnt) startIndex = i;
            currentCnt++;
        }
        else {
            if (currentCnt > mxLength) {
                mxLength = currentCnt;
                mxStartIndex = startIndex;
            }
            currentCnt = 0;
        }
    }

    cout << "Sequence: ";
    for (int i = mxStartIndex; i < mxStartIndex + mxLength; i++) cout << arr[i] << " ";
    cout << endl;
}
```

---

#### [Longest contiguous subsequence]()

```cpp
static void printLargestSubset() {
    vector<int> arr = { 1, 6, 10, 4, 7, 9, 5 };
    unordered_map<int, int> map;
    int beg, end;
    int mxBeg = 0, mxEnd = 0;
    for (auto e : arr) {
        beg = end = e;
        if (map.find(beg - 1) != map.end()) beg = map[beg - 1];
        if (map.find(end + 1) != map.end()) end = map[end + 1];
        map[beg] = end;
        map[end] = beg;
        if (end - beg > mxEnd - mxBeg) {
            mxBeg = beg;
            mxEnd = end;
        }
    }

    for (int i = mxBeg; i <= mxEnd; i++) cout << i << " ";
    cout << endl;
}
```

---

#### [Find shortest subsequence containing subarray]()

```cpp
pair<int, int> findShortestSubsequence(vector<int>& arr, vector<int>& sub) {
    unordered_map<int, int> subCnt, arrCnt;

    for (auto e : sub) subCnt[e]++;

    pair<int, int> result = { INT_MIN, INT_MAX };
    int total = 0, start = 0;
    for (int i = 0; i < arr.size(); i++) {
        int e = arr[i];
        arrCnt[e]++;

        if (arrCnt[e] <= subCnt[e]) total++;

        if (total == sub.size()) {
            while (start <= i && arrCnt[arr[start]] > subCnt[arr[start]]) {
                arrCnt[arr[start]]--;
                start++;
            }

            if (result.first == INT_MIN || (result.second - result.first > i - start)) {
                result = { start, i };
            }

            arrCnt[arr[start]]--;
            start++;
            total--;
        }
    }

    return result;
}
```

---

#### [Split array into 2 equal parts with minimum difference]()

**_Using Recursion_**

```cpp
bool splitArrays(vector<int>& arr) {
    int n = arr.size();
    if(n&1) return false;

    int s = accumulate(arr.begin(), arr.end(), 0);

    int diff = INT_MAX;
    vector<int> indices;
    splitArray(arr, 0, n, s, 0, 0, {}, diff, indices);

    vector<int> arr1, arr2;
    for(int i=0,j=0;i<n;i++) {
        if(j < n/2 && indices[j] == i) {
            arr1.push_back(arr[i]);
            j++;
        } else {
            arr2.push_back(arr[i]);
        }
    }

    cout << to_string(arr1) << " with sum = " << accumulate(arr1.begin(), arr1.end(), 0) << endl;
    cout << to_string(arr2) << " with sum = " << accumulate(arr2.begin(), arr2.end(), 0) << endl;

    return true;
}

void splitArray(vector<int>& arr, int idx, int n, int totalSum,
                int curSetSize, int curSum, vector<int> curSet, int& diff, vector<int>& indices)
{
    if(curSetSize == n/2) {
        int curDiff = abs(totalSum - 2*curSum);
        if(curDiff < diff) {
            diff = curDiff;
            indices = curSet;
            return;
        }
    }

    if(idx >= n || curSetSize > n/2) return;

    splitArray(arr, idx+1, n, totalSum, curSetSize, curSum, curSet, diff, indices);

    curSet.push_back(idx);
    splitArray(arr, idx+1, n, totalSum, curSetSize+1, curSum+arr[idx], curSet, diff, indices);
    curSet.pop_back();
}
```

**_Using Dynamic Programming_**

```cpp
void splitArrayUsingDP(vector<int>& arr) {
    int totalSum = accumulate(arr.begin(), arr.end(), 0)/2;
    int sz = arr.size()/2;

    vector<vector<bool>> memo(sz+1, vector<int>(total+1, false));
    memo[0][0] = true;

    for(int i=0;i<arr.size();i++) {
        for(int k=sz-1;k>=0;k--) {
            for(int j=0;j<=totalSum-arr[i];j++) {
                if(memo[k][j] && arr[i] + j <= totalSum) {
                    memo[k+1][j+arr[i]] = true;
                }
            }
        }
    }

    for (int j = total; j >= 1; j--) {
        if (memo[sz][j]) {
            cout << "sum : " << j << endl;
            break;
        }
    }
}
```

---

#### [Task Assignment]()

There are at most eight servers in a data center. Each server has got a capacity/memory limit. There can be at most 8 tasks that need to be scheduled on those servers. Each task requires certain capacity/memory to run, and each server can handle multiple tasks as long as the capacity limit is not hit. Write a program to see if all of the given tasks can be scheduled or not on the servers?

Ex:
Servers capacity limits: 8, 16, 8, 32
Tasks capacity needs: 18, 4, 8, 4, 6, 6, 8, 8
For this example, the program should say 'true'.

Ex2:
Server capacity limits: 1, 3
Task capacity needs: 4
For this example, program should return false.

Got some idea that this needs to be solved using dynamic programming concept, but could not figure out exact solution.

```cpp
bool taskAssignment(vector<int>& servers, vector<int>& needs, int pos, unordered_map<int, vector<int>>& taskAssignment) {
    if(pos >= needs.size()) return true;

    int index = 0;
    for(auto& server : servers) {
        if(needs[pos] <= server) {
            server -= needs[pos];
            taskAssignment[index].push_back(needs[pos]);
            if(taskAssignment(servers, needs, pos+1, taskAssignment)) return true;
            server += needs[pos];
        }
        index++;
    }

    return false;
}
```

---

#### [Value greater than N]()

Given an unsorted array of integers, you need to return maximum possible n such that the array consists at least n
values greater than or equals to n. Array can contain duplicate values.
Sample input : [1, 2, 3, 4] -- output : 2
Sample input : [900, 2, 901, 3, 1000] -- output: 3

```cpp
static int valueGreaterThanN(vector<int>& arr) {
    int n = arr.size();
    vector<int> count(n+1, 0);

    for(auto val : arr) {
        if(val >= n) count[n]++;
        else if(val > 0) count[val]++;
    }

    int right = 0;
    for(int i=n;i>=1;i--) {
        right += count[i];
        if(right >= i) return i;
    }

    return 0;
}
```

---

#### [Sort binary array]()

```cpp
void sort(vector<int>& arr) {
    for(int i=0,j=0;j<arr.size();j++) {
        if(arr[j] == 0) swap(arr[i++], arr[j]);
    }
}
```

```cpp
void sort(vector<int>& arr) {
    int s=0,e=arr.size()-1;
    while(s<e) {
        if(arr[s] == 0) s++;
        else {
            if(arr[e] == 0) swap(arr[s++], arr[e]);
            else e--;
        }
    }
}
```

---

#### [Generate Coin Combinations upto given sum]()

```cpp
vector<int> generateCombinationsUptoGivenSum(vector<int>& coins, int n, int sum) {
    vector<int> result;
    unordered_set<int> seen;
    seen.insert(0);

    for(int i=0;i<=sum;i++) {
        for(int j=0;j<n;j++) {
            if(s.find(i-coins[j]) != s.end()) {
                result.push_back(i);
                s.insert(i);
                break;
            }
        }
    }

    return result;
}
```

---

#### [Search element in almost sorted arrat]()

An array consist of elements whose difference is positive or negative 1. I have to find the given elements without using linear search.
Say
Int arr[]={1,2,3,4,3,4,5,6,7
To find : 6

The answer is skipping. If you're starting at 1, and you're looking for 6, then you know that the best case scenario is the next five values
increase by one each. So you skip 5 and check that value. You repeat the same step. It turns out the value at that position is 4.
The best case scenario is the next two values increase by one each. So you jump two positions and there's your number.

The key is to figure out at each position how far the goal value is from that position's value and jump ahead by as many steps.

```cpp
int search(vector<int>& arr, int x) {
    int n = arr.size();
    int l=0, r = arr[n-1];

    while(l <= r) {
        if(arr[l] == x) return l;
        l += (x-arr[l]);
    }

    return -1;
}
```

---

#### [Find insert positions in sorted array]()

```cpp
int findInsertPosition(vector<int>& arr, int e) {
    int l = 0, r = arr.size() - 1;
    if (r == -1) return l; // if array is empty, then insert at 0th position

    while (l < r) {
        int m = (l + r) / 2;
        if (e >= arr[m]) l = m + 1; // insert element right before next greater element
        else r = m;
    }

    return arr[l] <= e ? (l + 1) : l; // if all array elements are less than e, then insert end+1 position, else lth position
}
```

---

#### [Find surpassers of an array]()

Surpassers count is number of larger elements present on right side of array element.

```cpp
vector<int> findSurpassers(vector<int>& arr) {
    vector<int> result;
    int n = arr.size();

    unordered_map<int, int> invCntMap;
    vector<int> dup = arr; // create array copy as merge sort will modify array
    vector<int> tmp(n); // tmp array for merging

    mergeSort(dup, 0, n - 1, tmp, invCntMap);

    for (int i = 0; i < n; i++) {
        result.push_back(n - i - 1 - invCntMap[arr[i]]); // remove elements on left including itself(i+1) + invCnt for element
    }

    return result;
}

void mergeSort(vector<int>& arr, int l, int r, vector<int>& tmp, unordered_map<int, int>& invCntMap) {
    if (l >= r) return;

    int m = (l + r) / 2;
    mergeSort(arr, l, m, tmp, invCntMap);
    mergeSort(arr, m + 1, r, tmp, invCntMap);

    merge(arr, l, m, r, tmp, invCntMap);
}

void merge(vector<int>& arr, int l, int m, int r, vector<int>& tmp, unordered_map<int, int>& invCntMap) {
    int i = l, j = m + 1, k = l, invCnt = 0;

    while (i <= m || j <= r) {
        if (i <= m && j <= r) {
            if (arr[i] <= arr[j]) {
                invCntMap[arr[i]] += invCnt;
                tmp[k++] = arr[i++];
            }
            else {
                tmp[k++] = arr[j++];
                invCnt++;
            }
        }
        else if (i <= m) {
            invCntMap[arr[i]] += invCnt;
            tmp[k++] = arr[i++];
        }
        else if (j <= r) {
            tmp[k++] = arr[j++];
        }
    }

    for (i = l; i <= r; i++) arr[i] = tmp[i];
}
```

---

#### [Get Next Or Prev Number by adding or substracting 1]()

```sh
Input=[1] , Next=[2] , Prev=[0]

Input=[5] , Next=[6] , Prev=[4]

Input=[3] , Next=[4] , Prev=[2]

Input=[2] , Next=[3] , Prev=[1]

Input=[9, 9] , Next=[1, 0, 0] , Prev=[9, 8]

Input=[1, 0, 0] , Next=[1, 0, 1] , Prev=[9, 9]

Input=[0] , Next=[1] , Prev=[-1]

Input=[9, 9, 9] , Next=[1, 0, 0, 0] , Prev=[9, 9, 8]

Input=[1, 0, 0, 1] , Next=[1, 0, 0, 2] , Prev=[1, 0, 0, 0]
```

```cpp
vector<int> next(vector<int> input) {
    int n = input.size();
    int carry = 1; // to add one element
    for (int i = n - 1; i >= 0; i--) {
        int e = input[i] + carry;
        input[i] = e % 10;
        carry = e / 10;
    }
    if (carry) input.insert(input.begin(), carry);

    return input;
}

vector<int> prev(vector<int> input) {
    int n = input.size();
    int borrow = 1; // substract 1

    for (int i = n - 1; i >= 0; i--) {
        int e = (input[i] - borrow);
        borrow = 0;

        if (e < 0) {
            e += 10;
            borrow = 1;
        }
        input[i] = e;
    }
    if (borrow) input[0] -= 10;
    if (input[0] == 0) input.erase(input.begin());
    if (input.empty()) input.push_back(0);

    return input;
}
```

---

#### [Maximize Aj-Ai such that j>i]()

One unsorted array is given.Find out the index i and j ,j> i for which a[j] - a[i] is maximum.perform in linear time complexity

```cpp
pair<int, pair<int, int>> findMaxDiff(vector<int> arr) {
    pair<int, pair<int, int>> result;
    int mnElemIndex = -1, mxElemIndex = -1;
    int mnElem = INT_MAX;
    int maxValueDiff = 0;
    for (int i = 0; i < arr.size(); i++) {
        if (arr[i] < mnElem) {
            mnElem = arr[i];
            mnElemIndex = i;
        }
        else {
            if (arr[i] - mnElem > maxValueDiff) {
                maxValueDiff = arr[i] - mnElem;
                mxElemIndex = i;
            }
        }
    }

    return { maxValueDiff,{mnElemIndex,mxElemIndex} };
}
```

---

#### [Find Minimum Length Subsequence in array with sum greater than given sum]()

```cpp
vector<int> findMinLengthSubarrayWithSumGreaterOrEqualToGivenSum(vector<int>& arr, int x) {
    vector<int> result;

    int curSum = 0;
    pair<int, int> window = {-1,-1};
    for(int i=0,j=0;j<arr.size();j++) {
        curSum += arr[j];

        while(i<=j && curSum-arr[i] > x) {
            curSum -= arr[i++];
        }

        if(curSum >= x && (window.first == -1 || (j-i < window.second-window.first>))) {
            window = {i, j};
        }
    }

    if(window.first != 0) {
        for(int i=window.first;i<=window.second;i++) result.push_back(arr[i]);
    }

    return result;
}
```

---

#### [Find Minimum length unsorted subarray](https://www.geeksforgeeks.org/minimum-length-unsorted-subarray-sorting-which-makes-the-complete-array-sorted/)

Given an unsorted array arr[0..n-1] of size n, find the minimum length subarray arr[s..e] such that sorting this subarray makes the whole array sorted.
Examples:

1. If the input array is [10, 12, 20, 30, 25, 40, 32, 31, 35, 50, 60], your program should be able to find that the subarray lies between the indexes 3 and 8.
2. If the input array is [0, 1, 15, 25, 6, 7, 30, 40, 50], your program should be able to find that the subarray lies between the indexes 2 and 5.

```cpp
static vector<int> minLengthUnsortedSubArr(vector<int>& v)
{
    int s = 0, e = v.size() - 1;
    while (s < e && v[s] <= v[s + 1]) s++;
    while (e > s && v[e] >= v[e - 1]) e--;
    if (s >= e) return {};

    int minElem = v[s], maxElem = v[e];
    for (int i = s; i <= e; i++) {
        minElem = min(minElem, v[i]);
        maxElem = max(maxElem, v[i]);
    }

    while (s >= 0 && v[s] > minElem) s--;
    while (e <= v.size() - 1 && v[e] < maxElem) e++;

    vector<int> result;
    for (int i = s + 1; i < e; i++) result.push_back(v[i]);

    return result;
}
```

---

#### [Pancake Sorting]()

```cpp
void pancakeSort(vector<int>& arr) {
    for(int r=arr.size()-1;r>=1;r--) {
        int mxi = maxElementIndex(arr, 0, r);
        if(mxi != r) {
            reverse(arr, 0, mxi); // get max element at start pos
            reverse(arr, 0, r); // move the max element to end
        }
    }
}
```

---

#### [Prime Extensible Numbers]()

```cpp
vector<int> primeExtensibles(vector<int>& primes, int n) {
    int m = primes.size();
    vector<int> indices(m, 0);
    vector<int> result(1, 1);

    for(int i=2;i<=n;i++) {
        int mn = getMin(primes, indices);
        result.push_back(mn);
    }

    return result;
}

int getMin() {
    int mnValue = INT_MAX;
    for(int i=0;i<primes.size();i++>) {
        mnValue = min(mnValue, primes[i] * result[indices[i]]);
    }

    for(int i=0;i<indices.size();i++) {
        if(primes[i] * result[indices[i]] == mnValue) indices[i]++;
    }

    return mnValue;
}
```

---

#### [Radix Sort]()

```cpp
class RadixSort
{
public:
    static void test()
    {
        RadixSort obj;

        {
            vector<vector<string>> inputs = {
                {"singh", "amit", "pratap"},
                {"hello", "world", "cpp"}
            };

            for (auto& input : inputs) {
                cout << "Sorting string list: " << to_string(input) << endl;

                obj.radixSort(input);
                cout << "Sorted string list: " << to_string(input) << endl;
            }
        }

        {
            vector<vector<int>> inputs = {
                {1,2,3,4,5},
                {5,4,3,2,1},
                {10,100,1000,10000},
                {1000,100,10,1},
                {},
                {1},
                {8},
                {10,8},
                {100,50,1},
                {1000,1}
            };

            for (auto& input : inputs) {
                cout << "Sorting string list: " << to_string(input) << endl;

                obj.radixSort(input);
                cout << "Sorted string list: " << to_string(input) << endl;
            }
        }
    }

    void radixSort(vector<string>& strlist)
    {
        int range = 256;
        int mxLength = 0;
        for (auto& str : strlist) mxLength = max(mxLength, (int)str.length());

        while (mxLength >= 1) {
            countSort(strlist, mxLength - 1, range);
            mxLength--;
        }
    }

    // Radix sort an string array
    void countSort(vector<string>& v, int pos, int range)
    {
        vector<string> output;
        output.resize(v.size());

        int* count = new int[range];
        memset(count, 0, sizeof(int) * range); // init count of each element in this range

        for (int i = 0; i < v.size(); i++) {
            int index = 0;
            if (v[i].length() > pos) {
                index = v[i][pos];
            }
            count[index]++; // increment count of each element
        }

        for (int i = 1; i < range; i++) count[i] += count[i - 1]; // get additive count i.e. its position in range

        // place element at corect position
        for (int i = v.size() - 1; i >= 0; i--) {
            int index = 0;
            if (v[i].length() > pos) {
                index = v[i][pos];
            }

            int elementCount = count[index];
            int elementLastIndx = elementCount - 1;
            output[elementLastIndx] = v[i];
            count[index]--;
        }

        // fill the old array back
        for (int i = 0; i < v.size(); i++) v[i] = output[i];
    }

    // Radix sort an integer array
    void radixSort(vector<int>& arr) {
        for (int exp = 1;; exp *= 10) {
            if (!countSort(arr, 10, exp)) break;
        }
    }

    bool countSort(vector<int>& arr, int range, int exp) {
        vector<int> count(range, 0);
        vector<int> output(arr.size());

        bool processed = false;
        for (auto e : arr) {
            count[(e / exp) % 10]++;
            if ((e / exp) != 0) processed = true;
        }

        if (!processed) return processed;

        for (int i = 1; i < range; i++) count[i] += count[i - 1];

        for (int i = arr.size() - 1; i >= 0; i--) {
            int e = arr[i];
            int idx = count[(e / exp) % 10];
            output[idx - 1] = arr[i];
            count[(e / exp) % 10]--;
        }

        arr = output;

        return processed;
    }
};
```

---

#### [Rearrange elements by their value]()

```cpp
void rearrangeElements() {
    int n = 3;
    vector<int> arr(2*n, 0);
    unordered_set<int> visited;

    if(canBeArranged(arr, 0, n, visited)) {
        cout<<to_string(arr)<<endl;
    } else {
        cout<<"Can't be arranged"<<endl;
    }
}

bool canBeArranged(vector<int>& arr, int index, int n, unordered_set<int>& visited) {
    if(visited.size() == n) return true;
    if(index >= 2*n || arr[index] != 0) return false;

    for(int num=1;num<=n;num++) {
        if(visited.find(num) != visited.end()) continue;

        int nextIndex = index+num+1;
        if(nextIndex >= 2*n || arr[nextIndex] != 0) continue;

        arr[index] = arr[nextIndex] = num;
        visited.insert(num);

        if(canBeArranged(arr, index+1, n, visited)) return true;

        arr[index] = arr[nextIndex] = 0;
        visited.erase(num);
    }

    return false;
}
```

---

#### [Find duplicate element in array](https://leetcode.com/problems/find-the-duplicate-number/)

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        return findDuplicateUsingBinarySearch(nums);
    }

    int findDuplicateByMovingOutOfPlaceElementToEnd(vector<int>& nums) {
        for(int i=0;i<nums.size();) {
            if(nums[nums[i]-1] == nums[i]) {
                // if element is already at correct position, it must be same position
                if(nums[i] != i+1) {
                    return nums[i]; // this will detect all duplicates
                }
                i++;
            } else { // if element isn't at correct position, move the current element to its correct position
                swap(nums[nums[i]-1], nums[i]);
            }
        }
        return INT_MAX;
        //return nums.back()+1; // this should never hit?
    }

    // O(nlogn)
    int findDuplicateUsingSorting(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        for(int i=1;i<nums.size();i++) {
            if(nums[i] == nums[i-1]) return nums[i];
        }
        return INT_MAX;
    }

    // O(nlogn)
    int findDuplicateUsingBinarySearch(vector<int>& nums) {
        int left = 1, right = nums.size() - 1;
        while(left < right) {
            int mid = left + (right - left) / 2;

            // count the number of elements smaller/ equal than middle element
            int c = 0;
            for(const int& el: nums)
                if(el <= mid)
                    ++c;

            if(c > mid)
                right = mid;
            else
                left = mid + 1;
        }
        return left;
    }

    // O(n) - only works if all elements are positive
    // this doesn't work because number can be repeated any number of times.
    int findDuplicateUsingIndexMarking(vector<int>& nums) {
        for(int i=0;i<nums.size();i++) {
            int index = abs(nums[i])-1;
            if(nums[index] < 0) return abs(nums[i]);
            nums[index] *= -1; // mark this index as seen
        }
        return INT_MAX;
    }

    // O(n)
    // same for this one
    int findDuplicateUsingFloyedCycleDetection(vector<int>& nums) {
        int slow = nums[0];
        int fast = nums[0];

        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while(slow != fast);

        fast = nums[0];
        while(slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }

        return slow;
    }

    // O(n)
    int findDuplicateUsingTwoEquations(vector<int>& nums) {
        long double product = 1.0;
        long long sum = 0;

        double index = 1.0;
        for(auto& num : nums) {
            product *= (double)num;
            product /= index;
            sum += num;
            index++;
        }
        // a-b=x, a/b=y
        // by-b=x => b(y-1)=x => b=x/(y-1)
        int n = nums.size();
        long long diff = n*(n+1)/2 - sum;

        int second = diff/(product-1);
        int first =  diff + second;

        for(auto& num : nums) {
            if(num == abs(first)) return num;
            if(num == abs(second)) return num;
        }

        return INT_MAX;
    }

    // O(n)
    int findDuplicateUsingXOR(vector<int>& nums) {
        int xorValue = 0;
        for(int i=0;i<nums.size();i++) {
            xorValue ^= (i+1);
            xorValue ^= nums[i];
        }

        int lastSetBit = ~(xorValue&(xorValue-1));
        int first = 0, second = 0;

        for(auto& num : nums) {
            if(num & lastSetBit) first ^= num;
            else second ^= num;
        }

        for(auto& num : nums) {
            if(num == first) return num;
            if(num == second) return num;
        }

        return INT_MAX;
    }
};
```
