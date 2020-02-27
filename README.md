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
	- 双指针+二分法

**代码：**
双指针和二分法
```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        n1 = len(nums1)
        n2 = len(nums2)
        if n1 > n2:
            return self.findMedianSortedArrays(nums2,nums1)
        k = (n1 + n2 + 1)//2
        left = 0
        right = n1
        while left < right :
            m1 = left +(right - left)//2
            m2 = k - m1
            if nums1[m1] < nums2[m2-1]:
                left = m1 + 1
            else:
                right = m1
        m1 = left
        m2 = k - m1
        c1 = max(nums1[m1-1] if m1 > 0 else float("-inf"), nums2[m2-1] if m2 > 0 else float("-inf") )
        if (n1 + n2) % 2 == 1:
            return c1
        c2 = min(nums1[m1] if m1 < n1 else float("inf"), nums2[m2] if m2 <n2 else float("inf"))
        return (c1 + c2) / 2
```


5. 最长回文子串
> 给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

> 示例1 :
>> 输入: "babad"
>> 输出: "bab"
>> 注意: "aba" 也是一个有效答案。

> 示例2 :
>> 输入: "cbbd"
>> 输出: "bb"

**思路：**
	- 暴力求解...python会超时
	- 中心扩散法，选中一个字符，向两边扩散，寻找最优解，时间复杂度为$ O(n^2) $
	- Manacher算法，专门解决最长回文子串问题，在中心扩散法的基础上改进，在字符串中插入`#`，使得字符串长度变为奇数，然后理由center和maxRight求解，时间复杂度为$ O(n) $
	- 动态规划，思路在代码，时间复杂度为$ O(n^2) $

**代码：**
动态规划
```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        length = len(s)
        if length <= 1:
            return s
        # 创建一个二维数组，存放动态规划状态，默认为False
        dp = [[False for _ in range(length)] for _ in range(length)]
        # 初始话最大长度和结果
        long_len = 0
        res = s[0]
        for i in range(1, length):
            for j in range(i):
            # 判断s[i]与s[j]是否相等，如果相等，且s[i+1, j-1]是回文字符串，那么dp[j][i]变为True，判断并更新长度
                if s[i]==s[j] and (i-j<=2 or dp[j+1][i-1]):
                    dp[j][i] = True
                    cur_len = i-j+1
                    if cur_len >  long_len:
                        long_len = cur_len
                        res = s[j:i+1]
        return res
```

6. Z字形变换
> 将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。
> 比如输入字符串为 `LEETCODEISHIRING` 行数为 3 时，排列如下：
> L   C   I   R
> E T O E S I I G
> E   D   H   N
> 之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`LCIRETOESIIGEDHN`。

> 示例1 :
>> 输入: `s = "LEETCODEISHIRING", numRows = 3`
>> 输出: `"LCIRETOESIIGEDHN"`

> 示例2 :
>> 输入: `s = "LEETCODEISHIRING", numRows = 4`
>> 输出: `"LDREOEIIECIHNTSG"`

**思路：**
	- 二维数组，例如俄罗斯方块，一层一层铺在数组上。时间复杂度和空间复杂度都为：$ O(n) $

**代码：**

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        a = [[] for i in range(numRows)]
        t = 0
        situ = False
        for i in range(len(s)):
            a[t].append(s[i])
            if t == 0 or t == numRows-1:
                situ = not situ
            t = m(situ, t)
        return ''.join([''.join(n) for n in a])

# 判断是否需要转向
def m(situ, a):
    if situ:
        return a+1
    else:
        return a-1
```

7. 整数反转
> 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

> 示例1 :
>> 输入: 123
>> 输出: 321

> 示例2 :
>> 输入: -123
>> 输出: -321

> 示例3 :
>> 输入: 120
>> 输出: 21

**思路：**
	- 整数除10取整，然后反转；
	- 变为字符串，然后反转；

**代码：**
整数除10取整
```python
class Solution:
    def reverse(self, x: int) -> int:
        r = 0
        m = x
        x = abs(x)
        while True:
            a = x // 10
            r = r * 10 + (x % 10)
            if a == 0:
                break
            x = x // 10
        # 保证反转之后的数字在int32的范围之内
        if r >= 2**31:
            return 0
        if m > 0:
            return r
        else:
            return -r
```

8. 字符串转换成整数(atoi)
> 请你来实现一个 atoi 函数，使其能将字符串转换成整数。
> 首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。
> 当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。
> 该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。
> 注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。
> 在任何情况下，若函数不能进行有效的转换时，请返回 0。
> 说明：
> 假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为$ [−2^{31},  2^{31} − 1] $。如果数值超过这个范围，请返回 $ MAX (2^{31} − 1) $或 $MIN (−2^{31}) $。

> 示例1 :
>> 输入: 123
>> 输出: 321

> 示例2 :
>> 输入: -123
>> 输出: -321

> 示例3 :
>> 输入: 120
>> 输出: 21

**思路：**
	- 整数除10取整，然后反转；
	- 变为字符串，然后反转；

**代码：**
整数除10取整
```python
class Solution:
    def reverse(self, x: int) -> int:
        r = 0
        m = x
        x = abs(x)
        while True:
            a = x // 10
            r = r * 10 + (x % 10)
            if a == 0:
                break
            x = x // 10
        # 保证反转之后的数字在int32的范围之内
        if r >= 2**31:
            return 0
        if m > 0:
            return r
        else:
            return -r
```

9. 回文数
> 判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

> 示例1 :
>> 输入: 121
>> 输出: true

> 示例2 :
>> 输入: -121
>> 输出: false
>> 解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。

> 示例3 :
>> 输入: 10
>> 输出: false
>> 解释: 从右向左读, 为 01 。因此它不是一个回文数。

**思路：**
	- 整数除10取整，然后反转对比；
	- 变为字符串，然后判断；

**代码：**
整数除10取整
```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0:
            return False
        m = x
        t = 0
        while True:
            if x // 10 == 0:
                t = t*10 + x
                break
            t = t*10 + x%10
            x = x // 10
        if t == m:
            return True
        else:
            return False
```

10. 正则表达式匹配
> 给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。
> ```
> '.' 匹配任意单个字符
> '*' 匹配零个或多个前面的那一个元素
> ```
> 所谓匹配，是要涵盖 整个字符串 `s`的，而不是部分字符串。

> 示例1 :
>> 输入:
>> s = "aa"
>> p = "a"
>> 输出: false
>> 解释: "a" 无法匹配 "aa" 整个字符串。

> 示例2 :
>> 输入:
>> s = "aa"
>> p = "a\*"
>> 输出: true
>> 解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。

> 示例3 :
>> 输入:
>> s = "ab"
>> p = ".\*"
>> 输出: true
>> 解释: ".\*" 表示可匹配零个或多个（'\*'）任意字符（'.'）。

> 示例4 :
>> 输入:
>> s = "aab"
>> p = "c\*a\*b"
>> 输出: true
>> 解释: 因为 '\*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。

> 示例5 :
>> 输入:
>> s = "mississippi"
>> p = "mis\*is\*p*."
>> 输出: false


**思路：**
	- TO DO

**代码：**

```python
TODO
```

11. 盛最多水的容器
> 给定 n 个非负整数 $a_1，a_2，...，a_n$，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为$ (i, a_i)$ 和 $(i, 0)$。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

> 示例1 :
>> 输入: [1,8,6,2,5,4,8,3,7]
>> 输出: 49

**思路：**
	- TO DO

**代码：**

```python
TODO
```
