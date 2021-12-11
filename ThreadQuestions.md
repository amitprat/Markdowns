#### [Print Even Odd Numbers Using 2 Threads]()

##### [Print using seperate methods for different thread]()

```cpp
class PrintEvenOdd {
    mutex m;
    condition_variable cv;
    int cnt = 0;

    void printEven(int mx) {
        while (cnt < mx) {
            unique_lock<mutex> lock(m);
            cv.wait(lock, [this]() { return (cnt & 1) == 0; });

            cout << "Even thread: "<<cnt++ << " ";
            cv.notify_all();
        }
    }

    void printOdd(int mx) {
        while (cnt < mx) {
            unique_lock<mutex> lock(m);
            cv.wait(lock, [this]() { return (cnt & 1) == 1; });

            cout << "Old thread: "<<cnt++ << " ";
            cv.notify_all();
        }
    }

public:
    void printEvenOdd(int mx) {
        thread evenThread(&PrintEvenOdd::printEven, this, mx);
        thread oddThread(&PrintEvenOdd::printOdd, this, mx);

        evenThread.join();
        oddThread.join();
    }
};
```

##### [Print using single method with passed predicate]()

```cpp
class PrintEvenOddPredicate {
    int cnt = 0;
    mutex m;
    condition_variable cv;

    void printMethod(function<bool()> predicate, int mx, string threadName) {
        while (cnt < mx) {
            unique_lock<mutex> lock(m);
            cv.wait(lock, [this, predicate]() {return predicate(); });

            cout << threadName << ":" <<cnt++ << " ";
            cv.notify_all();
        }
    }
public:
    void printEvenOdd(int mx) {
        auto evenPredicate = [this]() {return (cnt & 1) == 0; };
        auto oddPredicate = [this]() {return (cnt & 1) == 1; };

        thread evenThread(&PrintEvenOddPredicate::printMethod, this, evenPredicate, mx, "EvenThread");
        thread oddThread(&PrintEvenOddPredicate::printMethod, this, oddPredicate, mx, "OddThread");

        evenThread.join();
        oddThread.join();
    }
};
```

---

#### [Print 0,1 and 2 using 3 threads with single method and predicate]()

```cpp
class Print012WithPredicate {
    int mxcnt = 0;
    int cnt = 0;
    mutex m;
    condition_variable cv;

    void printMethod(function<bool()> predicate, int mx, string threadName) {
        while (mxcnt < mx) {
            unique_lock<mutex> lock(m);
            cv.wait(lock, [this, predicate]() { return predicate(); });

            cout << threadName <<": "<<cnt <<endl;
            cnt = (cnt + 1) % 3;
            mxcnt++;

            cv.notify_all();
        }
    }
public:
    void print012(int mx) {
        auto printZeroPredicate = [this]() {return cnt == 0; };
        auto printOnePredicate = [this]() {return cnt == 1; };
        auto printTwoPredicate = [this]() {return cnt == 2; };

        thread zeroThread(&Print012WithPredicate::printMethod, this, printZeroPredicate, mx, "Thread1");
        thread oneThread(&Print012WithPredicate::printMethod, this, printOnePredicate, mx, "Thread2");
        thread twoThread(&Print012WithPredicate::printMethod, this, printTwoPredicate, mx, "Thread3");

        zeroThread.join();
        oneThread.join();
        twoThread.join();
    }
};
```

---

#### [Print multiple array elements in order using multiple threads]()

```cpp
class AccessArrayElementsInOrder {
    vector<int> arr1, arr2, arr3;
    bool printFirst = true;
    bool printSecond = false;
    bool printThird = false;
    int cur1 = 0, cur2 = 0, cur3 = 0, total = 0;

    mutex m;
    condition_variable cv;

    void printInorder(function<bool()> predicate, function<void()> action, function<void()> next)
    {
        while (total < 100) {
            unique_lock<mutex> lock(m);
            cv.wait(lock, [predicate]() { return predicate(); });

            action();
            next();

            cv.notify_all();
        }
    }

public:
    void test() {
        for (int i = 0; i <= 102; i += 3) arr1.push_back(i);
        for (int i = 1; i <= 102; i += 3) arr2.push_back(i);
        for (int i = 2; i <= 102; i += 3) arr3.push_back(i);

        auto pred1 = [this]() {return printFirst; };
        auto pred2 = [this]() {return printSecond; };
        auto pred3 = [this]() {return printThird; };

        auto action1 = [this]() { cout << "Thread1: "<<arr1[cur1++]<<endl; total++; };
        auto action2 = [this]() { cout << "Thread2: "<<arr2[cur2++]<<endl; total++; };
        auto action3 = [this]() { cout << "Thread3: "<<arr3[cur3++]<<endl; total++; };

        auto next1 = [this]() { printFirst = false;  printSecond = true; };
        auto next2 = [this]() { printSecond = false;  printThird = true; };
        auto next3 = [this]() { printThird = false;  printFirst = true; };

        thread t1(&AccessArrayElementsInOrder::printInorder, this, pred1, action1, next1);
        thread t2(&AccessArrayElementsInOrder::printInorder, this, pred2, action2, next2);
        thread t3(&AccessArrayElementsInOrder::printInorder, this, pred3, action3, next3);

        t1.join();
        t2.join();
        t3.join();
    }
};
```

---

#### [Bounded queues using multiple threads]()

```cpp
template <class T>
class BoundedQueue
{
    queue<T> q;
    mutex m;
    condition_variable cv;
    int n;

public:
    BoundedQueue(int n) : n(n) {}

    void add(T item) {
        unique_lock<mutex> lock(m);
        cv.wait(lock, [this]() { return q.size() != n; });

        q.push(item);
        cv.notify_all();
    }

    T front() {
        shared_lock<mutex> lock(m);
        cv.wait(lock, [this]() { return !q.empty(); });

        auto res = q.front();
        cv.notify_all();

        return res;
    }

    T pop() {
        unique_lock<mutex> lock(m);
        cv.wait(lock, [this]() { return !q.empty(); });

        auto res = q.front(); q.pop();
        cv.notify_all();

        return res;
    }

    static void test() {
        BoundedQueue<int> obj(10);
        thread write1, write2, write3;
        thread read1, read2, read3;

        write1 = thread([&obj]() {obj.add(1); });
        write2 = thread([&obj]() {obj.add(2); });

        read1 = thread([&obj]() {cout << obj.pop(); });
        read2 = thread([&obj]() {cout << obj.pop(); });
        read3 = thread([&obj]() {cout << obj.pop(); });

        write3 = thread([&obj]() {obj.add(3); });

        write1.join();
        write2.join();
        write3.join();

        read1.join();
        read2.join();
        read3.join();

    }
};
```

---
