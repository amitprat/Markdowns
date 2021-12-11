####[UnionFind]()

```cpp
class UnionFind {
    int *parent;
    int *height;
    public:
    UnionFind(int n) {
        parent = new int[n];
        height = new int[n];
        
        for(int i=0;i<n;i++) {
            parent[i] = i;
            height[i] = 0;
        }
    }
    
    void insert(Edge e) {
        insert(e.u, e.v);
    }
    
    bool isCycle(Edge e) {
        return connected(e.u, e.v);
    }
    
    void insert(int a, int b) {
        int pa = find(a);
        int pb = find(b);
        
        if(pa != pb) {
            if(height[pa] == height[pb]) height[pa]++;
            
            if(height[pa] < height[pb])
                parent[pb] = pa;
            else 
                parent[pa] = pb;
        } 
    }
    
    int find(int a) {
        while(a != parent[a]) a = parent[a];
        return a;
    }
    
    bool connected(int a, int b) {
        int pa = find(a);
        int pb = find(b);
        
        return pa == pb;
    }
};
```
---
