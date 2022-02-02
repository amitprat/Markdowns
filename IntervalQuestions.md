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
