## 无后效性

- [动态规划、中心扩散、Manacher 算法](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)

![image-20200521103822372](https://i.loli.net/2020/05/21/XK7VUnNjkPaJZQs.png)



## 理解

- 先内for，后外for
- 先箭头方向，后下一个箭头

- dp\[m]\[n]    for i in m, for j in n  

---

## 算法图解

- 9.2.7 处理相互依赖的情况
  - 问题可分解为离散子问题时，可使用动态规划来解决
- 9.2.8 计算最终的解时会涉及两个以上的子背包吗
- 9.3 最长公共子串
- 9.3.1 绘制网格
  - 单元格中的值是什么？
  - 如何将这个问题划分为子问题？
  -  网格的坐标轴是什么？
- 9.3 最长公共**子串**
  - 最终答案并不在最后一个单元格中
  - 如何计算最长公共**子序列**呢？
- 9.3.5 最长公共**子序列**之解决方案

## tips

- 网格
- **单元格**中的值通常就是你要优化的值
- 每个单元格都是一个子问题
  - 如何将问题分成子问题，这有助于你找出网格的坐标轴



## 力扣题

- [115. 不同的子序列](https://leetcode-cn.com/problems/distinct-subsequences/solution/bu-tong-de-zi-xu-lie-by-leetcode-solutio-urw3/)

- #### [879. 盈利计划](https://leetcode-cn.com/problems/profitable-schemes/)

- #### [63. 不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

