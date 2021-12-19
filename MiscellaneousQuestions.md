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
