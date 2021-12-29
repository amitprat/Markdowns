####[Implement Bitset datastructure]()

_Given an input stream of boolean values, design a data structure that can support following modules in optimal time-
i) setTrue(index)
ii) setFalse(index)
iii) setAllTrue()
iv) setAllFalse()
v) getIndex(index)_

We can record the timestamp / operation number of each of the operation. With that said we will store the last time setAllTrue was executed and last time setAllFalse was executed. Also we store the last time particular index was updated. We can compare the time to get the value at that index.

```cpp
vector<bool> arr(n);
vector<int> lastUpdatedAt;
int op = 0;
int lastSetTrue, lastSetFalse;

void setTrue(int i) {
    op++;
    arr[i] = true;
    lastUpdatedAt[i] = op;
}

void setFalse(int i) {
    op++;
    arr[i] = false;
    lastUpdatedAt[i] = op;
}

void setAllTrue() {
    op++;
    lastSetTrue = op;
}
void setAllFalse() {
    op++;
    lastSetFalse = op;
}


bool get(int i) {
    if(lastUpdatedAt[i] < lastSetTrue && lastUpdatedAt[i] < lastSetFalse) {
        return lastSetTrue > lastSetFalse;
    }
    if(lastUpdatedAt[i] < lastSetTrue) {
        return true;
    }
    if(lastUpdatedAt[i] < lastSetFalse) {
        return false;
    }
    return arr[i];
}
```

Assume 32 bit integer

```cpp
bit_vector = vector<int>;

void setTrue(index) {
    int remainder = index % 32;
    bit_vector[index/32] |= 1 << remainder;
}

void setFalse(index) {
    int remainder = index % 32;
    bit_vector[index/32] &= ~(1 << remainder);
}

void setAllTrue() {
    for (int& i : bit_vector) {
        i |= 0xFFFF;
    }
}

void setAllFalse() {
    for (int& i : bit_vector) {
        i &= 0;
    }
}

bool getIndex(index) {
    int remainder = index % 32;
    return (bit_vector[index/32] >> remainder) & 1;
}
```

---

#### [Reverse Bits](https://practice.geeksforgeeks.org/problems/reverse-bits3556/1#)

Given a 32 bit number X, reverse its binary form and print the answer in decimal.

```sh
Input:
X = 1
Output:
2147483648
Explanation:
Binary of 1 in 32 bits representation-
00000000000000000000000000000001
Reversing the binary form we get,
10000000000000000000000000000000,
whose decimal value is 2147483648.
```

```cpp
static int reversedBits(int num) {
    int n = 32;
    int rev = 0;

    for (int i = 0; i < n; i++) {
        if (num & (1 << i)) rev |= (1 << (n - i - 1));
    }

    return rev;
}
```

```cpp
static int reversedBits(int num) {
    int n = 32 - 1;
    int rev = num;

    while (num) {
        rev |= (num & 1);
        num >>= 1;
        rev <<= 1;
        n--;
    }

    rev <<= n;

    return rev;
}
```

```cpp
// reverse bits only in set bits range
static int reversedBits(int num) {
    int rev = 0;
    while (num) {
        rev <<= 1;
        rev |= num & 1;
        num >>= 1;
    }

    return rev;
}
```

---

#### [Toggle Bits In Given Range](https://practice.geeksforgeeks.org/problems/toggle-bits-given-range0952/1)

Given a non-negative number N and two values L and R. The problem is to toggle the bits in the range L to R in the binary representation of N, i.e, to toggle bits from the rightmost Lth bit to the rightmost Rth bit. A toggle operation flips a bit 0 to 1 and a bit 1 to 0. Print N after the bits are toggled.

```cpp
int toggleBits(int N , int L , int R) {
    int d = R-L+1;
    int mask = (1 << d) - 1;
    mask <<= (L-1); // after this step, we only have those 2 specific bits on.

    // xor of this mask will keep all the bits same as original and will flip the specified bits
    N = N ^ mask;
    return N;
}
```

---

#### [Find Position of right most set bit](https://practice.geeksforgeeks.org/problems/find-first-set-bit-1587115620/1)

```cpp
unsigned int getFirstSetBit(int n)
{
    return log2(n&~(n-1)) + 1;
}
```

---

#### [Rightmost different bit in 2 numbers](https://practice.geeksforgeeks.org/problems/rightmost-different-bit-1587115621/1)

```cpp
int posOfRightMostDiffBit(int m, int n)
{
    int c = m^n; // this will enable only bits which are different in 2 numbers
    c = c&~(c-1); // this should give the right most different bit
    return log2(c)+1; // get its position
}
```

---

#### [Find 2 numbers repeating odd number of times](https://www.careercup.com/question?id=16306671)

You are given an integer array, where all numbers except for TWO numbers appear even number of times.
Q: Find out the two numbers which appear odd number of times.

```sh
1. XOR all the n numbers.
2. Result will be knocked out for all the even pairs as a^a=0 The result now contains only XOR of the two odd out numbers.
3. Find the first bit position in the result that is 1. Definitely this bit position both the odd numbers have different bit values. i.e. one has a 0 and another has a 1 at this position. Let this position be x
4. XOR the elements that have 1 at x bit position and XOR the elements that have 0 at x bit position. The two XOR results would give the two odd count numbers.
```

```cpp
vector<int> findNumbers(vector<int>& arr) {
    int xorVal = 0;
    for (auto num : arr) xorVal ^= num;

    // find first set bit
    // we can also find it using val = val & ~(val-1);
    int i = 0;
    for (; i < 32; i++) {
        if (xorVal & (1 << i)) break;
    }

    int num1 = 0, num2 = 0;
    for (int num : arr) {
        if (num & (1 << i)) num1 ^= num;
        else num2 ^= num;
    }

    return { num1, num2 };
}
```

---

#### [Multiply/Sum numbers without using multiplication/addition operator]()

```cpp
static int multiply(int a, int b) {
    int sign = (a ^ b) >> 31;
    a = abs(a);
    b = abs(b);

    int res = 0;
    while (b) {
        if (b & 1) res = sum(res, a);

        a <<= 1;
        b >>= 1;
    }

    return sign ? (~res + 1) : res;
}

static int sum(int a, int b) {
    while (b) {
        int carry = (a & b) << 1;
        a = a ^ b;
        b = carry;
    }

    return a;
}
```

---
