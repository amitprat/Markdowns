#### [Generate combinations of numbers upto N]()

```sh
Input: 4
Output: [1, 1, 1, 1] , [1, 1, 2] , [1, 3] , [2, 2] , [4]
```

```cpp
void generate(int n) {
    vector<int> result;
    generate(1, n, result);
}
```

```cpp
void generate(int s, int n, vector<int>& result) {
    if(n == 0) {
        cout<<to_string(result)<<endl;
        return;
    }
    if(s > n || n < 0) return;

    result.push_back(s);
    generate(s, n-s, result);

    result.pop_back();
    generate(s, n, result);
}
```

```cpp
void generate(int s, int n, vector<int>& result) {
    if (n < 0) return;
    if(n == 0) {
        cout<<to_string(result)<<endl;
        return;
    }

    for(int num=s;num<=n;num++) {
        result.push_back(num);
        generate(num, n-num, result);
        result.pop_back();
    }
}
```

```cpp
//generate(n, n, result);
void generate(int n, int upper, vector<int>& result) {
    if (n < 0) return;
    if(n == 0) {
        cout<<to_string(result)<<endl;
        return;
    }

    for(int num=1;num<=upper;num++) {
        result.push_back(num);
        generate(n-num, num, result);
        result.pop_back();
    }
}
```

---
