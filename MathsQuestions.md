#### [Find Prime numbers in a range](https://practice.geeksforgeeks.org/problems/find-prime-numbers-in-a-range4718/1)

Given two integers M and N, generate all primes between M and N.

```cpp
vector<int> primeRange(int M, int N) {
    if(N < 2) return {};

    vector<bool> seive(N+1, true);
    seive[0] = seive[1] = false;

    vector<int> result;
    for(int i=2;i<=N;i++) {
        if(!seive[i]) continue;

        for(int j=2;j*i<=N;j++) {
            seive[i*j] = false;
        }
        if(i >= M && i <= N) result.push_back(i);
    }

    return result;
}
```

```cpp
vector<int> primeRange(int M, int N) {
    vector<int> result;
    for(int i=M;i<=N;i++) {
        if(isPrime(i)) result.push_back(i);
    }

    return result;
}

bool isPrime(int num) {
    if(num < 2) return false;
    for(int i=2;i*i<=num;i++) {
        if(num%i == 0) return false;
    }
    return true;
}
```

---

#### [Check if a number is perfect square]()

```cpp
int checkIfPerfectSquare(int n)
{
    int i = 1;
    while (n > 0)
    {
        n -= i;
        printf("[%d]", n);
        i += 2;
    }
    if (n == 0) return 1;
    return 0;
}
```

---

#### [Count trailing zeros in factorial]()

```sh
5! = 5 * 4 * 3 * 2 * 1 = 120 (it contains 1 zero).
How many zeroes will be contained in 100! then.
Explain with logic.
```

**_We can get 0 at end only with 2 and 5. Out of 2 and 5, we only need to count 5 because that is always lower than multiples of 2._**

```cpp
int countTrailingZerosOfFactorial(int n)
{
    int count = 0;
    for (int powerOfFive = 5; powerOfFive <= n; powerOfFive *= 5)
    {
        count += n / powerOfFive;
    }
    return count;
}
```

---

#### [Largest prime factor](https://practice.geeksforgeeks.org/problems/largest-prime-factor2601/1)

Given a number N, the task is to find the largest prime factor of that number.

```cpp
long long int largestPrimeFactor(int N){
    for(int i=2;i*i<=N;) {
        if(N%i == 0) {
            N /= i;
        } else {
            i++;
        }
    }

    return N;
}
```

---

#### [Find Nth root of M ](https://practice.geeksforgeeks.org/problems/find-nth-root-of-m5843/1)

You are given 2 numbers (n , m); the task is to find n√m (nth root of m).

```cpp
int NthRoot(int n, int m)
{
    long long l = 1, r = m;
    while(l<=r) {
        long long mid = (l+r)/2;

        long long x = 1;
        for(int i=0;i<n;i++) { x *= mid; if(x>m) break; }

        if(x == m) return mid;
        else if(x < m) l = mid+1;
        else r = mid-1;
    }

    return -1;
}
```

---

#### [Implement Rand7 using Rand5]()

```cpp
int rand7() {
    int val;
    do {
        val = 5*rand5() + rand5() - 5;
    } while(val >= 21);

    return val%7 + 1;
}

int rand5() {
    return 1 + (rand()%5);
}
```

---

#### [Print all positive numbers that can be expressed as sum pair of sum of cuberoots](https://www.careercup.com/question?id=5647694703886336)

Write a function that would print all positive numbers smaller than n that can be expressed as the sum of two cubes in two different ways. Bonus: calculate the complexity of that function.

For example, 1729 is one such number because 1729 = 1^3 + 12^3 = 9^3 + 10^3.

**_print all positive numbers smaller than n={} that can be expressed as the sum of two cubes_**

```cpp
void printAllPositiveNumbersSmallestThanNExpressedAsPairSum(int n) {
    for (int i = 1; i < n; i++) {
        auto res = calculateCubePairSum(i);
        if (res.empty()) continue;

        for (auto e : res)
            cout << format("[{}^3 + {}^3] = {}", e.first, e.second, i) << endl;
    }
}
```

**_Get cuberoot sum for a number_**

```cpp
void printCuberoots() {
    for (auto num : nums) {
        cout << format("Print all combinations of cuberoot to make the target number = {}", num) << endl;
        obj.calculateAllCubeRootSum(num);
        cout << endl;

        cout << "Cuberoot pair sum" << endl;
        auto res = obj.calculateCubePairSum(num);
        for (auto e : res)
            cout << format("[{}^3 + {}^3] = {}", e.first, e.second, num) << endl;
        cout << endl;
    }
}
```

**_Calculate all cubeRoot sum_**

```cpp
void calculateAllCubeRootSum(int n)
{
    int m = cbrt(n);
    vector<int> cuberoots;
    for(int i=0;i<=m;i++) cuberoots.push_back(pow(i,3));

    vector<string> result;
    calculateAllCubeRootSum(cuberoots, m, n, result);
}

void calculateAllCubeRootSum(vector<int> cubeRoots, int m, int n, vector<string> result)
{
    if(n == 0) {
        cout<<to_string(result)<<endl;
        return;
    }

    if(m < 0 || n < 0) return;

    calculateAllCubeRootSum(cubeRoots, m-1, n, result);
    result.push_back(format("{}^3",(int)cbrt(cubeRoots[m-1])));
    calculateAllCubeRootSum(cubeRoots, m-1, n-cubeRoots[m-1], result);
}
```

**_Calculate pair cubeRoot sum_**

```cpp
vector<pair<int,int>> calculateCubePairSum(int n) {
    int m = cbrt(n);
    vector<int> cubeRoots;

    for(int i=0;i<=m;i++) cubeRoots.push_back(pow(i,3));

    vector<pair<int,int>> result;
    int i=0, j=m;
    while(i < j) {
        if(cubeRoots[i] + cubeRoots[j] < n) i++;
        else if(cubeRoots[i] + cubeRoots[j] > n) j--;
        else {
            result.push_back({cbrt(cubeRoots[i]), cbrt(cubeRoots[j])})
            i++, j--;
        }
    }

    return result;
}
```

---
