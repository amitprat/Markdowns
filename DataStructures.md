#### [UnionFind]()

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

#### [Trie]()

```cpp
class Trie
{
private:
    class Node
    {
    public:
        bool end;
        unordered_map<char, Node*> children;
        Node(bool end = false) : end(end) {}
    };
    Node* root = nullptr;

public:
    void insert(string str)
    {
        root = insert(root, str, 0);
    }

    void remove(string str)
    {
        remove(root, str, 0);
    }

    bool contains(string str)
    {
        return contains(root, str, 0);
    }

    vector<string> traverse()
    {
        vector<string> result;
        traverse(root, "", result);

        return result;
    }

    vector<string> prefixes(string str)
    {
        vector<string> result;
        prefixes(root, str, 0, result);
        return result;
    }

    vector<string> suffixes(string str)
    {
        vector<string> result;
        suffixes(root, str, 0, result);
        return result;
    }

private:
    Node* insert(Node* cur, string str, int l)
    {
        if (cur == nullptr) cur = new Node();
        if (l == str.length())
        {
            cur->end = true;
            return cur;
        }

        cur->children[str[l]] = insert(cur->children[str[l]], str, l + 1);
        return cur;
    }

    bool remove(Node*& cur, string str, int l)
    {
        if (cur == nullptr) return false;
        if (l == str.length())
        {
            cur->end = false;
            return isLeaf(cur);
        }

        if (remove(cur->children[str[l]], str, l + 1))
        {
            delete cur->children[str[l]];
            cur->children.erase(str[l]);
            return isLeaf(cur);
        }

        return false;
    }

    bool contains(Node* cur, string str, int l)
    {
        if (cur == nullptr) return false;
        if (l == str.length()) return cur->end;

        return contains(cur->children[str[l]], str, l + 1);
    }

    void traverse(Node* cur, string prefix, vector<string>& result)
    {
        if (!cur) return;

        if (cur->end)
        {
            result.push_back(prefix);
        }

        for (auto child : cur->children)
        {
            traverse(child.second, prefix + child.first, result);
        }
    }

    void prefixes(Node* cur, string str, int index, vector<string>& result)
    {
        if (!cur) return;
        if (cur->end) result.push_back(str.substr(0, index));
        if (index == str.length()) return;

        prefixes(cur->children[str[index]], str, index + 1, result);
    }

    void suffixes(Node* cur, string str, int index, vector<string>& result)
    {
        if (!cur) return;
        if (index == str.length()) {
            traverse(cur, str, result);
        }

        suffixes(cur->children[str[index]], str, index + 1, result);
    }

    bool isLeaf(Node* cur)
    {
        return cur->end && cur->children.empty();
    }
};
```

---
