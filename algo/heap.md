## 什么是堆

1. 完全二叉树
2. Parent > child



## 除了根节点，底部已经是堆


![image-20200701211405181](https://i.loli.net/2020/07/01/6YDOeUykVpluxjn.png)

堆化过程：自顶向下，从左到右

![image-20200701213259518](https://i.loli.net/2020/07/01/r1uMLABfz3eSvyZ.png)

1. 自根节点开始，与子节点比较交换，保证当前节点最大。
2. 对被替换的子节点，继续做heapify
3. 直到叶子结点



## 整个树都不是堆

![image-20200701214219740](https://i.loli.net/2020/07/01/PeaWSc4kjKX9MUT.png)

自底向上，从右到左。逐步堆化。



## 怎样表示一个堆

![image-20200701214655975](https://i.loli.net/2020/07/01/polibXQUBcFYK58.png)

 用数组存储，自顶向下，从左到右。

`arr = [10,5,8,3,4,6,7,1,2]`

- 因为是完全二叉树，有`不间断`的特征。所以可以用数组来表示

- 对任一结点位置`i`有：
  - `parent = (i-1) / 2`
  - `child1 = 2i + 1`
  - `child2 = 2i + 2`



## 代码

### heapify 自顶向下,从左到右

- 除了根节点，基本是堆

```c
void heapify(int tree[], int n, int i) {
  // 递归出口
  if (i >= n) {
    return;
  }
  
  int c1 = 2 * i + 1;
  int c2 = 2 * i + 2;
  int max = i;
  if (c1 < n && tree[c1] > tree[max]) {
    max = c1;
  }
  if (c2 < n && tree[c2] > tree[max]) {
    max = c2;
  }
  if (max != i) {
    swap(tree, max, i);
    heapify(tree, n, max); // max被替换为较大的【子节点】位置
  }
}

void swap (int arr[], int i, int j) {
  int temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```



### build_heap 自底向上，从右到左

- 从头构建堆

![image-20200701221106863](https://i.loli.net/2020/07/01/9gCfNulbe6kcOhE.png)

```c
void build_heap(int tree[], int n) {
  int last_node = n - 1;
  int parent = (last_node - 1) / 2;
  int = i;
  for (i = parent; i >= 0; i--) {
    heapify(tree, n, i);
  }
}
```



### heap_sort 堆排序

1. 根节点和末尾节点交换位置
2. 将末尾节点砍断
3. 对根节点作`heapify`
4. 重复以上操作

![image-20200701231658453](https://i.loli.net/2020/07/01/nP8awYBjzFQiJXh.png)

```c
void heap_sort(int tree[], int n) {
  build_heap(tree, n);
  int i;
  for (i = n - 1; i >= 0; i--) {
    swap(tree, i, 0); // 0根节点，i末尾节点。 0-i交换
    heapify(tree, i, 0); // 砍断，不必写出来，其实i节点后就是【已排好序并不包含在堆里(被砍断)的节点】
  }
}
```



## 力扣题
- [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)