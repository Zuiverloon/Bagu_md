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
LinkedList: save elements in distributed space. Length not fixed. 查找 O(1)，增删 O(n)  
如果要频繁查找，且知道长度，用线性表
要频繁插入，不知道长度，用单链表

### 环形链表 circularLinkedList

### 双向链表 doublyLinkedList(prev,val,next)

### 顺序查找 sequential search

### 二分查找 binary search

### 平方 the square of 立方 the cubic power of 四次方 the fourth power of 二进制 binary 十进制 decimal 十六进制 hexadecimal 八进制 octal 分治法 divide and conquer

## Sort

### **insertion sort O(n2)**

Step 1 - If the element is the first element, assume that it is already sorted. Return 1.  
Step2 - Pick the next element, and store it separately in a key.  
Step3 - Now, compare the key with all elements in the sorted array.  
Step 4 - If the element in the sorted array is smaller than the current element, then move to the next element. Else, shift greater elements in the array towards the right.  
Step 5 - Insert the value.

### **selection sort O(n2)**

firstly, go thought all elements to see the smallest element, then swap it with the first element  
then, go through all element from the second to the end to see the second smallest element, then swap

### **bubble sort O(n2)**

compare the neighbour elements to see whether they need to swap.

### **quicksort O(nlogn)**

选出一个 pivot value, put elements smaller than pivot to the left side, put elements greater than pivot value to the right side, recursively do sort on the left side and the right side.

    void quicksort(arr,l,r){
        int pivot = a[l];
        int i = l,j = r;
        if (i<r){
            while (i<j){
                while (i<=j && t<a[j]){
                    j--;
                }
                if (i<j) a[i++] = a[j];
                while (i<=j && t>a[i]){
                    i++;
                }
                if (i<j) a[j--] = a[i];
            }
            a[i] = t;
            quicksort(arr,l,i-1);
            quicksort(arr,i+1,r);
        }
    }

### **merge sort O(nlogn)**

(分治法)divide the given list into 2 halves, sort the 2 halves and merge them together, recursively

    sort(arr,left,right){
        if (left<right){
            int mid = (left+right)/2;
            sort(arr,left,mid);
            sort(arr,mid,right);
            merge(arr,left,mid,right);
        }
    }

    merge(arr,l,m,r){
        ...
    }

### **heapsort O(nlogn)**

make a heap to get the top value
recursively find the top in each loop

## tree

节点(node) 父节点(parent node) 子节点(child node) 同胞节点(sibling node) 叶子节点(leaf node) 中间节点(internal node) 路径(path) 边(edge)
前序遍历(preorder,左，右，自己)、后序遍历(postorder，右，左，自己)、中序遍历(inorder 左，自己、右)、层次遍历(非递归，依靠队列实现)
二叉树(binary tree)
