从2020年10月27开始，每天学习的总结

# 算法

### 二叉树的前序遍历

Leetcode104:给定一个二叉树，返回它的 *前序* 遍历。

#### 递归

```java
class Solution {
    List<Integer> ans=new ArrayList();
    public List<Integer> preorderTraversal(TreeNode root) {
        if(root==null) return ans;
        dfs(root);
        return ans;
    }
  public void dfs(TreeNode root)
  {
    if(root==null) return ;
    ans.add(root.val);
    dfs(root.left);
    dfs(root.right);
  }
}
```

#### 非递归

```java
class Solution {
    List<Integer> ans=new ArrayList();
    public List<Integer> preorderTraversal(TreeNode root) {
        if(root==null) return ans;
        Deque<TreeNode> stack=new LinkedList();
        while(!stack.isEmpty()||root!=null)
        {
            while(root!=null)
            {
                ans.add(root.val);
                stack.push(root);
                root=root.left;
            }
            root=stack.pop();
            root=root.right;   
        }
        return ans;
    }
    
}
```

或者

```java
class Solution {
    List<Integer> ans=new ArrayList();
    public List<Integer> preorderTraversal(TreeNode root) {
        if(root==null) return ans;
        Deque<TreeNode> stack=new LinkedList();
        stack.push(root);
        while(!stack.isEmpty())
        {
            TreeNode node=stack.pop();
            ans.add(node.val);
            if(node.right!=null)
                stack.push(node.right);
            if(node.left!=null)
                stack.push(node.left);
        }
        return ans;
    }
}
```

# 前端

### video

标签播放视频

### audio 

播放音频

### Canvas

绘制画布，注意Canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成

首先获取DOM,创建画布 getContext('2d');