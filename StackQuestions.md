####[Stack using two queues ](https://practice.geeksforgeeks.org/problems/stack-using-two-queues/1)

Implement a Stack using two queues q1 and q2.

```cpp
class StackUsingQueues {
    queue<int> q1;
    queue<int> q2;

public:
    void push(int val) {
        q1.push(val); //[1,2,3] -> [1,2,3]
    }

    int top() {
        if(!q2.empty()) return q2.front();

        if(q1.empty()) return -1;
        while(q1.size() != 1) {
            q2.push(q1.front()); q1.pop();
        }

        swap(q1, q2);

        return q2.front();
    }

    int pop() {
        int val;
        if(!q2.empty()) {val = q2.top(); q2.pop();}
        if(q1.empty()) val = -1;
        else {
            while(q1.size() != 1) {
                q2.push(q1.front()); q1.pop();
            }
            val = q1.front(); q1.pop();
            swap(q1, q2);
        }

        return val;
    }
}
```

---

####[Queue using two Stacks ](https://practice.geeksforgeeks.org/problems/queue-using-two-stacks/1)

Implement a Queue using 2 stacks s1 and s2 .
A Query Q is of 2 Types
(i) 1 x (a query of this type means pushing 'x' into the queue)
(ii) 2 (a query of this type means to pop element from queue and print the poped element)

```cpp
class QueueUsing2Stack {
    stack<int> st1, st2;

public:
    void push(int val) {
        st1.push(val);
    }

    int front() {
        if(!st2.empty()) return st2.top();

        if(st1.empty()) return -1;

        while(!st1.empty()) {
            st2.push(st1.top()); st1.pop();
        }

        return st2.top();
    }

    int pop() {
        int val;
        if(!st2.empty()) { val = st2.top(); st2.pop(); return val; }

        if(st1.empty()) val = -1;
        else {
            while(!st1.empty()) {
                st2.push(st1.top());
                st1.pop();
            }
            val = st2.top(); st2.pop();
        }

        return val;
    }
}
```

---

####[Infix to postfix]()

```sh
a+b*(c+d)+e*f

res: abcd
operstack: +*(+

res: abcd+
operstack: +*

res: abcd+*+ef
operstack: +*

res: abcd+*+ef*+
operstack:
```

```cpp
//precedence : higher value represents higher precedence
string convertInfixToPostfix(string infix) {
    for(auto ch : infix) {
        if(isOperator(ch)) { // operators (+, -, *, ...)
            if(ch == '(') st.push(ch); // always push the start bracket
            if(ch == ')') { // pop till we see start bracket
                while(!operStack.empty() && operStack.top() != '(') {
                    postfix += operStack.top(); operStack.pop();
                }
                if(operStack.empty() || operStack.top() != '(') throw exception("Invalid");
                operStack.pop();
            } else { // remove operators with higher precedence so far
                while(!operStack.empty() && precedence(operStack.top()) >= precedence(ch)) {
                    postfix += operStack.top(); operStack.pop();
                }
                st.push(ch);
            }
        } else { // operand ( numbers from 0 to 9)
            postfix += ch;
        }
    }

    // pop remaining operators
    while(!operStack.empty()) {
        postfix += operStack.top(); operStack.pop();
    }

    return postfix;
}
```
####[Evaluate a simple expression only containing multiplication and addition(sum)]()

```cpp
int evaluate(string expr)
{
    vector<string> additives = tokenize(expr, '+');
    int res = 0;
    for (auto i : additives)
    {
        vector<string> multiplicatives = tokenize(i, '*');
        int mul = 1;
        for (auto j : multiplicatives)
        {
            mul *= stoi(j);
        }
        res += mul;
    }

    return res;
}

vector<string> tokenize(string str, char ch)
{
    vector<string> res;

    int start = 0;
    size_t pos = str.find(ch, start);
    while (pos != string::npos)
    {
        res.push_back(str.substr(start, pos - start));
        start = pos + 1;
        pos = str.find(ch, start);
    }
    if (start != str.length())
        res.push_back(str.substr(start));

    return res;
}
```
---
####[Simplify a Unix Path]()

```sh
{"/home/","/home"},
{"/a/./b/../../c/","/c"},
{"/../","/"},
{"/home//foo/","/home/foo"}
```

```cpp
string simplifyPath(string& path) {
    stack<string> st;
    removeTrailingSpacesAndSlashes(path);

    int start = 0;
    for (int i = 0; i <= path.length(); i++) {
        string cur;
        if (i == path.length()) {
            cur = path.substr(start);
        }
        else if (path[i] == '/') {
            cur = path.substr(start, i - start);
            start = i + 1;
        }

        if (cur.empty()) continue;
        if (cur == ".") continue;
        if (cur == "..") { if (!st.empty()) st.pop(); }
        else st.push(cur);
    }

    string result;
    while (!st.empty()) {
        auto cur = st.top(); st.pop();
        if (result.empty()) result = cur;
        else result = cur + "/" + result;
    }
    result = "/" + result; // add slash in beginning

    return result;
}

void removeTrailingSpacesAndSlashes(string& str) {
    int i = str.length() - 1;
    while (str[i] == '/' || str[i] == ' ') i--;
    str = str.substr(0, i + 1);
}
```
---
