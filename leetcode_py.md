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