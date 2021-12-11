####[Next Permutation](https://practice.geeksforgeeks.org/problems/next-permutation5226/1)

Implement the next permutation, which rearranges the list of numbers into Lexicographically next greater permutation of list of numbers. If such arrangement is not possible, it must be rearranged to the lowest possible order i.e. sorted in an ascending order. You are given an list of numbers arr[ ] of size N.

```cpp
vector<int> nextPermutation(int N, vector<int> arr){
    int l = N-2;
    while(l >= 0 && arr[l]>=arr[l+1]) l--;
    if(l < 0) {
        reverse(arr.begin(), arr.end());
        return arr;
    }

    int r = l+1;
    for(int i=r;i<N;i++) {
        if(arr[i] < arr[r] && arr[i]>arr[l]) r = i;
    }

    swap(arr[l], arr[r]);
    sort(arr.begin()+l+1,arr.end());

    return arr;
}
```

---

#### [Trapping Rain Water ](https://practice.geeksforgeeks.org/problems/trapping-rain-water-1587115621/1)

Given an array arr[] of N non-negative integers representing the height of blocks. If width of each block is 1, compute how much water can be trapped between the blocks during the rainy season.

```cpp
long long trappingWater(int arr[], int n){
    long long mxBoundary = 0;
    long long mxWater = 0;
    int l = 0, r = n-1;

    while(l < r) {
        long long curBoundary = arr[l] < arr[r] ? arr[l++] : arr[r--];
        mxBoundary = max(mxBoundary, curBoundary);

        mxWater += (mxBoundary - curBoundary);
    }

    return mxWater;
}
```

```cpp
int trappingWater(vector<int> arr) {
    int water = 0;
    vector<int> leftMaxBoundary(arr.size());
    vector<int> rightMaxBoundary(arr.size());

    for (int i = 0; i < leftMaxBoundary.size(); i++) {
        leftMaxBoundary[i] = max(i > 0 ? leftMaxBoundary[i-1]: INT_MIN, arr[i]);
    }

    mx = INT_MIN;
    for (int i = arr.size() - 1; i >= 0; i--) {
        rightMaxBoundary[i] = max(i < arr.size()-1 ? rightMaxBoundary[i+1] : INT_MIN, arr[i]);
    }

    for (int i = 0; i < arr.size(); i++) {
        result += min(rightMaxBoundary[i], leftMaxBoundary[i]) - arr[i];
    }

    return water;
}
```

---
