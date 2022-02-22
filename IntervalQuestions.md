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

#### [Merge sorted intervals inplace]()

```cpp
static void mergeIntervals(vector<Interval>& intervals) {
    if (intervals.empty()) return;

    int i = 0, n = intervals.size();
    for (int j = 1; j < n; j++) {
        auto& prev = intervals[i];
        auto& cur = intervals[j];
        if (overlap(prev, cur)) prev = { min(prev.start,cur.start),max(prev.end,cur.end) };
        else intervals[++i] = cur;
    }

    intervals.erase(intervals.begin() + i + 1, intervals.end());
}

static bool overlap(Interval& it1, Interval& it2) {
    return it1.end >= it2.start && it2.end >= it1.start;
}
```

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

---

#### [Merge Sorted Intervals]()

```cpp
class MergeSortedIntervals {
public:
    static void test() {
        MergeSortedIntervals obj;
        vector<Interval> arr1 = { Interval{3,19},Interval{4,8}, Interval{17,25},Interval{20,45}, Interval{58,73} };
        vector<Interval> arr2 = { Interval{6,18},Interval{7,9},Interval{8,27}, Interval{40,47} };

        vector<Interval> res1 = obj.merge(arr1, arr2);
        vector<Interval> res2 = obj.merge1(arr1, arr2);

        assert(isSame(res1, res2));

        cout << to_string(res1);
    }

    bool overlap(Interval prev, Interval next) {
        bool notOverlap = prev.end < next.start || prev.start > next.end;
        return !notOverlap;
    }

    vector<Interval> merge1(vector<Interval> arr1, vector<Interval> arr2) {
        vector<Interval> result;

        Interval prev = { -1,-1 };
        int i = 0, j = 0;
        while (i < arr1.size() || j < arr2.size()) {
            Interval smallest;
            if (i < arr1.size() && j < arr2.size() && arr1[i].start <= arr2[j].start) {
                smallest = arr1[i++];
            }
            else if (j < arr2.size()) {
                smallest = arr2[j++];
            }
            else {
                smallest = arr1[i++];
            }

            if (prev.start == -1) prev = smallest;
            else if (overlap(prev, smallest)) prev = { min(prev.start,smallest.start),max(prev.end,smallest.end) };
            else {
                result.push_back(prev);
                prev = smallest;
            }
        }
        if (prev.start != -1) result.push_back(prev);

        return result;
    }

    vector<Interval> merge(vector<Interval> arr1, vector<Interval> arr2) {
        vector<Interval> result;
        stack<int> st;

        vector<pair<int, bool>> values;
        for (auto& s : arr1) {
            values.push_back({ s.start,true });
            values.push_back({ s.end,false });
        }

        for (auto& s : arr2) {
            values.push_back({ s.start,true });
            values.push_back({ s.end,false });
        }

        sort(values.begin(), values.end(), [](const auto& first, const auto& second) {return first.first < second.first; });

        for (auto& val : values) {
            if (val.second) st.push(val.first);
            else {
                auto top = st.top(); st.pop();
                if (st.empty()) {
                    result.push_back({ top,val.first });
                }
            }
        }

        return result;
    }
};
```

---

#### [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

Given an array of intervals intervals where intervals[i] = [starti, endi], return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

```sh
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
```

- We sort by end time if we want to consider the maximum number of intervals in result group. Here, we want to minimize the number of intervals to remove, so we need to select maximum number of intervals and hence sort by end time.
- We sort by start time when when we want to cover the maximum overall range (or time range) covered by intervals.

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        int cnt = 0;
        sort(intervals.begin(), intervals.end(), [](auto& f, auto& s) {
            return f[1] < s[1];
        });

        for(int i=0,j=1;j<intervals.size();j++) {
            if(intervals[j][0] < intervals[i][1]) {
                cnt++;
            } else {
                i=j;
            }
        }

        return cnt;
    }
```

---

#### [Minimum Platforms Required]()

```cpp
// Maintain seperate queue for each of the bus and look for best queue where we can fit next bus
// Bad time and space complexity
int minPlatforms0(StationTimeChart& chart)
{
    int result = 0;
    if (chart.schedules.empty()) return result;

    vector<queue<StationTimeChart::Schedule>> vq;
    vector<StationTimeChart::Schedule> schedules = chart.schedules;
    sort(schedules.begin(), schedules.end(), [](auto& f, auto& s) {return f.arrival < s.arrival; });

    queue<StationTimeChart::Schedule> q; q.push(schedules[0]);
    vq.push_back(q);

    for (int i = 1; i < schedules.size(); i++) {
        bool found = false;
        for (auto& q : vq) {
            if (schedules[i].arrival > q.back().departure) {
                q.push(schedules[i]);
                found = true;
                break;
            }
        }

        if (!found) {
            queue<StationTimeChart::Schedule> tmp; tmp.push(schedules[i]);
            vq.push_back(tmp);
        }
    }

    return vq.size();
}
```

```cpp
/*
Time Complexity: O( N* LogN).
Since we are inserting into multiset and it maintain elements in sorted order. So N insert operations in multiset requires N*LogN time complexity.
*/
int minPlatforms1(StationTimeChart& chart)
{
    int result = 0;
    // multimap won't work here if arrival and departure time are same.
    // https://stackoverflow.com/questions/52074218/custom-compare-function-for-stdmultimap-when-keys-are-equal
    // multisort will instead first sort by first value in pair and then second. because 'a' < 'd' so it will maintain order

    multiset<pair<int, char>> multiset;
    for (auto& schedule : chart.schedules) {
        multiset.insert({ schedule.arrival, 'a' });
        multiset.insert({ schedule.departure, 'd' });
    }

    int platCnt = 0;
    for (auto& e : multiset) {
        if (e.second == 'a') {
            platCnt++;
            result = max(result, platCnt);
        }
        else platCnt--;
    }

    return result;
}
```

```cpp
// T - O(nlogn)
int minPlatforms2(StationTimeChart& chart)
{
    int n = chart.schedules.size();
    if (n == 0) return 0;

    vector<int> startTimes, endTimes;
    for (auto& e : chart.schedules) {
        startTimes.push_back(e.arrival);
        endTimes.push_back(e.departure);
    }
    sort(startTimes.begin(), startTimes.end());
    sort(endTimes.begin(), endTimes.end());

    int a = 1, d = 0;
    int result = 1;
    while (a < n) {
        if (endTimes[d] < startTimes[a]) d++;
        else a++;

        result = max(result, a - d);
    }

    return result;
}
```

```cpp
/*
Complexity Analysis:

Time Complexity: O(N).
Space Complexity: O(1).
*/
int minPlatforms3(StationTimeChart& chart)
{
    int platform[2361] = { 0 };
    int requiredPlatform = 1;

    for (int i = 0; i < chart.schedules.size(); i++) {
        // increment the platforms for arrival
        ++platform[chart.schedules[i].arrival];

        // once train departs we decrease the platform count
        --platform[chart.schedules[i].departure + 1];
    }

    // We are running loop till 2361 because maximum time value in a day can be 23:60
    for (int i = 1; i < 2361; i++) {
        // taking cumulative sum of platform give us required
        // number of platform for every minute
        platform[i] = platform[i] + platform[i - 1];
        requiredPlatform = max(requiredPlatform, platform[i]);
    }

    return requiredPlatform;
}
```

```cpp
// https://www.programcreek.com/2014/05/leetcode-meeting-rooms-ii-java/
int minPlatforms4(StationTimeChart& chart) {
    vector<StationTimeChart::Schedule> schedules = chart.schedules;
    if (schedules.empty()) return 0;

    sort(schedules.begin(), schedules.end(), [](auto& f, auto& s) {return f.arrival < s.arrival; });

    priority_queue<int, vector<int>, greater<int>> minheap;
    int count = 0;

    for (StationTimeChart::Schedule& schedule : schedules) {
        if (minheap.empty()) {
            count++;
            minheap.push(schedule.departure);
        }
        else {
            if (schedule.arrival <= minheap.top()) {
                count++;
            }
            else {
                minheap.pop();
            }

            minheap.push(schedule.departure);
        }
    }

    return count;
}
```

---

#### [Find Period during which most animal lived]()

```cpp
// multiset is more correct than this
pair<int, Interval> findMaxPeriod(vector<Interval> intervals) {
    multimap<int, char> m;
    int period = 0;
    Interval curPeriod = { INT_MIN, INT_MAX };
    int mxPeriodCount = 0;
    Interval maxPeriod = curPeriod;

    for (auto& interval : intervals) {
        m.insert({ interval.start, 'a' });
        m.insert({ interval.end, 'd' });
    }

    for (auto& entry : m) {
        if (entry.second == 'a') {
            curPeriod.start = max(curPeriod.start, entry.first);
            period++;
        }
        else if (entry.second == 'd') {
            curPeriod.end = entry.first;

            if (period > mxPeriodCount) {
                maxPeriod = curPeriod;
                mxPeriodCount = period;
            }
            period--;
        }
    }

    mxPeriodCount = max(mxPeriodCount, period);

    return { mxPeriodCount, maxPeriod };
}

pair<int, Interval> findMaxPeriod2(vector<Interval> intervals) {
    pair<int, Interval> result = { 0, {INT_MAX, INT_MIN} };
    if (intervals.empty()) return result;

    sort(intervals.begin(), intervals.end(), [](auto& f, auto& s) {return f.start < s.start; });

    int i = 0, j = 1;
    int curMaxPeriod = 1;
    Interval curMaxInterval = intervals[i];
    result = { curMaxPeriod, curMaxInterval };

    while (j <= intervals.size()) {
        if (j == intervals.size() || intervals[j].start > intervals[i].end) {
            if (curMaxPeriod > result.first) result = { curMaxPeriod, curMaxInterval };
            curMaxPeriod--;
            i++;
        }
        else {
            curMaxInterval = { max(curMaxInterval.start, intervals[j].start), min(curMaxInterval.end, intervals[j].end) };
            curMaxPeriod++;
        }
        j++;
    }

    return result;
}
```

---

#### [Merge 2 sorted interval arrays]()

```cpp
vector<Interval> mergeSortedArrays(vector<Interval>& v1, vector<Interval>& v2) {
    vector<Interval> result;

    int i=0, j=0;
    while(i < v1.size() || j < v2.size()) {
        if(i == v1.size()) merge(result, v2[j++]);
        else if(j == v2.size()) merge(result, v1[i++]);
        else {
            if(v1[i].start < v2[j].start) merge(result, v1[i++]);
            else merge(resilt, v2[j++]);
        }
    }

    return result;
}

void merge(vector<Interval>& result, Interval& it) {
    if(result.empty() || it.start > result.back().end) result.push_back(it);
    else {
        it = merge(result.back(), it); result.pop_back();
        result.push_back(it);
    }
}

Interval merge(Interval& it1, Interval& it2) {
    return {min(it1.start, it2.start), max(it1.end, it2.end)};
}

static void test()
{
    vector<Interval> v1 = { {1, 5} ,{10, 14},{16, 18} };
    vector<Interval> v2 = { {2, 6} ,{8, 10},{11, 20} };

    MergeTwoSortedIntervalsArrays obj;
    sort(v1.begin(), v1.end());
    sort(v2.begin(), v2.end());
    cout << "Interval Set1: " << to_string(v1) << endl;
    cout << "Interval Set2: " << to_string(v2) << endl;

    vector<Interval> output = obj.merge(v1, v2);
    cout << "Interval Output Set: " << to_string(output) << endl;
}
```

---

#### [Merge overlapping intervals](https://leetcode.com/problems/merge-intervals/submissions/)

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end(),[](const auto&f, const auto& s) {
        return f[0] < s[0];
    });

    int s = 0, e = 1;
    while(e < intervals.size()) {
        if(overlap(intervals[s], intervals[e])) {
            intervals[s] = merge(intervals[s], intervals[e]);
        } else {
            intervals[++s] = intervals[e];
        }
        e++;
    }

    return {intervals.begin(), intervals.begin()+s+1};
}

bool overlap(vector<int> it1, vector<int> it2) {
    return it2[0] <= it1[1];
}

vector<int> merge(vector<int> it1, vector<int> it2) {
    return {min(it1[0],it2[0]), max(it1[1],it2[1])};
}
```

---

#### [Insert interval in sorted interval set and remove overlapping intervals. Original interval array doesn't have overlapping intervals]()

```cpp
void merge(vector<Interval>& in, Interval item) {
    int index = 0;
    for (auto it = in.begin(); it != in.end();) {
        if (item < *it) {
            break;
        }

        if (it->overlap(item)) {
            item.start = min(it->start, item.start);
            item.end = max(it->end, item.end);
            it = in.erase(it);
        }
        else {
            it++;
            index++;
        }
    }
    in.insert(in.begin() + index, item);
}
```

---
