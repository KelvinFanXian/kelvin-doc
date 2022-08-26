- [二分查找细节详解](https://blog.csdn.net/asmartkiller/article/details/96179338)

Although the basic idea of binary search is comparatively straitforward.  the detail can be surprisingly tricky.

- 框架

```.java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = (right + left) / 2; 
        // int mid = ((right - left) >> 1) + left;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```



- 不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节
- 计算 mid 时需要技巧**防止溢出**，即 mid=left+(right-left)/2



## 搜索区间

1. ###### 为什么 while 循环的条件中是 <=，而不是 < ？

2. ###### 为什么 left = mid + 1，right = mid - 1？

#### 寻找左侧边界的二分搜索

```java
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0;
    int right = nums.length; // 注意
    
    while (left < right) { // 注意
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意
        }
    }
    return left;
}
```

1. 为什么 while(left < right) 而不是 <= ?
   答：用相同的方法分析，因为 right = nums.length 而不是 nums.length - 1。因此每次循环的「搜索区间」是 [left, right) 左闭右开。
   while(left < right)终止的条件是 left == right，此时搜索区间 [left, left) 为空，所以可以正确终止。

2. 为什么没有返回 -1 的操作？如果 nums 中不存在 target 这个值，怎么办？

3. 为什么 left = mid + 1，right = mid ？和之前的算法不一样？

4. 为什么该算法能够搜索左侧边界？

5. 为什么返回 left 而不是 right？

   因为 while 终止的条件是 left == right。



### 寻找右侧边界的二分搜索

```java
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;
    
    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1; // 注意
}
```

1. 为什么这个算法能够找到右侧边界？

2. 为什么最后返回 left - 1 而不像左侧边界的函数，返回 left？而且我觉得这里既然是搜索右侧边界，应该返回 right 才对。

3. 为什么没有返回 -1的操作？如果 nums 中不存在 target 这个值，怎么办？
   答：类似之前的左侧边界搜索，因为 while 的终止条件是 left == right，就是说 left 的取值范围是 [0, nums.length]，所以可以添加两行代码，正确地返回 -1

   ```java
   while (left < right) {
       // ...
   }
   if (left == 0) return -1;
   return nums[left-1] == target ? (left-1) : -1;
   ```

   