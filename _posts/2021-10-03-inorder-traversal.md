---
layout: post
title: Details of Inorder Traversal (why this way?)
author: 'Xin'
excerpt_separator: <!--more-->
---

After revisiting the inorder traversal (using stack), some if-statements make more sense to me... Here's a wrong version and a corrected version.

<!--more-->

What’s wrong with the following code?
You can paste it in [leetcode no.94](https://leetcode.com/problems/binary-tree-inorder-traversal/), it won’t work because of timeout.
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        // (left), (root), (right)
        var stack = new ArrayDeque<TreeNode>();
        var res = new ArrayList<Integer>();

        while (!stack.isEmpty() || root != null) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            if (!stack.isEmpty()) {
                root = stack.pop();
            }
            if (root != null) {
                res.add(root.val);
                if (root.right != null) {
                    root = root.right;
                }
            }
        }
        return res;
    }
}
```

Take the following example:
		1
				3
			2
1. Init, stack is empty but root is not null.
2. Stack = [], root = 1
	1. Stack = [1], root = null
	2. stack is not empty, root = 1, stack = []
	3. Root != null, res.add(1). Res = [1]
	4. Root.right != null, root = root.right
3. stack = [], root = 3
	1. Stack = [3, 2], root = null
	2. Stack is not empty, root = 2
	3. Root != null, res.add(2), res = [1, 2]
	4. root.right == null, pass
4. Stack = [3], root = 2
	1. stack = [3, 2], root = null (pushed again!!)
	2. Stack is not empty, root = 2
	3. ...

The problem is when root has no left child, we should let `root = root.right`  even though `root.right` might be `null`.
Otherwise,
1. `root` will not be `null` and therefore the subroutine will never end.
2. When `root` is not `null`, we add it in the stack at the beginning, then we pop the stack, retrieving the **same** node, and it’s going on and on…

Further more, it seems logical to add `if (!stack.isEmpty())` before we pop the stack, but come to think of it, we don’t actually need this. While entering the subroutine, the conditions ensure that:
* stack is not empty OR node is not null.
If stack is not empty, we don’t need the `if` statement. However, even when stack **is empty**, node is not null. Therefore, it will be **pushed** to the stack, which results a non-empty stack. Therefore, either way, the stack is not empty.

If the stack is not empty, we will surely get a non-null node after `root = stack.pop()`, thus even the `if (root != null)` is excessive.

The code can be corrected and simplified as:

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        // left, root, right

        var stack = new ArrayDeque<TreeNode>();
        var res = new ArrayList<Integer>();

        while (!stack.isEmpty() || root != null) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
			  // At this point, stack can't be empty.
            root = stack.pop();
            res.add(root.val);
			  // if root.right == null, it's fine.
            root = root.right;
        }
        return res;
    }
}
```
