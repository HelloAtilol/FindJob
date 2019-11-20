# leetcode


1. 两数之和
> 给定一个整数数组`nums`和一个目标值`target`，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
> 你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

> 示例：
>> 给定 nums = [2, 7, 11, 15], target = 9	
>> 因为 nums[0] + nums[1] = 2 + 7 = 9
>> 所以返回 [0, 1]

**思路：**
	- 穷举法，把所有的可能的组合全部列出，直到找到答案，时间复杂度: $O(n^2)$，空间复杂度为：$ O(1) $.
	- 哈希求解，只遍历一遍数组，建立`{target-nums[i], i}`的字典，然后每到一个值，就检查数值是否在字典中；时间复杂度为：$O(n)$，空间复杂度为：$O(n)$

**代码：**
哈希求解

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        res = {}
        for i in range(len(nums)):
            if nums[i] not in res:
                res[target-nums[i]] = i
            else:
                return [res[nums[i]], i]
```

2. 两数相加
> 给出两个非空的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。
> 如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。
> 您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

> 示例:
>> 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
>> 输出：7 -> 0 -> 8
>> 原因：342 + 465 = 807

**难点：**
	- 大于等10时，进位的处理

**代码：**
```python
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        so = Solution()
        s = l1.val + l2.val  
        if l1.next is None and l2.next is not None:
            l1.next = ListNode(0)
        elif l1.next is not None and l2.next is None:
            l2.next = ListNode(0)
        if l1.next is None and l2.next is None:
            if s > 9:
                res = ListNode(s-10)
                res.next = ListNode(1)
            else:
                res = ListNode(s)
            return res
        if s > 9:
            res = ListNode(s-10)
            l1.next = so.addTwoNumbers(l1.next, ListNode(1))
            res.next = so.addTwoNumbers(l1.next, l2.next)
        else:
            res = ListNode(s)
            res.next = so.addTwoNumbers(l1.next, l2.next)
        return res
```

3. 无重复字符的最长子串
> 给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

> 示例1 :
>> 输入: "abcabcbb"
>> 输出: 3 
>> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

> 示例2 :
>> 输入: "bbbbb"
>> 输出: 1
>> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

**思路：**
	- 穷举法，列举出所有不重复子串，选择最长，时间复杂度$ O(n^2) $；
	- 滑动窗口，其实就是一个队列,比如例题中的`abcabcbb`，进入这个队列（窗口）为`abc` 满足题目要求，当再进入`a`，队列变成了`abca`，这时候不满足要求。所以，我们要移动这个队列！只需要去掉窗口左边的元素即可。时间复杂度 $ O(n) $

**代码：**
滑动窗口
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left = 0
        lookup = set()
        max_len = 0
        cur_len = 0
        for i in range(len(s)):
            cur_len += 1
            while s[i] in lookup:
                lookup.remove(s[left])
                left += 1
                cur_len -= 1
            lookup.add(s[i])
            max_len = max(max_len, cur_len)
        return max_len
```

4. 寻找两个有序数组的中位数
> 给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。
> 请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。
> 你可以假设 nums1 和 nums2 不会同时为空。

> 示例1 :
>> nums1 = [1, 3] 
>> nums2 = [2]
>> 则中位数是 2.0

> 示例2 :
>> nums1 = [1, 2]
>> nums2 = [3, 4]
>> 则中位数是 (2 + 3)/2 = 2.5

**思路：**
	- 合并两个数组，取中间的数，时间复杂度(最坏)：$ O(nlog(n)) $，空间复杂度：$ O(m+n) $ 
	- 双指针，
	- 做简单排序，只需要取第$ (m+n)/2 $ 个数；时间复杂度：$O(log(m+n))$

**代码：**
滑动窗口
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left = 0
        lookup = set()
        max_len = 0
        cur_len = 0
        for i in range(len(s)):
            cur_len += 1
            while s[i] in lookup:
                lookup.remove(s[left])
                left += 1
                cur_len -= 1
            lookup.add(s[i])
            max_len = max(max_len, cur_len)
        return max_len
```
