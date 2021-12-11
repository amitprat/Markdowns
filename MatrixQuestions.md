#### [Spirally traversing a matrix ](https://practice.geeksforgeeks.org/problems/spirally-traversing-a-matrix-1587115621/1)

Given a matrix of size r\*c. Traverse the matrix in spiral form

```cpp
vector<int> spiralOrder(vector<vector<int>>& matrix) {
    vector<int> ans;
    int left=0,right=matrix[0].size()-1;
    int top=0,bot=matrix.size()-1;
    while(true)
    {
        if(left>right || top>bot) break;

        if(left==right)
        {
            for(int i=top;i<=bot;i++) ans.push_back(matrix[i][left]);
            break;
        }
        if(top==bot)
        {
            for(int i=left;i<=right;i++) ans.push_back(matrix[top][i]);
            break;
        }

        for(int i=left;i<=right;i++)
        {
            ans.push_back(matrix[top][i]);
        }
        for(int i=top+1;i<=bot;i++)
        {
            ans.push_back(matrix[i][right]);
        }
        for(int i=right-1;i>=left;i--)
        {
            ans.push_back(matrix[bot][i]);
        }
        for(int i=bot-1;i>=top+1;i--)
        {
            ans.push_back(matrix[i][left]);
        }

        left++,right--;
        top++,bot--;
    }

    return ans;
}
```

---

#### [Count islands](https://leetcode.com/problems/number-of-islands/)

Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

```sh
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

```cpp
void dfs(vector<vector<char>>& grid, int x, int y, int n, int m) {
    if(x < 0 || y < 0 || x >= n || y >= m) return;
    if(grid[x][y] != '1') return;

    grid[x][y] = '2';

    for(int i=-1;i<=1;i++) {
        for(int j=-1;j<=1;j++) {
            if(abs(i) == abs(j)) continue;
            dfs(grid, x+i, y+j, n, m);
        }
    }
}

int numIslands(vector<vector<char>>& grid) {
    int n = grid.size();
    if(n == 0) return 0;
    int m= grid[0].size();
    int cnt = 0;

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(grid[i][j] == '1') {
                dfs(grid, i, j, n, m);
                cnt++;
            }
        }
    }

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(grid[i][j] == '2') {
                grid[i][j] = '1';
            }
        }
    }

    return cnt;
}
```

---
####[Find longest consecutive sequence of neighhour elements in matrix](https://www.careercup.com/question?id=5147801809846272)

Given a NxN matrix which contains all distinct 1 to n^2 numbers, write code to print sequence of increasing adjacent sequential numbers.

```sh
Given a NxN matrix which contains all distinct 1 to n^2 numbers, write code to print sequence of increasing adjacent sequential numbers.
ex:
1 5 9
2 3 8
4 6 7

should print
6 7 8 9
```

```cpp
vector<int> findLongestSeq(vector<vector<int>> matrix)
{
    int n = matrix.size();
    vector<bool> bits(n * n, false);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < matrix[i].size(); j++) {
            if ((i > 0 && matrix[i][j] + 1 == matrix[i - 1][j]) ||
                (j > 0 && matrix[i][j] + 1 == matrix[i][j - 1]) ||
                (i < n - 1 && matrix[i][j] + 1 == matrix[i + 1][j]) ||
                (j < n - 1 && matrix[i][j] + 1 == matrix[i][j + 1]))
            {
                bits[matrix[i][j] - 1] = true;
            }
        }
    }

    int mxLen = 0;
    int mxStart = -1;
    int curLen = 0;
    for (int i = 0; i <= n * n; i++) {
        if (i < n * n && bits[i]) curLen++;
        else {
            if (curLen > mxLen) {
                mxLen = curLen;
                mxStart = i - curLen;
            }
            curLen = 0;
        }
    }

    vector<int> res;
    for (int i = mxStart + 1; i <= mxStart + mxLen + 1; i++) res.push_back(i);

    return res;
}
```
---
####[Find longest consecutive sequence with equal characters and digits]()

```sh
{ "a1b2","a","1","a1","a11","a11b","ab123c","213sdvS23vSvs123cscsCR1412323t1214rcR1241" }

Longest seq in a1b2 = a1b2
Longest seq in a =
Longest seq in 1 =
Longest seq in a1 = a1
Longest seq in a11 = a1
Longest seq in a11b = a11b
Longest seq in ab123c = ab123c
```

```cpp
string findLongestSeq(string str) {
    vector<int> delta = findDeltas(str);
    unordered_map<int, int> map;
    pair<int, int> mx = { -1,-1 };
    map.insert({ 0,-1 });

    int len = 0;
    for (int i = 0; i < delta.size(); i++) {
        if (map.find(delta[i]) == map.end()) {
            map[delta[i]] = i;
        } else {
            auto start = map[delta[i]];
            auto end = i;
            if (end - start > len) {
                len = end - start;
                mx = { start, end };
            }
        }
    }

    if (mx.second - mx.first == 0) return "";
    return str.substr(mx.first + 1, mx.second - mx.first);
}

vector<int> findDeltas(string str) {
    vector<int> v(str.size());
    int delta = 0;
    int index = 0;

    for (auto ch : str) {
        if (isalpha(ch)) delta++;
        else delta--;
        v[index++] = delta;
    }

    return v;
}
```
---
