---
title: 排序算法总结（内含python实现）
date: 2022-04-23 20:36:33
tags: sort algorithm python
categories: 算法
---

本文总结了常用的四大类别的算法：插入排序（直接插入排序，希尔排序）、交换排序（冒泡排序，快速排序）、选择排序（直接选择排序，堆排序）、归并排序，并用python实现。

> 注：其中一些排序算法可稳定，也可不稳定，关键在于在处理相同的元素时，跳过还是依次遍历。
> 注：下列代码实现为升序排序。

## 插入排序

#### 直接插入排序

``` python
# 从左往右遍历，依次将当前元素插入到前面已经有序的序列中
# 稳定
# 时间复杂度： O(n^2)
# 空间复杂度： O(1)
# 改进：可使用二分法插入排序（暂略）

def insert_sort(nums: list) -> list:
    # 从左往右开始遍历
    for i in range(1, len(nums)):
        index = i
        temp = nums[i]
        while index > 0 and nums[index - 1] > temp:
            # 若当前元素较小，则将有序的序列向后移
            nums[index] = nums[index - 1]
            index -= 1
        # 找到正确的位置，赋值
        nums[index] = temp
    return nums

nums = [1, 2, 4, 9, 3, 55, 64, 25]
print(insert_sort(nums))
```

#### 希尔排序

``` python
# 在直接插入的基础上， 分组（间隔为n的为一组，这里初始值设置为数据的一半）依次插入，然后将n缩小一半，依次插入，如此迭代，直至 n 为 0
# 这样做的好处在于，避免直接插入时，若元素为逆序，需要过多次的插入和元素移动操作
# 不稳定
# 时间复杂度： 
# 空间复杂度： O(1)

def shell_sort(nums: list) -> list:
    # 初始分组设置为数组的一半
    group = 2
    n = len(nums)
    gap = n // group
    while gap > 0:
        for i in range(gap, n):
            temp = nums[i]
            j = i
            # 从右往左，依次插入
            while j >= gap and nums[j - gap] > temp:
                nums[j] = nums[j - gap]
                j -= gap
            nums[j] = temp
        # 分组长度减半
        gap //= group
    return nums

nums = [1, 2, 4, 9, 3, 55, 64, 25]
print(shell_sort(nums))
```

## 交换排序

#### 冒泡排序

``` python
# 相邻两进行比较，每一轮将最大值移到最后
# 不稳定
# 时间复杂度： O(n^2)
# 空间复杂度： O(1)

def bubble_sort(nums: list) -> list:
    # 每轮迭代，将最大值移到最后，故待排序序列长度减一
    for end in range(len(nums) - 1, 1, -1):
        # 从左往右依次两两比较，将较大值移到后面
        for i in range(1, end):
            if nums[i - 1] > nums[i]:
                nums[i - 1], nums[i] = nums[i], nums[i - 1]
    return nums

nums = [1, 2, 4, 9, 3, 55, 64, 25]
print(bubble_sort(nums))
```

#### 快速排序

``` python
# 选择一个值，将比它大的移动右边，比它小的移到左边,故该值的位置已经找到；然后分别在左边和右边的序列中，继续选择一个值、划分，直至划分的序列中只有一个值
# 稳定
# 时间复杂度： O(nlogn)
# 空间复杂度： O(1)

# 找出选择的值的位置
# 这里默认取序列第一个元素，来划分左右序列
def partition(nums: list, left: int, right: int) -> int:
    targt = nums[left]
    mid, fast = left, right
    while mid != fast:
        if nums[fast] > targt and fast > mid:
            fast -= 1
        elif nums[fast] < targt and fast < mid:
            fast += 1
        else:
            nums[fast], nums[mid] = nums[mid], nums[fast]
            mid, fast = fast, mid
            if fast >= mid:
                fast -= 1
            elif fast < mid:
                fast += 1
    return mid

# 递归实现： 划分左右序列
def quick_sort(nums, left, right):
    if not nums: return nums
    mid = partition(nums, left, right)
    if left < mid - 1: quick_sort(nums, left, mid - 1)
    if right > mid + 1: quick_sort(nums, mid + 1, right)
    return nums


nums = [1, 5, 4, 9, 3, 55, 64, 25]
print(quick_sort(0, len(nums) - 1, nums))
```

## 选择排序

#### 直接选择排序

``` python
# 确定最小值或最大值的位置，最后才交换
# 稳定
# 时间复杂度： O(n^2)
# 空间复杂度： O(1)

def select_sort(nums: list) -> list:
    n = len(nums)
    # 从左往右依次遍历
    for start in range(n - 1):
        index = start
        # 确定比当前元素小的元素的位置
        for i in range(start + 1, n):
            if nums[index] > nums[i]:
                index = i
        # 赋值
        nums[start], nums[index] = nums[index], nums[start]
    return nums

nums = [1, 2, 4, 9, 3, 55, 64, 25]
print(select_sort(nums))    
```

#### 堆排序

``` python
# 从下往上，从左往右，创建最大堆
# 最大堆特征：父亲节点比其左右子树的节点的值都大
# 
# 时间复杂度： 
# 空间复杂度： O(1)

# 创建堆
def bulid_heap(nums: list, head: int, end: int) -> list:
    larget = head
    # 当前节点的左孩子
    left = 2 * head + 1
    # 当前节点的右孩子
    right = 2 * head + 2
    # 找到左右孩子中，比父亲节点大的节点的位置
    if left < end and nums[left] > nums[larget]:
        larget = left
    if right < end and nums[right] > nums[larget]:
        larget = right
    # 若孩子节点比父亲节点的值大，则交换
    if larget != head:
        nums[larget], nums[head] = nums[head], nums[larget]
        # 被交换的孩子节点作为父节点，继续向其孩子节点遍历
        bulid_heap(nums, larget, end)
    return nums

# 堆排序
def heap_sort(nums):
    n = len(nums)
    # 创建堆，从右往左依次插入节点
    for i in range(n - 1, -1, -1):
        bulid_heap(nums, i, n)
    # 排序，将最大值和末尾节点进行交换，然后将末尾节点之前的序列继续构建最大堆；如此遍历
    for j in range(n - 1, 0, -1):
        nums[0], nums[j] = nums[j], nums[0]
        bulid_heap(nums, 0, j)
    return nums


nums = [1, 2, 4, 9, 3, 55, 64, 25]
print(heap_sort(nums)) 
```

## 归并排序

``` python
# 分治的思想
# 
# 时间复杂度： 
# 空间复杂度： O(n)

# 将两个有序的序列，依次从左往右比较，将较小的值插入新的序列，最后合成一个有序的序列
def merge(nums, left, mid, right):
    numsa = nums[left:mid + 1]
    numsb = nums[mid + 1: right + 1]
    result = list()
    p, q = 0, 0
    # 两数组分别开始比较
    while p < len(numsa) and q < len(numsb):
        if numsa[p] >= numsb[q]:
            result.append(numsb[q])
            q += 1
        else:
            result.append(numsa[p])
            p += 1
    #若其中一个数组还有值，则追加后末尾
    result.extend(numsa[p:])
    result.extend(numsb[q:])
    nums[left:right + 1] = result

# 递归拆分
# 没有返回值，传入数组，默认在此词数组上移动排序
def merge_sort(nums: list, start: int, end: int):
    if start < end:
        # 默认是对半拆分
        mid = (start + end) // 2
        merge_sort(nums, start, mid)
        merge_sort(nums, mid + 1, end)
        merge(nums, start, mid, end)

nums = [1, 2, 4, 9, 3, 55, 64, 25]
merge_sort(nums, 0, len(nums) - 1)
print(nums)
```
