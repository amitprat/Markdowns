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
            inv += m-i+1;
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
pair<int, int> smallestRange(vector<vector<int>> arrs) {
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
        mn = f.first;
        mx = max(f.first, mx);

        if (mx - mn < result.second - result.first) {
            result = { mn, mx };
        }

        int arrIdx = f.second.first;
        int arrPos = f.second.second;
        if (arrPos < arrs[arrIdx].size() - 1) {
            arrPos++;
            pq.push({ arrs[arrIdx][arrPos], {arrIdx, arrPos} });
        }
        else {
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

```cpp
int leastNumberOfPerfectSquares(int n) {
    vector<int> table(n+1);
    for(int i=0;i<=n;i++) table[i] = i;

    for(int i=2;i*i<=n;i++) {
        int p = pow(i, 2);
        for(int j=p;j<=n;j++) {
            table[j] = min(table[j], 1 + table[j-p]);
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
