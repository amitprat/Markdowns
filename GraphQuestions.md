#### [Minimum Cost Path](https://practice.geeksforgeeks.org/problems/minimum-cost-path3833/1)

Given a square grid of size N, each cell of which contains integer cost which represents a cost to traverse through that cell, we need to find a path from top left cell to bottom right cell by which the total cost incurred is minimum.
From the cell (i,j) we can go (i,j-1), (i, j+1), (i-1, j), (i+1, j).

```cpp
struct Node
{
    int x,y,distance;
};

struct Compare
{
    bool operator()(Node& f, Node& s){
        return f.distance > s.distance;
    }
};

int minimumCostPath(vector<vector<int>>& grid)
{
    int n = grid.size();
    vector<vector<int>> cost(n, vector<int>(n, INT_MAX));

    priority_queue<Node, vector<Node>, Compare> pq;
    pq.push({0,0,grid[0][0]});
    cost[0][0] = grid[0][0];

    while(!pq.empty())
    {
        auto u = pq.top(); pq.pop();
        if(cost[u.x][u.y] == INT_MAX) continue;

        for(int j=-1;j<=1;j++) {
            for(int k=-1;k<=1;k++) {
                if(abs(j) == abs(k)) continue;
                int nr = u.x+j;
                int nc = u.y+k;
                if(nr < 0 || nc < 0 || nr >= n || nc >= n) continue;

                if(grid[nr][nc]+cost[u.x][u.y] < cost[nr][nc]) {
                    cost[nr][nc] = grid[nr][nc]+cost[u.x][u.y];
                    pq.push({nr, nc, cost[nr][nc]});
                }
            }
        }
    }
    return cost[n-1][n-1];
}
```

```cpp
// minimum cost path if can move only to right or bottom
int minimumCostPath(vector<vector<int>>& grid)
{
    int n = grid.size();
    if(n == 0) return 0;
    int m = grid[0].size();
    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(i == 0 && j == 0) continue;
            else if(i == 0) grid[i][j] += grid[i][j-1];
            else if(j == 0) grid[i][j] += grid[i-1][j];
            else grid[i][j] += min(grid[i-1][j], grid[i][j-1]);
        }
    }

    return grid[n-1][m-1];
}
```

---

#### [Minimum Spanning Tree](https://practice.geeksforgeeks.org/problems/minimum-spanning-tree/1)

Given a weighted, undirected and connected graph of V vertices and E edges. The task is to find the sum of weights of the edges of the Minimum Spanning Tree.

```cpp
int spanningTree(int V, vector<vector<int>> adj[])
{
    vector<Edge> edges;
    for(int i=0;i<V;i++) {
        for(auto u : adj[i]) {
            edges.push_back({i,u[0],u[1]});
        }
    }

    int res = krushkals(V, edges);
    //int res = prims(V, edges);

    return res;
}
```

**Krushkals MST**

```cpp
struct compare {
    bool ()(Edge& f, Edge& s) {
        return f.w < s.w;
    }
};

int krushkals(int V, vector<Edge>& edges) {
    UnionFind uf(V);
    priority_queue<Edge, vector<Edge>, compare> pq;
    for(auto e : edges) pq.push(e);

    vector<Edge> result;
    while(result.size() < V-1) {
        auto f = pq.top(); pq.pop();
        if(uf.connected(f.u, f.v)) continue;

        uf.insert(f.u, f.v);
        result.push_back(f);
    }

    cout<<"Edges in mst: "<<endl;
    for(auto e : result) cout<<e.to_string()<<endl;
    cout<<endl;

    int wt = 0;
    for(auto e : result) wt += e.w;

    return wt;
}
```

**Prims MST**

```cpp
int prims(int V, vector<Edge>& edges) {
    unordered_map<int, vector<Edge>> adjGraph;
    for(auto e: edges) adjGraph[e.u].push_back({e});

    vector<int> dist(V, INT_MAX);
    vector<bool> visited(V, false);
    vector<pair<int,int>> parent(V, -1);

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq; //{w,v}
    int src = 0;
    dist[src] = 0;
    pq.push({dist[src], src});

    while(!pq.empty()) {
        auto f = pq.top(); pq.pop();
        int u = f.second;

        if(visited[u]) continue;
        visited[u] = true;

        for(auto nb : adjGraph[u]) {
            int v = nb.v;
            int w = nb.w;
            if(visited[v]) continue;

            if(w < dist[v]) {
                dist[v] = w;
                parent[v] = u;
                pq.push({dist[v], v});
            }
        }
    }

    int wt = 0;
    for(int v=1;v<V;v++) {
        wt += dist[v];
        cout << format("({}->{},{})", parent[i], v, dist[v]);
        if (v != V - 1) cout << "<--->";
    }
    cout<<endl;

    return wt;
}
```

**_Primts MST in Matrix Graph_**

```cpp
using Graph = vector<vector<int>>;
int minimumSpanningTree(Graph g) {
    int V = g.size();
    vector<int> dist(V, INT_MAX);
    vector<int> parent(V, -1);
    vector<bool> visited(V, false);

    for(int i=0;i<V-1;i++) {
        auto u = findMinWtEdge(dist, visited, V);
        visited[u] = true;

        for(int v=0;v<V;v++) {
            if(g[u][v] && !visited[v] && g[u][v] < dist[v]) {
                dist[v] = g[u][v];
                parent[j] = u;
            }
        }
    }

    int wt = 0;
    for(int v=1;v<V;v++) {
        wt += dist[v];
        cout << format("({}->{},{})", parent[i], v, dist[v]);
        if (v != V - 1) cout << "<--->";
    }
    cout<<endl;

    return wt;
}

int findMinWtEdge(vector<int>& dist, vector<bool>& visited, int V) {
    int res = -1;
    for(int v=0;v<V;v++) {
        if(visited[v]) continue;
        if(res == -1 || dist[v] < dist[res]) res = v;
    }
    return res;
}
```

---

#### [Bellman Ford]()

```cpp
vector<int> bellman(Graph g, int s) {
    vector<int> dist(g.V, INT_MAX);
    dist[s] = 0;

    // relax all edges for V-1 times
    for (int cnt = 0; cnt < g.V - 1; cnt++) {
        for (auto e : g.edges) {
            relax(e, dist);
        }
    }

    // try relaxing edges again and if we can still relax edge then its negative weight cycle
    for (auto e : g.edges) {
        if (canRelax(e, dist)) {
            cout << "Contains negative weight cycle!!";
            return {};
        }
    }

    return dist;
}

void relax(Graph::Edge e, vector<int>& dst) {
    // relax is just a way to find if we can reach other end of edge using this edge which has lesser total weight
    if (dst[e.v] > dst[e.u] + e.w) dst[e.v] = dst[e.u] + e.w;
}

bool canRelax(Graph::Edge e, vector<int> dst) {
    return dst[e.v] > dst[e.u] + e.w;
}
```

---

#### [Dijkastra Shortest Path](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-using-priority_queue-stl/)

Given a graph and a source vertex in graph, find shortest paths from source to all vertices in the given graph.

**_In adjacency list representation of graph_**

```cpp
vector<int> shortestDistanceFromSrcToAllVertices(Graph g, int src) {
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
    vector<int> dist(g.size(), INT_MAX);
    dist[src] = 0;
    pq.push({dist[src], src});

    while(!pq.empty()) {
        auto f = pq.top(); pq.pop();
        int u = f.second;
        if(dist[u] == INT_MAX) continue;

        for(auto edge : g.adjList[src]) {
            int v = edge.v;
            int w = edge.w;

            if(dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }

    for(int i=0;i.size();i++) {
        cout<<"Distance from "<<src<<" to i = "<<dist[i]<<endl;
    }

    return dist;
}
```

**_In matrix representation of graph_**

```cpp
vector<int> dijkastra(int graph[][V], int s) {
    vector<int> distance(V, INT_MAX);
    vector<bool> visited(V, false);
    distance[s] = 0;

    for (int i = 0; i < V - 1; i++) {
        int u = minCostVertex(distance, visited);
        visited[u] = true;

        for (int v = 0; v < V; v++) {
            if (visited[v]) continue;
            if (distance[u] == INT_MAX || !graph[u][v]) continue;

            distance[v] = min(distance[v], distance[u] + graph[u][v]);
        }
    }

    return distance;
}
```

---
