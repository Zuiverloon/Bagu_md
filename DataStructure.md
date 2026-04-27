# Data Structure

### 线性表 Linear Table

array, occupying continuous space in memory

### 栈 Stack

LIFO

### 队列 queue

FIFO(push into the queue at one side, pull out at the other side)

### 环形队列 circular queue

### 双向队列 doubly ended queue

push or pull at both side

### 单链表 LinkedList

a val field and a next field pointing to next node.

### 单链表和线性表的区别？

LinearTable: allocate continuous memory for fixed length 查找 O(1)，增删 O(n)  
LinkedList: save elements in distributed space. Length not fixed. 查找 O(n)，增删 O(1)  
如果要频繁查找，且知道长度，用线性表
要频繁插入，不知道长度，用单链表  
| linear table(array) |linkedlist|
|---|---|
|store element in continuous memory space for fixed length|store element in distributed space,length not fixed|
|support random access|do not support. If want to find the element,need to iterate|
|search O(1), modify O(n)|search O(n),modify O(n), but if given the previous node, then modify O(1)|

### 环形链表 circularLinkedList

### 双向链表 doublyLinkedList(prev,val,next)

### 顺序查找 sequential search

### 二分查找 binary search

### 复杂度

平方 the square of  
立方 the cubic power of  
四次方 the fourth power of  
二进制 binary  
十进制 decimal  
十六进制 hexadecimal  
八进制 octal  
分治法 divide and conquer

## kmp

https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/

思想：当匹配失败时，找 pattern 串的相同前缀和后缀  
abeababeabf  
 ｜  
abeabf 此时(a,f)失败，但有相同前缀后缀 ab，因此转变为  
ababeabf  
abeabf 此时(a,e)失败，没有相同前缀后缀，移动匹配串到  
abeabf  
abeabf

```c++
vector<int> getkmpnext(string c){
    vector<int> next = vector<int>(c.length(),0);
    next[0] = 0;//if the matching fails at i, then next[i] will be the next starting position(the suffix of the pattern is equal to the prefix of the pattern).
    for (int i = 1,j = 0;i<c.length;i++){
        while (j>0 && c[i]!=c[j])j = next[j-1];
        if (c[i] == c[j]){
            j++;
        }
        next[i] = j;
    }
    return next;
}
// ababab [0,0,1,2,3,4]

int strStr(string haystack, string needle) {
        vector<int> flink = getFlink(needle);
        // cout<<"flink fin\n";
        for (int i = 0,j = 0;i<haystack.length();i++){
            // cout<<i<<" "<<j<<"\n";
            while (j>0 && haystack[i]!=needle[j]){
                j = flink[j-1];
            }
            //  cout<<i<<" "<<j<<" aa\n";
            if (haystack[i] == needle[j]){
                j++;
            }
            if (j == needle.length()) {
                return i-j+1;
            }
        }
        return -1;
    }
```

## Sort

**验证模版**

```c++
#include <vector>
#include <algorithm>
#include <random>
#include <iostream>

using namespace std;

template <typename T>
void shuffleVector(vector<T> &v)
{
    static std::random_device rd;
    static std::mt19937 gen(rd());
    std::shuffle(v.begin(), v.end(), gen);
}

void printVector(std::vector<int> &v)
{
    for (int i = 0; i < v.size(); i++)
    {
        std::cout << v[i] << " \n"[i == v.size() - 1];
        // printf("%lld%c", v[i], " \n"[i == v.size() - 1]);
    }
}


void validate(vector<int> &v)
{
    bool valid = true;
    for (int i = 0; i < v.size(); i++)
    {
        if (v[i] != i + 1)
        {
            valid = false;
            break;
        }
    }
    cout << (valid ? "" : "in") << "valid\n";
}

int main()
{
    int n = 100;
    vector<int> v(n, 0);
    for (int i = 0; i < n; i++)
    {
        v[i] = i + 1;
    }
    shuffleVector(v);;
    // qsort(v, 0, v.size() - 1);
    // qsort(v);
    // msortb(v);
    validate(v);
    printVector(v);
}
```

### **insertion sort O(n2)**

Step 1 - If the element is the first element, assume that it is already sorted. Return 1.  
Step2 - Pick the next element, and store it separately in a key.  
Step3 - Now, compare the key with all elements in the sorted array.  
Step 4 - If the element in the sorted array is smaller than the current element, then move to the next element. Else, shift greater elements in the array towards the right.  
Step 5 - Insert the value.

```java
public static void insertionsort(int[] arr){
        for (int i = 1;i<arr.length;i++){
            int key = arr[i];
            for (int j = i-1;j>=0;j--){
                if (arr[j]>key){
                    arr[j+1] = arr[j];
                    arr[j] = key;
                }
                else{
                    break;
                }
            }
        }
        return;
    }
```

### **selection sort O(n2)**

firstly, go thought all elements to see the smallest element, then swap it with the first element  
then, go through all element from the second to the end to see the second smallest element, then swap

```java
public static void selectionsort(int[] arr){
        for (int i = 0;i<arr.length;i++){
            int minIndex = i;
            int minValue = arr[i];
            for (int j = i+1;j<arr.length;j++){
                if (arr[j]<minValue){
                    minValue = arr[j];
                    minIndex = j;
                }
            }
            int t = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = t;
        }
        return;
    }
```

### **bubble sort O(n2)**

compare the neighbour elements to see whether they need to swap.

```java
public static void bubblesort(int[] arr){
        for (int i = 0;i<arr.length-1;i++){
            for (int j = arr.length-1;j>i;j--){
                if (arr[j]<arr[j-1]){
                    int t = arr[j];
                    arr[j] = arr[j-1];
                    arr[j-1] = t;
                }
            }
        }
        return;
    }
```

### **quicksort O(nlogn)**

https://leetcode.cn/problems/kth-largest-element-in-an-array/

选出一个 pivot value, put elements smaller than pivot to the left side, put elements greater than pivot value to the right side, recursively do sort on the left side and the right side.

```c++
void quicksort(vector<int> arr,int left,int right){
    if (left>=arr.length || left<0 || right>=arr.length || right<0) return;
    int pivot = arr[left];
    int l = left,r = right;
    if(l<r){
        while (l<r) {
            while (l < r && arr[r] > pivot) r--;
            if (r > l) {
                arr[l] = arr[r];l++;
            }
            while (l < r && arr[l] < pivot) l++;
            if (r > l) {
                arr[r] = arr[l];r--;
            }
        }
        arr[l] = pivot;
        quicksort(arr,left,l-1);
        quicksort(arr,l+1,right);
    }
}
```

### **merge sort O(nlogn)**

递归：(分治法)divide the given list into 2 halves, sort the 2 halves and merge them together, recursively
不递归：先两个一组，再四个一组，再8个一组。。。

```c++
void merge(vector<int> arr,int low, int mid,int high,vector<int> tmp){
        int j = low,k = mid+1;
        int tmpp = 0;
        while (j<=mid && k<=high){
            if (arr[j]<arr[k]){
                tmp[tmpp++] = arr[j++];
            }
            else{
                tmp[tmpp++] = arr[k++];
            }
        }
        while (j<=mid){
            tmp[tmpp++] = arr[j++];
        }
        while (k<=high){
            tmp[tmpp++] = arr[k++];
        }
        for (int i = 0;i<tmpp;i++){
            arr[low+i] = tmp[i];
        }
        return;
    }

void mergesort(vector<int> arr,int low,int high,vector<int> tmp){
        if (low<high){
            int mid = (low+high)/2;
            mergesort(arr,low,mid,tmp);
            mergesort(arr,mid+1,high,tmp);
            merge(arr,low,mid,high,tmp);
        }
    }
```

### **heapsort O(nlogn)**

heap 堆: 节点的值不比左子节点小，也不必右子节点小  
make a heap to get the top value
recursively find the top in each loop

```java
void heapsort(vector<int> arr){
        for (int i = arr.length/2;i>=0;i--){
            heapify(arr,i,arr.length-1);
        }
        for (int i = 0;i<arr.length;i++){
            int curMax = arr[0];
            arr[0] = arr[arr.length-1-i];
            arr[arr.length-1-i] = curMax;
            heapify(arr,0,arr.length-2-i);
        }
    }

void heapify(int[] arr,int i,int right){
        // make a max heap. arr[i] should be greater than arr[i*2](if existed) and arr[i*2+1](if existed)
        while (i*2<=right){ // make sure that node i has children
            int t = i*2;// t is the index of the greater children
            if (t+1<=right && arr[t]<arr[t+1]){ // has two children
                t++;
            }
            if (arr[i]<arr[t]){ // if children is greater, than swap
                int tmp = arr[i];
                arr[i] = arr[t];
                arr[t] = tmp;
                i = t;// do it recursively/ move to next layer
            }
            else return;
        }
    }
```

## 排序稳定性 stability

两个元素值相等，原本在前面的元素排完后还在前面，就是稳定的  
稳定的：冒泡，插入，归并  
不稳定：选择，快排，堆

### 近似有序数列的排序，元素的移动距离不大于 k

堆排序 堆的大小为 k

## tree

节点(node) 父节点(parent node) 子节点(child node) 同胞节点(sibling node) 叶子节点(leaf node) 中间节点(internal node) 路径(path) 边(edge)
遍历(traverse)前序遍历(preorder,自己，左，右，压栈顺序(自己，右，左))、后序遍历(postorder 左，右，自己，(压栈顺序左，右，用另一个栈倒序输出))、中序遍历(inorder 左，自己、右，压栈尽量压左)、层次遍历(level order 非递归，依靠队列实现)
二叉树(binary tree)  
平衡二叉树 balanced tree:左右子树高度(gap between the length of the left and the right tree)在 1 和-1 之间
perfect binary tree:每个非叶子结点都有两个子节点
full binary tree:每个节点都是 0 或 2 个子节点
complete binary tree:每个节点都是 0 或 2 个子节点，并且最后一层的节点都在左边

## graph

Floyd(find the shortest path between any node a to any node b) O(n3)

```java
// floyd 基本流程为三层循环：
// 枚举中转点 - 枚举起点 - 枚举终点 - 松弛操作
for (int p = 1; p <= n; p++) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            w[i][j] = Math.min(w[i][j], w[i][p] + w[p][j]);
        }
    }
}
```

Dijkstra(find the shortest path between a specific node a to any node b)

```java
// dist[x] = y 代表从「源点/起点」到 x 的最短距离为 y
int[] dist = new int[N];
// 记录哪些点已经被更新过
boolean[] vis = new boolean[N];
Arrays.fill(vis, false);
Arrays.fill(dist, INF);
// 只有起点最短距离为 0
dist[k] = 0;
// 迭代 n 次
for (int p = 1; p <= n; p++) {
    // 每次找到「最短距离最小」且「未被更新」的点 t
    int t = -1;
    for (int i = 1; i <= n; i++) {
        if (!vis[i] && (t == -1 || dist[i] < dist[t])) t = i;
    }
    // 标记点 t 为已更新
    vis[t] = true;
    // 用点 t 的「最小距离」更新其他点
    for (int i = 1; i <= n; i++) {
        dist[i] = Math.min(dist[i], dist[t] + w[t][i]);
    }
}
```

## LRU cache

https://leetcode.cn/problems/lru-cache-lcci/

环形双向链表+hash 表：

## LFU cache

https://leetcode.cn/problems/lfu-cache/

需求：

1. update(key, value). If full, remove the least recent item
2. get(key), if key does not exist, return -1

一个map记录key到node节点，另一个map key是频次，value是环形双向链表

## 莫队/回滚莫队

左端点抖动，右端点变大 lc3636(https://leetcode.cn/problems/threshold-majority-queries/description/)

## Ringbuffer

需要push方法，pop方法

### 无同步版

vector，一个head，一个tail，写入head，从tail拿，多余一个格子/或多使用一个size变量用来判断是否已满  
需要lock就在push和pop的时候用lock_guard(mutex)

```c++
#include <vector>
#include <iostream>
#include <optional>
#include <mutex>

using namespace std;
// requirement
// 1. a ringbuffer with capacity of x
// 2. push(if full then abort), pop(if empty then return -1), empty, full
// 3. single consumer single producer, no lock
// 4.
template <typename T>
class LockRingBuffer
{
private:
    int capacity;
    int size;
    vector<T> queue;
    int head;
    int tail;
    mutex mut;

public:
    LockRingBuffer(int _capacity)
    {
        this->capacity = _capacity;
        this->size = 0;
        this->queue = vector<T>(capacity);
        head = 0;
        tail = 0;
    }

    bool push(T val)
    {
        lock_guard<mutex> lock(mut);
        if (head == tail && size > 0)
        {
            return false;
        }
        queue[head++] = val;
        head %= capacity;
        size++;
        return true;
    }

    optional<T> pop()
    {
        lock_guard<mutex> lock(mut);
        if (head == tail && size == 0)
        {
            return -1;
        }
        T ans = queue[tail++];
        tail %= capacity;
        size--;
        return ans;
    }

    bool full()
    {
        return head == tail && size > 0;
    }
    bool empty()
    {
        return head == tail && size == 0;
    }
};
```

### SPSC

head和tail都使用atomic int，写的时候load head是acquire，load tail是relaxed，store tail是release，读的时候load tail是acquire，store head是release

```c++
#include <atomic>
#include <vector>
#include <optional>
#include <cstdint>
template <typename T>
class SPSCRingBuffer
{
private:
    std::vector<T> buffer_;
    size_t capacity_;
    size_t mask_; // 用于位运算取模：capacity - 1 (要求 capacity 是 2 的幂)

    alignas(64) std::atomic<uint64_t> head_{0}; // 单调递增
    alignas(64) std::atomic<uint64_t> tail_{0}; // 单调递增

public:
    SPSCRingBuffer(size_t cap) : buffer_(cap), capacity_(cap), mask_(cap - 1)
    {
        // 确保容量是 2 的幂，以便使用位运算优化
        assert((cap & mask_) == 0);
    }

    bool push(const T &item)
    {
        // 1. 读取当前的 head 和 tail
        uint64_t current_tail = tail_.load(std::memory_order_relaxed);
        uint64_t current_head = head_.load(std::memory_order_acquire);

        // 2. 判满：如果差值等于容量，说明满了
        if (current_tail - current_head >= capacity_)
        {
            return false;
        }

        // 3. 计算物理位置 (使用位与代替取模)
        size_t idx = current_tail & mask_;
        buffer_[idx] = item;

        // 4. 推进 tail
        tail_.store(current_tail + 1, std::memory_order_release);
        return true;
    }

    std::optional<T> pop()
    {
        // 1. 读取当前的 head 和 tail
        uint64_t current_head = head_.load(std::memory_order_relaxed);
        uint64_t current_tail = tail_.load(std::memory_order_acquire);

        // 2. 判空
        if (current_tail - current_head == 0)
        {
            return std::nullopt;
        }

        // 3. 计算物理位置
        size_t idx = current_head & mask_;
        T item = buffer_[idx];

        // 4. 推进 head
        head_.store(current_head + 1, std::memory_order_release);
        return item;
    }
};
```

### MPMC

因为可能多个线程同时拿到tail的值，所以需要引入slot 机制，生产者需通过CAS更新slot状态，CAS成功的那个生产者才拥有写入权

```c++
template <typename T>
class MPMCRingBuffer
{
private:
    // 为每个原子变量进行缓存行对齐，防止伪共享
    struct alignas(64) AlignedAtomicSize
    {
        std::atomic<size_t> value;
        AlignedAtomicSize() : value(0) {}
    };

public:
    explicit MPMCRingBuffer(size_t capacity)
        : capacity_(capacity),
          buffer_(capacity),
          slots_(capacity)
    {
        // 初始化槽位序列号：slot[i] = i
        for (size_t i = 0; i < capacity_; ++i)
        {
            slots_[i].value.store(i, std::memory_order_relaxed);
        }
        head_.store(0, std::memory_order_relaxed);
        tail_.store(0, std::memory_order_relaxed);
    }

    bool push(const T &item)
    {
        size_t pos;
        while (true)
        {
            pos = tail_.load(std::memory_order_relaxed);
            size_t idx = pos % capacity_;
            // 读取该槽位的序列号
            size_t seq = slots_[idx].value.load(std::memory_order_acquire);

            // 检查序列号是否匹配，判断槽位是否可用
            intptr_t diff = (intptr_t)seq - (intptr_t)pos;
            if (diff == 0)
            {
                // 尝试抢占 tail 指针
                if (tail_.compare_exchange_weak(pos, pos + 1,
                                                std::memory_order_relaxed, std::memory_order_relaxed))
                {
                    break; // 抢占成功
                }
            }
            else if (diff < 0)
            {
                // 队列满
                return false;
            }
            // 如果 diff > 0，说明 tail 落后了，重试
        }

        // 写入数据
        buffer_[pos % capacity_] = item;
        // 更新槽位序列号，标记为“已填充”
        slots_[pos % capacity_].value.store(pos + 1, std::memory_order_release);
        return true;
    }

    std::optional<T> pop()
    {
        size_t pos;
        while (true)
        {
            pos = head_.load(std::memory_order_relaxed);
            size_t idx = pos % capacity_;
            size_t seq = slots_[idx].value.load(std::memory_order_acquire);

            // 检查序列号是否匹配，判断槽位是否有数据
            intptr_t diff = (intptr_t)seq - (intptr_t)(pos + 1);
            if (diff == 0)
            {
                // 尝试抢占 head 指针
                if (head_.compare_exchange_weak(pos, pos + 1,
                                                std::memory_order_relaxed, std::memory_order_relaxed))
                {
                    break; // 抢占成功
                }
            }
            else if (diff < 0)
            {
                // 队列空
                return std::nullopt;
            }
            // 如果 diff > 0，说明 head 落后了，重试
        }

        T item = buffer_[pos % capacity_];
        // 更新槽位序列号，标记为“已消费”，允许生产者复用
        slots_[pos % capacity_].value.store(pos, std::memory_order_release);
        return item;
    }

private:
    const size_t capacity_;
    std::vector<T> buffer_;
    std::vector<AlignedAtomicSize> slots_;

    alignas(64) std::atomic<size_t> head_;
    alignas(64) std::atomic<size_t> tail_;
};
```

## Lock free queue
