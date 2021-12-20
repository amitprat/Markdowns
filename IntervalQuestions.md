#### [Maximum Interval Overlap]()

Find the time during which most animals were alive.
Find the time/duration during which most people lived.

```sh
Input: [{1,4}, {2,5}, {9,12}, {5,9}, {5,12}]
Output: 3, {5,5}
```

**_Approach1: Using 2 pointers_**

```cpp
static pair<int, Interval> findMaxIntervalOverlap(vector<Interval> intervals) {
    vector<int> startTimes;
    vector<int> endTimes;

    for (auto& interval : intervals) {
        startTimes.push_back(interval.start);
        endTimes.push_back(interval.end);
    }

    sort(startTimes.begin(), startTimes.end());
    sort(endTimes.begin(), endTimes.end());

    int i = 0, j = 0;
    int mxOverlap = 0;
    Interval maxInterval;

    while (i < startTimes.size() && j < endTimes.size()) {
        if (startTimes[i] <= endTimes[j]) i++;
        else {
            if (i - j > mxOverlap) {
                mxOverlap = i - j;
                maxInterval = { startTimes[i - 1], endTimes[j] };
            }
            j++;
        }
    }

    return { mxOverlap , maxInterval };
}
```

**_Approach2: Using combined sorted result_**

```cpp
static pair<int, Interval> findMaxIntervalOverlap2(vector<Interval> intervals) {
    vector<pair<int, char>> sortedSet;
    for (auto& interval : intervals) {
        sortedSet.push_back({ interval.start, 's' });
        sortedSet.push_back({ interval.end, 'e' });
    }

    sort(sortedSet.begin(), sortedSet.end(), [](const auto& f, const auto& s) {
        if (f.first < s.first) return true;
        if (f.first > s.first) return false;
        return f.second > s.second;
        });

    int mxOverlap = 0, curOverlap = 0;
    Interval maxInterval;
    for (int i = 0; i < sortedSet.size(); i++) {
        if (sortedSet[i].second == 's') curOverlap++;
        else if (sortedSet[i].second == 'e') {
            if (curOverlap > mxOverlap) {
                mxOverlap = curOverlap;

                maxInterval = { sortedSet[i - 1].first,sortedSet[i].first };
            }
            curOverlap--;
        }
    }

    return { mxOverlap , maxInterval };
}
```

---

#### [Total time covered by intervals]()

Given a list of arrays of time intervals, write a function that calculates the total amount of time covered by the intervals.

```sh
For example:
input = [(1,4), (2,3)]
return 3
input = [(4,6), (1,2)]
return 3
input = {{1,4}, {6,8}, {2,4}, {7,9}, {10, 15}}
return 11
```

```cpp
int maxTimeConvered1(vector<pair<int, int>> input) {
    sort(input.begin(), input.end(), [](const auto& f, const auto& s) {return f.first < s.first; });

    int result = 0;
    int lastEnd = 0;
    for (int i = 0; i < input.size(); i++) {
        int curStart = max(input[i].first, lastEnd); // {(1,4), (2,3), (2,6)} -> curStart: {1, 4, 4}
        result += max(input[i].second - curStart, 0); // curEnd - curStart -> {3, max(-1,0), 2}
        lastEnd = max(input[i].second, lastEnd); // update lastEnd to max of current end or lastEnd
    }
    return result;
}
```

---

#### [Merge Intervals]()

```sh
Merged result of it1=[{1,2}, {3,9}]  and it2=[{4,5}, {8,10}, {11,12}]  is [{1,2}, {3,10}, {11,12}]
Merged result of it1=[{1,20}, {3,9}]  and it2=[{4,5}, {8,10}, {11,12}]  is [{1,20}]
```

```cpp
static vector<Interval> merge(vector<Interval>& it1, vector<Interval>& it2) {
    int n = it1.size();
    int m = it2.size();
    vector<Interval> result;

    int i = 0, j = 0;
    while (i < n || j < m) {
        if (i == n) merge(result, it2[j++]);
        else if (j == m) merge(result, it1[i++]);
        else if (it1[i].start <= it2[j].start) merge(result, it1[i++]);
        else merge(result, it2[j++]);
    }

    return result;
}

static void merge(vector<Interval>& result, Interval it) {
    if (result.empty() || result.back().end < it.start) result.push_back(it);
    else {
        int n = result.size();
        result[n - 1] = { min(result[n - 1].start, it.start), max(result[n - 1].end, it.end) };
    }
}
```

---

#### [Total time covered by intervals]()

Total time covered by intervals [{1,4}, {2,4}, {6,8}, {7,9}, {10,15}] is 11

```cpp
int totalIntervalCovered(vector<Interval>& intervals) {
    sort(intervals.begin(), intervals.end(), [](const auto& f, const auto& s) {
        return f.start < s.start;
        });

    int timeCovered = 0;
    int last = 0;
    for (auto it : intervals) {
        timeCovered += max(it.end - max(last, it.start), 0);
        last = max(last, it.end);
    }

    return timeCovered;
}
```
