####[Spirally traversing a matrix ](https://practice.geeksforgeeks.org/problems/spirally-traversing-a-matrix-1587115621/1)

Given a matrix of size r*c. Traverse the matrix in spiral form

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
####[Count islands](https://leetcode.com/problems/number-of-islands/)

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
