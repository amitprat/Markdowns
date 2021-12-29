#### [Minimum Platforms](https://practice.geeksforgeeks.org/problems/minimum-platforms-1587115620/1)

Given arrival and departure times of all trains that reach a railway station. Find the minimum number of platforms required for the railway station so that no train is kept waiting.
Consider that all the trains arrive on the same day and leave on the same day. Arrival and departure time can never be the same for a train but we can have arrival time of one train equal to departure time of the other. At any given instance of time, same platform can not be used for both departure of a train and arrival of another train. In such cases, we need different platforms.

```sh
Input: n = 6
arr[] = {0900, 0940, 0950, 1100, 1500, 1800}
dep[] = {0910, 1200, 1120, 1130, 1900, 2000}
Output: 3
Explanation:
Minimum 3 platforms are required to
safely arrive and depart all trains.
```

```cpp
int findPlatform(int arr[], int dep[], int n)
{
    if(n == 0) return 0;

    sort(arr, arr+n);
    sort(dep, dep+n);

    int i =0, j =0;
    int cnt = 0, mxCnt = 0;
    while(i<n) {
        if(arr[i]<=dep[j]) {
            cnt++; i++;
        } else {
            mxCnt = max(mxCnt, cnt);
            cnt--;
            j++;
        }
    }

    return mxCnt;
}
```

---

#### [Find median in a stream ](https://practice.geeksforgeeks.org/problems/find-median-in-a-stream-1587115620/1)

Given an input stream of N integers. The task is to insert these numbers into a new stream and find the median of the stream formed by each insertion of X to the new stream.

```cpp
class MedianOfStream
{
    priority_queue<int,vector<int>,greater<int>> mnHeap;
    priority_queue<int> mxHeap;

    public:
    void insertHeap(int &x)
    {
        mnHeap.push(x);
        mxHeap.push(mnHeap.top()); mnHeap.pop();

        if(mnHeap.size() < mxHeap.size()) {
            mnHeap.push(mxHeap.top()); mxHeap.pop();
        }
    }

    double getMedian()
    {
        if(mnHeap.size() > mxHeap.size()) return mnHeap.top();

        return (mxHeap.top()+mnHeap.top())/2.0;
    }
};
```

---

#### [Excel Sheet](https://practice.geeksforgeeks.org/problems/excel-sheet5448/1)

Given a positive integer N, return its corresponding column title as it would appear in an Excel sheet.
For N =1 we have column A, for 27 we have AA and so on.

Note: The alphabets are all in uppercase.

```cpp
string ExcelColumn(int N)
{
    string res;
    while(N) {
        int rem = (N-1)%26;
        res += ('A' + rem);
        N = (N-1)/26;
    }
    reverse(res.begin(), res.end());

    return res;
}
```

```cpp
string ExcelColumn(int N)
{
    string res;
    while(N) {
        int rem = N%26;

        if(rem == 0) {
            res += 'Z';
            N = N/26 -1;
        } else {
            res += ('A' + rem -1);
            N /= 26;
        }
    }
    reverse(res.begin(), res.end());

    return res;
}
```

```cpp
int excelToNum(string num) {
    int res = 0;
    for(char ch : num) {
        res = res*26 + (ch-'A'+1);
    }

    return res;
}
```

---

#### [Maximum Intervals Overlap ](https://practice.geeksforgeeks.org/problems/maximum-intervals-overlap5708/1#)

Consider a big party where N guests came to it and a log register for guest’s entry and exit times was maintained. Find the minimum time at which there were maximum guests at the party. Note that entries in the register are not in any order.

```cpp
vector<int> findMaxGuests(int entry[], int exits[], int N)
{
    sort(entry, entry+N);
    sort(exits, exits+N);

    int i=0,j=0,mx=0,mntime=INT_MAX;
    while(i<=N) {
        if(i<N && entry[i] <= exits[j]) i++;
        else {
            if(i-j>mx) {
                mntime = entry[i-1];
                mx = i-j;
            }
            if(i == N) break;
            j++;
        }
    }

    return {mx,mntime};
}
```

```cpp
vector<int> findMaxGuests(int entry[], int exits[], int N)
{
    vector<pair<int,char>> v;
    for(int i=0;i<N;i++) v.push_back({entry[i], 'S'});
    for(int i=0;i<N;i++) v.push_back({exits[i], 'E'});
    sort(v.begin(), v.end(),[](const auto& f, const auto& s) {
        if(f.first != s.first) return f.first < s.first;
        return s.second == 'S' ? false : true;
    });

    int mx = 0, curmx = 0, mn=INT_MAX;
    for(auto e: v) {
        if(e.second == 'S'){
            curmx++;
            if(curmx > mx) {
                mn = e.first;
                mx = curmx;
            }
        } else {
            curmx--;
        }
    }

    return {mx, mn};
}
```

---

#### [Find 2 words that can be joined together to form a palindrome](https://www.careercup.com/question?id=4879869638868992)

You are given a list of word. Find if two words can be joined to-gather to form a palindrome. eg Consider a list {bat, tab, cat} Then bat and tab can be joined to gather to form a palindrome.

Expecting a O(nk) solution where n = number of works and k is length. There can be multiple pairs.

```cpp
class FindTwoWordsThatCanBeJoinedTogetherToFormPalindrome
{
    class Trie
    {
    public:
        struct Node {
            bool end = false;
            unordered_map<char, Node*> children;
        };

        Node* root = new Node();

        void insert(string word) {
            root = insert(root, word, 0);
        }

        bool find(string word) {
            return find(root, word, 0);
        }

        bool isPrefix(string word, vector<string>& words) {
            return isPrefix(root, word, 0, words);
        }

    private:
        Node* insert(Node* node, string word, int index) {
            if (!node) node = new Node();
            if (index == word.size()) {
                node->end = true;
                return node;
            }

            node->children[word[index]] = insert(node->children[word[index]], word, index + 1);

            return node;
        }

        bool find(Node* node, string word, int index) {
            if (!node) return false;
            if (index == word.size()) return node->end;

            return find(node->children[word[index]], word, index + 1);
        }

        bool isPrefix(Node* node, string word, int index, vector<string>& words) {
            if (!node) return false;
            if (index == word.size()) {
                populateSuffixes(node, "", words);
                return true;
            }
            return isPrefix(node->children[word[index]], word, index + 1, words);
        }

        void populateSuffixes(Node* node, string cur, vector<string>& words) {
            if (!node) return;
            if (node->end) words.push_back(cur);

            for (auto child : node->children) {
                populateSuffixes(child.second, cur + child.first, words);
            }
        }
    };

    static vector<string> findWords(vector<string>& input) {
        Trie forwardTrie, reverseTrie;

        for (auto word : input) {
            string rev = word;
            reverse(rev.begin(), rev.end());

            if (forwardTrie.find(rev)) return { word, rev };
            if (reverseTrie.find(word)) return { rev , word };

            vector<string> suffixes;
            if (forwardTrie.isPrefix(rev, suffixes)) {
                for (auto suffix : suffixes) {
                    if (isPalin(suffix)) return { word + suffix, rev };
                }
            }

            suffixes.clear();
            if (reverseTrie.isPrefix(word, suffixes)) {
                for (auto suffix : suffixes) {
                    if (isPalin(suffix)) return { rev + suffix, word };
                }
            }

            forwardTrie.insert(word);
            reverseTrie.insert(rev);
        }

        return {};
    }

    static bool isPalin(string word) {
        int l = 0, r = word.size() - 1;
        while (l < r && word[l] == word[r]) { l++; r--; }

        return l >= r;
    }
};
```

---

#### [Maximum Profit Car Rental](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)

We have n jobs, where every job is scheduled to be done from startTime[i] to endTime[i], obtaining a profit of profit[i].

You're given the startTime, endTime and profit arrays, return the maximum profit you can take such that there are no two jobs in the subset with overlapping time range.

If you choose a job that ends at time X you will be able to start another job that starts at time X.

```cpp
int maxProfitDP(vector<pair<int, int>> arr, vector<int> price) {
    int n = arr.size();
    vector<tuple<int, int, int>> tuples;

    for (int i = 0; i < n; i++) {
        tuples.push_back({ arr[i].first,arr[i].second,price[i] });
    }
    sort(tuples.begin(), tuples.end(), [](auto f, auto s) {return get<1>(f) < get<1>(s); });

    vector<int> mxProfit(n);
    for (int i = 0; i < n; i++) {
        int curMax = get<2>(tuples[i]);
        for (int j = i - 1; j >= 0; j--) {
            if (get<1>(tuples[j]) <= get<0>(tuples[i])) {
                curMax = max(curMax, mxProfit[j] + get<2>(tuples[i]));
            }
        }
        mxProfit[i] = curMax;
    }

    return *max_element(mxProfit.begin(), mxProfit.end());
}
```

---

#### [Find median of 2 sorted arrays of same size]()

**_By linear counting_**

```cpp
double median(int arr1[], int arr2[], int n) {
    int p1 = 0, p2 = 0;
    int i = 0, j = 0;
    for (int cnt = 0; cnt <= n; cnt++) {
        p1 = p2;
        if (i == n) {
            p2 = arr2[0];
        }
        else if (j == n) {
            p2 = arr1[0];
        }
        else if (arr1[i] <= arr2[j]) {
            p2 = arr1[i++];
        }
        else {
            p2 = arr2[j++];
        }
    }

    return (double)(p1 + p2) / 2.0;
}
```

**_Using binary search_**

```cpp
double median2(int arr1[], int arr2[], int n) {
    if (n == 1) return (double)(arr1[0] + arr2[0]) / 2.0;

    double m1 = median(arr1, n);
    double m2 = median(arr2, n);

    if (m1 == m2) return m1;
    else if (m1 <= m2) return median2(arr1 + n / 2, arr2, n - n / 2);
    else return median2(arr1, arr2 + n / 2, n - n / 2);
}

double median(int arr[], int n) {
    if (n & 1) return arr[n / 2];
    return (double)(arr[n / 2 - 1] + arr[n / 2]) / 2.0;
}
```

---

#### [Minimum steps required by Knight to reach target]()

```cpp
using Board = vector<vector<int>>;
int minimumSteps(Board& board, Point& start, Point& end) {
    int N = board.size();
    queue<Point> q;
    unordered_set<int> vistied;

    q.push(start);
    visited.insert(start.x * N + start.y);
    int steps = 0;

    while(!q.empty())  {
        auto u = q.front(); q.pop();
        if(u == end) return steps;

        int sz = q.size();
        while(sz--) {
            auto neighbourPositions = getNeighbourPositions(u, visited, N);
            for(auto neighbourPos : neighbourPositions) {
                q.push(neighbourPos);
                visited.insert(neighbourPos.x * N + neighbourPos.y);
            }
        }

        steps++;
    }

    return steps;
}

vector<Point> knightMoves = {{1,2},{2,1},{-1,2},{2,-1},{1,-2},{2,-1},{-1,-2},{-2,-1}}};
vector<Point> getNeighbourPositions(Point u, unordered_set<int>& visited, int N) {
    vector<Point> result;
    for(auto move : knightMoves) {
        auto next = u + move;
        if(!isSafe(next, visited, N)) continue;
        result.push_back(next);
    }

    return result;
}

bool isSafe(Point next, unordered_set<int>& visited, int N) {
    if(next.x < 0 || next.x >= N || next.y < 0 || next.y >= N || visited.count(next.x*N+next.y) != 0) return false;
    return true;
}
```

---

#### [K Closest Points to given point]()

5 closed points to (0, 0) in point set [(1, 2), (3, 1), (0, 2), (1, 1), (3, 4), (1, 0), (0, 1), (0, 0)] are [(0, 0), (1, 1), (1, 0), (0, 1), (0, 2)]

```cpp
int distance(Point p1, Point p2) {
    return sqrt(pow(p1.x - p2.x, 2) + pow(p1.y - p2.y, 2));
}

int partition(vector<Point>& points, int l, int r, Point origin) {
    int i = 0;
    int pivotDistance = distance(origin, points[r]);
    for (int j = i; j < r; j++) {
        if (distance(points[j], origin) <= pivotDistance) {
            swap(points[j], points[i++]);
        }
    }
    swap(points[i], points[r]);

    return i;
}

int selectionAlgorithm(vector<Point>& points, int l, int r, Point source, int k) {
    int p = partition(points, l, r, source);
    int q = p - l + 1;
    if (q == k) return p;

    if (q < k) return selectionAlgorithm(points, p + 1, r, source, k - q);
    return selectionAlgorithm(points, l, p - 1, source, k);
}

vector<Point> kClosest(vector<Point> points, Point source, int k) {
    int l = 0, r = points.size() - 1;
    auto p = selectionAlgorithm(points, l, r, source, k);

    return { points.begin(), points.begin() + p + 1 };
}
```

---

#### [Generate permutations]()

```cpp
class Permutations {
    string str;
public:
    Permutations(string str) : str(str) {}

    bool hasNext() {
        if (str.length() == 1) return false;
        for (int i = str.length() - 1; i >= 1; i--) {
            if (str[i - 1] < str[i]) return true;
        }
        return false;
    }

    string next() {
        if (str.length() == 1) return str;

        // find first adjancent smaller element
        int l = str.length() - 2;
        while (l >= 0 && str[l] >= str[l + 1]) {
            l--;
        }
        if (l < 0) return "Not possible";

        // find next greater element
        int r = l + 1;
        int j = l + 1;
        while (j < str.length()) {
            if (str[j] > str[l] && str[j] < str[r]) r = j;
            j++;
        }

        // swap these two values
        swap(str[l], str[r]);
        //cout << l << " " << r << endl;

        // sort the str after l
        sort(str.begin() + l + 1, str.end());

        return str;
    }
};
```

---

#### [Find actual count of people with same or similar names]()

[{Jonathan, John}, {Jon, Johnny}, {Johnny, John}, {Kari, Carrie}, {Carleton, Carlton}]
{John=10, Jonathan=9, Carlton=8, Kari=3, Jon=3, Davis=2, Johnny=11, Carleton=2, Carrie=5}

Name mapping after graph traversal: {Jonathan=John, Johnny=John, Kari=Carrie, Jon=John, Carleton=Carlton}
(Result) Frequency mapping: {Carlton=10, John=33, Carrie=8, Davis=2}

```cpp
//TODO
```

---

#### [Find number of ways to select N identical from a bucket](https://www.careercup.com/question?id=15072768)

We define C(n) as the number of ways to take n identical objects out of a bucket, where objects may be taken 1, 2, or 3 at a time.

Example: C(4)=7, because you can take 4 objects in the following 7 ways:
1,1,1,1
2,1,1
1,2,1
1,1,2
2,2
3,1
1,3

```cpp
int numberOfWays(int n)
{
    // number of ways to select 0 objects = {} - 1
    // number of ways to select 1 object = {1} - 1
    // number of ways to select 2 objects = {(1,1),(2)} -2
    // number of ways to select 3 objects = {(1,1,1),(1,2),(2,1),(3)} - 4 and so on...
    int res[] = { 1,1,2 }; // number of ways to select 0, 1 or 2 obects at a time
    while (n) {
        int tmp = res[0] + res[1] + res[2];
        res[0] = res[1];
        res[1] = res[2];
        res[2] = tmp;
        n--;
    }
    return res[0];
}
```

---

#### [Print path in setup box](https://www.careercup.com/question?id=5127611667709952)

Write code to give the character sequence given a word, For example, if the word is "CON", the function will print this:

```sh
Right//now we're at B
Right//now we're at C
OK//to select C
Down
DOwn
Right
Right
OK//to select O
Left//now at N
OK//to select N
```

**_Tester code_**

```cpp
static void test() {
    int colSize = 5;
    int limit = 25;

    {
        string word = "constable";
        string path1 = getPath1(colSize, limit, word);
        string path2 = getPath2(colSize, limit, word);
        assert(path1 == path2);
        cout << path1 << endl;
    }
}
```

```cpp
string getPath(string str, int rowSize)
{
    string res;
    Point src = {0, 0};

    for(auto ch : str) {
        string curRes;
        Point dst = {(ch-'a')/5, (ch-'a')%5};
        curRes += string(abs(dst.x-src.x), (dst.x > src.x ? 'd':'u'));
        curRes += string(abs(dst.y-src.y), (dst.y > src.y ? 'r':'l'));
        curRes += '\n';

        res += curRes;
        src = dst;
    }

    return res;
}
```

---

#### [Generate prime factors of 2 & 3](https://www.careercup.com/question?id=4816567298686976)

Given a prime set, we call "prime expressible" if a number can be factorized only using given prime numbers. Find n-th big expressible number.

E.g., prime set = {2, 3}
expressible number = {1,2,3,4,6,8, 9, 12...}
non-expressible number = {5, 10... }

The primes in the prime set are ordered in an increasing order, and can include a prime < 10^4 (don't remember the exact range), and n can also be as large as 1-10^6.

```cpp
// print 2^i*3^j increasing order
vector<int> print23(int n) {
    vector<int> result(n);
    result[0] = 1;
    int i2 = 0, i3 = 0;

    for(int i=1;i<n;i++) {
        int a2 = 2 * result[i2];
        int a3 = 3 * result[i3];

        if(a2 < a3) {
            result[i] = a2;
            i2++;
        } else if(a2 > a3) {
            result[i] = a3;
            i3++;
        } else {
            result[i] = a2;
            i2++; i3++;
        }

        return result;
    }

    return result;
}
```

---

#### [Print combinations from 2 vectors of strings]()

```cpp
void printCombinations(vector<vector<string>>& strs, int i, int n, vector<string>& res) {
    if(i == n) {
        cout<<to_string(res)<<endl;
        return;
    }

    for(int j=0;j<strs[i].size();j++) {
        res.push_back(strs[i][j]);
        printCombinations(strs, i+1, n, res);
        res.pop_back();
    }
}
```

---

#### [Print Ugly Numbers](https://www.careercup.com/question?id=23823662)

Given an equation in the form 2^i _ 3^j _ 5^k \* 7^l where i,j,k,l >=0 are integers.write a program to generate numbers from that equation in sorted order efficiently.

for example numbers from that equation will be in the order 2,3,5,6,7,8,9.....and so on..

```cpp
static vector<int> printAllMultiples(int n)
{
    if (n == 0) return {};

    vector<int> result(n);
    result[0] = 1;
    int i2 = 0, i3 = 0, i5 = 0, i7 = 0;
    for (int i = 1; i < n; i++) {
        int minMultiple = min(min(2 * result[i2], 3 * result[i3]), min(5 * result[i5], 7 * result[i7]));
        result[i] = minMultiple;
        if (minMultiple == 2 * result[i2]) i2++;
        if (minMultiple == 3 * result[i3]) i3++;
        if (minMultiple == 5 * result[i5]) i5++;
        if (minMultiple == 7 * result[i7]) i7++;
    }

    return result;
}
```

---

#### [Probability of being alive on an island after k steps]()

Given an Island of N\*N and K steps, calculate the probability of being alive after taking K steps from given
start position(x,y). If a person steps outside of boundary of island, he is dead. The person can move only in 4 direction from given point.

```cpp
float probability(vector<vector<Point>>& island, int n, Point start, int steps, unordered_map<string, double>& memo) {
    if(!isValid(start, n)) return 0.0;
    if(steps == 0) return 1.0;

    string key = getKey(start, steps);
    if(memo.find(key) != memo.end()) return memo[key];

    memo[key] = 0.25 * probability(island, n, start+{-1,0}, steps-1, memo)
              + 0.25 * probability(island, n, start+{0,-1}, steps-1, memo)
              + 0.25 * probability(island, n, start+{1,0}, steps-1, memo)
              + 0.25 * probability(island, n, start+{0,1}, steps-1, memo);

    return memo[key];
}

string getKey(Point pos, int steps) {
    return "{" + pos.to_string() + "," + std::to_string(steps) + "}";
}
```

---
