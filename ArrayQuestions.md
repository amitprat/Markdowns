####[Consecutive 1's not allowed ](https://practice.geeksforgeeks.org/problems/consecutive-1s-not-allowed1912/1)

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

####[Maximum Index ](https://practice.geeksforgeeks.org/problems/maximum-index-1587115620/1)

Given an array A[] of N positive integers. The task is to find the maximum of j - i subjected to the constraint of A[i] < A[j] and i < j.

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

####[Count Inversions](https://practice.geeksforgeeks.org/problems/inversion-of-array-1587115620/1)

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
            inv += m+1-i;
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

####[Iterative Quick Sort]()

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
####[Arrange numbers by their value]()

```sh
Input: [1,3]
Output:[3 1 2 1 3 2]

Input: [1,7]
Output:[1 4 1 5 6 7 4 2 3 5 2 6 3 7]
```

```cpp
bool canBePlaced(int startNum, int endNum, int pos, vector<int>& result, unordered_set<int>& set) {
    if(set.size() == endNum) return true;
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

####[Print First N Prime Numbers]()

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
    for(int cnt=0;cnt<n;) {
        if(isPrime(num)) {
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
    for(int i=2;i<mxValue && cnt<n;i++) {
        if(primes[i]) {
            result.push_back(i);
            markItsFactors(primes, i, mxValue);
            cnt++;
        }
    }

    return result;
}

void markItsFactors(vector<bool>& primes, int i, int mxValue) {
    for(int j=i*2;j<=mxValue;j+=i) {
        primes[j] = false;
    }
}
```
---

####[Arrange Numbers in alternative orders](https://www.careercup.com/question?id=5684901156225024)

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

####[Assign A or B Without Conditional Operator](https://www.careercup.com/question?id=5135296679116800)

You are given four integers 'a', 'b', 'y' and 'x', where 'x' can only be either zero or one. Your task is as follows:

If 'x' is zero assign value 'a' to the variable 'y', if 'x' is one assign value 'b' to the variable 'y'.

You are not allowed to use any conditional operator (including ternary operator).
Follow up: Solve the problem without utilizing arithmetic operators '+ - * /'.

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
####[Check if array is in arithmetic prgression (AP)](https://www.careercup.com/question?id=5724684365594624)

Check if an integer array is arithmetic sequence.

Example: 1, 2, 3, 4, 5, 6, 7, 8 => true
1, 3, 5, 7, 9 => true

Array may not be sorted.

```
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



