#### [Implement strstr](https://practice.geeksforgeeks.org/problems/implement-strstr/1)

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

#### [Base 62 encoding](https://practice.geeksforgeeks.org/problems/design-a-tiny-url-or-url-shortener2031/1)

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

#### [Rat in Maze](https://practice.geeksforgeeks.org/problems/rat-in-a-maze-problem/1)

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

#### [Shortest Common Supersequence ](https://practice.geeksforgeeks.org/problems/shortest-common-supersequence0322/1#)

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

#### [Longest Distinct/Unique characters in string ](https://practice.geeksforgeeks.org/problems/longest-distinct-characters-in-string5848/1)

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

#### [Rearrange Characters to form string such that no 2 characters are adjacent](https://practice.geeksforgeeks.org/contest/coding-try-outs-amazon/problems#)

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

#### [Anagram Substring Search]()

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
        if(i >= m-1) {
            if(txtCnt[txt[i-m+1]] <= pat[txt[i-m+1]]) curCnt--;
            txtCnt[txt[i-m+1]]--;
        }
    }

    return patFound;
}
```

---

#### [Check if strings can be chained together to form a string]()

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

#### [Check if word is valid](https://www.careercup.com/question?id=4774716350922752)

In a language, there are only 4 characters h,i,r,e. and we have to write a function which takes a string as input and returns whether the given input string is a �valid word� or not.

Definition of valid word :

1. A given word is a valid word if it is of the form h^n i^n r^n e^n where n >=1. (eg: hhiirree)
2. Valid words has concatenation property i.e. if w1 and w2 are valid words w1w2 is also a valid word.

Let's make it an "ordered" alphabet array
by letting (compile time fixed or get from somewhere at runtime):
alpha[]={ your ordered list of characters in your alphabet }
M be size of your ordered alphabet (i.e., size of alphabet array above)

**_Idea_**

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

#### [Compress string]()

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

#### [Convert one string to another string only using adjacent swaps]()

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

#### [Convert string a palindrome by adding some characters in front](https://www.careercup.com/question?id=6218545979457536)

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

#### [Count strings of a,b and c with size n made only of 1 b and 2 c](https://www.geeksforgeeks.org/count-strings-can-formed-using-b-c-given-constraints/)

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

**_Print strings_**

```cpp
static int count(int b, int c, int n, string word) {
    if (b < 0 || c < 0) return 0;
    if (n == 0) {
        cout<<word<<endl;
        return 1;
    }

    return count(b, c, n - 1, word + "a") + count(b - 1, c, n - 1, word + "b") + count(b, c - 1, n - 1, word + "c");
}
```

**_O(n) time solution_**

```cpp
int findStrings(int n, int b, int c)
{
    vector<int> A(n + 1, 0);
    A[0] = 1, A[1] = 2, A[2] = 4;

    for (int i = 3; i <= n; i++) {
        A[i] = A[i - 1] + A[i - 2] + A[i - 3];
    }

    return A[n] + n * A[n - 1];
}
```

**_Using DP based solution_**

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

#### [Generate all possible strings are replacing question mark with 0 and 1](https://www.careercup.com/question?id=5192571630387200)

Given a string (for example: "a?bc?def?g"), write a program to generate all the possible strings by replacing ? with 0 and 1.
Example:
Input : a?b?c?
Output: a0b0c0, a0b0c1, a0b1c0, a0b1c1, a1b0c0, a1b0c1, a1b1c0, a1b1c1.

```cpp
vector<string> generateUsingBitSet(string str)
{
    vector<string> result;
    int qMarks = 0;
    for (auto ch : str) {
        if (ch == '?') qMarks++;
    }
    int limit = pow(2, qMarks);

    for (int i = 0; i < limit; i++) {
        string cur;

        // check for set bits in 'i' for each '?'
        int pos = qMarks - 1;
        for (auto ch : str) {
            if (ch == '?') {
                if (i & (1 << pos)) cur += '1';
                else cur += '0';
                pos--;
            }
            else {
                cur += ch;
            }
        }
        result.push_back(cur);
    }

    return result;
}
```

```cpp
vector<string> generateRec(string str)
{
    vector<string> result;

    generateRec(str, 0, "", result);

    return result;
}

void generateRec(string str, int index, string cur, vector<string>& result)
{
    if (index == str.length()) {
        result.push_back(cur);
        return;
    }

    if (str[index] == '?')
    {
        generateRec(str, index + 1, cur + "0", result);
        generateRec(str, index + 1, cur + "1", result);
    }
    else {
        generateRec(str, index + 1, cur + str[index], result);
    }
}
```

---

#### [Generate all substring or generate powerset from given string]()

```sh
Example:
substring for abc: c b bc a ac ab abc
```

**_Using recursion_**

```cpp
void generateAllSubstrings(string prefix, string str)
{
    if (str.empty()) {
        if (!prefix.empty()) cout << prefix << " ";
        return;
    }

    generateAllSubstrings(prefix, str.substr(1));
    generateAllSubstrings(prefix + str[0], str.substr(1));
}
```

**_Using powerset method_**

```cpp
void generateAllSubstrings(string str)
{
    int n = str.length();
    int sz = pow(2, n);

    for (int i = 0; i < sz; i++) {
        string cur;
        for (int j = 0; j < n; j++) {
            if (i & (1 << j)) cur += str[j];
        }
        cout << cur << " ";
    }
}
```

---

#### [Generate all permutatios of given string]()

```sh
Example:
Permutations of abc:
abc acb bac bca cba cab
```

**_Using recursion and backtracking method_**

```cpp
void generateAllPermutations(string str, int index = 0)
{
    if (index == str.length()) {
        cout << str << " ";
        return;
    }

    for (int i = index; i < str.length(); i++) {
        swap(str[i], str[index]);
        generateAllPermutations(str, index + 1);
        swap(str[i], str[index]);
    }
}
```

---

#### [Check if string is periodic, if yes then return the period]()

Find whether string S is periodic.
Periodic indicates S = nP.
e.g.
S = "ababab", then n = 3, and P = "ab"
S = "xxxxxx", then n = 1, and P = "x"
S = "aabbaaabba", then n = 2, and P = "aabba"
follow up:
Given string S, find out the P (repetitive pattern) of S.

```cpp
void test() {
    vector<string> strs = { "waterwater", "ababab", "xxxxxx", "aabbaaabba", "abcd", "dbcasdssddbcasds" };
    GetPeriod obj;

    for (auto str : strs) {
        auto res = obj.isPeriod(str);
        cout << "Is string='" << str << "' periodic: " << res << endl;

        if (res) {
            auto p = obj.getPeriod(str);
            cout << "Period = " << p.first << ", " << p.second << endl;
        }
    }
}

bool isPeriod(string s) {
    string tmp = s + s;
    tmp = tmp.substr(1, tmp.length() - 2);
    return (tmp.find(s) != string::npos);
}

pair<string, int> getPeriod(string s) {
    int n = s.length();
    for (int len = 1; len <= n / 2; len++) {
        if (n % len == 0 && isRepeat(s, len, n)) {
            return { s.substr(0,len), n / len };
        }
    }
    return { "", -1 };
}

bool isRepeat(string s, int periodLen, int totalLen) {
    for (auto i = 0; i < periodLen; i++) {
        for (int j = periodLen + i; j < totalLen; j += periodLen) {
            if (s[i] != s[j]) return false;
        }
    }
    return true;
}
```

---

#### [Print I18N](https://www.careercup.com/question?id=5733696185303040)

i18n (where 18 stands for the number of letters between the first i and the last n in the word “internationalization,”) Wiki it.
Generate all such possible i18n strings for any given string. for eg. "careercup"=>"c7p","ca6p","c6up","car5p","ca5up","care4p","car4up","caree3p","care3up"..
till the count is 0 which means its the complete string again.

```cpp
void print(string str) {
    int n = str.length();
    for(int i=n-2;i>=1;n--) {
        int rem = n-i;
        for(int k=1;k<rem;k++) {
            string first = str.substr(0, k);
            string middle = to_string(i);
            string last = str.substr(n-(rem-k), rem-k);

            cout<<first<<middle<<last<<endl;
        }
    }
}
```

---

#### [Check if string is interleaved]()

```sh
c='axybczd' is interleaved by a='abcd' and b='xyz', res(using DP)? true
c='axybczd' is interleaved by a='abcd' and b='xyz', res(using recursion)? true
```

**_Using recursion_**

```cpp
bool isInterleaved(string a, int l, string b, int m, string c, int n) {
    if(l == 0 && m == 0 && n == 0) return true;
    if(l < 0 || m < 0 || n <= 0) return false;

    return (l > 0 && a[l-1] == c[n-1] && isInterleaved(a, l-1, b, m, c, n-1)) ||
           (m > 0 && b[m-1] == c[n-1] && isInterleaved(a, l, b, m-1, c, n-1));
}
```

**_Using dynamic programming_**

```cpp
bool isInterleaved(string a, string b, string c) {
    int n = a.length();
    int m = b.length();

    vector<vector<int>> table(n+1, vector<int>(m+1, false));

    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if(i == 0 && j == 0) table[i][j] = true;
            else if(i == 0) table[i][j] = (c[i+j-1] == b[j-1] && table[i][j-1]);
            else if(j == 0) table[i][j] = (c[i+j-1] == a[i-1] && table[i-1][j]);
            else if(c[i+j-1] == a[i-1] || c[i+j-1] == b[j-1]) table[i][j] = table[i-1][j] || table[i][j-1];
        }
    }

    return table[n][m];
}
```

---

#### [Is string K-Palindrome](https://www.careercup.com/question?id=6287528252407808)

A k-palindrome is a string which transforms into a palindrome on removing at most k characters.

Given a string S, and an interger K, print "YES" if S is a k-palindrome; otherwise print "NO".
Constraints:
S has at most 20,000 characters.
0<=k<=30

```sh
Sample Test Case#1:
Input - abxa 1
Output - YES
Sample Test Case#2:
Input - abdxa 1
Output - No
```

**_Using Modified LCS_**

```cpp
bool isKPalindromeStringOriginal(string str, int k) {
    int n = str.length();
    vector<vector<int>> table(n + 1, vector<int>(n + 1));
    string revstr = str;
    reverse(revstr.begin(), revstr.end());

    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= n; j++) {
            if (i == 0 && j == 0) table[i][j] = 0;
            else if (i == 0) table[i][j] = j; // insert
            else if (j == 0) table[i][j] = i; // delete
            else {
                if (str[i - 1] == revstr[j - 1]) {
                    // char match
                    table[i][j] = table[i - 1][j - 1];
                }
                else {
                    // count difference, minimum of insert and delete
                    table[i][j] = 1 + min(table[i - 1][j], table[i][j - 1]);
                }
            }
        }
    }

    int minDist = table[n][n];
    cout << format("Minimum distance={} and k={} for string={}", minDist, k, str) << endl;

    return minDist <= 2 * k;
}
```

**_K-Palindrome string optimized_**

```cpp
bool isKPalindromeStringOptimized(string str, int k) {
    int n = str.length();
    vector<vector<int>> table(n + 1, vector<int>(n + 1, INT_MAX));
    string revstr = str;
    reverse(revstr.begin(), revstr.end());

    for (int i = 0; i <= n; i++) {
        int from = max(0, i - k);
        int to = min(n, i + k);
        for (int j = from; j <= to; j++) {
            if (i == 0 && j == 0) table[i][j] = 0;
            else if (i == 0) table[i][j] = j; // insert
            else if (j == 0) table[i][j] = i; // delete
            else {
                if (str[i - 1] == revstr[j - 1]) {// char match
                    table[i][j] = table[i - 1][j - 1];
                }
                else {
                    // count difference, minimum of insert and delete
                    table[i][j] = 1 + min(table[i - 1][j], table[i][j - 1]);
                }
            }
        }
    }

    int minDist = table[n][n];
    cout << format("Minimum distance={} and k={} for string={}", minDist, k, str) << endl;

    return minDist <= 2 * k;
}
```

---

#### [Longest Palindromic Subsequence](https://www.geeksforgeeks.org/longest-palindromic-subsequence-dp-12/)

Given a sequence, find the length of the longest palindromic subsequence in it.

```sh
Longest palindromic subsequence in 'GEEKS FOR GEEKS' is 'EE F EE'
Longest palindromic subsequence in 'a' is 'a'
Longest palindromic subsequence in 'aba' is 'aba'
Longest palindromic subsequence in 'abba' is 'abba'
Longest palindromic subsequence in 'abcba' is 'abcba'
Longest palindromic subsequence in 'abcdasdafxcfdsg' is 'sdfxfds'
Longest palindromic subsequence in 'iafn asdaad sdf' is 'f daad f'
```

**_Using Recursion_**

```cpp
int longestPalindromicSubsequence(string& str, int l, int r) {
    if(l > r) return 0;
    if(l == r) return 1;

    if(str[l] == str[r]) return 2 + longestPalindromicSubsequence(str, l+1, r-1);
    return max(longestPalindromicSubsequence(str, l+1,r), longestPalindromicSubsequence(str, l, r-1));
}
```

```cpp
string longestPalindromicSubsequence(string& str, int l, int r) {
    if(l > r) return "";
    if(l == r) return string(1, str[l]);

    if(str[l] == str[r]) return str[l] + longestPalindromicSubsequence(str, l+1, r-1) + str[r];

    auto ls = longestPalindromicSubsequence(str, l+1, r);
    auto rs = longestPalindromicSubsequence(str, l, r-1);

    return ls.length() > rs.length() ? ls : rs;
}
```

**_Using Dynamic Programming_**

```cpp
int longestPalindromicSubsequence(string& str) {
    int n = str.length();
    vector<vector<int>> table(n, vector<int>(n, 0));
    for (int i = 0; i < n; i++) table[i][i] = 1;

    for (int p = 2; p <= n; p++) {
        for (int i = 0; i <= n - p; i++) {
            int j = i + p - 1;
            if (i == j) table[i][j] = 1;
            else if (str[i] == str[j]) {
                if (i + 1 == j) table[i][j] = 2;
                else table[i][j] = 2 + table[i + 1][j - 1];
            }
            else {
                table[i][j] = max(table[i + 1][j], table[i][j - 1]);
            }
        }
    }

    return table[0][n - 1];
}
```

```cpp
string lpsStrDP(string str) {
    int n = str.length();
    vector<vector<int>> memo(n, vector<int>(n, 0));
    for (int i = 0; i < n; i++) memo[i][i] = 1;

    for (int p = 2; p <= n; p++) {
        for (int i = 0; i < n - p + 1; i++) {
            int j = i + p - 1;
            if (str[i] == str[j]) {
                if (p == 2) memo[i][j] = 2;
                else memo[i][j] = 2 + memo[i + 1][j - 1];
            }
            else {
                memo[i][j] = max(memo[i + 1][j], memo[i][j - 1]);
            }
        }
    }

    string left, right;
    int l = 0, r = n - 1;
    while (l <= r) {
        if (l == r) left += str[l++];
        else if (str[l] == str[r]) {
            left += str[l++]; right = str[r--] + right;
        }
        else if (memo[l + 1][r] > memo[l][r - 1]) l++;
        else r--;
    }

    return left + right;
}
```

---

#### [Longest Palindromic substring](https://leetcode.com/problems/longest-palindromic-substring/)

Given a string s, return the longest palindromic substring in s.

```sh
Longest palindromic substring for str='forgeeksskeegfor' is 'geeksskeeg'
Longest palindromic substring for str='abcsdc' is 'a'
Longest palindromic substring for str='sdfacxcasvsa' is 'acxca'
```

**_Using expand around center_**

```cpp
string longestPalSubstrExpandAroundCenter(string str) {
    string res;
    int n = str.length();
    for (int i = 0; i < n; i++) {
        auto cur = expand(str, i, i, n);
        if (cur.length() > res.length()) res = cur;
    }

    for (int i = 0; i < n - 1; i++) {
        auto cur = expand(str, i, i + 1, n);
        if (cur.length() > res.length()) res = cur;
    }

    return res;
}

string expand(string& str, int l, int r, int n) {
    while (l >= 0 && r < n && str[l] == str[r]) {
        l--; r++;
    }
    return str.substr(l + 1, r - l - 1);
}
```

**_Using dynamic programming_**

```cpp
string longestPalSubstrDP(string str) {
    string longest = "";
    int n = str.length();
    vector<vector<bool>> table(n, vector<bool>(n, false));

    for (int i = 0; i < n; i++) {
        if (longest.length() < 1) longest = string(1, str[i]);
        table[i][i] = true;
    }
    for (int i = 0; i < n - 1; i++) {
        if (str[i] == str[i + 1]) {
            if (longest.length() < 2) longest = str[i] + str[i + 1];
            table[i][i + 1] = true;
        }
    }

    for (int p = 3; p <= n; p++) {
        for (int i = 0; i < n - p + 1; i++) {
            int j = i + p - 1;
            if (str[i] == str[j] && table[i + 1][j - 1]) {
                if (j - i + 1 > longest.length()) {
                    longest = str.substr(i, j - i + 1);
                }
                table[i][j] = true;
            }
        }
    }

    return longest;
}
```

---

#### [Longest repeated substring](https://www.careercup.com/question?id=5931067269709824)

```cpp
string longestRepeatedSubstring(string str) {
    if (!isPeriod(str)) return "";

    return getPeriod(str);
}

bool isPeriod(string s)
{
    string rep = s + s;
    string part = s.substr(1, s.length() - 2);

    return rep.find(part) != string::npos;
}

string getPeriod(string s)
{
    int l = s.length();
    for (int i = l / 2; i > 0; i--)
    {
        if (l % i == 0 && isRepeat(s, i, l)) return s.substr(0, i);
    }

    return "";
}

bool isRepeat(string s, int patLen, int totalLen)
{
    string period = s.substr(0, patLen);
    int j = patLen;

    while (j + patLen <= totalLen)
    {
        if (period != s.substr(j, patLen)) return false;
        j += patLen;
    }

    return true;
}
```

---

#### [Longest repeated subsequence](https://www.careercup.com/question?id=5931067269709824)

Given a string (1-d array) , find if there is any sub-sequence that repeats itself.
Here, sub-sequence can be a non-contiguous pattern, with the same relative order.

Eg:

1. abab <------yes, ab is repeated
2. abba <---- No, a and b follow different order
3. acbdaghfb <-------- yes there is a followed by b at two places
4. abcdacb <----- yes a followed by b twice

**_Using recursion_**

```cpp
string getLongestRepeatedSubseqRec(string s)
{
    string res;

    auto len = longestRepeatedSubseqRec(s, s, 0, 0, "", res);

    return res;
}

int longestRepeatedSubseqRec(string s1, string s2, int i, int j, string cur, string& res)
{
    if(i >= s1.length() || j >= s2.length()) {
        if(cur.length() > res.length()) res = cur;
        return res.length();
    }

    if(i != j && s1[i] == s2[j]) return 1 + longestRepeatedSubseqRec(s1, s2, i+1, j+1, cur+s1[i], res);
    else return max(longestRepeatedSubseqRec(s1, s2, i+1, j, cur, res), longestRepeatedSubseqRec(s1, s2, i, j+1, cur, res));
}
```

**_Using Dynamic programming_**

```cpp
pair<bool, int> longestReaptedSubsequenceDP(string str) {
    auto len = longestReaptedSubsequenceDP(str, str);

    return {len>1, len}
}

int longestReaptedSubsequenceDP(string s1, string s2) {
    int n = s1.length();
    int m = s2.length();
    vector<vector<int>> memo(n+1, vector<int>(m+1));

    for(int i=0;i<=n;i++) {
        for(int j=0;j<=m;j++) {
            if(i == 0 || j == 0) memo[i][j] = 0;
            else if(i != j && s1[i] == s2[j]) memo[i][j] = memo[i-1][j-1] + 1;
            else memo[i][j] = max(memo[i-1][j], memo[i],[j-1]);
        }
    }

    return memo[n][m];
}
```

---

#### [Longest word made of others](https://stackoverflow.com/questions/32984927/find-the-longest-word-made-of-other-words)

```sh
EXAMPLE
Input: test, tester, testertest, testing, testingtester
Output: testingtester
```

**_Using simple recursion_**

```cpp
string longestWordMadeFromOthersWithoutMemoization(vector<string> words) {
    unordered_set<string> dict;
    for (auto word : words) dict.insert(word);

    sort(words.begin(), words.end(), [](const auto& f, const auto& s) {return f.size() > s.size(); });

    for (auto word : words) {
        dict.erase(word);
        if (canBuildFromOtherWordsWithoutMemoization(word, dict)) return word;
        dict.insert(word);
    }

    return "";
}

bool canBuildFromOtherWordsWithoutMemoization(string word, unordered_set<string> dict) {
    if (dict.find(word) != dict.end()) return true;
    for (int i = 1; i <= word.length(); i++) {
        string left = word.substr(0, i);
        if (dict.find(left) != dict.end() && canBuildFromOtherWordsWithoutMemoization(word.substr(i), dict)) {
            return true;
        }
    }
    return false;
}
```

**_Using recursion with memoization_**

```cpp
string longestWordMadeFromOthersWithMemoization(vector<string> words) {
    unordered_set<string> dict;
    for (auto word : words) dict.insert(word);

    sort(words.begin(), words.end(), [](const auto& f, const auto& s) {return f.size() > s.size(); });

    for (auto word : words) {
        dict.erase(word);
        if (canBuildFromOtherWordsWithMemoization(word, dict)) return word;
        dict.insert(word);
    }

    return "";
}

bool canBuildFromOtherWordsWithMemoization(string word, unordered_set<string>& dict) {
    if (dict.find(word) != dict.end()) return true;

    for (int i = 1; i <= word.length(); i++) {
        string left = word.substr(0, i);
        if (dict.find(left) != dict.end() && canBuildFromOtherWordsWithMemoization(word.substr(i), dict)) {
            dict.insert(word.substr(i)); // memoize the current result (we can reach this combined string)
            return true;
        }
    }

    return false;
}
```

**_Using trie_**

```cpp
string longestWordMadeFromOthersWithTrie(vector<string> words) {
    Trie trie;

    for (auto word : words) {
        trie.insert(word);
    }

    sort(words.begin(), words.end(), [](auto f, auto s) {return f.length() > s.length(); });

    for (auto word : words) {
        trie.remove(word);

        if (exists(trie, word)) return word;

        trie.insert(word);
    }

    return "";
}

bool exists(Trie& trie, string word) {
    if (word.empty()) return true;
    for (int i = 1; i <= word.length(); i++) {
        auto left = word.substr(0, i);
        auto right = word.substr(i);

        if (trie.contains(left) && exists(trie, right)) return true;
    }

    return false;
}
```

---

#### [Put Commas In String]()

```cpp
static string putCommas(string num) {
    if (num.size() < 3) return num;
    auto cur = putCommas(num.substr(0, num.size() - 3)); // select first (0, n-3) characters from string
    return cur + "," + num.substr(num.size() - 3); // append the formatted output + "," + last 3 characters
}
```

```cpp
static string putCommas(int num) {
    if (num == 0) return "";
    if (num < 1000) return to_string(num);
    auto cur = putCommas(num / 1000);
    string formatted(5, '\0');
    sprintf_s(const_cast<char*>(formatted.c_str()), 5, ",%03d", num % 1000);
    return cur + formatted;
}
```

---

#### [C++ String splitter]()

**_Split by space_**

```cpp
{
    string str = "hello how are you";
    stringstream ss(str);
    string word;
    while (ss >> word) { cout << word << endl; }
}
```

**_Split by character_**

```cpp
{
    string str = "hello, how, are, you";
    stringstream ss(str);
    string word;
    char delim = ',';

    while (getline(ss, word, delim)) {
        cout << word << " ";
    }
    cout << endl;
}
```

**_Split by any string_**

```cpp
static vector<string> split(string strs, vector<string> delims) {
    vector<string> result;
    result.push_back(strs);

    for (auto& delim : delims) {
        vector<string> newResult;
        for (auto& cur : result) {
            auto parts = splitStr(cur, delim);
            newResult.insert(newResult.end(), parts.begin(), parts.end());
        }
        result = newResult;
    }
    return result;
}

static vector<string> splitStr(string str, string delim) {
    auto start = 0;
    auto pos = string::npos;
    auto len = str.length();

    vector<string> result;
    do {
        pos = str.find(delim, start);
        auto end = (pos == string::npos) ? len : pos;
        if (end > start) result.push_back(str.substr(start, end - start));
        start = pos + 1;
    } while (pos != string::npos);

    return result;
}
```

---

#### [Rearrange characters such that no characters repeats twice](https://www.careercup.com/question?id=5693863291256832)

Rearrange characters in a string so that no character repeats twice.

```sh
Input: aaabc
Output: abaca

Input: aa
Output: No valid output

Input: aaaabc
Output: No valid output
```

```cpp
static string rearrangeCharacters(string& str) {
    int n = str.length();
    unordered_map<char, int> freq;
    for (auto ch : str) freq[ch]++;

    priority_queue<pair<int, char>> pq;
    for (auto& e : freq) {
        if (e.second > (n + 1) / 2) return "Invalid Input";

        pq.push({ e.second, e.first });
    }

    string result;
    while (!pq.empty()) {
        auto first = pq.top(); pq.pop();
        result += first.second; first.first--;

        while (!pq.empty()) {
            auto second = pq.top(); pq.pop();
            result += second.second; second.first--;

            if (second.first > 0) pq.push(second);
        }

        if (first.first > 0) pq.push(first);
    }

    return result;
}
```

---

#### [Remove B and AC]()

```cpp
string removeBAndAC(string str) {
    int i = 0;
    for(int j=0;j<str.length();j++) {
        if(str[j] == 'b') continue;

        if(i>0 && str[i-1] == 'a' && str[j] == 'c') i--;
        else str[i++] = str[j];
    }

    str.erase(str.begin()+i, str.end());
    return str;
}
```

```cpp
string removeBAndAC(string str) {
    stack<char> st;
    for(auto ch : str) {
        if(ch == 'b') continue;

        if(ch == 'c' && !st.empty() && st.top() == 'a') st.pop();
        else st.push(ch);
    }

    string res;
    while(!st.empty()) { res += st.top(); st.pop(); }
    reverse(res.begin(), res.end());

    return res;
}
```

---

#### [Shortest subsequence](https://www.geeksforgeeks.org/shortest-common-supersequence/)

Given two strings str1 and str2, the task is to find the length of the shortest string that has both str1 and str2 as subsequences.

Examples :
Input: str1 = "geek", str2 = "eke"
Output: 5
Explanation:
String "geeke" has both string "geek"
and "eke" as subsequences.

Input: str1 = "AGGTAB", str2 = "GXTXAYB"
Output: 9
Explanation:
String "AGXGTXAYB" has both string
"AGGTAB" and "GXTXAYB" as subsequences.

**_Using LCS method. SuperSequence = strLen1 + strLen2 - lcsLen_**

```cpp
int shotestCommonSubsequence(string& str1, string& str2) {
    int l = lcs(str1, str2);

    return str1.size() + str2.size() - l;
}

int lcs(string& s1, string& s2) {
    int l1 = lcsUsingRec(s1, s1.length(), s2, s2.length());
    int l2 = lcsUsingDP(s1, s2);
    assert(l1 == l2);

    return l1;
}

int lcsUsingRec(string& s1, int l1, string& s2, int l2) {
    if (l1 == 0 || l2 == 0) return 0;
    if (s1[l1 - 1] == s2[l2 - 1]) return 1 + lcsUsingRec(s1, l1 - 1, s2, l2 - 1);

    return max(lcsUsingRec(s1, l1 - 1, s2, l2), lcsUsingRec(s1, l1, s2, l2 - 1));
}

int lcsUsingDP(string& s1, string& s2) {
    int l1 = s1.length();
    int l2 = s2.length();

    vector<vector<int>> memo(l1 + 1, vector<int>(l2 + 1));

    for (int i = 0; i <= l1; i++) {
        for (int j = 0; j <= l2; j++) {
            if (i == 0 || j == 0) memo[i][j] = 0;
            else if (s1[i - 1] == s2[j - 1]) memo[i][j] = 1 + memo[i - 1][j - 1];
            else memo[i][j] = max(memo[i - 1][j], memo[i][j - 1]);
        }
    }

    return memo[l1][l2];
}
```

**_Using Modified version of LCS to count supersequence in it_**

```cpp
int shotestCommonSubsequence(string& s1, string& s2) {
    int l1 = shotestCommonSubsequenceRec(s1, s1.length(), s2, s2.length());
    int l2 = shotestCommonSubsequenceDP(s1, s2);

    assert(l1 == l2);

    return l1;
}

int shotestCommonSubsequenceRec(string& s1, int l1, string& s2, int l2) {
    if (l1 == 0 || l2 == 0) return (l1 == 0) ? l2 : l1;

    if (s1[l1 - 1] == s2[l2 - 1]) return 1 + shotestCommonSubsequenceRec(s1, l1 - 1, s2, l2 - 1);
    return 1 + min(shotestCommonSubsequenceRec(s1, l1 - 1, s2, l2), shotestCommonSubsequenceRec(s1, l1, s2, l2 - 1));
}

int shotestCommonSubsequenceDP(string& s1, string& s2) {
    int l1 = s1.length(), l2 = s2.length();
    vector<vector<int>> memo(l1 + 1, vector<int>(l2 + 1));

    for (int i = 0; i <= l1; i++) {
        for (int j = 0; j <= l2; j++) {
            if (i == 0) memo[i][j] = j;
            else if (j == 0) memo[i][j] = i;
            else if (s1[i - 1] == s2[j - 1]) memo[i][j] = 1 + memo[i - 1][j - 1];
            else memo[i][j] = 1 + min(memo[i - 1][j], memo[i][j - 1]);
        }
    }

    return memo[l1][l2];
}
```

---
