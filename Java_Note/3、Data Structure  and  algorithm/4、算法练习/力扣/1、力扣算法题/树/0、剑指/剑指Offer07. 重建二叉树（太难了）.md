# 剑指Offer07. 重建二叉树（太难了）
--------------------------------------------------------------------------------
# 一、题目
输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。


```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

    3
   / \
  9  20
    /  \
   15   7
```



# 二、解题方法

## 递归法
### 1、解题思路：
#### 题目分析：
> 前序遍历特点： 节点按照 [ 根节点 | 左子树 | 右子树 ] 排序，以题目示例为例：[ 3 | 9 | 20 15 7 ]
>
> 中序遍历特点： 节点按照 [ 左子树 | 根节点 | 右子树 ] 排序，以题目示例为例：[ 9 | 3 | 15 20 7 ]
>
> 根据题目描述输入的前序遍历和中序遍历的结果中都不含重复的数字，其表明树中每个节点值都是唯一的。

- 根据以上特点，可以按顺序完成以下工作：
  - 前序遍历的首个元素即为根节点 root 的值；
  - 在中序遍历中搜索根节点 root 的索引 ，可将中序遍历划分为 [ 左子树 | 根节点 | 右子树 ] 。
  - 根据中序遍历中的左（右）子树的节点数量，可将前序遍历划分为 [ 根节点 | 左子树 | 右子树 ] 。
- 自此可确定 三个节点的关系 ：1.树的根节点、2.左子树根节点、3.右子树根节点（即前序遍历中左（右）子树的首个元素）。

> 子树特点： 子树的前序和中序遍历仍符合以上特点，以题目示例的右子树为例：前序遍历：[20 | 15 | 7]，中序遍历 [ 15 | 20 | 7 ] 。
>

- 根据子树特点，我们可以通过同样的方法对左（右）子树进行划分，**每轮可确认三个节点的关系** 。此递推性质让我们联想到用 递归方法 处理。


## 2、递归解析：
- 递推参数： 前序遍历中根节点的索引pre_root、中序遍历左边界in_left、中序遍历右边界in_right。
- 终止条件： 当 `in_left > in_right `，子树中序遍历为空，说明已经越过叶子节点，此时返回 nullnull 。
- 递推工作：
  - 建立根节点root： 值为前序遍历中索引为pre_root的节点值。
  - 搜索根节点root在中序遍历的索引i： 为了提升搜索效率，本题解使用哈希表 dic 预存储中序遍历的值与索引的映射关系，每次搜索的时间复杂度为 O(1)O(1)。
  - 构建根节点root的左子树和右子树： 通过调用 recur() 方法开启下一层递归。
    - 左子树： 根节点索引为 pre_root + 1 ，中序遍历的左右边界分别为 in_left 和 i - 1。
    - 右子树： 根节点索引为 i - in_left + pre_root + 1（即：根节点索引 + 左子树长度 + 1），中序遍历的左右边界分别为 i + 1 和 in_right。
- 返回值： 返回 root，含义是当前递归层级建立的根节点 root 为上一递归层级的根节点的左或右子节点。

## 3、代码实现

```java
class Solution {
    HashMap<Integer, Integer> dic = new HashMap<>();
    int[] po;
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        po = preorder;
        for(int i = 0; i < inorder.length; i++) 
            dic.put(inorder[i], i);
        return recur(0, 0, inorder.length - 1);
    }
    TreeNode recur(int pre_root, int in_left, int in_right) {
        if(in_left > in_right) return null;
        TreeNode root = new TreeNode(po[pre_root]);
        int i = dic.get(po[pre_root]);
        root.left = recur(pre_root + 1, in_left, i - 1);
        root.right = recur(pre_root + i - in_left + 1, i + 1, in_right);
        return root;
    }
}
```





参考：

[二叉树的题，就那几个框架，枯燥至极🤔](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485871&idx=1&sn=bcb24ea8927995b585629a8b9caeed01&chksm=9bd7f7a7aca07eb1b4c330382a4e0b916ef5a82ca48db28908ab16563e28a376b5ca6805bec2&scene=126&sessionid=1595593405&key=f4e53e261e827f63474723697083aca80eb0077fe96ab2f8ca253b74920f0fedad27a3c0c46acf52cdfd1e9c77f95b98b4b9944dec5266fff25ab248b5b89fb24d83ef58d86fbabfa4a7ebb501354b0a&ascene=1&uin=MTg5MjE1NTkyOQ%3D%3D&devicetype=Windows+10+x64&version=62090529&lang=zh_CN&exportkey=A8Aq14DOnHbQQzZ4HXsCaVc%3D&pass_ticket=Q%2FyOxI8Rq%2FVwxMo%2BsOtF0O0p8bsOvb6GlOHSBlCiv0whfNKUcodneQssut9qWY11)