# python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findBottomLeftValue(self, root: Optional[TreeNode]) -> int:
        q =deque([root])
        while q:
            node = q.popleft()
            if node.right:
                q.append(node.right)
            if node.left:
                q.append(node.left)
            ans = node.val
        return ans
```



# c++

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        int ret;
        queue<TreeNode *> q;
        q.push(root);
        while(!q.empty()) {
            auto p = q.front();
            q.pop();
            if (p->right) {
                q.push(p->right);
            }
            if(p->left){
                q.push(p->left);
            }
            ret = p->val;
        }
        return ret;
    }
};
```



# go

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func findBottomLeftValue(root *TreeNode) (ans int) {
    q := []*TreeNode{root}
    for len(q) > 0 {
        node := q[0]
        q = q[1:]
        if node.Right != nil {
            q = append(q, node.Right)
        }
        if node.Left != nil {
            q = append(q, node.Left)
        }
        ans = node.Val
    }
    return ans
}
```



# Attention

```bash
## 为什么 python的res可以被while外部访问
循环在python中不形成作用域


```



