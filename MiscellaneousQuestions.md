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

#### [Rearrange positive and negative numbers in original order]()

Give you an array which has n integers,it has both positive and negative integers.Now you need sort this array in a special way.
After that,the negative integers should in the front,and the positive integers should in the back.Also the relative position should not be changed.
eg. -1 1 3 -2 2 ans: -1 -2 1 3 2.
o(n)time complexity and o(1) space complexity is perfect.

```sh
This can be done in O(nlogn) using divide and conquer scheme. Before starting the algorithm, please see the following observation:

Observation: given an array A, say [1, -2, ..., 4], with n elements, we can get the inverse of A, denoted as A’ (4, …, -2, 1), in \theta(n) time with O(1) space complexity.

The basic idea of the algorithm is as follows:
1. We recursively ‘sort’ two smaller arrays of size n/2 (here ‘sort’ is defined in the question)
2. Then we spend \theta(n) time merging the two sorted smaller arrays with O(1) space complexity.
How to merge?
Suppose the two sorted smaller array is A and B. A1 denotes the negative part of A, and A2 denotes positive part of A. Similarly, B1 denotes the negative part of B, and B2 denotes positive part of B.
2.1. Compute the inverse of A2 (i.e., A2’) in \theta(|A2|) time; compute the inverse of B1 (i.e., B1’) in \theta(|B1|) time. [See observation; the total time is \theta(n) and space is O(1)]
Thus the array AB (i.e., A1A2B1B2) becomes A1A2’B1’B2.
2.2. Compute the inverse of A2’B1’ (i.e., B1A2) in \theta(|A2|) time. [See observation; the total time is \theta(n) and space is O(1)]
Thus the array A1A2’B1’B2 becomes A1B1A2B2. We are done.

Time complexity analysis:
T(n) = 2T(n/2) + \theta(n) = O(nlogn)
```

**_Rearrange using extra space_**

```cpp
void rearrange(vector<int>& arr) {
    int n = arr.size();
    vector<int> tmp(n);

    int k = 0;
    for(int i=0;i<n;i++) {
        if(arr[i] < 0) arr[k++] = arr[i];
    }

    for(int i=0;i<n;i++) {
        if(arr[i] >= 0) arr[k++] = arr[i];
    }

    arr = tmp;
}
```

**_Rearrange numbers using Insertion Sort_**

```cpp
void rearrangeNumbers(vector<int>& arr) {
    for(int i=0;i<arr.size();i++) {
        auto key = arr[i];
        if(key > 0) continue;

        for(int j=i-1;j>=0 && arr[j] > 0;j--) {
            arr[j+1] = ar[j];
        }
        arr[j] = key;
    }
}
```

**_Rearrange using merge sort_**

```cpp
void rearrangeNumbers(vector<int>& arr) {
    vector<int> tmp(arr.size());
    rearrangeNumbers(arr, 0, arr.size()-1, tmp);
}

void rearrangeNumbers(vector<int>& arr, int l, int r, vector<int>& tmp) {
    if(l >= r) return;

    int m = (l+r)/2;
    rearrangeNumbers(arr, l, m, tmp);
    rearrangeNumbers(arr, m+1, r, tmp);

    merge(arr, l, m, r, tmp);
}

void merge(vector<int>& arr, int l, int m, int r, vector<int>& tmp) {
    int i=l, j=m+1, k=l;
    while(i<=m && arr[i] < 0) arr[k++] = arr[i++];
    while(j<=r && arr[j] < 0) arr[k++] = arr[j++];

    while(i<=m) arr[k++] = arr[i++];
    while(j<=r) arr[k++] = arr[j++];

    i = l;
    while(i<=r) arr[i++] = tmp[i++];
}
```

**_Rearrange using optimized merge sort_**

```cpp
void rearrangeNumbers(vector<int>& arr) {
    rearrangeNumbers(arr, 0, arr.size()-1);
}

void rearrangeNumbers(vector<int>& arr, int l, int r) {
    if(l >= r) return;

    int m = (l+r)/2;
    rearrangeNumbers(arr, l, m);
    rearrangeNumbers(arr, m+1, r);

    merge(arr, l, m, r);
}

void merge(vector<int>& arr, int l, int m, int r) {
    int i=l, j=m+1;
    while(i<=m && arr[i]<0) i++;
    while(j<=r && arr[j]<0) j++;

    reverse(arr, i, m);
    reverse(arr, j, r);
    reverse(arr, i, j-1);
}

void reverse(vector<int>& arr, int l, int r) {
    while(l<r) { swap(arr[l++], arr[r--]); }
}
```

---

#### [Rearrange positive/negative numbers alternatively]()

```sh
{ -1,-1,-3,-4,-5 }   -> [-1, -1, -3, -4, -5]
{ 1,1,3,4,5 }        -> [1, 1, 3, 4, 5]
{ -1,-1,-3,-4,-5,6 } -> [6, -1, -1, -3, -4, -5]
{ 1,1,3,4,5,-6 }     -> [1, -6, 1, 3, 4, 5]
{ -1,6,9,-4,-10,-9,8,8,4 } -> [6, -1, 9, -4, 8, -10, 8, -9, 4]
```

```cpp
void rearrangePositiveNegativeNumbersAlternatively(vector<int>& arr) {
    int n = arr.size();
    for(int i=0;i<n;i++) {
        if((i&1) && (arr[i] > 0)) {
            auto k = findNextNeg(arr, i+1, n);
            if(k == -1) break;
            rotate(arr, i, k);
        } else if(!(i&1) && (arr[i] < 0)) {
            auto k = findNegPos(arr, i+1, n);
            if(k == -1) break;
            rotate(arr, i, k);
        }
    }
}

int findNextNeg(vector<int>& arr, int s, int n) {
    while(s < n && arr[s] > 0) s++;
    if(s == n) return -1;

    return s;
}

int findNextPos(vector<int>& arr, int s, int n) {
    while(s < n && arr[s] < 0) s++;
    if(s == n) return -1;

    return s;
}

void rotate(vector<int>& arr, int l, int r) {
    reverse(arr, l, r-1);
    reverse(arr, l, r);
}
```

---

#### [621. Task Scheduler](<https://leetcode.com/problems/task-scheduler/discuss/104504/C%2B%2B-8lines-O(n)>)

Given a characters array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer n that represents the cooldown period between two same tasks (the same letter in the array), that is that there must be at least n units of time between any two same tasks.

Return the least number of units of times that the CPU will take to finish all the given tasks.

```sh
Example 1:

Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation:
A -> B -> idle -> A -> B -> idle -> A -> B
There is at least 2 units of time between any two same tasks.
```

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        unordered_map<char,int>mp;
        int count = 0;
        for(auto e : tasks)
        {
            mp[e]++;
            count = max(count, mp[e]);
        }

        int ans = (count-1)*(n+1);
        for(auto e : mp) if(e.second == count) ans++;
        return max((int)tasks.size(), ans);
    }
};
```

---

#### [Check if all given tasks can be scheduled on servers](https://www.careercup.com/question?id=6282171643854848)

There are at most eight servers in a data center. Each server has got a capacity/memory limit. There can be at most 8 tasks that need to be scheduled on those servers. Each task requires certain capacity/memory to run, and each server can handle multiple tasks as long as the capacity limit is not hit. Write a program to see if all of the given tasks can be scheduled or not on the servers?

Ex:
Servers capacity limits: 8, 16, 8, 32
Tasks capacity needs: 18, 4, 8, 4, 6, 6, 8, 8
For this example, the program should say 'true'.

Ex2:
Server capacity limits: 1, 3
Task capacity needs: 4
For this example, program should return false.

```cpp
bool canBeScheduled(vector<int>& servers, vector<int>& tasks) {
    return canBeScheduled(servers, tasks, 0);
}

bool canBeScheduled(vector<int>& servers, vector<int>& tasks, int taskIndex) {
    if(taskIndex == tasks.size()) return true;

    for(auto& server : servers) {
        if(server >= tasks[taskIndex]) {
            server -= tasks[taskIndex];
            if(canBeScheduled(servers, tasks, taskIndex+1)) return true;
            server += tasks[taskIndex];
        }
    }

    return false;
}
```

**_Another Solution_**

```cpp
bool canBeScheduled(vector<int>& servers, vector<int>& tasks) {
    vector<bool> used(tasks.size(), false)
    return canBeScheduled(servers, tasks, used);
}

bool canBeScheduled(vector<int>& servers, vector<int>& tasks, vector<bool>& used) {
    bool allUsed = false;
    for(auto u : used) allUsed &= u;
    if(allUsed) return true;

    for(int i=0;i<tasks.size();i++) {
        if(!used[i]) {
            for(auto& server : servers) {
                if(server >= tasks[i]) {
                    used[i] = true;
                    server -= tasks[i];

                    if(canBeScheduled(servers, tasks, used)) return true;

                    used[i] = false;
                    server += tasks[i];
                }
            }
        }
    }
    return false;
}
```

---

#### [Set Row and Column To Zeros](https://www.careercup.com/question?id=4959773472587776)

Given an n X n matrix, find all elements which are zero, when found set all the elements in that row and column to zero.

```cpp
void rowColToZeros(vector<vector<int>>& matrix) {
    int n = matrix.size();
    if(n == 0) return;
    int m = matrix[0].size();

    vector<bool> row(m, false);
    vector<bool> col(n, false);

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(matrix[i][j] == 0) {
                row[j] = col[i] = true;
            }
        }
    }

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(row[j] || col[i]) matrix[i][j] = 0;
        }
    }
}
```

```cpp
void rowColToZeros(vector<vector<int>>& matrix) {
    int n = matrix.size();
    if(n == 0) return;
    int m = matrix[0].size();

    bool firstRowZero = false, firstColZero = false;

    for(int j=0;j<m;j++) firstRowZero |= (matrix[0][j] == 0);
    for(int i=0;i<n;i++) firstColZero |= (matrix[i][0] == 0);

    for(int i=1;i<n;i++) {
        for(int j=1;j<m;j++) {
            if(matrix[i][j] == 0) matrix[i][0] = matrix[0][j] = 0;
        }
    }

    for(int i=1;i<n;i++) {
        for(int j=1;j<m;j++) {
            if(matrix[i][0] == 0 || matrix[0][j]) matrix[i][j] = 0;
        }
    }

    if(firstRowZero) for(int j=0;j<m;j++) matrix[0][j] = 0;
    if(firstColZero) for(int i=0;i<n;i++) matrix[i][0] = 0;
}
```

---

#### [Snake Ladder]()

```cpp
int minimumJumps(Board& board, int s, int d, unordered_map<int,int>& jumps) {
    queue<int> q;
    q.push(s);

    unordered_set<int> visited;
    visited.insert(s);

    int  jumps = 0;
    while(!q.empty()) {
        int sz = q.size();
        while(sz--) {
            auto f = q.front(); q.pop();
            if(f == d) return jumps;

            for(int c=1;c<=6 && f+c<=d;c++) {
                int n = f+c;

                if(jumps.find(n) != jumps.end()) n = jumps[n];
                if(visited.count(n) > 0) continue;

                q.push(n);
                visited.insert(n);
            }
        }
        jumps++;
    }

    return -1;
}
```

**_Print path_**

```cpp
struct Node {
    int u;
    int d;
    vector<int> path;

    Node(int u, int d) : u(u), d(d) { pat.push_back(u); }
    Node(int u, int d, vector<int>& oldPath) : u(u), d(d), path(oldPath)
    {
        path.push_back(u);
    }
};

int getMinDiceThrows(vector<int>& moves) {
    int N = 30;
    return getMinDiceThrows(moves, 0, N-1);
}

int getMinDiceThrows(vector<int>& moves, int s, int e) {
    unordered_set<int> visited;
    queue<Node> q;

    q.push({s, 0});
    visited.insert(s);

    while(!q.empty()) {
        auto node = q.front(); q.pop();
        if(node.u == e) {
            cout<<to_string(node.path)<<endl;
            return node.d;
        }

        for(int dice=1;dice<=6 && node.u+dice<=e;dice++) {
            int v = node.u+dice;
            if(moves[v] != -1) v = moves[v];

            if(visited.find(v) == visited.end()) {
                q.push({v, node.d+1, node.path});
            }
        }
    }

    return -1;
}
```

---

#### [Special Keyboard (Ctrl-A + Ctrl-C + Ctrl-V)]()

```cpp
int maxChars(int keysCount) {
    vector<int> memo(keysCount + 1);
    for(int i=0;i<=6;i++) memo[i] = i;

    for(int i=7;i<=keysCount;i++) {
        memo[i] = i;

        for(int j=i-3;j>=1;j--) {
            int cur = (i-j-1) * memo[j];
            memo[i] = max(memo[i], cur);
        }
    }

    return memo[keysCount];
}
```

---

#### [Largest square surrounded by 1s]()

```cpp
int largestSquareSurroundedBy1s(vector<vector<int>>& arr) {
    int n = arr.size();
    if(n == 0) return 0;

    int m = arr[0].size();
    vector<vector<pair<int,int>>> memo(n, vector<pair<int,int>>(m));

    for(int i=0;i<n;i++) {
        for(int j=0;j<m;j++) {
            if(arr[i][j]) {
                if(i == 0 && j == 0) memo[i][j] = {1,1};
                else if(i == 0) memo[i][j] = {1, memo[i][j-1].second+1};
                else if(j == 0) memo[i][j] = {memo[i-1][j].first+1, 1};
                else memo[i][j] = {memo[i-1][j].first+1,memo[i][j-1].second+1};
            }
        }
    }

    int maxLen = 0;
    pair<int,int> start;
    for(int i=n-1;i>=0;i++) {
        for(int j=m-1;j>=0;j--) {
            int curLen = min(memo[i][j].first, memo[i][j].second);
            while(curLen) {
                bool isSquare = (memo[i-curLen+1][j].second >= curLen) && (memo[i][j-curLen+1].first >= curLen);
                if(isSquare && curLen > maxLen) {
                    maxLen = curLen;
                    start = {i-curLen,j-curLen};
                    break;
                }
                curLen--;
            }
        }
    }

    return maxLen;
}
```

---

#### [Move into a Turtle Grid]()

```cpp
class TurtleGrid {
    enum class Dir {
        North, East, South, West
    };

public:
    void test() {
        string str = "FFFRRFLF";
        Point startPos = {1,1};
        Dir startDir = Dir::North;
        auto res = getFinalPos(str, startPos, startDir);
        cout<<format("Final position of Turtle = {}, Direction={}", res.first, res.second)<<endl;
    }

    pair<Point,Dir> getFinalPos(string input, Point start, Dir startDir) {
        for(auto ch : input) {
            switch(ch) {
                case 'F':
                    start = moveForward(start, startDir);
                    break;
                case 'R':
                    startDir = changeDir(startDir, +1);
                    break;
                case 'L':
                    startDir = changeDir(startDir, -1);
                    break;
            }
        }

        return {start, startDir};
    }

    Point moveForward(Point start, Dir curDir) {
        switch(curDir) {
            case Dir::North: return {start.x, start.y-1};
            case Dir::South: return {start.x, start.y+1};
            case Dir::East: return {start.x+1, start.y};
            case Dir::West: return {start.x-1, start.y};
        }
    }

    Dir changeDir(Dir curDir, int change) {
        return (Dir)(((int)(curDir) + 4 + change)/4);
    }
}
```

---

#### [Find Missing Number in a string of increasing numbers]()

```cpp
int missingNumber(string& str) {
    int n = str.length();
    for(int i=1;i<=n;i++) {
        string cur = str.substr(0, i);
        int curNum = stoi(cur);

        int missingNum = -1;
        int j = i;

        for(;j+len(curNum+1)<=n;j+=len(curNum)) {
            string nextNumStr = str.substr(j, len(curNum+1)); // get next expected num
            int nextNum = stoi(nextNumStr);

            if(nextNum == curNum+1) curNum = nextNum;
            else if(nextNum == curNum+2 && missingNum == -1) {
                missingNum = curNum+1;
                curNum = nextNum;
            } else {
                break;
            }
        }

        if(j == n) return (missingNum == -1) ? curNum+1 : missingNum;
    }

    return -1;
}
```

---

#### [Merge K Sorted Arrays]()

```cpp
using Node = tuple<int, int, int>;
struct Compare {
    bool operator()(Node& f, Node& s) {
        return get<0>(f) > get<0>(s);
    }
};

vector<int> mergeKSortedArrays(vector<vector<int>>& arrays) {
    priority_queue<Node, vector<Node>, Compare> pq;

    int i = 0;
    for (auto& arr : arrays) {
        if (arr.size() > 0) pq.push({ arr[0], i, 0 });
        i++;
    }

    vector<int> result;
    while (!pq.empty()) {
        auto elem = pq.top(); pq.pop();
        result.push_back(get<0>(elem));

        int arrIndex = get<1>(elem), pos = get<2>(elem);
        if (pos + 1 < arrays[arrIndex].size()) {
            pq.push({ arrays[arrIndex][pos + 1], arrIndex, pos + 1 });
        }
    }

    return result;
}
```

---

#### [Implement Power Function]()

```cpp
int pow(int x, int y) {
    if(y == 0) return 1;

    int p = pow(x, y/2);
    if(y&1) p *= x;

    return p;
}
```

```cpp
double pow(double x, int y) {
    if(y == 0) return 1;
    if(y < 0) return pow(1.0/x, -y);

    int p = pow(x, y/2);
    if(y&1) p *= x;

    return p;
}
```

---

#### [Total cost of connecting ropes with minimum cost]()

```cpp
template <class T>
class Minheap {
    vector<int> arr;
    int sz;

public:
    void insert(int num) {
        arr.push_back(num);
        sz++;

        upHeapify(sz-1);
    }

    int top() {
        if(arr.empty()) throw exception("Heap is empty!");
        return arr.front();
    }

    void pop() {
        if(arr.empty()) throw exception("Heap is empty!");
        arr[0] = arr[sz-1];
        arr.pop_back();
        sz--;

        downHeapify(0);
    }

    int size() { return sz; }
    bool empty() { return arr.empty(); }

private:
    int left(int n) { return 2*n + 1; }
    int right(int n) { return 2*n + 2; }
    int parent(int n) { return (n-1)/2; }

    void upHeapify(int idx) {
        int p = parent(idx);

        while(idx > 0 && arr[p] > arr[idx]) {
            swap(arr[p], arr[idx]);
            idx = p;
            p = parent(idx);
        }
    }

    void downHeapify(int idx) {
        int sm = idx;

        int l = left(idx);
        int r = right(idx);

        if(l < sz && arr[l] < arr[sm]) sm = l;
        if(r < sz && arr[r] < arr[sm]) sm = r;

        if(sm != idx) {
            swap(arr[sm], arr[idx]);
            downHeapify(sm);
        }
    }
};

int minimumCost(vector<int>& ropes) {
    Minheap<int> mh;

    for(auto& rope : ropes) {
        mh.insert(rope);
    }

    int totalCost = 0;

    while(mh.size() > 1) {
        auto cost1 = mh.top(); mh.pop();
        auto cost2 = mh.top(); mh.pop();

        mh.insert(cost1 + cost2);
        totalCost += cost1 + cost2;
    }

    return totalCost;
}
```

```cpp
int minimumCost(vector<int>& ropes) {
    priority_queue<int, vector<int>, greater<int>> mh;

    for(auto& rope : ropes) {
        mh.push(rope);
    }

    int totalCost = 0;

    while(mh.size() > 1) {
        auto cost1 = mh.top(); mh.pop();
        auto cost2 = mh.top(); mh.pop();

        mh.push(cost1 + cost2);
        totalCost += cost1 + cost2;
    }

    return totalCost;
}
```

---

#### [Kth smallest/largest element in an unordered array]()

**_We have following approaches for finding kth smallest element_**

1. Sort array in ascending order and return arr[k-1], Time: O(n \* log(n))
2. Use bubble sort to perform K outer iterations. On each iteration, move the smallest element to front. After k iterations, kth smallest element is at correct position i.e. arr[k-1]. We can use selection sort as well. Time: O(k\*n)
3. Use Maxheap of size K and replace the top element for rest of elements. After all elements are inserted, the top most element is kth smallest. Space: O(k), Time: O(n \* log(k))
4. Use Minheap of size N and pop k-1 elements. Next element is kth smallest. Space: O(N), Time: O(n*log(n) + k*log(n))
5. Use quick sort partitioning to partition around pivot and keep partitioning element until the element before partition index == k, then we can return that element. Average Time: O(n) , Worst Time: O(n^2)

- We can use similar approach for finding kth largest as well.

**_Using sorting approach_**

```cpp
int kthSmallest(vector<int>& arr, int k) {
    sort(arr.begin(), arr.end());
    return arr[k-1];
}
```

```cpp
int kthLargest(vector<int>& arr, int k) {
    sort(arr.begin(), arr.end(), greater<int>());
    return arr[k-1];
}
```

**_Using modified bubble/selection sort approach_**

```cpp
int kthSmallestUsingBubbleSort(vector<int>& arr, int k) {
    for(int i=0;i<k;i++) {
        for(int j=arr.size()-1;j>=i+1;j--) {
            if(arr[j-1] > arr[j]) swap(arr[j-1], arr[j]);
        }
    }

    return arr[k-1];
}
```

```cpp
int kthSmallestUsingSelectionSort(vector<int>& arr, int k) {
    int smallest;
    for(int i=0;i<k;i++) {
        smallest = i;
        for(int j=i+1;j<arr.size();j++) {
            if(arr[j] < arr[i]) smallest = j;
        }
        if(smallest != i) {
            swap(arr[i], arr[smallest]);
        }
    }

    return arr[k-1];
}
```

**_Using Maxheap of size K_**

```cpp
int kthSmallestUsingMaxheap(vector<int>& arr, int k) {
    priority_queue<int> pq;
    for(int i=0;i< arr.size() && i < k;i++) {
        pq.push(arr[i]);
    }

    for(int i=k;i < arr.size(); i++) {
        if(arr[i] < pq.top()) {
            pq.pop();
            pq.push(arr[i]);
        }
    }

    return pq.top();
}
```

**_Using Minheap of size N_**

```cpp
int kthSmallestUsingMinheap(vector<int>& arr, int k) {
    priority_queue<int, vector<int>, greater<int>> pq;
    for(auto e : arr) pq.push(e);

    while(k-- > 1) { pq.pop(); }

    return pq.top();
}
```

**_Using quick partitioning_**

```cpp
int kthSmallestUsingQuickSelect(vector<int>& arr, int k) {
    return kthSmallestUsingQuickSelect(arr, 0, arr.size()-1, k);
}

int kthSmallestUsingQuickSelect(vector<int>& arr, int s, int e, int k) {
    if(s > e) return INT_MIN;

    int p = partition(arr, s, e);
    int q = p-s+1; // smaller on left

    if(q == k) return arr[p];
    else if(q < k) return kthSmallestUsingQuickSelect(arr, p+1, e, k-q);
    else return kthSmallestUsingQuickSelect(arr, s, p-1, k);
}

int partition(vector<int>& arr, int s, int e) {
    int pivot = arr[e];
    int i = s;
    for(int j=s;j<e;j++) {
        if(arr[j] <= pivot) {
            swap(arr[i++], arr[j]);
        }
    }
    swap(arr[i], arr[e]);

    return i;
}
```

```cpp
int kthLargestUsingQuickSelect(vector<int>& arr, int k) {
    return kthLargestUsingQuickSelect(arr, 0, arr.size()-1, k);
}

int kthLargestUsingQuickSelect(vector<int>& arr, int s, int e, int k) {
    if(s > e) return INT_MIN;

    int p = partition(arr, s, e);
    int q = e-p+1; // larger on right

    if(q == k) return arr[p];
    else if(q < k) return kthLargestUsingQuickSelect(arr, s, p-1, k-q);
    else return kthLargestUsingQuickSelect(arr, p+1, e, k);
}

int partition(vector<int>& arr, int s, int e) {
    int pivot = arr[e];
    int i = s;
    for(int j=s;j<e;j++) {
        if(arr[j] <= pivot) {
            swap(arr[i++], arr[j]);
        }
    }
    swap(arr[i], arr[e]);

    return i;
}
```

---

#### [Distance between two words in a string]()

**_Distance between 2 words in string without preprocessing_**

```cpp
int minDistanceBetweenTwoWords(string& input, string& w1, string& w2) {
    vector<string> parts = split(input,' ');

    int dist = INT_MAX, first = -1, second = -1;
    for(int i=0;i<parts.size();i++) {
        string& part = parts[i];
        if(part == w1) first = i;
        if(part == w2) second = i;

        if(first != -1 && second != -1) {
            dist = min(dist, abs(first-second));
        }
    }

    return dist;
}
```

**_Distance between 2 words in string with preprocessing_**

```cpp
void process(string& str, unordered_map<string, vector<int>>& posMap) {
    vector<string> parts = split(str, ' ');

    for(int i=0;i<parts.size();i++) {
        posMap[parts[i]].push_back(i);
    }
}

int minDistanceBetweenTwoWords(string& input, string& w1, string& s2) {
    unordered_map<string, vector<int>> posMap;
    process(input, posMap);

    auto& l1 = posMap[w1];
    auto& l2 = posMap[w2];

    int i=0, j=0;
    int dist = INT_MAX;

    while(i<l1.size() && j<l2.size()) {
        int curDist = abs(l1[i]-l2[j]);
        dist = min(dist, curDist);

        if(l1[i] < l2[j]) i++;
        else j++;
    }

    return dist;
}

vector<string> split(string s, char ch) {
    vector<string> result;
    stringstream ss(s);

    string word;
    while (ss >> word) {
        result.push_back(word);
    }

    return result;
}
```

---

#### [Word Ladder]()

A transformation sequence from word beginWord to word endWord using a dictionary wordList is a sequence of words beginWord -> s1 -> s2 -> ... -> sk such that:

Every adjacent pair of words differs by a single letter.
Every si for 1 <= i <= k is in wordList. Note that beginWord does not need to be in wordList.
sk == endWord
Given two words, beginWord and endWord, and a dictionary wordList, return the number of words in the shortest transformation sequence from beginWord to endWord, or 0 if no such sequence exists.

Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.

**_Shortest path between begin and end word_**

```cpp
vector<string> findShortestPath(string& begin, string& end, unordered_set<string> dict) {
    unordered_set<string> visited;
    queue<pair<string, vector<string>>> q;
    q.push({ begin, {begin} });
    visited.insert(begin);

    vector<string> res;
    while (!q.empty()) {
        int size = q.size();
        for (int i = 0; i < size; i++) {
            auto cur = q.front(); q.pop();
            if (cur.first == end) {
                res = cur.second; break;
            }

            auto neighbours = getOneDistantWords(cur.first, dict, visited);
            for (auto& neighbour : neighbours) {
                auto tmp = cur.second;
                tmp.push_back(neighbour);
                q.push({ neighbour, tmp });
                visited.insert(neighbour);
            }
        }
    }

    return res;
}

vector<string> getOneDistantWords(string begin, unordered_set<string>& dict, unordered_set<string>& visited) {
    vector<string> res;
    for (int i = 0; i < begin.length(); i++) {
        char tmp = begin[i];
        for (char ch = 'a'; ch <= 'z'; ch++) {
            if (begin[i] != ch) {
                begin[i] = ch;
                if (dict.find(begin) != dict.end() && visited.find(begin) == visited.end()) res.push_back(begin);
            }
        }
        begin[i] = tmp;
    }

    return res;
}
```

**_Find ladder length_**

```cpp
int ladderLength(string& begin, string& end, unordered_set<string> dict) {
    queue<string> q;
    q.push(begin);
    int len = 1;

    while (!q.empty()) {
        int n = q.size();
        while (n--) {
            auto cur = q.front(); q.pop();
            if (cur == end) return len;
            dict.erase(cur);

            vector<string> neighbours = getOneDistantWords(cur, dict);
            for (auto& neighbour : neighbours) {
                q.push(neighbour);
            }
        }
        len++;
    }

    return 0;
}

vector<string> getOneDistantWords(string word, unordered_set<string>& dict) {
    vector<string> neighbours;
    for (int i = 0; i < word.length(); i++) {
        char ch = word[i];
        for (char t = 'a'; t <= 'z'; t++) {
            if (t != ch) {
                word[i] = t;
                if (dict.find(word) != dict.end()) neighbours.push_back(word);
            }
        }
        word[i] = ch;
    }
    return neighbours;
}
```

```cpp
int ladderLength2(string beginWord, string endWord, unordered_set<string> wordDict) {
    queue<pair<string, int>> queue;
    queue.push({ beginWord, 1 });

    while (!queue.empty()) {
        auto front = queue.front(); queue.pop();
        if (front.first == endWord) {
            return front.second;
        }

        string word = front.first;
        for (int i = 0; i < word.size(); i++) {
            for (char c = 'a'; c <= 'z'; c++) {
                char temp = word[i];
                if (word[i] != c) {
                    word[i] = c;
                }

                if (wordDict.contains(word)) {
                    queue.push({ word, front.second + 1 });
                    wordDict.erase(word);
                }

                word[i] = temp;
            }
        }
    }

    return 0;
}
```

**_Search using bidirectional bfs search_**

```cpp
int findLadderBidirection(string begin, string end, unordered_set<string> dict) {
    unordered_set<string> seen;
    unordered_set<string> head, tail;
    head.insert(begin);
    tail.insert(end);
    int ladder = 2;

    while (!head.empty() && !tail.empty()) {
        if (head.size() > tail.size()) swap(head, tail);

        unordered_set<string> tmp;
        for (auto it = head.begin(); it != head.end(); it++) {
            vector<string> neighbours = getOneDistantWords(*it, dict);
            for (auto& neighbour : neighbours) {
                if (tail.find(neighbour) != tail.end()) {
                    return ladder;
                }
                if (seen.find(neighbour) == seen.end() && dict.find(neighbour) != dict.end()) {
                    tmp.insert(neighbour);
                    seen.insert(neighbour);
                }
            }
        }
        ladder++;
        swap(head, tmp);
    }

    return 0;
}
```

---

#### [Regex Pattern Matching/ Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/discuss/5665/My-concise-recursive-and-DP-solutions-with-full-explanation-in-C%2B%2B)

Regular Expression Matching
Given an input string s and a pattern p, implement regular expression matching with support for '.' and '_' where:
'.' Matches any single character.
'_' Matches zero or more of the preceding element.
The matching should cover the entire input string (not partial).

Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".

Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".

**_Recursive approach_**

```cpp
bool isMatch(string txt, string pat) {
    return isMatch(txt, pat, txt.size()-1, pat.size()-1);
}
bool isMatch(string txt, string pat, int idx1, int idx2) {
    if(idx2 < 0) return idx1 < 0;
    if(idx1 < 0) return pat[idx2] == '*' && isMatch(txt, pat, idx1, idx2-2);

    if(txt[idx1] == pat[idx2] || pat[idx2] == '.') return isMatch(txt, pat, idx1-1, idx2-1);

    if(pat[idx2] == '*')
        return (txt[idx1] == pat[idx2-1] || pat[idx2-1] == '.') && isMatch(txt, pat, idx1-1, idx2) ||
                isMatch(txt, pat, idx1, idx2-2);

    return false;
}
```

**_Dynamic programming approach_**

```cpp
bool isMatch(string txt, string pat) {
    int n = txt.size(), m = pat.size();
    vector<vector<bool>> dp(n+1, vector<bool>(m+1));

    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if(i == 0 && j == 0) dp[i][j] = true;
            else if(i == 0) dp[i][j] = (pat[j-1]== '*' && dp[i][j-2]);
            else if(j == 0) dp[i][j] = false;
            else {
                if(txt[i-1] == pat[j-1] || pat[j-1] == '.') dp[i][j] = dp[i-1][j-1];
                else if(pat[j-1] == '*')
                    dp[i][j] = ((pat[j-2] == txt[i-1] || pat[j-2] == '.') && dp[i-1][j]) || dp[i][j-2];
                else dp[i][j] = false;
            }
        }
    }

    return dp[n][m];
}
```

---

#### [Wildcard Matching]()

```cpp

```

---

#### [One edit distance away]()

```cpp
bool oneEditDistance(string& str1, string& str2, char& different) {
    different = '$';
    if(str1.length() > str2.length()) return oneEditDistance(str2, str1, different);
    if(str2.length()-str1.length() > 1) return false;

    bool flag = false;
    for(int i=0,j=0;j<str2.length();i++,j++) {
        if(i == str1.length() || str1[i] != str2[j]) {
            if(flag) return false;
            flag = true;

            different = str2[j];
            if(str1.length() != str2.length()) i--;
        }
    }

    return flag;
}
```

```cpp
bool oneEditDistance(string& str1, string& str2, char& different) {
    different = '$';
    if(str1.length() > str2.length()) return oneEditDistance(str2, str1, different);
    if(str2.length()-str1.length() > 1) return false;

    bool flag = false;
    int i=0,j=0;
    for(;i<str1.length();i++,j++) {
        if(str1[i] != str2[j]) {
            if(flag) return false;
            flag = true;

            different = str2[j];
            if(str1.length() != str2.length()) i--;
        }
    }
    if(!flag && (i+1 == j)) { flag = true; different = str2[j]; }

    return flag;
}
```

```cpp
bool oneEditDistance(string& str1, string& str2) {
    int n = str1.length();
    int m = str2.length();

    if(abs(n-m) > 1) return false;
    if(n > m) { swap(str1, str2); swap(n, m); }

    for(int i=0;i<n;i++) {
        if(str1[i] != str2[i]) {
            if(n == m) return str1.substr(i) == str2.substr(i);
            else return str1.substr(i) == str2.substr(i+1);
        }
    }

    return n != m;
}
```

```cpp
bool isOneEditDistance(string& str1, string& str2) {
    int n = str1.length();
    int m = str2.length();

    int diff = 0;
    for (int i = 0, j = 0; diff <= 1 && (i < n || j < m); i++, j++) {
        if (i >= n || j >= m) { diff++; continue; }

        if (str1[i] != str2[j]) {
            diff++;

            if (n < m) i--;
            else if (n > m) j--;
        }
    }

    return diff == 1;
}
```

---

#### [Count all employees under each manager]()

```cpp
unordered_map<string, vector<string>> countEmployees(unordered_map<string, string>& dataSet) {
    unordered_map<string, vector<string>> partSet;
    for(auto& e : dataSet) {
        partSet[e.second].push_back(e.first);
    }

    unordered_map<string, vector<string>> result;
    for(auto& e : partSet) {
        unordered_set<string> visited;
        auto employees = expand(partSet, e.second, visited);
        result[e.first] = employees;
    }

    return result;
}

vector<string> expand(unordered_map<string, vector<string>>& partSet, vector<string>& employees, unordered_set<string>& visited)
{
    vector<string> result;

    for(auto& e : employees) {
        if(visited.find(e) == visited.end()) {
            result.push_back(e);
            visited.insert(e);

            auto nextEmpSet = partSet[e];
            result.insert(result.end(), expand(partSet, nextEmpSet, visited));
        }
    }

    return result;
}
```

---

#### [Max value of array after applying operators at different positions]()

```cpp
maxValueOfExpression({1,2,3,4}, 0, 3, {'+','-','*','/', memo});

double maxValueOfExpression(vector<double>& arr, int s, int e, vector<char>& ops, unordered_map<string, double>& memo) {
    if(s == e) return arr[s];
    if(memo[key(s, e)]) return memo[key(s,e)];

    double mxValue = 0.0;
    for(int k=s;k<e;k++) {
        double left = maxValueOfExpression(arr, s, k);
        double right = maxValueOfExpression(arr, k+1, e);

        mxValue = max(mxValue, curMaxValue(left, right, ops));
    }

    memo[key(s,e)] = mxValue;

    return memo[key(s,e)];
}

double curMaxValue(double left, double right, vector<char>& ops) {
    double mxValue = 0.0;
    for(auto op : ops) {
        switch(op) {
            case '+':
                mxValue = max(mxValue, left+right);
                break;
            case '-':
                mxValue = max(mxValue, left-right);
                break;
            case '*':
                mxValue = max(mxValue, left*right);
                break;
            case '/':
                mxValue = max(mxValue, left/right);
                break;
        }
    }

    return mxValue;
}

string key(int s, int e) {
    return "{" + std::to_string(s) + "," + std::to_string(e) + "}";
}
```

- Even if we have '(' and ')' operators in above ops array, algorithm will remain same because when combining left and right subarrays, we are doing the same thing.

---

#### [Pattern Match With Given Pattern To Text]()

```sh
Input:
{"abba", "redbluebluered"},
{"aaaa", "asdasdasdasd"},
{"aabb","xyzabcxyzabc"},
{"abc", "redbluebluered"},
{"acbae", "redbluegreenredyellow"},
{"abcdefghijabcd","abcdefghijabcd"}

Output:
{a=red, b=blue}
Does pattern='abba' matches text='redbluebluered', result='true'
{a=asd}
Does pattern='aaaa' matches text='asdasdasdasd', result='true'
Does pattern='aabb' matches text='xyzabcxyzabc', result='false'
{a=r, b=e, c=dbluebluered}
Does pattern='abc' matches text='redbluebluered', result='true'
{a=r, c=e, b=dblueg, e=eenredyellow}
Does pattern='acbae' matches text='redbluegreenredyellow', result='true'
{a=a, b=b, c=c, d=d, e=e, f=f, g=g, h=h, i=i, j=j}
Does pattern='abcdefghijabcd' matches text='abcdefghijabcd', result='true'
```

```cpp
bool isMatch(string& txt, string& pat) {
    int patIdx = 0, txtIdx = 0;

    unordered_map<char, string> map;
    return isMatch(txt, txtIdx, pat, patIdx, map);
}

bool isMatch(string& txt, int txtIdx, string& pat, int patIdx, unordered_map<char, string>& map)
{
    if(txtIdx == txt.size() && patIdx == pat.size())
    {
        cout<<to_string(map)<<endl;
        return true;
    }

    if(txtIdx >= txt.size() || patIdx >= pat.size()) return false;

    if(map.find(pat[patIdx]) == map.end())
    {
        // if current pattern character isn't seen yet
        for(int txtEndIdx=txtIdx;txtEndIdx<txt.size();txtEndIdx++) {
            string curStr = txt.substr(txtIdx, txtEndIdx-txtIdx+1);

            map[pat[patIdx]] = curStr;
            if(isMatch(txt, txtEndIdx+1, pat, patIdx+1, map)) return true;
            map.erase(pat[patIdx]);
        }
    }
    else
    {
        // If pattern already seen, then match the old matched string to current txt string
        string oldStr = map[pat[patIdx]];
        string curStr = txt.substr(txtIdx, oldStr.length());

        if(oldStr != curStr) return false;

        return isMatch(txt, txtIdx+curStr.size(), pat, patIdx+1, map);
    }

    return false;
}
```

---
