#### [Edit Distance ](https://practice.geeksforgeeks.org/problems/edit-distance3702/1)

```sh
Given two strings s and t. Return the minimum number of operations required to convert s to t.
The possible operations are permitted:

Insert a character at any position of the string.
Remove any character from the string.
Replace any character from the string with any other character.
```

```cpp
int editDistance(string s, string t) {
    int n = s.length(), m= t.length();
    vector<vector<int>> table(n+1,vector<int>(m+1));

    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if (i == 0 && j == 0) table[i][j] = 0;
            else if (i == 0) table[i][j] = j; // need to delete j characters
            else if (j == 0) table[i][j] = i; // need to insert i characters
            else {
                if (s[i-1] == t[j-1]) table[i][j] = table[i-1][j-1];
                else table[i][j] = 1+min(table[i-1][j-1],min(table[i-1][j],table[i][j-1]));
            }
        }
    }

    return table[n][m];
}
```

---

#### [Coin Change](https://practice.geeksforgeeks.org/problems/coin-change2448/1)

Given a value N, find the number of ways to make change for N cents, if we have infinite supply of each of S = { S1, S2, .. , SM } valued coins.

**Count number of ways to make change for given sum**

```cpp
long long int countDP(int S[], int m, int n) {
    vector<long long> memo(n+1, 0);
    memo[0] = 1;

    for(int i=0;i<m;i++) {
        for(int j=S[i];j<=n;j++) {
            memo[j] += memo[j-S[i]];
        }
    }

    return memo[n];
}

long long int countRec(int S[], int m, int n) {
    if(n == 0) return 1;
    if(n < 0 || m <= 0) return 0;

    return countRec(S, m, n-S[m-1]) + countRec(S, m-1, n);
}
```

#### [Minimum sum partition ](https://practice.geeksforgeeks.org/problems/minimum-sum-partition3317/1)

Given an integer array arr of size N, the task is to divide it into two sets S1 and S2 such that the absolute difference between their sums is minimum and find the minimum difference.

```sh
Input: N = 4, arr[] = {1, 6, 11, 5}
Output: 1
Explanation:
Subset1 = {1, 5, 6}, sum of Subset1 = 12
Subset2 = {11}, sum of Subset2 = 11
```

**Dynamic Programming Approach**

```cpp
int minDifference(int arr[], int n)  {
    int sum = std::accumulate(arr, arr+n, 0);
    int half = sum/2;

    vector<vector<bool>> memo(half+1, vector<bool>(n+1, false));

    int minDiff = INT_MAX;
    for(int s=0;s<=half;s++) {
        for(int i=0;i<=n;i++) {
            if(s == 0) memo[s][i] = true;
            else if(i == 0) memo[s][i] = false;
            else if(arr[i-1] <= s) {
                memo[s][i] = memo[s][i-1] || memo[s-arr[i-1]][i-1];
            } else {
                memo[s][i] = memo[s][i-1];
            }

            if(memo[s][i]) {
                minDiff = min(minDiff, sum - 2*s);
            }
        }
    }

    return minDiff;
}
```

**Recursion Approach**

```cpp
int minDifferenceRec(int arr[], int n)  {
    int diff = INT_MAX;
    int sum = std::accumulate(arr, arr+n, 0);

    minDifference(arr, 0, n, 0, sum, diff);

    return diff;
}

void minDifference(int arr[], int index, int n, int curSum, int totalSum,int& diff)
{
    if(index == n) {
        int newDiff = totalSum - 2*curSum;
        diff = min(diff, abs(newDiff));
        return;
    }

    minDifference(arr, index+1, n, curSum, totalSum, diff);
    minDifference(arr, index+1, n, curSum+arr[index], totalSum, diff);
}
```

```cpp
int findMinSumDiffRecursion(vector<int>& arr, int index, int n, int curSum, int totalSum)
{
    if (index == n) return abs(totalSum - 2*curSum);
    if (index >= n) return INT_MAX;

    return min(findMinSumDiffRecursion(arr, index + 1, n, curSum, totalSum),
               findMinSumDiffRecursion(arr, index + 1, n, curSum + arr[index], totalSum));
}
```

---

#### [Maximum sum increasing subsequence ](https://practice.geeksforgeeks.org/problems/maximum-sum-increasing-subsequence4749/1)

Given an array arr of N positive integers, the task is to find the maximum sum increasing subsequence of the given array.

**Maximum Sum Increasing Subsequence Using Recursion**

```cpp
int maxSumSubsequence(int arr[], int n, int next=INT_MAX) {
    if(n == 0) return 0;

    int curSum = 0;
    if(arr[n-1] < next) {
        curSum = arr[n-1] + maxSumSubsequence(arr, n-1, arr[n-1]);
    }

    curSum = max(curSum, maxSumSubsequence(arr, n-1, next));

    return curSum;
}
```

**Maximum sum increasing subsequence Using DP**

```cpp
int maxSumIncreasingSubsequence(int arr[], int n) {
    int dp[n+1];
    dp[0] = 0;

    for(int i=1;i<=n;i++) {
        dp[i] = arr[i-1];
        for(int j=i-1;j>0;j--) {
            if(arr[i-1] > arr[j-1]) {
                dp[i] = max(dp[i], arr[j-1] + dp[j]);
            }
        }
    }

    return *max_element(dp, dp+n+1);
}
```

---

#### [Longest Increasing Subsequence ](https://practice.geeksforgeeks.org/problems/longest-increasing-subsequence-1587115620/1)

Given an array of integers, find the length of the longest (strictly) increasing subsequence from the given array.

```cpp
int longestIncreasingSubsequence(int arr[], int n, int next=INT_MAX) {
    if(n == 0) return 0;

    int cnt = 0;
    if(arr[n-1] < next)
        cnt = 1 + longestIncreasingSubsequence(arr, n-1, arr[n-1]);

    cnt = max(cnt, longestIncreasingSubsequence(arr, n-1, next));

    return cnt;
}
```

```cpp
int lisDP(int a[], int n) {
    vector<int> memo(n+1);
    memo[0] = 0;

    for(int i=1;i<=n;i++) {
        memo[i] = 1;
        for(int j=i-1;j>0;j--) {
            if(a[i-1] > a[j-1])
                memo[i] = max(memo[i], 1+ memo[j]);
        }
    }

    return *max_element(memo.begin(), memo.end());
}
```

---

#### [Longest Increasing Subsequence in Matrix](https://www.geeksforgeeks.org/find-the-longest-path-in-a-matrix-with-given-constraints/)

```sh
[
   {  1,   2,   9, }
   {  5,   3,   8, }
   {  4,   6,   7, }
]
Result = (6,4)
Longest subsequence: 6 7 8 9

[
   {  1,   2,   8, }
   {  9,   3,   7, }
   {  4,   5,   6, }
]
Result = (4,5)
Longest subsequence: 4 5 6 7 8
```

**_Using Recursion_**

```cpp
static pair<int, int> longestSequenceRec(vector<vector<int>> mat) {
    int start = 0, longest = 0;
    int n = mat.size();
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            int curLongest = longestSequenceRec(mat, i, j, n);
            if (curLongest > longest) {
                start = mat[i][j];
                longest = curLongest;
            }
        }
    }

    return { start, longest };
}

static int longestSequenceRec(vector<vector<int>> mat, int r, int c, int n) {
    int longest = 1;
    for (int i = -1; i <= 1; i++) {
        for (int j = -1; j <= 1; j++) {
            if (abs(i) != abs(j)) {
                int nr = r + i;
                int nc = c + j;

                if (!isValid(nr, nc, n) || mat[r][c] + 1 != mat[nr][nc]) continue;

                int curLongest = 1 + longestSequenceRec(mat, nr, nc, n);
                longest = max(longest, curLongest);
            }
        }
    }

    return longest;
}

static bool isValid(int r, int c, int n) {
    return !(r < 0 || c < 0 || r >= n || c >= n);
}
```

**_Using DP_**

```cpp
static pair<int, int> longestSequenceDP(vector<vector<int>> mat) {
    int n = mat.size();
    vector<vector<int>> memo(n, vector<int>(n, -1));

    int start = 0, longest = 0;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (memo[i][j] == -1) {
                int curLongest = longestSequenceDP(mat, i, j, n, memo);
                if (curLongest > longest) {
                    start = mat[i][j];
                    longest = curLongest;
                }
            }
        }
    }

    return { start, longest };
}

static int longestSequenceDP(vector<vector<int>> mat, int r, int c, int n, vector<vector<int>>& memo) {
    if (memo[r][c] != -1) return memo[r][c];

    int longest = 1;
    for (int i = -1; i <= 1; i++) {
        for (int j = -1; j <= 1; j++) {
            if (abs(i) == abs(j)) continue;
            int nr = r + i;
            int nc = c + j;

            if (!isValid(nr, nc, n) || mat[r][c] + 1 != mat[nr][nc]) continue;

            int curLongest = 1 + longestSequenceDP(mat, nr, nc, n, memo);
            longest = max(longest, curLongest);
        }
    }

    memo[r][c] = longest;

    return memo[r][c];
}
```

---

#### [Longest Common Substring ](https://practice.geeksforgeeks.org/problems/longest-common-substring1452/1)

Given two strings. The task is to find the length of the longest common substring.

```cpp
int longestCommonSubstr (string S1, string S2, int n, int m)
{
    return longestCommonSubstrDP(S1, S2, n, m);
}

int longestCommonSubstrRec(string S1, string S2, int n, int m, int len)
{
    if(n == 0 || m == 0) return len;

    if(S1[n-1] == S2[m-1]) return longestCommonSubstrRec(S1,S2,n-1,m-1,len+1);

    return max(longestCommonSubstrRec(S1,S2,n-1,m,0), longestCommonSubstrRec(S1,S2,n,m-1,0));
}

int longestCommonSubstrDP (string S1, string S2, int n, int m)
{
    vector<vector<int>> table(n+1,vector<int>(m+1));
    int longest = 0;
    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            table[i][j] = 0;
            if(i == 0 || j == 0) table[i][j] = 0;
            else if(S1[i-1] == S2[j-1]) table[i][j] = 1 + table[i-1][j-1];

            longest = max(longest, table[i][j]);
        }
    }

    return longest;
}
```

---

#### [Longest Common Subsequence]()

Longest common subsequence for str1=AGGTAB and str2=GXTXAYB is length=4
Subsequence: GTAB

```cpp
int longestCommonSubsequenceRec(string& str1, string& str2, int n, int m) {
    if(n == 0 || m == 0) return 0;
    if(str1[n-1] == str2[m-1]) return 1 + longestCommonSubsequenceRec(str1, str2, n-1, m-1);
    return max(longestCommonSubsequenceRec(str1, str2, n-1, m), longestCommonSubsequenceRec(str1, str2, n, m-1));
}

int longestCommonSubsequenceDP(string& str1, string& str2, int n, int m) {
    vector<vector<int>> table(n+1, vector<int>(m+1));

    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if(i == 0 || j == 0) table[i][j] = 0;
            else if(str1[i-1] == str2[j-1]) table[i][j] = 1 + table[i-1][j-1];
            else table[i][j] = max(table[i-1][j], table[i][j-1]);
        }
    }

    return table[n][m];
}
```

**_Print the longets subsequence_**

```cpp
string longestCommonSubsequenceDP(string a, int n, string b, int m) {
    vector<vector<int>> table(n + 1, vector<int>(m + 1));

    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= m; j++) {
            if (i == 0 || j == 0) table[i][j] = 0;
            else if (a[i - 1] == b[j - 1]) table[i][j] = 1 + table[i - 1][j - 1];
            else table[i][j] = max(table[i - 1][j], table[i][j - 1]);
        }
    }

    string res;
    int i = n, j = m;
    while (table[i][j] > 0) {
        if (i == 0) {
            j--;
        }
        else if (j == 0) {
            i--;
        }
        else if (a[i - 1] == b[j - 1]) {
            res += a[i - 1]; i--; j--;
        }
        else if (table[i - 1][j] > table[i][j - 1]) {
            i--;
        }
        else {
            j--;
        }
    }
    reverse(res.begin(), res.end());

    return res;
}
```

---

#### [Longest repeated subsequence](https://www.geeksforgeeks.org/longest-repeated-subsequence/)

Given a string, print the longest repeating subsequence such that the two subsequence don’t have same string character at same position, i.e., any i’th character in the two subsequences shouldn’t have the same index in the original string.

```sh
Input: str = "aabb"
Output: "ab"

Input: str = "aab"
Output: "a"
The two subsequence are 'a'(first) and 'a'
(second). Note that 'b' cannot be considered
as part of subsequence as it would be at same
index in both.
```

```cpp
int findLongestRepeatingSubSeq(string str)
{
    int n = str.length();
    int dp[n+1][n+1];

    for(int i=0;i<=n;i++){
      dp[i][0] =0;
      dp[0][i] =0;
    }

    for (int i=1; i<=n; i++)
    {
        for (int j=1; j<=n; j++)
        {
            // for repeated subsequence, we don't consider same index element
            if (str[i-1] == str[j-1] && i != j) dp[i][j] =  1 + dp[i-1][j-1];
            else dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
        }
    }

    return dp[n][n];
}
```

**_Print the longest repeated subsequence_**

```cpp
string longestRepeatedSubSeq(string str)
{
    // First part of code remains same as above.
    int n = str.length();
    int dp[n+1][n+1];
    for (int i=0; i<=n; i++) {
        dp[i][0] = 0;
        dp[0][i] = 0;
    }

    for (int i=1; i<=n; i++) {
        for (int j=1; j<=n; j++) {
            if (str[i-1] == str[j-1] && i != j) dp[i][j] =  1 + dp[i-1][j-1];
            else dp[i][j] = max(dp[i][j-1], dp[i-1][j]);
        }
    }

    // This code prints the subsequence
    string res = "";
    int i = n, j = n;
    while (i > 0 && j > 0)
    {
        if (dp[i][j] == dp[i-1][j-1] + 1)
        {
           res = res + str[i-1];
           i--;
           j--;
        }
        else if (dp[i][j] == dp[i-1][j]) i--;
        else j--;
    }

    // Since we traverse dp[][] from bottom,
    // we get result in reverse order.
    reverse(res.begin(), res.end());

    return res;
}
```

---
