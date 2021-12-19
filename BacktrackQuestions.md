#### [N Queeens Problem]()

```cpp
int getNumWays() {
    vector<int> columns(N, -1);
    vector<vector<int>> result;

    getNumWays(0, columns, result);
    print(result);
    return result.size();
}

bool getNumWays(int row, vector<int> columns, vector<vector<int>>& result) {
    if (row == N) {
        result.push_back(columns);
        return true;
    }

    for (int col = 0; col < N; col++) {
        if (isValid(row, col)) {
            columns[row] = col;
            getNumWays(row + 1, columns, result);
            columns[row] = -1;
        }
    }
    return false;
}

bool isValid(int row, int col) {
    // check above this row
    for (int i = 0; i < row; i++) {
        if (board[i][col] != -1) return false;
    }
    // check left upper diagonal
    for (int i = row, j = col; i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] != -1) return false;
    }

    // check right upper diagonal
    for (int i = row, j = col; i >= 0 && j < N; i--, j++) {
        if (board[i][j] != -1) return false;
    }

    return true;
}
```

---

#### [Graph coloring - Can a graph be colored using m colors]()

```sh
Input:
vector<vector<int>> graph = {
    { 0, 1, 1, 1 },
    { 1, 0, 1, 0 },
    { 1, 1, 0, 1 },
    { 1, 0, 1, 0 },
};

int m = 3;

Result: Yes
```

```cpp
using Graph = vector<vector<int>>;
bool canBeColored(Graph graph, int m) {
    int n = graph.size();
    vector<int> colors(n, -1);

    return canBeColored(graph, 0, n, colors, m);
}

bool canBeColored(Graph graph, int u, int n, vector<int>& colors, int m) {
    if(u == n) return true;

    for(int c=1;c<=m;c++) {
        if(isSafe(graph, u, n, colors, c)) {
            colors[u] = c;
            if(canBeColored(graph, u+1, n, colors, m)) return true;
            colors[u] = -1;
        }
    }
    return false;
}

bool isSafe(Graph g, int u, int n, vector<int>& colors, int c) {
    for(int v=0;v<n;v++) {
        if(g[u][v] && colors[v] == c) return false;
    }
    return true;
}
```

---
