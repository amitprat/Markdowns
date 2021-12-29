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

#### [Check if all paths from src to dst node in a directed graph are connected](https://www.careercup.com/question?id=5713002834165760)

Given a Start Node and an End Node in a graph report if they are “necessarily connected”. This means that all paths from the start
node lead to the end node. Report true all paths from start node lead to end node and false if at least one path does not lead to
the end node. This is a directed graph which can have cycles.

```cpp
bool isNecessarilyConnected(Graph g, int s, int e, unordered_set<int>& visited, unordered_set<int> recStack)
{
    if (s == e) return true; // path in destination node

    visited.insert(s); // optimization to not visit the visited nodes again and again
    recStack.insert(s);

    auto neighbours = getNeighbours(g, s);
    if (neighbours.empty()) return false; // if this is leaf node, then this path doesn't need to destination node

    for (auto v : neighbours) {
        if (visited.find(v) == visited.end()) {
            if (!isNecessarilyConnected(g, v, e, visited, recStack)) return false;
        }
        else if (recStack.find(v) != recStack.end()) return false; // if this graph has cycle
    }

    return true;
}
```

---

#### [Get friend suggestions for a person based on mutual friends count]()

```sh
Peoples: [A, B, C, D]
Friend Relationship:
A: [B]
B: [A, C, D]
C: [B]
D: [B]

Friend suggestions:
Get friend suggestion for A [D:1, C:1]
Get friend suggestion for B []
Get friend suggestion for C [D:1,A:1]
Get friend suggestion for D [C:1,A:1]
```

```cpp
struct GraphNode {
    string id;
    unordered_map<string, GraphNode*> friends;

    GraphNode(string id) : id(id) {}

    void addFriend(GraphNode* other) {
        friends[other->id] = other;
    }

    vector<GraphNode*>getFriends() {
        vector<GraphNode*> result;
        for (auto e : friends) result.push_back(e.second);

        return result;
    }
};

class Graph {
    unordered_map<string, GraphNode*> peoples;

public:
    void add(string id) {
        peoples[id] = new GraphNode(id);
    }

    void addFriend(string id1, string id2) {
        if (peoples.find(id1) == peoples.end()) return;
        if (peoples.find(id2) == peoples.end()) return;

        auto first = peoples[id1];
        auto second = peoples[id2];

        first->addFriend(second);
        second->addFriend(first);
    }

    vector<GraphNode*> getFriends(string id) {
        if (peoples.find(id) == peoples.end()) return {};

        return peoples[id]->getFriends();
    }
};

static void friendSuggestionTest() {
    Graph g;
    g.add("A");
    g.add("B");
    g.add("C");
    g.add("D");
    g.addFriend("A", "B");
    g.addFriend("B", "C");
    g.addFriend("B", "D");

    vector<string> persons = { "A","B","C","D" };
    for (auto person : persons) {
        cout << format("Get friend suggestion for {}", person) << endl;

        // get its own friends and friend's friends, count freq of occurance.
        auto friends = g.getFriends(person);
        unordered_map<string, int> freq;
        for (auto f : friends) {
            auto f1 = g.getFriends(f->id);
            for (auto e : f1) if (e->id != person) freq[e->id]++;
        }

        // Maintain a maxheap
        priority_queue<pair<int, string>> pq;
        for (auto e : freq) pq.push({ e.second,e.first }); // {id, count of mutual friends}

        // get the friends in order of their count as common friends
        // we can also take a variable k here to only suggest top k friends
        while (!pq.empty()) {
            cout << pq.top().second << ":" << pq.top().first << endl;
            pq.pop();
        }
    }
}
```

---

#### [Print friendship levels](https://www.careercup.com/question?id=5675048708866048)

Print the level of friendship.

Given a person and list of his friends, print all his friends by level of association.

The text file will be like one below

A: B,C,D
D: B,E,F
E: C,F,G

If the input is A, the out put should be:

Level 1 - B,C,D
Level 2 - E,F
Level 3 - G

```cpp
void preprocess() {
    ifstream is("C:\\Users\\amisi\\source\\repos\\CodeProject\\CodeProject\\careercup\\friendshipfile.txt");
    string line;
    while (getline(is, line)) {
        pair<string, vector<string>> friendship = parseFriendship(line);
        for (auto v : friendship.second) friendshipMap[friendship.first].push_back(v);
    }
}

void printLevels(string input) {
    queue<string> q;
    unordered_set<string> visited;
    for (auto& person : friendshipMap[input]) {
        q.push(person);
        visited.insert(person);
    }

    int level = 1;
    while (!q.empty()) {
        cout << "Level " << level << ": ";
        int sz = q.size();
        while (sz--) {
            auto f = q.front(); q.pop();
            cout << f << " ";

            for (auto& n : friendshipMap[f]) {
                if (visited.find(n) == visited.end()) {
                    q.push(n);
                    visited.insert(n);
                }
            }
        }
        level++;
        cout << endl;
    }
}

static pair<string, vector<string>> parseFriendship(string line)
{
    stringstream ss(line);
    string part1, part2;
    ss >> part1 >> part2;
    part1 = trim(part1);
    vector<string> part2Splits = split(part2, { " ","," });
    part2Splits = trim(part2Splits);

    return { part1, part2Splits };
}

static vector<string> trim(vector<string>& strs) {
    vector<string> result;
    for (auto& str : strs) {
        result.push_back(trim(str));
    }

    return result;
}

static string trim(string str) {
    int i = 0;
    while (i < str.length() && (isspace(str[i]) || str[i] == ':')) {
        str.erase(str.begin() + i); i++;
    }

    i = str.length() - 1;
    while (i >= 0 && (isspace(str[i]) || str[i] == ':')) {
        str.erase(str.begin() + i); i--;
    }

    return str;
}
```

---
