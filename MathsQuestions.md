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
