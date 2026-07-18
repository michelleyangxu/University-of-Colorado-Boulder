以下是使用分治法（Divide and Conquer）解决最大子数组问题的完整 Python 代码。

该实现严格遵循《算法导论》中的经典思路，不仅返回最大和，还返回最大子数组的起始索引和结束索引，以便直观地看到是哪一段子数组产生了最大和。

Python 代码实现

```python
import sys

def find_max_crossing_subarray(arr, low, mid, high):
    """
    查找跨越中点 mid 的最大子数组
    :param arr: 输入数组
    :param low: 左边界索引
    :param mid: 中点索引
    :param high: 右边界索引
    :return: (左边界索引, 右边界索引, 最大跨中和)
    """
    1. 处理左半部分：从 mid 向左扫描，寻找最大后缀和
    left_sum = -sys.maxsize  初始化为负无穷
    current_sum = 0
    max_left_idx = mid
    
    for i in range(mid, low - 1, -1):
        current_sum += arr[i]
        if current_sum > left_sum:
            left_sum = current_sum
            max_left_idx = i
            
    2. 处理右半部分：从 mid+1 向右扫描，寻找最大前缀和
    right_sum = -sys.maxsize  初始化为负无穷
    current_sum = 0
    max_right_idx = mid + 1
    
    for j in range(mid + 1, high + 1):
        current_sum += arr[j]
        if current_sum > right_sum:
            right_sum = current_sum
            max_right_idx = j
            
    3. 返回跨越中点的最大子数组信息
    return (max_left_idx, max_right_idx, left_sum + right_sum)

def find_maximum_subarray(arr, low, high):
    """
    分治法主函数：查找数组 arr[low..high] 中的最大子数组
    :param arr: 输入数组
    :param low: 当前处理的左边界
    :param high: 当前处理的右边界
    :return: (起始索引, 结束索引, 最大和)
    """
    基准情况：只有一个元素
    if low == high:
        return (low, high, arr[low])
    
    分解：计算中点
    mid = (low + high) // 2
    
    递归求解三种情况：
    1. 最大子数组完全在左半部分
    (left_low, left_high, left_sum) = find_maximum_subarray(arr, low, mid)
    
    2. 最大子数组完全在右半部分
    (right_low, right_high, right_sum) = find_maximum_subarray(arr, mid + 1, high)
    
    3. 最大子数组跨越中点
    (cross_low, cross_high, cross_sum) = find_max_crossing_subarray(arr, low, mid, high)
    
    合并：比较三种情况，返回和最大的那个
    if left_sum >= right_sum and left_sum >= cross_sum:
        return (left_low, left_high, left_sum)
    elif right_sum >= left_sum and right_sum >= cross_sum:
        return (right_low, right_high, right_sum)
    else:
        return (cross_low, cross_high, cross_sum)

--- 测试代码 ---
if __name__ == "__main__":
    示例数组
    nums = [13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12, -5, -22, 15, -4, 7]
    
    print(f"输入数组: {nums}")
    
    调用分治算法
    start_idx, end_idx, max_sum = find_maximum_subarray(nums, 0, len(nums) - 1)
    
    输出结果
    print(f"最大子数组和: {max_sum}")
    print(f"最大子数组范围: 索引 [{start_idx}] 到 [{end_idx}]")
    print(f"最大子数组内容: {nums[start_idx : end_idx + 1]}")
```

代码逻辑解析

1.  `find_max_crossing_subarray` (跨越中点的情况)
    *   核心逻辑：任何跨越中点 `mid` 的子数组都由两部分组成：`A[i..mid]` 和 `A[mid+1..j]`。
    *   左侧扫描：从 `mid` 开始向左遍历到 `low`，累加元素并记录出现的最大和 `left_sum` 及其起始索引 `max_left_idx`。
    *   右侧扫描：从 `mid+1` 开始向右遍历到 `high`，累加元素并记录出现的最大和 `right_sum` 及其结束索引 `max_right_idx`。
    *   合并：跨越中点的最大和即为 `left_sum + right_sum`。此步骤时间复杂度为 $O(n)$。

2.  `find_maximum_subarray` (主递归函数)
    *   分解 (Divide)：将数组从中间 `mid` 切分为左右两半。
    *   解决 (Conquer)：
        *   递归调用自身求解左半部分的最大子数组。
        *   递归调用自身求解右半部分的最大子数组。
        *   调用辅助函数求解跨越中点的最大子数组。
    *   合并 (Combine)：比较上述三个结果的和，返回最大值对应的三元组 `(低索引, 高索引, 和)`。

3.  时间复杂度
    *   递归关系式为 $T(n) = 2T(n/2) + O(n)$。
    *   根据主定理（Master Theorem），总时间复杂度为 $O(n \log n)$。

运行示例

对于输入数组 `[13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12, -5, -22, 15, -4, 7]`，程序将输出：

```text
输入数组: [13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12, -5, -22, 15, -4, 7]
最大子数组和: 43
最大子数组范围: 索引  到 
最大子数组内容: [18, 20, -7, 12]
```
*(注：不同实现对于和相等的情况可能选取不同的子数组，但最大和值是唯一确定的)*<br>参考资料<br>[1] [分治策略之最大子数组(Python实现) - 腾讯云](https://cloud.tencent.com/developer/article/1670254)<br>[2] [LeetCode 53M 最大子数组和 Maximum Subarray - 贪心滑窗+分治+DP - 王几行XING](https://zhuanlan.zhihu.com/p/599166065)<br>[3] [**最大子数组和问题(Maximum Subarray Sum)的分治解法**,其核心函数 `MaxSubSum` 的实现逻辑清晰、结构严谨 - CSDN技术社区](https://programb.blog.csdn.net/article/details/157872452)<br>[4] [python实现最大子序和(分治+动态规划) - 脚本之家](https://www.jb51.net/article/164664.htm)<br>[5] [求取最大子数组(Python语言实现) - CSDN博客](https://blog.csdn.net/weixin_44327634/article/details/123509927)<br>[6] [算法导论————最大子数组问题(C,Java,python) - CSDN博客](https://blog.csdn.net/qq_52659550/article/details/130066485)<br>[7] [53. 最大子数组和 : 图解从 O(n) 的常规理解到 O(n) 的分治做法 - 宫水三叶](http://zhuanlan.zhihu.com/p/667741454)<br>[8] [分治法解决最大子数组问题 - 博客园](https://www.cnblogs.com/Christal-R/p/Christal_R.html)<br>[9] [Python笔记 之 分治法求数组最大子数组 - CSDN博客](https://blog.csdn.net/weixin_50648794/article/details/108573250)<br>[10] [LeetCode第53题:最大子数组和【python 5种算法】 - CSDN博客](https://blog.csdn.net/CCIEHL/article/details/138046803)<br>[11] [Python每日一题【M2D8】 - qi_hao](http://www.bilibili.com/video/BV1CtWRe1Edb)<br>[12] [力扣53.(剑指offer42)最大(连续)子数组的和 python实现 - Scrapper黄同学](http://m.bilibili.com/video/BV1be4y177pt/)<br>[13] [3957. M 个非重叠子数组最大和 II - 力扣](https://leetcode.cn/problems/maximum-sum-of-m-non-overlapping-subarrays-ii?plan_progress=ziuorzr)<br>[14] [子数组 - 力扣](https://leetcode.cn/problems/maximum-subarray/description/)<br>[15] [剑指刷题-面试题42. 连续子数组的最大和 - CSDN博客](https://blog.csdn.net/qq_42182596/article/details/105699177)<br>[16] [python求解连续子数组的最大和(暴力、动态规划、贪心、分治) - CSDN博客](https://blog.csdn.net/qq_41577750/article/details/119574654)<br>[17] [最大子数组算法问题的几种解法代码(使用Python描述) - 高坦的博客 - 博客园 - 博客园](https://www.cnblogs.com/gtscool/p/12461249.html)<br>[18] [一文看懂《最大子序列和问题》(内含Java,Python,JS代码) - 腾讯云](https://cloud.tencent.com/developer/article/1491530)<br>[19] [53. 最大子数组和 : 图解从 O(n) 的常规理解到 O(n) 的分治做法 - 51CTO博客](https://blog.51cto.com/acoier/8578976)<br>[20] [LeetCode题目(Python实现):最大子序和 - CSDN博客](https://blog.csdn.net/qq_45556599/article/details/104503551)<br>[21] [LeetCode 53. 最大子序和(Python、动态规划、分治(线段树)) - CSDN博客](https://blog.csdn.net/Mai_M/article/details/111614365)<br>[22] [leetcode(力扣)hot100 之 最大数组和 - timger鈩](http://zhuanlan.zhihu.com/p/685352128)<br>[23] [3743. 循环划分的最大得分 - 力扣](https://leetcode.cn/problems/maximize-cyclic-partition-score/description/?orderBy=most_votes&languageTags=golang)<br>[24] [C++算法实战包:背包问题、哈夫曼树、最大子序和等5个经典问题完整实现与作业解析 - CSDN博客](https://blog.csdn.net/python9snake/article/details/161848574)<br>[25] [[算法导论&python学习] 求最大子数组(分治法) - CSDN博客](https://blog.csdn.net/weixin_52207736/article/details/117440515)<br><br>百度AI生成，内容仅供参考