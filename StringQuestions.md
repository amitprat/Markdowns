####[Implement strstr](https://practice.geeksforgeeks.org/problems/implement-strstr/1)

Your task is to implement the function strstr. The function takes two strings as arguments (s,x) and locates the occurrence of the string x in the string s. The function returns and integer denoting the first occurrence of the string x in s (0 based indexing).

```cpp
int strstr(string s, string x)
{
    //return naive(s,x);
    return kmp(s,x);
}

int naive(string& s, string& p) {
    int n = s.length();
    int m = p.length();
    for(int i=0;i<=n-m;i++) {
        if(isMatch(s,p,i,0)) return i;
    }

    return -1;
}

bool isMatch(string& s, string& p, int i, int j) {
    while(j < p.length() && s[i] == p[j]) {
        i++, j++;
    }

    return j == x.length();
}

int kmp(string& s, string& p) {
    int n = s.length();
    int m = p.length();

    // calculate lps
    vector<int> lps(m);
    lps[0] = 0;
    int len = 0;
    for(int i=1;i<m;) {
        if(p[len] == p[i]) lps[i++] = ++len;
        else {
            if(len == 0) lps[i++] = len;
            else len = lps[len-1];
        }
    }

    // find match
    for(int i=0,j=0;i<n&&j<m;) {
        if(s[i] == p[j]) {
            i++; j++;
            if(j == m) return i-j;
        } else {
            if(j != 0) j = lps[j-1];
            else i++;
        }
    }

    return -1;
}
```

---

####[Base 62 encoding](https://practice.geeksforgeeks.org/problems/design-a-tiny-url-or-url-shortener2031/1)

```cpp
string idToShortURL(long long int n) {
    string result;
    while(n) {
        int rem = n%62;
        if(rem >= 0 && rem <= 25) result += 'a' + rem;
        else if(rem >= 26 && rem <= 51) result += 'A' + (rem-26);
        else result += '0' + (rem-52);

        n /= 62;
    }
    reverse(result.begin(), result.end());
    return result;
}
```

---

#### [Generate IP Address](https://practice.geeksforgeeks.org/problems/generate-ip-addresses/1)

```sh
Given a string S containing only digits, Your task is to complete the function genIp() which returns a vector containing all possible combination of valid IPv4 ip address and takes only a string S as its only argument.
Note : Order doesn't matter.

For string 11211 the ip address possible are
1.1.2.11
1.1.21.1
1.12.1.1
11.2.1.1
```

```cpp
vector<string> genIp(string &s) {
    int n = s.length();
    vector<string> result;
    for(int i=1;i<=3 && i <= n-3;i++) {
        // first octet, break if invalid
        string fs = s.substr(0,i);
        if(stoi(fs) > 255 || (fs.length()>1 &&fs[0] == '0')) break;

        for(int j=i+1;j<=i+3 && j <= n-2;j++) {
            // second octet, break if invalid
            string ss = s.substr(i,j-i);
            if(stoi(ss) > 255 || (ss.length() > 1 && ss[0] == '0')) break;

            for(int k=j+1;k<=j+3 && k <= n-1;k++) {
                // third octet, break if invalid
                string ts = s.substr(j,k-j);
                if(stoi(ts) > 255 || (ts.length() > 1 && ts[0] == '0')) break;

                // For the last octet, we should still try if it is invalid
                string frs = s.substr(k);;
                if(frs.length() > 3 || stoi(frs) > 255) continue;
                if(frs.length() > 1 && frs[0] == '0') continue;

                result.push_back(fs + "." + ss + "." + ts + "." + frs);
            }
        }
    }

    return result;
}
```

---

####[Rat in Maze](https://practice.geeksforgeeks.org/problems/rat-in-a-maze-problem/1)

Consider a rat placed at (0, 0) in a square matrix of order N \* N. It has to reach the destination at (N - 1, N - 1). Find all possible paths that the rat can take to reach from source to destination. The directions in which the rat can move are 'U'(up), 'D'(down), 'L' (left), 'R' (right). Value 0 at a cell in the matrix represents that it is blocked and rat cannot move to it while value 1 at a cell in the matrix represents that rat can be travel through it.
Note: In a path, no cell can be visited more than one time.

```cpp
vector<string> findPath(vector<vector<int>> &matrix, int n) {
    vector<string> result;
    unordered_set<int> visited;

    findPath(matrix, 0, 0, n, "", visited, result);

    sort(result.begin(), result.end());

    return result;
}

void findPath(vector<vector<int>> &matrix, int r, int c, int n,
    string word, unordered_set<int>& visited, vector<string>& result)
{
    if(r == n-1 && c == n-1 && matrix[r][c] == 1) {
        result.push_back(word);
        return;
    }

    if(r < 0 || c < 0 || r >= n || c >= n ||
        (visited.find(r*n+c) != visited.end()) ||
        (matrix[r][c] == 0))
    {
        return;
    }

    visited.insert(r*n + c);

    findPath(matrix, r+1, c, n, word + "D", visited, result);
    findPath(matrix, r-1, c, n, word + "U", visited, result);
    findPath(matrix, r, c+1, n, word + "R", visited, result);
    findPath(matrix, r, c-1, n, word + "L", visited, result);

    visited.erase(r*n + c);
}
```

---

####[Shortest Common Supersequence ](https://practice.geeksforgeeks.org/problems/shortest-common-supersequence0322/1#)

Given two strings X and Y of lengths m and n respectively, find the length of the smallest string which has both, X and Y as its sub-sequences.
Note: X and Y can have both uppercase and lowercase letters.

```cpp
int shortestCommonSupersequence(string X, string Y, int m, int n)
{
    int memo[m+1][n+1];
    for(int i=0;i<=m;i++) {
        for(int j=0;j<=n;j++) {
            if(i == 0) memo[i][j] = j;
            else if(j == 0) memo[i][j] = i;
            else if(X[i-1] == Y[j-1]) memo[i][j] = 1 + memo[i-1][j-1];
            else memo[i][j] = 1 + min(memo[i-1][j], memo[i][j-1]);
        }
    }

    return memo[m][n];
}
```

---

####[Longest Distinct/Unique characters in string ](https://practice.geeksforgeeks.org/problems/longest-distinct-characters-in-string5848/1)

Given a string S, find length of the longest substring with all distinct characters.

```cpp
int longestSubstrDistinctChars (string str)
{
    vector<int> lastIndex(256,-1);

    int mx = 0;
    for(int s=0,e=0;e<=str.length();e++) {
        if(e == str.length()) {
            mx = max(mx, e-s);
            break;
        }

        char cur = str[e];
        if(lastIndex[cur] == -1) {
            lastIndex[cur] = e;
        } else {
            mx = max(mx, e-s);
            while(s<=lastIndex[cur]) {lastIndex[str[s]] = -1; s++; }
            lastIndex[cur] = e;
        }
    }
    return mx;
}
```

---

####[Rearrange Characters to form string such that no 2 characters are adjacent](https://practice.geeksforgeeks.org/contest/coding-try-outs-amazon/problems#)

```cpp
bool canRearrange(string s)
{
    int cnt[26] = {0};
    for(auto ch : s) {
        cnt[ch-'a']++;
    }

    priority_queue<pair<int,char>> pq;
    for(int i=0;i<26;i++) {
        if(cnt[i] > 0) pq.push({cnt[i], i+'a'});
    }

    string res;
    bool canForm = false;
    while(pq.size() >= 2) {
        auto top1 = pq.top(); pq.pop();
        auto top2 = pq.top(); pq.pop();

        res += top1.second;
        res += top2.second;

        if(top1.first>1) pq.push({top1.first-1, top1.second});
        if(top2.first>1) pq.push({top2.first-1, top2.second});
    }

    if(!pq.empty() && res.back() != pq.top().second && pq.top().first == 1) {
        res += pq.top().second; pq.pop();
    }

    canForm = pq.empty();;
    //cout<<"Output: "<<res<<", canform"<<canForm<<endl;

    return canForm;
}
```
---

####[Anagram Substring Search]()
```cpp
bool anagramSubstringSearch(string txt, string pat) {
    vector<int> txtCnt(256, 0);
    vector<int> patCnt(256, 0);
    for(auto ch : pat) patCnt[ch]++;

    int m = pat.size();
    bool patFound = false;

    int curCnt = 0;
    for(int i=0;i<txt.size();i++) {
        char ch = txt[i];
        txtCnt[ch]++;
        if(txtCnt[ch] <= patCnt[ch]) curCnt++;

        if(curCnt == m) {
            patFound = true;
            cout << "Found at index = from " << (i - m + 1) << " to " << i << endl;
        }
        txtCnt[txt[i-m+1]]--;
    } 
    
    return patFound;
}
```
---

####[Check if strings can be chained together to form a string]()

```sh
{ "for", "geek", "rig", "kaf" }
{ "forriggeekkaf"}
```

```cpp
struct Graph {
    unordered_map<char, vector<char>> edges;
    unordered_map<char, int> indegree;
    unordered_set<char> vertices;

    void addEdge(char u, char v) {
        vertices.insert(u);
        vertices.insert(v);

        edges[u].push_back(v);
        indegree[v]++;
    }
};

bool canBeChained(vector<string> strs) {
    Graph g;

    for(auto str : strs) {
        g.addEdge(str.front(), str.back());
    }

    return isEulerianCycle(g);
}

bool isEulerianCycle(Graph g) {
    if(!isStronglyConnected(g)) return false;

    // check if indegree and outdegree isn't same
    for(auto u : g.vertices) {
        if(g.edges[u].size() != g.indegree[u]) return false;
    }

    return true;
}

bool isStronglyConnected(Graph g) {
    unordered_set<char> visited;

    // can reach all vertices from first vertex
    dfs(g, *g.vertices.begin(), visited);
    if(visited.size() != g.vertices.size()) return false;

    visited.clear();

    // get transpose and try reaching all vertices in transposed graph
    Graph gr = transpose(g);
    dfs(gr, *gr.vertices.begin(), visited);
    if(visited.size() != gr.vertices.size()) return false;

    return true;
}

Graph transpose(Graph g) {
    Graph gr;

    for(auto e : g.edges) {
        for(auto v : e.second) {
            gr.addEdge(v, e.first);
        }
    }

    return gr;
}
```
---

####[Check if word is valid](https://www.careercup.com/question?id=4774716350922752)

In a language, there are only 4 characters h,i,r,e. and we have to write a function which takes a string as input and returns whether the given input string is a �valid word� or not.

Definition of valid word :
1. A given word is a valid word if it is of the form h^n i^n r^n e^n where n >=1. (eg: hhiirree)
2. Valid words has concatenation property i.e. if w1 and w2 are valid words w1w2 is also a valid word.


Let's make it an "ordered" alphabet array
by letting (compile time fixed or get from somewhere at runtime):
alpha[]={ your ordered list of characters in your alphabet }
M be size of your ordered alphabet (i.e., size of alphabet array above)

***Idea***
```sh
for(t=0,i = 0,count=0; i < str.length ; i++)
    if( str[i] == alpha[t % M] ) count++;
    else if( str[i] == alpha[++t % M] && count > 1) count=1;
    else return false;

return ( count >;1 && t==M-1) ? true : false;
```

```cpp
bool isValid(string str, vector<char> dict)
{
    int n = str.length();
    int m = dict.size();
    int j = 0, cnt = 0;

    for (int i = 0; i < n; i++)
    {
        if (str[i] == dict[j]) cnt++;
        else if (cnt >= 1 && str[i] == dict[(j+1)%m]) { cnt = 1; j = (j+1)%m; }
        else return false;
    }

    return cnt >= 1 && j == m - 1;
}
```
---
####[Compress string]()

```cpp
string compress(string str) {
    int i = 0;
    char prev = '\0';
    int cnt = 0;

    for (auto ch : str) {
        if (prev != ch) {
            if (cnt > 0) {
                str[i++] = prev;
                if (cnt > 1) str[i++] = cnt + '0';
            }
            prev = ch;
            cnt = 0;
        } 
        cnt++;
    }
    if (cnt > 0) {
        str[i++] = prev;
        if (cnt > 1) str[i++] = cnt + '0';
    }

    return str.substr(0, i);
}
```
---
####[Convert one string to another string only using adjacent swaps]()

Given a string S1, convert it to another string S2 (Anagram) by swapping only adjacent elements. Print all the intermediate strings formed.

eg: s1: abcde
s2: bcdae

output: bacde,bcade, bcdae.

```sh
label each character in s2 with1 to n,
s2: bcdae
n2:12345
convert s1 to a array of number according to the labels,
s1: abcde
n1: 41235

the next is to sort 41235 using bubble sort:
41235
14235
12435
12345
```

```cpp
int countRequiredSwapsToConvertS1ToS2(string &s1, string s2)
{
    int cnt = 0;

    int order[256] = {0};
    for (int i = 0; i < s2.length(); i++)
        order[s2[i]] = i;

    bool swapped;
    do
    {
        cout<<s1<<endl;
        swapped = false;
        for (int i = 0; i < s1.length() - 1; i++)
        {
            if (order[s1[i]] > order[s1[i + 1]])
            {
                swap(s1[i], s1[i + 1]);
                swapped = true;
                cnt++;
            }
        }
    } while(swapped);

    return cnt;
}
```
---
####[Convert string a palindrome by adding some characters in front](https://www.careercup.com/question?id=6218545979457536)

Given a string S, you are allowed to convert it to a palindrome by adding 0 or more characters in front of it.
Find the length of the shortest palindrome that you can create from S by applying the above transformation.

The thing is, the time complexity gets reduced by simply finding palindrome that start from first character. If we reduce the suffix tree implementation:

1. Create suffix tree from the reverse string (Ukkonen proved this is O(n))
2. Walk this tree from the beginning with the original string until there is path.

This is largest palindrome that starts with beginning. So you need to add [original string length] - [found polyndrome] characters in the front

```cpp
int calculate(string s)
{
    int cnt = 0;
    for (int j = s.length() - 1; j >= 1; j--) {
        if (isPalin(s, 0, j)) return cnt;
        cnt++;
    }

    return cnt;
}

bool isPalin(string str, int s, int e) {
    while (s < e) {
        if (str[s] != str[e]) return false;
        s++;
        e--;
    }
    return true;
}
```

```cpp
int getMinCharToAddedToMakeStringPalin(string str)
{
    string rev = str;
    reverse(rev.begin(), rev.end());

    string concat = str + "$" + rev;
    vector<int> lps = getLps(concat);

    return str.length() - lps.back();
}

vector<int> getLps(string str)
{
    vector<int> lps(str.length(), 0);
    int len = 0;
    lps[0] = len;

    int i = 1;
    while (i < str.length()) {
        if (str[i] == str[len]) {
            len++;
            lps[i++] = len;
        } else {
            if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
    }

    return lps;
}
```
---

####[Count strings of a,b and c with size n made only of 1 b and 2 c](https://www.geeksforgeeks.org/count-strings-can-formed-using-b-c-given-constraints/)

Given a length n, count the number of strings of length n that can be made using ‘a’, ‘b’ and ‘c’ with at-most one ‘b’ and two ‘c’s allowed.

```sh
Input : n = 3 
Output : 19 
Below strings follow given constraints:
aaa aab aac aba abc aca acb acc baa
bac bca bcc caa cab cac cba cbc cca ccb 

Input  : n = 4
Output : 39
```

```cpp
static int count(int b, int c, int n) {
    if (b < 0 || c < 0) return 0;
    if (n == 0) return 1;

    return count(b, c, n - 1) + count(b - 1, c, n - 1) + count(b, c - 1, n - 1);
}
```

```cpp
static int countDP(int b, int c, int n) {
    vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(b + 1, vector<int>(c + 1)));

    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= b; j++) {
            for (int k = 0; k <= c; k++) {
                if (i == 0) dp[i][j][k] = 1;
                else {
                    dp[i][j][k] += dp[i - 1][j][k];
                    if (j > 0) dp[i][j][k] += dp[i - 1][j - 1][k];
                    if (k > 0) dp[i][j][k] += dp[i - 1][j][k - 1];
                }
            }
        }
    }

    return dp[n][b][c];
}
```
---
