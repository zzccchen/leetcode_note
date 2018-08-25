# py 系列

## 26. 删除排序数组中的重复项 [easy]

解法 1：

使用 `list` 自带的 `pop` 函数，通过 now 和 length 两个标识符记录当前位置和列表长度，通过 i 记录上一位置的值

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        else:
            length = len(nums)
            i = nums[0]
            now = 1
            while(now!=length):
                if nums[now] == i:
                    length -= 1
                    nums.pop(now)
                else:
                    i = nums[now]
                    now +=1  
            return now

# 耗时 116ms，捂脸
```

注意点：

1. 如果使用 `for i in list:` 这种写法并在此循环中对 `list` 进行修改是无效的，此处 `list` 仅作为一个生成器来使用，如需修改只能使用下标进行

2. 在 `for` 循环中使用 `pop` 等操作对列表操作是非常不好的，因为无法自己控制 index，很有可能导致缺值漏值问题，所以使用 `while` 循环

3. 需对列表为空进行判断，否则会导致超程问题

解法 2：

使用 p1，p2 两个位置标识符记录前后位置

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        else:
            p1 = 0
            i1 = nums[p1]
            i2 = None
            for p2 in range(1,len(nums)):
                i2 = nums[p2]
                if i2 != i1:
                    p1 += 1
                    i1 = nums[p1] = i2
            return p1+1

# 耗时 80ms
```

解法 3：

把

```python
if not nums:
    return 0
```

替换成

```python
if len(nums) < 2:
    return len(nums)
```

60ms 的大佬操作，能偷就偷

解法 4：

借鉴下一题（122 题）解法 4 的操作

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        p1 = 0
        for p2 in range(1,len(nums)):
            if nums[p2] != nums[p1]:
                p1 += 1
                nums[p1] = nums[p2]
        return p1+1 if nums else 0

# 耗时 76ms
```

因为平时 `len` 的长度要加一，所以还是要加个判段

## 122. 买卖股票的最佳时机 II [easy]

解法 1：

本题的关键就是怎么求最优的解，而这个解法非常简单。。。一开始想复杂了，甚至还想用递归来做==

```python
[7,1,5,3,6,4]  # 最优解就是跌买跌卖，如7，1这里跌了，那就在7这里卖，1这里买
               # 同理，5卖，3买
```

```python
class Solution:
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        length = len(prices)-1
        if length < 1:
            return 0
        for p1 in range(length):
            if prices[p1+1] > prices[p1]:
                break
        if p1 == length:
            return 0
        head = prices[p1]
        sum = 0
        for p1 in range(p1, length):
            if prices[p1+1] < prices[p1]:
                sum += (prices[p1] - head)
                head = prices[p1+1]
        if prices[p1+1] >= prices[p1]:
            sum += (prices[p1+1] - head)
        return sum

# 耗时 52ms，惊了
```

注意点：

需注意价格一路上涨这种情况，如 `[1, 2, 3, 4, 5, 6]`，避免漏值

解法 2：

解法 1 完全就是一大堆废话。。。

```python
class Solution:
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        length = len(prices)-1
        if length < 1:
            return 0
        head = prices[0]
        sum = 0
        for p1 in range(0, length):
            if prices[p1+1] < prices[p1]:
                sum += (prices[p1] - head)
                head = prices[p1+1]
        if prices[p1+1] >= prices[p1]:
            sum += (prices[p1+1] - head)
        return sum

# 耗时 68ms，有点慢了。。。
```

解法 3：

解法 1，2 的方法不太好，按数学的方法来想这道题还可以这么算

```python
[7,1,5,3,6,4]  # 求前后两项的插值，正加负丢
               # 如 1-7 = -6 丢
               # 5-1 = 4 加
```

```python
class Solution:
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        length = len(prices)-1
        if length < 1:
            return 0
        sum = 0
        i1 = prices[0]
        for p1 in range(0, length):
            i2 = prices[p1+1]
            if i2 > i1:
                sum += (i2 - i1)
            i1 = i2
        return sum

# 耗时 52ms
```

解法 4：

关于 `[]` 这种情况还有别的大佬操作

```python
class Solution:
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        sum = 0
        for p2 in range(1, len(prices)):
            if prices[p2] > prices[p2-1]:
                sum += (prices[p2] - prices[p2-1])
        return sum

# 耗时 48ms
```

经测试：直接通过下标访问列表似乎比通过变量缓存速度更快（我也不懂

## 189. 旋转数组 [easy]

解法 1：

废话多了些

```python
class Solution:
    def rotate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        length = len(nums)
        k = k if k < length else k % length
        i2 = nums[length-k:]
        nums[k:] = nums[0:length-k]
        nums[0:k] = i2

# 耗时 64ms
```

解法 2：

显然，在列表的操作上还有更 Pythonic 的方法

```python
class Solution:
    def rotate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        k = k % len(nums)
        nums[:] = nums[-k:]+nums[:-k]
        # 或
        # nums[:] = nums[len(nums)-k:] + nums[:len(nums)-k]

# 耗时 104ms，不管了这平台测速有点问题。。。
```

说明：

1. 对于
    ```python
    >>> nums = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    >>> nums[-k:]  # 等同于 nums[len(nums)-k:]
    [7, 8, 9]
    >>> nums[:-k]  # 等同于 nums[:len(nums)-k]
    [7, 8, 9, 0, 1, 2, 3, 4, 5, 6]
    ```
2. `nums[:]` 中的 `[:]` 是必须的，这点好奇怪啊

## 217. 存在重复 [easy]

解法 1：

py 的专属方法了

```python
class Solution:
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        return False if len(nums) == len(set(nums)) else True
```

解法 2：

原来还可以再偷啊。。。

```python
class Solution:
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        return len(set(nums)) != len(nums)
```

## 136. 只出现一次的数字 [easy]

解法 1：

显然通过排序速度慢了

```python
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        length = len(nums)-1
        if length == 0:
            return nums[0]
        nums.sort()
        for p1 in range(0,length,2):
            if nums[p1] != nums[p1+1]:
                return nums[p1]
        return nums[length]
```

注意：

1. 不能通过在循环内修改下标改变迭代步长，只能在 `range` 中修改

    ```python
    for p1 in range(0,len(nums)):
        print(nums[p1])
        p1 += 1  # 此代码是无效的，p1来自于生成器range
    ```

解法 2：

通过数学方法==

```python
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        return sum(set(nums))*2 - sum(nums)
```

解法 3：

位运算，学习了

```python
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = 0
        for num in nums:
            n ^= num
        return n
```

说明：

1. `^` 的用法

    ```python
    2 ^ 3
    1 0 = 2
    1 1 = 3
    -------
    0 1 = 1

    2 ^ 255
    0 0 0 0 0 0 1 0 = 2
    1 1 1 1 1 1 1 1 = 255
    ---------------------
    1 1 1 1 1 1 0 1 = 253

    3 ^ 2
    1 1 = 3
    1 0 = 2
    -------
    0 1 = 1
    ```

## 350. 两个数组的交集 II [easy]

解法 1：

常规方法

```python
class Solution:
    def intersect(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
        res = []
        for i in nums1:
            if i in nums2:
                res.append(i)
                nums2.remove(i)
        return res
```

解法 2：

先排序后比较

```python
class Solution:
    def intersect(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
        nums1.sort()
        nums2.sort()

        ind1 = 0
        ind2 = 0

        len1 = len(nums1)
        len2 = len(nums2)

        res = []
        while ind1 < len1 and ind2 < len2:
            diff = nums1[ind1] - nums2[ind2]
            if diff == 0:
                res.append(nums1[ind1])
                ind1 += 1
                ind2 += 1
            elif diff > 0:
                ind2 += 1
            else:
                ind1 += 1
        return res
```

## 66. 加一 [easy]

解法 1：

```python
class Solution:
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        if digits[-1] != 9:
            digits[-1] += 1
        else:
            i = -1
            length = -len(digits)-1
            while(i > length and digits[i] ==9):
                digits[i] = 0
                i -= 1
            if i == length:
                digits[0] = 1
                digits.append(0)
            else:
                digits[i] += 1
        return digits
```

## 283. 移动零 [easy]

解法 1：

这个解法有点慢了

```python
class Solution:
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        ti = nums.count(0)
        for i in range(ti):
            nums.remove(0)
            nums.append(0)
```

解法 2：

快了挺多

```python
class Solution:
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        i = dl = 0
        length = len(nums)
        while(dl + i != length-1):
            if nums[i] == 0:
                del nums[i]
                dl += 1
            else:
                i += 1
        nums += [0]*dl
```

解法 3：

妙啊

```python
class Solution:
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        j = 0
        for i in range(len(nums)):
            if nums[i] != 0:
                nums[j], nums[i] = nums[i], nums[j]
                j += 1
```

## 1. 两数之和 [easy]

解法 1：

这个解法很慢了

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for p1 in range(len(nums)):
            try:
                res = [p1, nums[p1+1:].index(target-nums[p1])+p1+1]
                return res
            except:
                pass

# 耗时 1300ms
```

解法 2：

。看起来 `index` 方法快挺多的

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        length = len(nums)
        for p1 in range(length):
            i1 = target - nums[p1]
            for p2 in range(p1+1,length):
                if i1 == nums[p2]:
                    return [p1,p2]

# 耗时 6600ms
```

解法 3：

原来字典的哈希表这么快啊。。。

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        dic = {}
        for i, num in enumerate(nums):
            if (target - num) in dic:
                return i,dic[target - num]
            dic[num] = i

# 耗时 76ms
```

## 36. 有效的数独 [medium]

解法 1：

有点累赘

```python
class Solution:
    def isValidSudoku(self, board):
        """
        :type board: List[List[str]]
        :rtype: bool
        """
        for col in range(9):
            dic = {}
            for line in range(9):
                if board[line][col] != '.':
                    if board[line][col] in dic:
                        return False
                    else:
                        dic[board[line][col]] = None
        for line in range(9):
            dic = {}
            for col in range(9):
                if board[line][col] != '.':
                    if board[line][col] in dic:
                        return False
                    else:
                        dic[board[line][col]] = None
        for c1 in range(3):
            for l1 in range(3):
                dic = {}
                for col in range(c1*3,c1*3+3):
                    for line in range(l1*3,l1*3+3):
                        if board[line][col] != '.':
                            if board[line][col] in dic:
                                return False
                            else:
                                dic[board[line][col]] = None
        return True

# 耗时 104ms
```

解法 2：

一次循环全判段，`col//3*3 + line//3` 很重要

```python
class Solution:
    def isValidSudoku(self, board):
        """
        :type board: List[List[str]]
        :rtype: bool
        """
        dc = {i:{} for i in range(9)}
        dl = {i:{} for i in range(9)}
        dcell = {i:{} for i in range(9)}
        for col in range(9):
            for line in range(9):
                if board[line][col] != '.':
                    ce = col//3*3 + line//3
                    sa = board[line][col]
                    if sa in collections.ChainMap(dc[col],dl[line],dcell[ce]):
                        return False
                    else:
                        dc[col][sa] = None
                        dl[line][sa] = None
                        dcell[ce][sa] = None
        return True

# 耗时 108ms，。。。
```

## 48. 旋转图像 [medium]

解法 1：

```python
class Solution:
    def rotate(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: void Do not return anything, modify matrix in-place instead.
        """
        n = len(matrix[0])
        n_1 = n-1
        for row in range(0, n//2):
            n_1_row = n_1-row
            for col in range(row, n_1-row):
                temp = matrix[row][col]
                n_1_col = n_1-col
                matrix[row][col] = matrix[n_1_col][row]
                matrix[n_1_col][row] = matrix[n_1_row][n_1_col]
                matrix[n_1_row][n_1_col] = matrix[col][n_1_row]
                matrix[col][n_1_row] = temp
```

## 344. 反转字符串 [easy]

解法 1：

```python
class Solution:
    def reverseString(self, s):
        """
        :type s: str
        :rtype: str
        """
        lis = [i for i in s]
        lis.reverse()
        return "".join(lis)
```

注意：

1. `reverse()` 函数没有返回值

解法 2：

```python
class Solution:
    def reverseString(self, s):
        """
        :type s: str
        :rtype: str
        """
        return s[::-1]
```

## 7. 反转整数 [easy]

解法 1：

```python
class Solution:
    def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        x = str(x)
        if x[0] == '-':
            x = '-' + x[:0:-1]
        else:
            x = x[::-1]
        x = int(x)
        if x < -2147483648 or x > 2147483647:
            return 0
        else:
            return x
```

解法 2：

把负号变正号再处理

```python
class Solution:
    def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x < 0:
            x = -int(str(-x)[::-1])
            if x < -2147483648:
                return 0
            else:
                return x
        else:
            x = int(str(x)[::-1])
            if x > 2147483647:
                return 0
            else:
                return x
```

## 387. 字符串中的第一个唯一字符 [easy]

解法 1：

```python
class Solution:
    def firstUniqChar(self, s):
        """
        :type s: str
        :rtype: int
        """
        length = len(s)
        if length > 1:
            dic = {i : None for i in "abcdefghijklmnopqrstuvwxyz" if s.count(i) == 1}
            if len(dic) == 0:
                return -1
            for i in range(length):
                if s[i] in dic:
                    return i
        elif length == 1:
            return 0
        else:
            return -1
```

解法 2：

有点意思，用 `string.ascii_lowercase` 来替代 `"abcdefghijklmnopqrstuvwxyz"` 确实好

```python
class Solution:
    def firstUniqChar(self, s):
        """
        :type s: str
        :rtype: int
        """
        lowercase = string.ascii_lowercase

        L_index = [s.index(label) for label in lowercase if s.count(label) == 1]
        return min(L_index) if len(L_index) else -1
```

## 242. 有效的字母异位词 [easy]

解法 1：

```python
class Solution:
    def isAnagram(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        dic1 = {i : s.count(i) for i in string.ascii_lowercase}
        dic2 = {i : t.count(i) for i in string.ascii_lowercase}

        return dic1 == dic2
```

解法 2：

没必要用全部 key，`set` 得到的 key 更好

```python
class Solution:
    def firstUniqChar(self, s):
        """
        :type s: str
        :rtype: int
        """
        return set(s) == set(t) and all(s.count(i) == t.count(i) for i in set(s))
```

## 125. 验证回文串 [easy]

解法 1：

```python
class Solution:
    def isPalindrome(self, s):
        """
        :type s: str
        :rtype: bool
        """
        s = s.lower()
        p2 = len(s)-1
        p1 = 0
        while(p2-p1>0):
            if s[p1] not in "abcdefghijklmnopqrstuvwxyz0123456789":
                p1 += 1
                continue
            if s[p2] not in "abcdefghijklmnopqrstuvwxyz0123456789":
                p2 -= 1
                continue
            if s[p2] != s[p1]:
                return False
            p1 += 1
            p2 -= 1
        return True
```

解法 2：

```python
class Solution:
    def isPalindrome(self, s):
        """
        :type s: str
        :rtype: bool
        """
        s = s.lower()
        q = 'abcdefghijklmnopqrstuvwxyz0123456789'
        t = ''
        for i in s:
            if i in q:
                t+=i
        return t == t[::-1]
```

## 8. 字符串转整数 (atoi) [medium]

解法 1：

```python
class Solution:
    def myAtoi(self, str):
        """
        :type str: str
        :rtype: int
        """
        res = re.search(r'([-+]?\d+)', str)
        first = re.search(r'(\S+)', str)
        if res and res.span()[0] == first.span()[0]:
            i = int(res.group(1))
            if i > 2147483647:
                return 2147483647
            elif i < -2147483648:
                return -2147483648
            else:
                return i
        else:
            return 0
```

## 28. 实现strStr() [easy]

解法 1：

这似乎不太符合规矩==

```python
class Solution:
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        return haystack.find(needle)
```

解法 2：

`index` 还能这么用，学习了

```python
class Solution:
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        try:
            index = haystack.index(needle)
            return index
        except:
            return -1
```

## 38. 数数并说 [easy]

解法 1：

暂时找不到其他规律了。。只能递推了

```python
class Solution:
    def countAndSay(self, n):
        """
        :type n: int
        :rtype: str
        """
        num = "1"
        for i in range(n-1):
            count = 0
            pre = num[0]
            temp = ""
            for p1 in range(len(num)):
                if num[p1] != pre:
                    temp = temp + str(count) + pre
                    pre = num[p1]
                    count = 1
                else:
                    count += 1
            num = temp + str(count) + pre
        return num
```

## 14. 最长公共前缀 [easy]

解法 1：

```python
class Solution:
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        length = len(strs)
        if length == 1:
            return strs[0]
        elif length == 0:
            return ""
        else:
            res = ""
            while(True):
                try:
                    res += strs[0][0]
                    strs[0] = strs[0][1:]
                except:
                    return res
                try:
                    for p1 in range(1,length):
                        if strs[p1][0] == res[-1]:
                            strs[p1] = strs[p1][1:]
                        else:
                            return res[:-1]
                except:
                    return res[:-1]
```

解法 2：

简化了解法 1

```python
class Solution:
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        length = len(strs)
        if length == 0:
            return ""
        else:
            strs.sort(key=len)
            res = ""
            for i in range(len(strs[0])):
                res += strs[0][i]
                for p1 in range(1, length):
                    if strs[p1][i] != res[-1]:
                        return res[:-1]
            return res
```

## 237. 删除链表中的节点 [easy]

解法 1：

原来 py 的链表这么写啊

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void Do not return anything, modify node in-place instead.
        """
        node.val = node.next.val
        node.next = node.next.next
```

解法 2：

手动删除

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void Do not return anything, modify node in-place instead.
        """
        node.val = node.next.val
        addr = node.next
        node.next = addr.next
        del addr
```

## 19. 删除链表的倒数第N个节点 [medium]

解法 1：

把顺序写进字典

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeNthFromEnd(self, head, n):
        """
        :type head: ListNode
        :type n: int
        :rtype: ListNode
        """
        node, i, dic = head, 0, {}
        while(node):
            dic[i] = node
            i += 1
            node = node.next
        length = len(dic)
        del_num = length - n
        if del_num == 0:
            return dic.get(1)
        elif n == 1:
            dic[del_num-1].next = None
            return head
        else:
            dic[del_num].val = dic[del_num+1].val
            dic[del_num].next = dic[del_num+1].next
            return head
```

解法 2：

通过指针追逐来实现，妙啊

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeNthFromEnd(self, head, n):
        """
        :type head: ListNode
        :type n: int
        :rtype: ListNode
        """
        if head is None:
            return

        step = 0
        ptr1 = head
        ptr2 = head
        while ptr1 is not None:
            ptr1 = ptr1.next
            step += 1
            if step > n + 1:
                ptr2 = ptr2.next

        if step == n:
            head = head.next
            return head

        if ptr2.next is not None:
            ptr2.next = ptr2.next.next
        return head
```

## 206. 反转链表 [easy]

解法 1：

迭代法

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        node = head
        pre = None
        while(node):
            temp = node.next
            node.next = pre
            pre = node
            node = temp
        return pre
```

## 21. 合并两个有序链表 [easy]

解法 1：

没看到有序二字。。。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeTwoLists(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        lis, i = [], 0
        while(l1):
            lis.append((l1.val, i, l1))
            i += 1
            l1 = l1.next
        while(l2):
            lis.append((l2.val, i, l2))
            i += 1
            l2 = l2.next
        lis.sort()
        length = len(lis)
        if length == 0:
            return None
        elif length == 1:
            lis[0][2].next = None
            return lis[0][2]
        else:
            for p2 in range(1, length):
                lis[p2-1][2].next = lis[p2][2]
            lis[p2][2].next = None
            return lis[0][2]
```

解法 2：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeTwoLists(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        if l2 == None:
            return l1
        if l1 == None:
            return l2
        result = ListNode(0)
        result1 = result
        while l1 and l2:
            if l1.val >= l2.val:
                result1.next = l2
                l2 = l2.next
            else:
                result1.next = l1
                l1 = l1.next
            result1 = result1.next
        while l1:
            result1.next = l1
            l1 = l1.next
            result1 = result1.next
        while l2:
            result1.next = l2
            l2 = l2.next
            result1 = result1.next
        return result.next
```

## 234. 回文链表 [easy]

解法 1：

`list.reverse()` 没有返回值，直接修改 `list` 的，要用就用 `list(reversed(t))` 或 `list[::-1]`

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def isPalindrome(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        t = []
        while(head):
            t.append(head.val)
            head = head.next
        return t == t[::-1]
```

## 141. 环形链表 [easy]

解法 1：

判段地方啰嗦了

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        if not head:
            return False
        p2 = head.next
        p1 = head
        while(p2):
            if p1 == p2:
                return True
            else:
                p1 = p1.next
                try:
                    p2 = p2.next.next
                except:
                    return False
        return False
```

解法 2：

判段地方啰嗦了

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        if not head:
            return False
        p2 = head.next
        p1 = head
        while(p1 != p2):
            p1 = p1.next
            try:
                p2 = p2.next.next
            except:
                return False
        return True
```

## 104. 二叉树的最大深度 [easy]

解法 1：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        ld = self.maxDepth(root.left)
        rd = self.maxDepth(root.right)
        return max(ld, rd) + 1
        # 或
        # return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1 if root else 0
```

## 98. 验证二叉搜索树 [medium]

解法 1：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if not root:
            return True
        try:
            self.isValidBST_(root)
            return True
        except:
            return False

    def isValidBST_(self, node):
        val = node.val
        if node.left:
            ld_min, ld_max = self.isValidBST_(node.left)
            if ld_max >= val:
                raise RuntimeError
        else:
            ld_min = val
        if node.right:
            rd_min, rd_max = self.isValidBST_(node.right)
            if rd_min <= val:
                raise RuntimeError
        else:
            rd_max = val
        return ld_min, rd_max
```

## 101. 对称二叉树 [easy]

解法 1：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        try:
            if root:
                self.isSymmetric_(root.left, root.right)
            return True
        except:
            return False

    def isSymmetric_(self, node1, node2):
        if node1 == None and node2 == None:
            return
        if node1.val == node2.val:
            self.isSymmetric_(node1.left, node2.right)
            self.isSymmetric_(node1.right, node2.left)
        else:
            raise RuntimeError
```

## 102. 二叉树的层次遍历 [medium]

解法 1：

递归

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    rst = []
    deep = -1

    def levelOrder(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        self.rst = []
        self.deep = -1
        self.levelOrder_(root, 0)
        return self.rst

    def levelOrder_(self, node, layer):
        if not node:
            return
        if layer > self.deep:
            self.deep += 1
            self.rst.append([node.val])
        else:
            self.rst[layer].append(node.val)
        layer += 1
        self.levelOrder_(node.left, layer)
        self.levelOrder_(node.right, layer)
```

解法 2：

遍历，按层读，善用 `pop`

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def levelOrder(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        res = []
        if root is None:
            return res

        res.append([root.val])
        queue = [root]
        while queue and queue[0]:
            level = []
            count = len(queue)
            for i in range(count):
                node = queue.pop(0)
                if node.left:
                    queue.append(node.left)
                    level.append(node.left.val)
                if node.right:
                    queue.append(node.right)
                    level.append(node.right.val)

            if level:
                res.append(level)
        return res
```

解法 3：

遍历

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def levelOrder(self, root):
        if root is None:
            return []

        result, current = [], [root]
        while current:
            next_level, vals = [], []
            for node in current:
                vals.append(node.val)
                if node.left:
                    next_level.append(node.left)
                if node.right:
                    next_level.append(node.right)

            current = next_level
            result.append(vals)

        return result
```

## 108. 将有序数组转换为二叉搜索树 [easy]

解法 1：

直接借鉴大佬的算法 = =

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sortedArrayToBST(self, nums):
        """
        :type nums: List[int]
        :rtype: TreeNode
        """
        if not nums:
            return None
        half_length = len(nums)//2
        root = TreeNode(nums[half_length])
        root.left = self.sortedArrayToBST(nums[:half_length])
        root.right = self.sortedArrayToBST(nums[half_length+1:])
        return root
```

## 88. 合并两个有序数组 [easy]

解法 1：

没有通过测试，百思不得其解

```python
class Solution:
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        nums1 = nums1[:m]
        nums1.extend(nums2)
        nums1.sort()
```

解法 2：

大佬写法

```python
class Solution:
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        nums1[m:] = nums2
        nums1.sort()
```

## 278. 第一个错误的版本 [easy]

解法 1：

大笨蛋写法

```python
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return a bool
# def isBadVersion(version):

class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        return self.firstBadVersion_(1, n, 0)

    def firstBadVersion_(self, head, tail, fhead):
        if fhead+1 >= tail:
            return tail
        half = (head + tail) // 2
        if isBadVersion(half):
            return self.firstBadVersion_(head, half, fhead)
        else:
            return self.firstBadVersion_(half+1, tail, half)
```

解法 2：

大笨蛋写法

```python
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return a bool
# def isBadVersion(version):

class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        return self.firstBadVersion_(1, n)

    def firstBadVersion_(self, head, tail):
        if head+1 >= tail:
            return head if isBadVersion(head) else tail
        half = (head + tail) // 2
        if isBadVersion(half):
            return self.firstBadVersion_(head, half)
        else:
            return self.firstBadVersion_(half, tail)
```

解法 3：

循环写法

```python
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return a bool
# def isBadVersion(version):

class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        head, tail = 1, n
        while(head+1 < tail):
            half = (head + tail) // 2
            if isBadVersion(half):
                tail = half
            else:
                head = half + 1
        return head if isBadVersion(head) else tail
```

## 155. 最小栈 [easy]

解法 1：

每次都写 ```if not self.min:``` 这次果然翻车了

```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.mem = []
        self.min = None

    def push(self, x):
        """
        :type x: int
        :rtype: void
        """
        self.mem.append(x)
        if self.min == None or self.min > x:
            self.min = x

    def pop(self):
        """
        :rtype: void
        """
        temp = self.mem.pop()
        if temp <= self.min:
            self.min = min(self.mem) if self.mem else None
        return temp

    def top(self):
        """
        :rtype: int
        """
        return self.mem[-1]

    def getMin(self):
        """
        :rtype: int
        """
        return self.min


# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

## 412. Fizz Buzz [easy]

解法 1：

```python
class Solution:
    def fizzBuzz(self, n):
        """
        :type n: int
        :rtype: List[str]
        """
        lis = []
        for i in range(1, n+1):
            if i % 15 == 0:
                lis.append("FizzBuzz")
            elif i % 3 == 0:
                lis.append("Fizz")
            elif i % 5 == 0:
                lis.append("Buzz")
            else:
                lis.append(str(i))
        return lis
```

## 204. 计数质数 [easy]

解法 1：

厄拉多塞筛法

```python
class Solution:
    #根据算术基本定理，每一个比1大的整数，要么本身是一个质数，要么可以写成一系列质数的乘积；
    def countPrimes(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n < 2:
            return 0
        res = [1] * n
        res[0],res[1] = 0,0
        for i in range(2, int(n ** 0.5) + 1):
            if res[i] == 1:
                res[i * i:n:i] = [0] * int((n-i*i-1)/i + 1)
        return sum(res)
```

## 326. 3的幂 [easy]

解法 1：

`round()` 得到最接近原数的整型

```python
import math
class Solution:
    def isPowerOfThree(self, n):
        """
        :type n: int
        :rtype: bool
        """
        return n > 0 and 3**round(math.log(n, 3)) == n
```

解法 2：

```python
import math
class Solution:
    def isPowerOfThree(self, n):
        """
        :type n: int
        :rtype: bool
        """
        if n < 1:
            return False
        temp = math.log(n, 3) % 1
        if temp == 0 or temp > 0.999999999999990:
            return True
        else:
            return False
```

## 13. 罗马数字转整数 [easy]

解法 1：

`round()` 得到最接近原数的整型

```python
class Solution:
    def romanToInt(self, s):
        """
        :type s: str
        :rtype: int
        """
        sum = 0
        lis = [i for i in s]
        temp = None
        while(lis):
            temp = lis.pop()
            if temp == "I":
                sum += 1
            else:
                break
        if temp == "V":
            sum += 5
            if lis:
                temp = lis.pop()
                if temp == "I":
                    sum -= 1
                    if lis:
                        temp = lis.pop()
                    else:
                        return sum
        if temp == "X":
            sum += 10
            while(lis):
                temp = lis.pop()
                if temp == "X":
                    sum += 10
                elif temp == "I":
                    sum -= 1
                else:
                    break
        if temp == "L":
            sum += 50
            if lis:
                temp = lis.pop()
                if temp == "X":
                    sum -= 10
                    if lis:
                        temp = lis.pop()
                    else:
                        return sum
        if temp == "C":
            sum += 100
            while(lis):
                temp = lis.pop()
                if temp == "C":
                    sum += 100
                elif temp == "X":
                    sum -= 10
                else:
                    break
        if temp == "D":
            sum += 500
            if lis:
                temp = lis.pop()
                if temp == "C":
                    sum -= 100
                    if lis:
                        temp = lis.pop()
                    else:
                        return sum
        if temp == "M":
            sum += 1000
            print(lis)
            while(lis):
                temp = lis.pop()
                if temp == "M":
                    sum += 1000
                else:
                    sum -= 100
        return sum
```