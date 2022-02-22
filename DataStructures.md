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

#### [Priority Queue Implementation]()

```cpp
template<class T>
class PriorityQueue {
private:
    using Comparator = std::function<bool(const T&, const T&)>;
    vector<T> container;
    Comparator comparator;

public:
    PriorityQueue() {
        comparator = [](const T& a, const T& b) { return a < b; };
    }
    PriorityQueue(Comparator comparator) : comparator(std::move(comparator)) {}

    void add(T item) {
        container.push_back(item);
        upHeapify(container.size() - 1);
    }

    T top() {
        if (container.empty()) return T();
        return container[0];
    }

    T pop() {
        auto res = top();
        swap(container[0], container[container.size() - 1]);
        container.pop_back();
        downHeapify(0);

        return res;
    }

    bool empty() { return container.empty(); }

private:
    void downHeapify(int startIndex) {
        int mnIndex = startIndex;
        int leftIndex = left(startIndex);
        if (leftIndex < container.size() && comparator(container[leftIndex], container[mnIndex])) mnIndex = leftIndex;
        int rightIndex = right(startIndex);
        if (rightIndex < container.size() && comparator(container[rightIndex], container[mnIndex])) mnIndex = rightIndex;
        if (mnIndex != startIndex) {
            swap(container[mnIndex], container[startIndex]);
            downHeapify(mnIndex);
        }
    }

    void upHeapify(int startIndex) {
        int p = parent(startIndex);
        while (startIndex != 0 && !comparator(container[p], container[startIndex])) {
            swap(container[p], container[startIndex]);
            startIndex = p;
            p = parent(startIndex);
        }
    }

    int parent(int index) { return (index - 1) / 2; }
    int left(int index) { return 2 * index + 1; }
    int right(int index) { return 2 * index + 2; }
};
```

---

#### [Circular Queue]()

```cpp
class CircularQueue {
    template <class T>
    class LinkedCircularQueue {
    public:
        class Node {
        public:
            T val;
            Node* prev, * next;
            Node(T val) :val(val), prev(nullptr), next(nullptr) {}
        };
    private:
        Node* head, * tail;
        int sz = 0;
    public:
        LinkedCircularQueue() :head(nullptr), tail(nullptr) {}
        void push(T item) {
            if (head == nullptr) {
                head = tail = new Node(item);
            }
            else {
                tail->next = new Node(item);
                tail = tail->next;
                tail->next = head;
            }
            sz++;
        }
        T pop() {
            if (head == nullptr) throw exception("Queue is empty");
            if (head == tail) {
                auto val = head->val;
                delete head;
                head = tail = nullptr;
                sz--;
                return val;
            }

            auto f = head;
            head = head->next;
            tail->next = head;
            auto val = f->val;
            delete f;
            sz--;

            return val;
        }
        T front() {
            if (head == nullptr) throw exception("Queue is empty");
            return head->val;
        }
        bool empty() {
            return head == nullptr;
        }
        int size() {
            return sz;
        }
    };

    template <class T>
    class ArrayCircularQueue {
    private:
        T* arr = nullptr;
        int cap = 0;
        int start, end;
    public:
        ArrayCircularQueue(int cap) :cap(cap) {
            arr = new T[cap];
            start = end = 0;
        }

        void pushBack(T item) {
            if (isFull()) throw exception("Queue is full");

            arr[end] = item;
            end = index(end + 1);
        }

        void pushFront(T item) {
            if (isFull()) throw exception("Queue is full");

            start = index(start - 1);
            arr[start] = item;
        }

        T popFront() {
            if (empty()) throw exception("Queue is empty");

            auto item = arr[start];
            start = index(start + 1);
            if (empty()) reset();

            return item;
        }

        T popBack() {
            if (empty()) throw exception("Queue is empty");

            end = index(end - 1);
            auto item = arr[end];
            if (empty()) reset();

            return item;
        }
        T front() {
            if (empty()) throw exception("Queue is empty");
            return arr[start];
        }
        T back() {
            if (empty()) throw exception("Queue is empty");
            return arr[end];
        }
        bool empty() {
            return start == end;
        }
        int size() {
            return (end + cap - start) % cap;
        }
        bool isFull() {
            return start == index(end + 1);
        }
        int index(int cur) {
            return (cur + cap) % cap;
        }
        void reset() {
            start = end = 0;
        }
    };
};
```

---

#### [LRU Cache, Least Recently Used Cache]()

```cpp
class LRUCache {
    int capacity;
    unordered_map<string, string> backend;

    list<pair<string, string>> dll;
    unordered_map<string, list<pair<string, string>>::iterator> map;

public:
    LRUCache(int capacity) : capacity(capacity) {}

    void put(string key, string val) {
        backend.insert({ key, val });
    }

    string get(string key) {
        string val;
        if (map.find(key) != map.end()) {
            // key exists in cache
            auto iter = map[key];
            val = iter->second;

            dll.push_back({ key, iter->second });
            map[key] = dll.begin();
            dll.erase(iter);

            cout << "Key exists in cache, reading from cache: ";
        }
        else {
            // key isn't in cache
            if (backend.find(key) == backend.end()) {
                throw exception("Key doesn't exist in cache");
            }

            cout << "Key doesn't exist in cache, reading from backend, ";
            if (map.size() == capacity) {
                // cache is full
                cout << "cache is full, evicting the last element, ";
                map.erase(dll.back().first);
                dll.pop_back();
            }

            cout << "inserting new element in front, ";
            dll.push_front({ key, backend[key] });
            map[key] = dll.begin();
            val = backend[key];
        }

        return val;
    }
};
```

---
