---
layout: post
title: A Day with Permutations
author: 'Xin Hong'
excerpt_separator: <!--more-->
tags: "leetcode"
---

Four leetcode problems that play with permutation.
<!--more-->

[46. Permutations](https://leetcode.com/problems/permutations/)
Two ways of backtracking — tracking what’s left using `ArrayList<Integer> left_nums`, and using visited array.

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        var res = new ArrayList<List<Integer>>();
		  // 1st
        // var left_nums = new ArrayList<Integer>(nums);
        // List<Integer> left_nums = Arrays.stream(nums).boxed().collect(Collectors.toList());
        // backtracking(new ArrayList<Integer>(left_nums), new ArrayDeque<Integer>(), res);

		  // 2nd
        boolean[] visited = new boolean[nums.length];
        backtracking1(nums, visited, new ArrayDeque<Integer>(), res);

        return res;
    }
    public void backtracking(ArrayList<Integer> left_nums, ArrayDeque<Integer> curr_list, List<List<Integer>> res) {
        if (left_nums.size() == 0) {
            res.add(new ArrayList<Integer>(curr_list));
        }
        for (int i = 0; i < left_nums.size(); i++) {
            curr_list.push(left_nums.get(i));
            var tempList = new ArrayList<Integer>(left_nums);
            tempList.remove(i);
            backtracking(tempList, curr_list, res);
            curr_list.pop();
        }
    }

// visited array
    public void backtracking1(int[] nums, boolean[] visited, ArrayDeque<Integer> curr, List<List<Integer>> res) {
        if (curr.size() == nums.length) {
            res.add(new ArrayList<Integer>(curr));
        }
        for (int i = 0; i < nums.length; i++) {
            if (visited[i]) {
                continue;
            }
            visited[i] = true;
            curr.push(nums[i]);
            backtracking1(nums, visited, curr, res);
            visited[i] = false;
            curr.pop();
        }
    }
}
```

[31. Next Permutation](https://leetcode.com/problems/next-permutation/)

The key is to find the “pivotal” element in the given array. That is, the element that’s the peek — it’s the first non-ascending element counting from the back.

Say we have an array of `n` elements -- $a_1, a_2, a_3, …, a_n$.
Let `p` denote it’s index, then it satisfies that:
$$a_{p-1} < a_p  >= a_{p+1} >= a_{p+2} >= … >= a_n$$.

Now, we can’t just simply swap $a_p$ and $a_{p-1}$, because there might be elements of index $p+1 ~ n$ that’s greater than $a_{p-1}$.

Therefore, we have to find $q \in p+1 ~ n$ such that $a_q$ is the least elements in $a_{p+1} ~ a_{n}$ that’s greater than $a_p$.

After we swap $a_{p-1}$ and $a_{q}$, $a_{p} ~ a_{n}$ is in descending order, we than **reverse** $a_{p} ~ a_{n}$ to generate the next permutation. It’s indeed the **next** one.

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        int p = n - 1;
        // finds the peek element from rear to front
        while (p >= 0) {
            if (p > 0 && nums[p-1] >= nums[p]) {
                p--;
            }
            else {
                break;
            }
        }
        if (p == 0) {
            reverseArray(nums, 0, n-1);
        }
        else {
            // v p v2 v3 v4 u ...
            // traverse back to find the first element after pivot that's bigger than v1 (say it's u)
            // swap u and v
            // reverseArray(nums, p, n-1);
            int q = p;
            while (q < n && nums[q] > nums[p-1]) {
                q++;
            }
            // now q-1 is the index we desire
            // p-1 pivot ... q-1 q
            // swap nums[p-1] and nums[q-1]
            swap(nums, p-1, q-1);
            // System.out.println(Arrays.toString(new int[] {p, q}));
            // System.out.println(Arrays.toString(nums));
            reverseArray(nums, p, n-1);
        }
    }

    private void reverseArray(int[] nums, int startIndex, int endIndex) {
        int n = nums.length;
        int p = startIndex, q = endIndex;
        int temp;
        while (p <= q) {
            temp = nums[p];
            nums[p] = nums[q];
            nums[q] = temp;
            p++;
            q--;
        }
    }
    private void swap(int[] nums, int p, int q) {
        int temp = nums[p];
        nums[p] = nums[q];
        nums[q] = temp;
    }
}
```

[47. Permutations II](https://leetcode-cn.com/problems/permutations-ii/)

The key is we sort the array, and for elements with the same value, we fixed their order appeared in the permutation. It’s like a stable sorts. If we labeled the elements, the relative position of repeating elements will not change.

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        var res = new ArrayList<List<Integer>>();
        boolean[] visited = new boolean[nums.length];
        Arrays.sort(nums);
        backtracking(nums, visited, new ArrayDeque<Integer>(), 0, res);
        return res;
    }
    private void backtracking(int[] nums, boolean[] visited, ArrayDeque<Integer> currPerm, int l, ArrayList<List<Integer>> res) {
        if (l == nums.length) {
            res.add(new ArrayList<Integer>(currPerm));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            // we are only going to visit nums[i] when visited[i-1] is true where nums[i] == nums[i-1]
            // this limits duplicated permutations.
            if (visited[i] || (i > 0 && nums[i] == nums[i-1] && !visited[i-1])) {
                continue;
            }
            // if (!visited[i] || (i > 0 && nums[i] != nums[i-1])) {
                visited[i] = true;
                currPerm.push(nums[i]);
                backtracking(nums, visited, currPerm, l+1, res);
                currPerm.pop();
                visited[i] = false;
            // }
        }
    }
}
```


[60. Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)

One way is to use what we have in the next permutation (i.e. brute force). We actually need to fetch the elements one by one and reduced the problem. Use a visited array, we are able to track what elements are available.

```java
class Solution {
    public String getPermutation(int n, int k) {
        boolean [] visited = new boolean[n+1];
        var sb = new StringBuilder();
        int nextNumPlace, level;
        level = 0;
        while (level < n) {
            int f = factorial(n-level-1);
            nextNumPlace = (k-1)/f + 1; // 1 ~ n
            int p = nextNumPlace;
            for (int i = 1; i <= n; i++) {
                if (visited[i]) {
                    continue;
                }
                if (p == 1) {
                    sb.append(i);
                    visited[i] = true;
                    break;
                }
                if (!visited[i]) {
                    p--;
                }
            }
            k -= (nextNumPlace-1) * f;
            level++;
        }
        return sb.toString();
    }
    private int factorial(int num) {
        int res = 1;
        for (int i = 1; i <= num; i++) {
            res *= i;
        }
        return res;
    }
}
```
