- [x] BFS框架

```java
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
  Queue<Node> q; // 核心数据结构
  Set<Node> visited; // 避免走回头路
  
  q.offer(start); // 从起点加入队列
  visited.add(start);
  int step = 0; // 记录扩散的步数
  
  while (q not empty) {
    int sz = q.size();
    /* 将当前队列中的所有节点向四周扩散 */
    for (int i = 0; i < sz; i++) {
      Node cur = q.poll();
      /* 划重点：这里判断是否到达终点 */
      if (cur is target)
        return step;
      /* 将 cur 的相邻节点加入队列 */
      for (Node x: cur.adj())
        if (x not in visited) {
          q.offer(x);
          visited.add(x);
        }
    }
    /* 划重点：更新步数在这里 */
    step++;
  }
}
```



- [x] #### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

```java
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode*> q;
        q.push(root);
        int depth = 1;

        while(!q.empty()) {
            int sz = q.size();
            for (int i=0; i<sz; i++){
                TreeNode* curr = q.front(); q.pop();
                if(!curr->left && !curr->right) return depth;
                if(curr->left) q.push(curr->left);
                if(curr->right) q.push(curr->right);
            }
            depth++;
        }
        return depth;
    }
}
```



- [x] #### [752. 打开转盘锁](https://leetcode-cn.com/problems/open-the-lock/)



