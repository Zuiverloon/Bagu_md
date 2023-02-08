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

思想：当匹配失败时，找 pattern 串的相同前缀和后缀  
abeababeabf  
 ｜  
abeabf 此时(a,f)失败，但有相同前缀后缀 ab，因此转变为  
ababeabf  
abeabf 此时(a,e)失败，没有相同前缀后缀，移动匹配串到  
abeabf  
abeabf

```java
public static int[] getkmpnext(String pattern){
    int[] next = new int[pattern.length()];
    char[] c = pattern.toCharArray();
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

public static int kmp(String s,String pattern){
    char[] sc = s.toCharArray();char[] pc = pattern.toCharArray();
    int[] next = getkmpnext(pattern);
    int n = s.length();
    int m = pattern.length();
    for (int i = 0,j = 0;i<n;i++){
        while (j>0 && sc[i]!=pc[j])j = next[j-1];
        if (sc[i] == pc[j])j++;
        if (j == m)return i-m+1;
    }
    return -1;
}
```

## Sort

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

选出一个 pivot value, put elements smaller than pivot to the left side, put elements greater than pivot value to the right side, recursively do sort on the left side and the right side.

```java
public static void quicksort(int[] arr,int left,int right){
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

(分治法)divide the given list into 2 halves, sort the 2 halves and merge them together, recursively

```java
public static void merge(int[] arr,int low, int mid,int high,int[] tmp){
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

    public static void mergesort(int[] arr,int low,int high,int[] tmp){
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
public static void heapsort(int[] arr){
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

    public static void heapify(int[] arr,int i,int right){
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

环形双向链表+hash 表：https://leetcode.cn/problems/lru-cache-lcci/
