# Implement Bitset datastructure

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

####[Reverse Bits](https://practice.geeksforgeeks.org/problems/reverse-bits3556/1#)

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
####[Find Position of right most set bit](https://practice.geeksforgeeks.org/problems/find-first-set-bit-1587115620/1)

```cpp
unsigned int getFirstSetBit(int n)
{
    return log2(n&~(n-1)) + 1;
}
```
---

####[Rightmost different bit in 2 numbers](https://practice.geeksforgeeks.org/problems/rightmost-different-bit-1587115621/1)

```cpp
int posOfRightMostDiffBit(int m, int n)
{
    int c = m^n; // this will enable only bits which are different in 2 numbers
    c = c&~(c-1); // this should give the right most different bit
    return log2(c)+1; // get its position
}
```
---




