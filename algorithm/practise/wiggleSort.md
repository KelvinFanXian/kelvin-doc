# python

```python
class Solution:
    def wiggleSort(self, nums: List[int]) -> None:
        n = len(nums)
        arr = sorted(nums)
        x = (n + 1) // 2
        j, k = x - 1, n - 1
        for i in range(0, n, 2):
            nums[i] = arr[j]
            if i + 1 < n:
                nums[i + 1] = arr[k]
            j -= 1
            k -= 1
```



# java

```java
class Solution {
    public void wiggleSort(int[] nums) {
        int[] arr = nums.clone();
        Arrays.sort(arr);
        int n = nums.length;
        int x = (n + 1) / 2;
        for (int i = 0, j = x - 1, k = n - 1; i < n; i += 2, j--, k--) {
            nums[i] = arr[j];
            if (i + 1 < n) {
                nums[i + 1] = arr[k];
            }
        }
    }
}
```



# js

```js
var wiggleSort = function(nums) {
    const arr = nums.slice(); // shallow copy [x, y)
    arr.sort((a, b) => a - b);
    const n = nums.length;
    const x = Math.floor((n + 1) / 2);
    for (let i = 0, j = x - 1, k = n - 1; i < n; i += 2, j--, k--) {
        nums[i] = arr[j];
        if (i + 1 < n) {
            nums[i + 1] = arr[k];
        }
    }
};
```





# c++

```c++
class Solution {
public:
    void wiggleSort(vector<int>& nums) {
        int n = nums.size();
        vector<int> arr = nums;
        sort(arr.begin(), arr.end());
        int x = (n + 1) / 2;
        for (int i = 0, j = x - 1, k = n - 1; i < n; i += 2, j--, k--) {
            nums[i] = arr[j];
            if (i + 1 < n) {
                nums[i + 1] = arr[k];
            }
        }
    }
};
```



# go

```go
func wiggleSort(nums []int) {
    n := len(nums)
    arr := append([]int{}, nums...)
    sort.Ints(arr)
    x := (n + 1) / 2
    for i, j, k := 0, x-1, n-1; i < n; i += 2 {
        nums[i] = arr[j]
        if i+1 < n {
            nums[i+1] = arr[k]
        }
        j--
        k--
    }
}
```

