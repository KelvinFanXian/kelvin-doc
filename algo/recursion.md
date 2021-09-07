## [486. 预测赢家](https://leetcode-cn.com/problems/predict-the-winner/)

- **关注当前。屏蔽掉递归的细节**，那是丢给子调用完成的

  - 关注当前，当前的 xx 分，想想**子调用应该返回什么**去和它作比较，才能判断获胜。
  - 于是，递归函数做的事：计算当前做选择的玩家赢过对手的分数。如果大于零，则代表他在这个子问题中赢了。

  - 怎么计算呢？当前选择的分数，减去，往后对手赢过自己的分数（对剩余数组递归）。因为选择有两种，所以有两种可能的差值，取较大的进行判断。

```python
class Solution:
    def PredictTheWinner(self, nums: List[int]) -> bool:
        def helper(i, j): # i，j是两端的索引
            if i==j: # 递归的出口，此时只有一个选择，并且没有剩余的可选
                return nums[i]
            pickI = nums[i] - helper(i+1, j) #选择左端
            pickJ = nums[j] - helper(i, j-1) #选择右端
            return max(pickI, pickJ) #取较大者
        return helper(0, len(nums) - 1) >= 0
```

