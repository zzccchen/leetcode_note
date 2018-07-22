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

2. 在循环中使用 `pop` 等操作对列表操作是非常不好的，很有可能导致缺值漏值问题

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