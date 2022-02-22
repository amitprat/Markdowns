#### [Stack using two queues ](https://practice.geeksforgeeks.org/problems/stack-using-two-queues/1)

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

#### [Queue using two Stacks ](https://practice.geeksforgeeks.org/problems/queue-using-two-stacks/1)

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

#### [Infix to postfix]()

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

#### [Evaluate a simple expression only containing multiplication and addition(sum)]()

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

#### [Simplify a Unix Path]()

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

#### [Identity redundant parenthesis](https://www.careercup.com/question?id=6276805505843200)

Find if the given expression contains redundant parantheses. ex :if expr = a+(b*c) , print false, if expr = a+((b*c)), print true.

We maintain a stack. We traverse the expression, and we push either a bracket, or a value. If the top of stack contains a value, we don't push another value,
just leave it as is. If we encounter a right bracket, we expect a value on top to pop. We then pop the value and pop a bracket. If we don't get a value on top
when we encounter a right bracket, then we have redundant brackets.

```cpp
bool isValid(string str) {
    stack<char> st;

    for(auto ch : str) {
        if(ch == '(') {
            st.push(ch);
        } else if(ch == ')') {
            if(st.empty() || st.top() == '(') return false;
            st.top();

            if(st.empty() || st.top() != '(') return false;
            st.top();
        } else {
            if(st.empty() || st.top() == '(') st.push(ch);
        }
    }

    return true;
}
```

---

#### [Simplify Linux Path]()

```cpp
class SimplifyPath {
public:
    static void test() {
        SimplifyPath obj;

        vector<pair<string, string>> tests = {
            {"/home/","/home"},
            {"/a/./b/../../c/","/c"},
            {"/../","/"},
            {"/home//foo/","/home/foo"}
        };

        for (auto& test : tests) {
            auto res = obj.simplifyPath(test.first);
            cout << format("Original path='{}', path after simplifying it='{}'", test.first, res) << endl;
            assert(test.second == res);
        }
    }

    string simplifyPath(string& path) {
        removeTrailingSpacesAndSlashes(path);

        stack<string> st;
        for (int start = 0, end = 0; end <= path.length(); end++) {
            string cur;
            if (end == path.length()) {
                cur = path.substr(start);
            }
            else if (path[end] == '/') {
                cur = path.substr(start, end - start);
                start = end + 1;
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
        result = "/" + result;

        return result;
    }

    void removeTrailingSpacesAndSlashes(string& str) {
        int i = str.length() - 1;
        while (str[i] == '/' || str[i] == ' ') i--;
        str = str.substr(0, i + 1);
    }
};
```

---

#### [Implement K stacks in a single array]()

**_Using fixed size for each stack_**

```cpp
FixedSizeKStack(int k, int totalCap) {
    this->k = k;
    this->totalCap = totalCap;
    this->arr = new int[totalCap];
    this->top = new int[k];
    this->mxLimitPerStack = totalCap / k; // this could cause some unused space if total size isn't multiple of k

    for (int i = 0; i < k; i++) {
        top[i] = (i * mxLimitPerStack - 1); // initialize each stack top as their start pos - 1 (empty marker)
    }
}

void push(int stNum, int elem) {
    if (isFull(stNum)) throw exception(format("Stack {} is full, not inserting elem {}", stNum, elem).c_str());

    top[stNum]++; // increase start position and insert element at that pos
    arr[top[stNum]] = elem;
}

int pop(int stNum) {
    if (isEmpty(stNum)) throw exception(format("Stack {} is empty, not popping elem", stNum).c_str());

    // decrease the start position and return element at current index
    int index = top[stNum]--;
    return arr[index];
}

bool isFull(int stNum) {
    int mxEndIndex = min((stNum + 1) * mxLimitPerStack, totalCap);
    if (top[stNum] + 1 >= mxEndIndex) return true;

    return false;
}

bool isEmpty(int stNum) {
    return top[stNum] == stNum * mxLimitPerStack - 1;
}
```

**_Using Dynamic Stack size_**

```cpp

// Initialization
struct Node {
    int val;
    int prevIndex;
};

class KStack {
    Node** nodes = nullptr;
    vector<int> indices;
    int curIndex;
    int cap;
};

KStack(int k, int cap) {
    this->indices = vector<int>(k, -1);
    this->nodes = new Node * [cap];
    this->cap = cap;
    this->curIndex = 0;
}

void push(int stNum, int val) {
    if (isFull()) throw exception("Stack is full");

    nodes[curIndex] = new Node{ val, indices[stNum] }; // insert previous pos for this stack and current val
    indices[stNum] = curIndex; // set the top of the stack

    curIndex++; // increase the overall index
}

int pop(int stNum) {
    if (isEmpty(stNum)) throw exception("Empty stack");

    int removedElemIndex = indices[stNum];
    Node* res = nodes[removedElemIndex]; // get the element at the top
    indices[stNum] = res->prevIndex; // store the previous top

    // clear up the last position
    if (removedElemIndex != curIndex) {
        swap(nodes[removedElemIndex], nodes[curIndex]);

        // we don't store what is the stNum of element stored at last overall index(curIndex)
        // so, we have to iterate through entire indices to find the stack num
        for (int i = 0; i < indices.size(); i++) {
            if (indices[i] == curIndex) {
                indices[i] = removedElemIndex; // update this index
                break;
            }
        }

        curIndex--;
    }

    return res->val;
}

bool isEmpty() {
    return curIndex == 0;
}

bool isFull() {
    return curIndex == cap;
}

bool isEmpty(int stNum) {
    return isEmpty() || indices[stNum] == -1;
}
```

---

#### [Stack with access to middle element](https://www.geeksforgeeks.org/design-a-stack-with-find-middle-operation/)

```cpp
template <class T>
class SpecialStackWithAccessToMiddleElementWithSTLStack {
    list<T>elements;
    list<T>::iterator middle = elements.end();

public:
    void push(int elem) {
        elements.push_back(elem);
        if (elements.size() == 1) middle = elements.begin();
        else if (elements.size() % 2 == 1) middle++;
    }

    T pop() {
        if (elements.empty()) throw exception("stack is empty");

        auto last = elements.back();
        elements.pop_back();

        if (elements.empty()) middle = elements.end();
        else if (elements.size() % 2 == 0) middle--;

        return last;
    }

    T top() {
        if (elements.empty()) throw exception("stack is empty");

        return elements.back();
    }

    T getMid() {
        if (middle == elements.end()) throw exception("No middle element");

        return *middle;
    }

    bool empty() {
        return elements.empty();
    }

    int size() {
        return elements.size();
    }

    void deleteMid() {
        if (middle == elements.end()) throw exception("No middle element");
        auto middlePrev = middle; middlePrev--;
        elements.erase(middle); // middle becomes invalid after erase

        middle = middlePrev; // update middle with middle prev
        if (elements.empty()) middle = elements.end();
        if (elements.size() % 2 == 1) middle++;
    }
};
```

```cpp
template <class T>
class SpecialStackWithAccessToMiddleElementWithCustomLinkedList
{
    class LinkedList {
        LinkedListNode<T>* head = nullptr;
        LinkedListNode<T>* middle = nullptr;

    public:
        int size = 0;

        void push_back(T elem) {
            if (head == nullptr) head = middle = new LinkedListNode<T>(elem);
            else {
                head->next = new LinkedListNode<T>(elem);
                head->next->prev = head;
                head = head->next;

                if (size % 2 == 0) middle = middle->next;
            }
            size++;
        }

        T back() {
            if (head == nullptr) throw exception("List is empty");
            return head->val;
        }

        T pop_back() {
            if (head == nullptr) throw exception("List is empty");
            auto last = head->val;
            head = head->prev;

            size--;

            if (head == nullptr) middle = nullptr;
            else if (size % 2 == 0) middle = middle->prev;

            return last;
        }

        T getMid() {
            if (middle == nullptr) throw exception("Invalid middle pointer");

            return middle->val;
        }

        void deleteMid() {
            if (middle == nullptr) throw exception("Invalid middle pointer");

            auto oldMiddle = middle;
            if (middle->prev) {
                middle->prev->next = middle->next;
                middle->next->prev = middle->prev;
                middle = middle->prev;
            }
            else middle = middle->next;
            delete oldMiddle;

            size--;

            if (middle && (size % 2 == 1)) middle = middle->next;
        }

        bool empty() {
            return size == 0;
        }
    };

    LinkedList elements;
public:
    void push(T elem) {
        elements.push_back(elem);
    }

    T top() {
        return elements.back();
    }

    T pop() {
        return elements.pop_back();
    }

    T getMid() {
        return elements.getMid();;
    }

    void deleteMid() {
        elements.deleteMid();
    }

    bool empty() {
        return elements.empty();
    }

    int size() {
        return elements.size;
    }
};
```

---

#### [Evaluate Infix Expression]()

```cpp
class Calculator {
public:
    int calculate(string infix) {
        string token;
        int start = 0;
        stack<int> val_stack;
        stack<string> ops_stack;

        while (!(token = next(infix, start)).empty()) {
            if (isOperator(token[0])) {
                evaluate(token, ops_stack, val_stack);
            }
            else {
                val_stack.push(stoi(token));
            }
        }

        while (!ops_stack.empty()) {
            val_stack.push(evaluate(ops_stack, val_stack));
        }

        return val_stack.top();
    }

    string next(string infix, int& index) {
        while (index < infix.length() && infix[index] == ' ') index++;
        if (index >= infix.length()) return "";

        string next;
        if (isOperator(infix[index])) {
            next.push_back(infix[index++]);
        }
        else {
            while (index < infix.length() && !isOperator(infix[index])) {
                next.push_back(infix[index++]);
            }
        }
        return next;
    }

    bool isOperator(char cur) {
        return cur == '+' || cur == '-' || cur == '*' || cur == '/' || cur == '(' || cur == ')';
    }

    bool isNum(char ch) {
        return ch >= '0' && ch <= '9';
    }

    int prec(string cur) {
        if (cur == "+" || cur == "-") return 1;
        if (cur == "*" || cur == "/") return 2;
        return 0;
    }
    void evaluate(string op, stack<string>& ops_stack, stack<int>& val_stack)
    {
        if (op == "(") ops_stack.push(op);
        else if (op == ")") {
            while (!ops_stack.empty() && ops_stack.top() != "(") {
                val_stack.push(evaluate(ops_stack, val_stack));
            }
            ops_stack.pop();
        }
        else {
            while (!ops_stack.empty() && prec(ops_stack.top()) >= prec(op)) {
                val_stack.push(evaluate(ops_stack, val_stack));
            }
            ops_stack.push(op);
        }
    }

    int evaluate(stack<string>& ops_stack, stack<int>& val_stack) {
        auto curToken = ops_stack.top();
        ops_stack.pop();

        int second = val_stack.top(); val_stack.pop();
        int first = val_stack.top(); val_stack.pop();

        switch (curToken[0]) {
        case '+':
            return first + second;
            break;
        case '-':
            return first - second;
            break;
        case '*':
            return first * second;
            break;
        case '/':
            return first / second;
            break;
        }
    }
};
```

```
class Calculator {
public:
    int evaluateInfixExpression(string& infix) {
        stack<int> valStack;
        stack<char> operStack;

        string curStr;
        for (char ch : infix) {
            if (ch == ' ') continue;

            if (isOperator(ch)) {
                if (!curStr.empty()) {
                    valStack.push(stoi(curStr));
                    curStr.clear();
                }

                processOperator(operStack, valStack, ch);
            }
            else {
                curStr += ch;
            }
        }
        if (!curStr.empty()) valStack.push(stoi(curStr));

        while (!operStack.empty()) {
            valStack.push(evaluateTop(operStack, valStack));
        }

        assert(valStack.size() == 1);

        return valStack.top();
    }

private:
    void processOperator(stack<char>& operStack, stack<int>& valStack, char ch) {
        if (ch == '(') operStack.push(ch);
        else if (ch == ')') {
            while (!operStack.empty() && operStack.top() != '(') {
                valStack.push(evaluateTop(operStack, valStack));
            }
            if (operStack.empty() || operStack.top() != '(') throw exception("Invalid expression");
            operStack.pop();
        }
        else {
            while (!operStack.empty() && prec(operStack.top()) >= prec(ch)) {
                valStack.push(evaluateTop(operStack, valStack));
            }
            operStack.push(ch);
        }
    }

    int prec(char ch) {
        switch (ch) {
        case '*':
        case '/':
            return 4;
        case '+':
        case '-':
            return 3;
        }

        return -1;
    }

    int evaluateTop(stack<char>& operStack, stack<int>& valStack) {
        char curOp = operStack.top(); operStack.pop();
        auto second = valStack.top(); valStack.pop();
        auto first = valStack.top(); valStack.pop();

        switch (curOp) {
        case '*':
            return first * second;
            break;
        case '/':
            return first / second;
            break;
        case '+':
            return first + second;
            break;
        case '-':
            return first - second;
            break;
        }
    }

    bool isOperator(char cur) {
        return cur == '+' || cur == '-' || cur == '*' || cur == '/' || cur == '(' || cur == ')';
    }
};
```

---
