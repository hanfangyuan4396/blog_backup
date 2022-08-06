---
title: 动态规划-剑指offer
date: 2022-03-02 14:51:20
tags: 
    - 刷题
    - 剑值offer
    - 动态规划
---
### 动态规划

动态规划做题步骤

1.确定dp数组及下标的含义

比如dp[i] 是第i+1个斐波那契数列的值

2.确定递推公式
<!-- more -->
比如dp[i] = dp[i-1] + dp[i-2]

3.初始化dp数组

比如dp[0]=1 dp[1]=1

4.确定遍历顺序

比如从前向后遍历

5.举例推导dp数组

比如第5项的值

value    1    1    2    3    5

index    0    1    2    3    4

#### 斐波那契数列

[https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=23255&ru=%2Fpractice%2F6e5207314b5241fb83f2329e89fdecc8&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=23255&ru=%2Fpractice%2F6e5207314b5241fb83f2329e89fdecc8&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

```java
1. dp[i] 第 i+1项的值
2. 确定递推公式 dp[i] = dp[i-1] + dp[i-2]
3. 初始化dp数组 dp[0]=1 dp[1]=1
4. 遍历顺序 从前向后遍历
5. 举例推到 第5项 即dp[5-1] dp[4]
value    1    1    2    3    5
index    0    1    2    3    4
```

```java
public int Fibonacci(int n) {
    if (n <= 2) {
        return 1;
    } 
    int[] dp = new int[n];
    dp[0] = 1;
    dp[1] = 1;
    for (int i=2; i<n; i++) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n-1];
}
```
#### 跳台阶

[https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=23261&ru=%2Fpractice%2Fc6c7742f5ba7442aada113136ddea0c3&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=23261&ru=%2Fpractice%2Fc6c7742f5ba7442aada113136ddea0c3&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

```java
1.确定dp数组及下标的含义
dp[i] 跳第i+1个台阶的跳法
2.确定递推公式
dp[i] = dp[i-1] + dp[i-2]
3.初始化数组
dp[0] = 1 dp[2] = 2
4.确定遍历顺序
5.举例到第五个台阶
1 2 3 5 8
0 1 2 3 4
```

```java
public int jumpFloor(int target) {
    if (target <= 2) {
        return target;
    }
    int[] dp = new int[target];
    dp[0] = 1;
    dp[1] = 2;
    for (int i=2; i<target; i++) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[target-1];
    
}
```
* 压缩空间
只用保存之前的结果

```java
public int climbStairs(int n) {
    if (n == 0) {
        return 1;
    }
    if (n == 1) {
        return 1;
    }
    int pre1 = 1;
    int pre2 = 1;
    int current=-1;
    for (int i=2; i<=n; i++) {
        current = pre1 + pre2;
        pre1 = pre2;
        pre2 = current;
    }
    return current;
}
```
* 斐波那契数列公式
a1 = 1

a2 = 1

a3 = 2

a4 = 3

an = ![图片](data:image/png;base64,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qNlsyjkna63yPNdJOefUbDaVpqmyLFOlUlGapgJUqVS0t7en44QQVK/XVa1WtbW1paMURaFqtaparaZOp6Nut6tqtao0TXX9+nV1u12dFy9evFClUtHa2ppCCHqTf/qnfxIga62stbLWylora62stUrTVPfv39dZ5pxTlmUClGWZnHOqVquq1WoKIeikut2u0jQVoCzLZK0VIGut1tbW5L3XcdbW1pSmqV68eKGjOOfUbDY1Pj6uVquloihUr9eVpqmuX7+uBw8eKISgg7z3evDgger1uqrVqrIsk/deh0HvyHuvtbU1PXjwQM1mU81mU81mU81mU81mU81mU/fv31dRFDpNeZ7LWivvvd7kxYsXArS2tqaTcM6pKAo55+Sc06tXr+Sck3NOzjkVRaF3FUJQrVZTs9nU++C9V5ZlAmSt1f3795Vlmbz3ehshBHU6Ha2trenOnTt68OCBtra2VBSFTsp7L2ut5ufn5ZzTYer1uh4+fKharaY0TWWtVbfbVQhB169fV61WUwhBZ93e3p6yLFO9XpdzTsfx3qsoCjnn5JxTURRyzsk5J+ec9vb2FELQWddsNgUI0L/9278JUKfT0dva29vT2tqa6vW6ms2mXrx4of/5n/+R914nEUJQvV5Xmqba2trSYTqdjubn5/XkyRMByrJM7XZb3nu1Wi0B6na7Okqe58qyTN57HQadU3mey1or772OUhSF0jRVpVKR915nRQhB1WpVzWZT70ue5wIECFCe53oXIQT9VJ1OR2maql6vK4Sgg0IIStNUzjllWaY0TeWc075araY0TeW911nmvVe9Xpe1VkVR6GPS7XaVZZkAAarVagoh6OfgvZe1VrVaTc45ve7u3bvqdDqan58XoDzPta/VaglQt9vVUfI8V5Zl8t7rMOicyvNc1lp573UY773q9boAdTodnTU7Ozvy3ut98d4ryzIByrJMzjn9XEIIqlarAtTpdPS67e1tee8FaH5+Xvu89wJ0/fp1hRB0lm1tbQlQvV7Xx6jZbAoQoE6no59Tq9USoGazqdd1u1157zU/P680TeW91775+XmlaSrnnI6S57myLJP3XodB51Se57LWynuvwxRFIUCVSkXee32M8jwXoDzP9XNrt9sCVK/XFULQ67rdrgC1223t29raEqD5+XmdZd571et1ASqKQh+jbrcrQLVaTd57/Zy8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)


a0 = 1

a1 = 1

a2 = 2

an = 把n+1代入通项公式中

```java
public int climbStairs(int n) {
    n++;
    double temp = ((Math.pow((1 + Math.sqrt(5)) / 2, n) - Math.pow((1 - Math.sqrt(5)) / 2, n)) / Math.sqrt(5));
    System.out.println(temp);
    int result = (int)temp;
    return result;
}
```

#### 
#### 跳台阶扩展

[https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=23262&ru=%2Fpractice%2F8c82a5b80378478f9484d87d1c5f12a4&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=23262&ru=%2Fpractice%2F8c82a5b80378478f9484d87d1c5f12a4&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

```java
1. 确定dp[i] 到第i层楼梯的跳法
2. 递推公式 dp[i] = dp[i-1] + dp[i-2] + dp[0]
3. 初始化dp[0] = 1 从最底下直接跳到n层有1中方法
4. 遍历顺序，从前往后
5. 举例 5
1 1 2 4 8 16
0 1 2 3 4 5
```

```java
public int jumpFloorII(int target) {
    int[] dp = new int[target+1];
    dp[0] = 1;
    for (int i=1; i<=target; i++) {
        dp[i] = this.getSum(dp, i);
    }
    return dp[target];
}

private int getSum(int[] dp, int i) {
    int sum = 0;
    for (int j=0; j<i; j++) {
        sum += dp[j];
    }
    return sum;
}
```
* 优化
上面的方法，每次都是从dp[0]累加到dp[i-1]，每次都得从头开始计算，可以定义属性作为全局遍历，记录dp[0]累加到dp[i-2]的和

```java
private int sum = 0;
public int jumpFloorII(int target) {
    int[] dp = new int[target+1];
    dp[0] = 1;
    for (int i=1; i<=target; i++) {
        dp[i] = this.getSum(dp, i);
    }
    return dp[target];
}

private int getSum(int[] dp, int i) {
    this.sum += dp[i-1];
    return this.sum;
}
```
* 再次优化
对于数列

a1 = 1

a2 = 1

a3 = 1+1 = 2

a4 = 1+1+2=4

a5 = 1+1+2+4=8

...

an = Σai(i=1,2...n-1)

an = a1+a2+a3+...+an-2+an-1 = (a1+a2+a3+...+an-2) + an-1 = 2an-1

所以求第n项不用从0开始累加，它是前一个元素的二倍，**竟然是等比数列！**

```java
public int jumpFloorII(int target) {
    /*
    1. 确定dp[i] 到第i层楼梯的跳法
    2. 递推公式 dp[i] = dp[i-1] + dp[i-2] + dp[0]
    3. 初始化dp[0] = 1 从最底下直接跳到n层有1中方法
    4. 遍历顺序，从前往后
    5. 举例 5
    1 1 2 4 8 16
    0 1 2 3 4 5
    */
    int[] dp = new int[target+1];
    dp[0] = 1;
    for (int i=1; i<=target; i++) {
        if (i==1) {
            dp[i] = dp[0];
        } else {
            dp[i] = 2 * dp[i-1];
        }
    }
    return dp[target];
}
```
* 完全背包
```java
public int jumpFloorII(int target) {
    /*
        可以把n个台阶当成背包，每次跳的台阶当成物品
        可以重复跳某个数列的台阶，也就是物品可以重复，完全背包
        求的是装满背包的方式，而不是物品的价值
        所以并不是用价值的递推公式 dp[j] = max(dp[j], dp[j-weights[i]]+values[i])
        而是用组合数或排列数的递推公式 dp[j] += dp[j-nums[i]] (i=0, 1, 2...)
        1. dp[j] 跳到j台阶的方法
        2. dp[j] += dp[j-nums[i]] (i=0, 1, 2...)
        3. dp[0] = 1
        4. 先跳1个台阶，再跳2个台阶 与 先跳2个台阶，再跳1个台阶 是不一样的方式，所以是求排列数
        先遍历背包 再遍历物品
        5. [1, 2, 3, 4]  n=4
            j=0    j=1    j=2    j=3    j=4
     初始化   1      0      0      0      0
       i=1    1     0+1    0+1    0+2    0+4
       i=2    1      1     1+1    2+1    4+2
       i=3    1      1      2     3+1    6+1
       i=4    1      1      2      4     7+1
    */
    int[] dp = new int[target+1];
    dp[0] = 1;
    for (int j=1; j<=target; j++) {
        // i<=j 物品不能超过背包容量，不然装不进去
        for (int i=1; i<=j; i++) {
            dp[j] += dp[j-i];
        }
    }
    return dp[target];
}
```



#### 连续子数组的最大和

[https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=23259&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=23259&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

* 暴力
不是按照滑动窗口的尺寸依次遍历，而是根据起始位置分类遍历，从不同的起始位置依次向后累加，也能包含所有尺寸滑动窗口极其所有位置

```java
public int FindGreatestSumOfSubArray(int[] array) {
    int result = Integer.MIN_VALUE; // 求最大，赋值最小，以免影响结果
    for (int i=0; i<array.length; i++) {
        int count = 0;
        for (int j=i; j<array.length; j++) {
            count += array[j];
            result = count > result ? count : result;
        }
    }
    return result;
}
```

* 贪心
思路: 如果前面累加的数是负数，就从0开始重新累加，因为加上负数值肯定变小，不如不要

```java
public int FindGreatestSumOfSubArray(int[] array) {
    int result = Integer.MIN_VALUE;
    int count = 0;
    for (int i=0; i<array.length; i++) {
        count += array[i];
        // 记录最大值
        if (count > result) {
            result = count;
        }
        // 如果是负数，重新累加
        if (count < 0) count = 0;
    }
    return result;
}
```
* 动态规划
不是很理解，本题dp[i]的含义，不明白为啥是必须包括i的之前最大的和，而且以往就是dp数组的末尾值就是最优解，用贪心好理解

```java
  1. 确定dp数组含义
  dp[i] 包括下标i之前的最大连续子序列和为dp[i] 注意，是必须包括i的连续序列
  2. 确定递推公式
  只有两种方法得到dp[i] dp[i-1] + num[i] 还有num[i]
  dp[i] = max{dp[i-1]+num[i], num[i]}
  dp[i-1] 前i-i连续子序列最大值，num[i] 当前数组元素的值
  3. 初始化数组
  dp[0] = num[0]
  4. 遍历方向，从前向后
  5.举例 [1,-2,3,10,-4,7,2,-5]
  1 -1 3 13 9 16 18 13
```

```java
public int FindGreatestSumOfSubArray(int[] array) {
    int[] dp = new int[array.length];
    dp[0] = array[0];
    int result = array[0];
    for (int i=1; i<array.length; i++) {
        dp[i] = Math.max(dp[i-1]+array[i], array[i]);
        result = dp[i] > result ? dp[i] : result;
    }
    return result;
}
```
#### 连续子数组的最大和Ⅱ

[https://www.nowcoder.com/practice/11662ff51a714bbd8de809a89c481e21?tpId=13&tqId=2282583&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/11662ff51a714bbd8de809a89c481e21?tpId=13&tqId=2282583&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

* 动态规划dp[i][2]
使用dp[i][1]记录改最大值子序列的开始索引，由于可能存在最大值相等的序列，遍历dp[i][0]，找到最大值，使用i+1-dp[i][1]记录长度，找到长度最长时的开始索引startIndex = dp[i][1]

结束索引i+1

```java
1. dp[i][0] 以num[i] 结尾的之前的子序列最大和
   dp[i][1] 以num[i] 结尾的之前最大和子序列开始的索引
2. 由于是num[i] 结尾要么包含num[i-1] 要么不包含num[i-1]
   dp[i] = max{dp[i-1]+num[i], num[i]}
3. 初始化 dp[0][0] = num[0] dp[0][1] = 0
4. 从前向后
5. 举例  [1,-2,3,10,-4,7,2,-5]
    dp[i][0] 1 -1 3 13 9 16 18 13
    dp[i][1] 0  0 2  2 2  2  2  2
```

```java
import java.util.*;
public class Solution {
    public int[] FindGreatestSumOfSubArray (int[] array) {
        // write code here
        int[][] dp = new int[array.length][2];
        dp[0][0] = array[0];
        dp[0][1] = 0;
        int startIndex = 0;
        int maxSum = dp[0][0];
        for (int i=1; i<array.length; i++) {
            if (array[i] > dp[i-1][0]+array[i]) {
                dp[i][0] = array[i];
                startIndex = i;
            } else {
                dp[i][0] = array[i] + dp[i-1][0];
            }
            if (dp[i][0] > maxSum) {
                maxSum = dp[i][0];
            }
            dp[i][1] = startIndex;
        }
        for (int i=0; i<array.length; i++) {
            System.out.println(dp[i][0] + " " + dp[i][1]);
        }
        int maxLength = 0;
        int endIndex = 0;
        for (int i=0; i<array.length; i++) {
            if (dp[i][0]==maxSum && i+1-dp[i][1]>maxLength) {
                startIndex = dp[i][1];
                endIndex = i+1;
            }
        }
        return Arrays.copyOfRange(array, startIndex, endIndex);
    }
}
```
* 动态规划dp[i]
```java
举例  [1,-2,3,10,-4,7,2,-5]
dp[i][0] 1 -1 3 13 9 16 18 13
dp[i][1] 0  0 2  2 2  2  2  2
```
举例发现dp[i]为负数的时候，就是序列开始索引改变的时候，可以在dp中找到最大值dp[i]，该最大值前面最近的负数为dp[j] 子序列索引为[j+1, i+1) 长度i-j
```java
1. dp[i] 以num[i] 结尾的之前的子序列最大和
2. 由于是num[i] 结尾要么包含num[i-1] 要么不包含num[i-1]
   dp[i] = max{dp[i-1]+num[i], num[i]}
3. 初始化 dp[0] = num[0] 
4. 从前向后
5. 举例  [1,-2,3,10,-4,7,2,-5]
    dp[i] 1 -1 3 13 9 16 18 13
```

```java
public int[] FindGreatestSumOfSubArray (int[] array) {
    int length = array.length;
    int[] dp = new int[length];
    dp[0] = array[0];
    int maxSum = dp[0];
    for (int i=1; i<length; i++) {
        dp[i] = Math.max(dp[i-1]+array[i], array[i]);
        maxSum = Math.max(maxSum, dp[i]);
    }
    int startIndex = 0, endIndex = 0, maxLength=0;
    for (int i=0; i<length; i++) {
        if (dp[i] == maxSum) {
            // dp中寻找前面离得最近的负数
            int j;
            for (j=i-1; j>=0; j--) {
                if (dp[j] < 0) {
                    break;
                }
            }
            if (i-j>maxLength) {
                startIndex = j + 1;
                endIndex = i + 1;
            }
        }
    }
    return Arrays.copyOfRange(array, startIndex, endIndex);
}
```
#### 矩形覆盖

[https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=23283&ru=%2Fpractice%2F72a5a919508a4251859fb2cfb987a0e6&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=23283&ru=%2Fpractice%2F72a5a919508a4251859fb2cfb987a0e6&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

```java
1. dp[i] 能放下i个小木板的矩形，小木板的放法
2. dp[i] = dp[i-1] + dp[i-2]
在i-1放法后面都竖着放一块，在i-2后面都横着放两块
3. dp[0] = 1 dp[1] = 1 
根据题目给的条件dp[0]应该为0，但是为了使dp[2] = dp[0] + dp[1]成立，故使dp[0] = 1
4. 前至后遍历
5. 举例 n=5
1 1 2 3 5 8
0 1 2 3 4 5

public int rectCover(int target) {
    if (target == 0) {
        return 0;
    }
    int[] dp = new int[target+1];
    dp[0] = 1;
    dp[1] = 1;
    for (int i=2; i<=target; i++) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[target];
}
```
#### 买卖股票的最好时机Ⅰ

[https://www.nowcoder.com/practice/64b4262d4e6d4f6181cd45446a5821ec?tpId=13&tqId=625&ru=%2Fpractice%2F72a5a919508a4251859fb2cfb987a0e6&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/64b4262d4e6d4f6181cd45446a5821ec?tpId=13&tqId=625&ru=%2Fpractice%2F72a5a919508a4251859fb2cfb987a0e6&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

* 暴力法
```java
public int maxProfit (int[] prices) {
    // write code here
    int profit = 0;
    for (int i=0; i<prices.length-1; i++) {
        // 找到后面几天的最大值减去今天的值
        profit = Math.max(this.getMax(prices, i+1)-prices[i], profit);
    }
    return profit;
}
private int getMax(int[] prices, int startIndex) {
    int max = 0;
    for (int i=startIndex; i<prices.length; i++) {
        if (prices[i] > max) {
            max = prices[i];
        }
    }
    return max;
}
```
* 贪心
```java
// 贪心，记录历史最小值，利润是今天减去历史最小值
public int maxProfit (int[] prices) {
    // write code here
    int min = Integer.MAX_VALUE;
    int profit = 0;
    for (int i=0; i<prices.length; i++) {
        min = Math.min(min, prices[i]);
        profit = Math.max(prices[i]-min, profit);
    }
    return profit;
}
```
* 动态规划
```java

 1. dp[i][0] 第i天持有股票，手里的最大现金
    dp[i][1] 第i天不持有股票，手里的最大现金
 2. dp[i][0] 两种方式得到，第i天买入，继续保持dp[i-1][0]股票
    dp[i][0] = max{-prices[i], dp[i-1][0]}
    dp[i][1] 两种方式得到，第i天卖出持有的股票，或者继续保持dp[i-1][1]不持有的状态
    dp[i][1] = max{dp[i-1][0]+prices[i], dp[i-1][1]}
 3. dp[0][0] = -prices[0] 第0天持有股票，就是买入，-prices[0]
    dp[0][1] = 0 第0天不持有股票，不买入，0
 4. 从前往后
 5. 举例
    [8,9,2,5,4,7,1]
    dp[i][0] -8 -8 -2 -2 -2 -2 -1
    dp[i][1]  0  1  1  3  3  5  5 
```

```java
public int maxProfit (int[] prices) {
    // write code here
    if (prices.length == 0) {
        return 0;
    }
    int[][] dp = new int[prices.length][2];
    dp[0][0] = -prices[0];
    dp[0][1] = 0;
    for (int i=1; i<prices.length; i++) {
        dp[i][0] = Math.max(-prices[i], dp[i-1][0]);
        dp[i][1] = Math.max(prices[i]+dp[i-1][0], dp[i-1][1]);
    }
    return dp[prices.length-1][1] > 0 ? dp[prices.length-1][1] : 0; 
}
```

#### 买卖股票的最好时机Ⅱ

[https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

* 动态规划
```java
1.  dp[i][0] 第i天持有股票，所获得的最大金币
    dp[i][1] 第i天不持有股票，所获得的最大金币
2.  dp[i][0] 持有股票，保持dp[i-1][0]现状，或者买入dp[i-1][1]-prices[i]
    dp[i][0] = max{dp[i-1][0], dp[i-1][1]-prices[i]}
    dp[i][1] 不持有股票，保持dp[i-1][1]现状，或者卖出dp[i-1][0] + prices[i]
    dp[i][0] = max{dp[i-1][1], dp[i-1][0] + prices[i]}
3.  dp[i][0] = -prices[0] dp[i][1] = 0
4.  前后
5.  举例
    [1,2,3,4,5]
    dp[i][0]    -1  -1  -1  -1  -1
    dp[i][1]     0   1   2   3   4
```

```java
public int maxProfit(int[] prices) {
    int length = prices.length;
    int[][] dp = new int[length][2];
    dp[0][0] = - prices[0];
    dp[0][1] = 0;
    for (int i=1; i<length; i++) {
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]-prices[i]);
        dp[i][1] = Math.max(dp[i-1][0] + prices[i], dp[i-1][1]);
    }
    return dp[length-1][1];
}
```
* 贪心
```java
public int maxProfit(int[] prices) {
    int result = 0;
    for (int i=1; i<prices.length; i++) {
         // 收集正利润
        result += Math.max(prices[i] - prices[i-1], 0);
    }
    return result;
}
```
#### 买卖股票的最好时机Ⅲ

[https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/submissions/)

* 单调增区间 **×想错了**
想把每个增区间的最大利润找出来，前两个的和就是最大利润，想错了

![图片](data:image/png;base64,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)


[1,2,4,2,5,7,2,4,9,0]

这种情况下，写的算法只会在各自的区间内计算最大利润

[1,2,4,     2,5,7,     2,4,9,    0]

但是最大利润是

[1,2,4,2,5,7,2,4,9,0]

```java
public int maxProfit(int[] prices) {
    if (prices.length == 1) {
        return 0;
    }
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> temp = new ArrayList<>();
    temp.add(prices[0]);
    for (int i=1; i<prices.length; i++) {
        if (prices[i] > prices[i-1]) {
            temp.add(prices[i]);
        } else {
            list.add(new ArrayList<>(temp));
            temp.clear();
            temp.add(prices[i]);
        }
    }
    list.add(temp);
    List<Integer> profit = new ArrayList<>();
    for (List<Integer> item : list) {
        if (item.size() <= 1) {
            continue;
        }
        profit.add(item.get(item.size()-1) - item.get(0));
    }
    if (profit.size() == 0) {
        return 0;
    } else if (profit.size() == 1) {
        return profit.get(0);
    } else {
        Collections.sort(profit);
        return profit.get(profit.size()-1) + profit.get(profit.size()-2);
    }
}
```
* 动态规划
```java
1. dp[i][j]
j= 0, 1, 2, 3, 4
0 没有操作
1 第一次买入股票
2 第一次卖出股票
3 第二次买入股票
4 第二次卖出股票
dp[i][j]表示第i天状态i手中的最大金币数量
2. 
没有操作
    dp[i][0] = dp[i-1][0]
第一次买入股票
    第一次买入股票可以保持前一天的第一次买入状态dp[i-1][1]，或者今天第一次买入dp[i-1][0] - prices[i]
    dp[i][1] = max{dp[i-1][1], dp[i-1][0]-prices[i]}
第一次卖出股票
    第一次卖出股票可以保持前一天第一次卖出的状态dp[i-1][2]，或者今天第一次卖出dp[i-1][1] + prices[i]
    dp[i][2] = max{dp[i-1][2], dp[i-1][1]+prices[i]}
第二次买入股票
    第一次买入股票可以保持前一天的第二次买入状态dp[i-1][3]，或者今天第二次买入dp[i-1][2] - prices[i]
    dp[i][3] = max{dp[i-1][3], dp[i-1][2] - prices[i]}
第二次卖出股票
    第二次卖出股票可以保持前一天第二次卖出的状态dp[i-1][4]，或者今天第二次卖出dp[i-1][3] + prices[i]
    dp[i][4] = max{dp[i-1][4], dp[i-1][3]+prices[i]}
3.
初始化 dp[0][0] = 0    dp[0][1] = -prices[0]    dp[0][2] = 0    dp[0][3] = -prices[0]    dp[0][4] = 0
4. 前往后
5.
         [1,   2,   3,   4,   5]

dp[i][0]  0    0    0    0    0
dp[i][1] -1   -1   -1   -1   -1
dp[i][2]  0    1    2    3    4
dp[i][3] -1   -1   -1   -1   -1
dp[i][4]  0    1    2    3    4
```

```java
public int maxProfit(int[] prices) {
    int length = prices.length;
    int[][] dp = new int[length][6];
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    dp[0][2] = 0;
    dp[0][3] = -prices[0];
    dp[0][4] = 0;
    for (int i=1; i<length; i++) {
        dp[i][0] = dp[i-1][0];
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i]);
        dp[i][2] = Math.max(dp[i-1][2], dp[i-1][1]+prices[i]);
        dp[i][3] = Math.max(dp[i-1][3], dp[i-1][2]-prices[i]);
        dp[i][4] = Math.max(dp[i-1][4], dp[i-1][3]+prices[i]);
    }
    return dp[length-1][4];
}
```
#### 买卖股票的最好时机Ⅳ

[https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

* 动态规划
```java
1.
dp[i][0] 第i天没有操作
dp[i][2*k]
奇数次买入股票，偶数次卖出股票
dp[i][j] 第i天买入或卖出股票最大金币数
2.
dp[i][0] = dp[i-1][0]
j 为奇数，买入股票，保持当前状态，或买入
dp[i][j] = max(dp[i-1][j], dp[i-1][j-1] - prices[i])
j 为偶数，卖出股票，保持当前状态，或卖出
dp[i][j] = max(dp[i-1][j], dp[i-1][j-1] + prices[i])
3.
dp[0][0] = 0
j为奇数
dp[0][j] = -prices[0]
j为偶数
dp[0][j] = 0
4. 从前往后
5. k=2 
         [1,   2,   3,   4,   5]

dp[i][0]  0    0    0    0    0
dp[i][1] -1   -1   -1   -1   -1
dp[i][2]  0    1    2    3    4
dp[i][3] -1   -1   -1   -1   -1
dp[i][4]  0    1    2    3    4
```

```java
public int maxProfit(int k, int[] prices) {
    int length = prices.length;
    if (length == 0) {
        return 0;
    }

    int[][] dp = new int[length][2 * k + 1];
    for (int i=1; i<=2*k-1; i+=2) {
        dp[0][i] = -prices[0];
    }
    for (int i=1; i<length; i++) {
        // 循环计算买入与卖出的最大金币
        for (int j=1; j<=2*k-1; j+=2) {
            dp[i][j] = Math.max(dp[i-1][j], dp[i-1][j-1] - prices[i]);
            dp[i][j+1] = Math.max(dp[i-1][j+1], dp[i-1][j] + prices[i]);
        }
    }
    return dp[length-1][2 * k];
}
```

#### 
#### 买卖股票最好时机冷冻期

[https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

* 动态规划
```java
1.
dp[i][j]
j = 0, 1, 2, 3
0: 持有股票
1: 卖出无操作状态
2: 今天卖出
3: 冷冻期
dp[i][j] 第i天状态j最大金币

2.
0: 持有股票 a.之前就买了dp[i-1][0] 
           b.今天才买: 昨天是冷冻期 dp[i-1][3] - prices[i]
                    昨天是卖出无操作状态 dp[i-1][1] - prices[i]
    dp[i][0] = max(dp[i-1][0], dp[i-1][3] - prices[i], dp[i-1][1] - prices[i])

1: 卖出无操作状态 a.昨天是卖出无操作状态 dp[i-1][1] 
                b.昨天是冷冻期 dp[i-1][3]
    dp[i][1] = max(dp[i-1][1], dp[i-1][3])

2: 今天卖出 a.昨天买入 dp[i-1][0] + prices[i]
    dp[i][2] = dp[i-1][0] + prices[i]

3: 冷冻期 a.昨天卖出 dp[i-1][2]
    dp[i][3] = dp[i-1][2]

3.
dp[0][0] = -prices[0]
dp[0][1] = 0
dp[0][2] = 0
dp[0][3] = 0

4. 后往前
5.       [1,   2,    3,    0,    2]
dp[i][0] -1   -1    -1     1     1
dp[i][1]  0    0     0     1     2
dp[i][2]  0    1     2    -1     3
dp[i][3]  0    0     1     2    -1
dp[length-1][1] dp[length-1][2] dp[length-1][3] 的最大值 
```

```java
public int maxProfit(int[] prices) {
    int length = prices.length;
    int[][] dp = new int[length][4];
    dp[0][0] = -prices[0];
    for (int i=1; i<length; i++) {
        dp[i][0] = Math.max(dp[i-1][0], Math.max(dp[i-1][3] - prices[i], dp[i-1][1] - prices[i]));
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][3]);
        dp[i][2] = dp[i-1][0] + prices[i];
        dp[i][3] = dp[i-1][2];
    }
    return Math.max(dp[length-1][1], Math.max(dp[length-1][2], dp[length-1][3]));
}
```
#### 买卖股票最好时机手续费

[https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

* 动态规划
```java
1.
dp[i][0] 第i天持有股票最大金币
dp[i][1] 第i天不持有股票最大金币
2.
持有股票 昨天就持有dp[i-1][0]  今天买 dp[i-1][1] - prices[i]
dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] - prices[i]);
不持有股票 昨天就没有dp[i-1][1], 今天卖 dp[i-1][0] + prices[i] - fee
dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] + prices[i] - fee);
3.
dp[0][0] = -prices[0]
dp[0][1] = 0
4.
从前往后
5.       [1, 3, 2, 8, 4, 9] fee = 2
dp[i][0] -1 -1 -1 -1  1  1
dp[i][1]  0  0  0  5  5  8
```

```java
public int maxProfit(int[] prices, int fee) {
    int length = prices.length;
    int[][] dp = new int[length][2];
    dp[0][0] = -prices[0];
    for (int i=1; i<length; i++) {
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] - prices[i]);
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] + prices[i] - fee);
    }
    return dp[length-1][1];
}
```
* 贪心
在贪心算法：122.买卖股票的最佳时机II中使用贪心策略不用关心具体什么时候买卖，只要收集每天的正利润，最后稳稳的就是最大利润了。

而本题有了手续费，就要关系什么时候买卖了，因为计算所获得利润，需要考虑买卖利润可能不足以手续费的情况。

如果使用贪心策略，就是最低值买，最高值（如果算上手续费还盈利）就卖。

此时无非就是要找到两个点，买入日期，和卖出日期。

买入日期：其实很好想，遇到更低点就记录一下。

卖出日期：这个就不好算了，但也没有必要算出准确的卖出日期，只要当前价格大于（最低价格+手续费），就可以收获利润，至于准确的卖出日期，就是连续收获利润区间里的最后一天（并不需要计算是具体哪一天）。

所以我们在做收获利润操作的时候其实有三种情况：

情况一：收获利润的这一天并不是收获利润区间里的最后一天（不是真正的卖出，相当于持有股票），所以后面要继续收获利润。

情况二：前一天是收获利润区间里的最后一天（相当于真正的卖出了），今天要重新记录最小价格了。

情况三：不作操作，保持原有状态（买入，卖出，不买不卖）


```java
public int maxProfit(int[] prices, int fee) {
    int result = 0;
    int minPrice = prices[0];

    for (int i=1; i<prices.length; i++) {
        // 当 minPrice是prices[i] - fee时，如果价格不比这个低，就没必要
        // 重新开启一次交易，因为会产生手续费，如果比这个低了，说明付了手续费
        // 也比持续收获利润的这种情况高
        if (prices[i] < minPrice) {
            minPrice = prices[i];
        } else if (prices[i] > minPrice + fee) {
            result += prices[i] - minPrice - fee;
            // 防止重复计算手续费
            minPrice = prices[i] - fee;
        } 
    }
    return result;
}
```

#### 
#### 礼物的最大价值

[https://www.nowcoder.com/practice/2237b401eb9347d282310fc1c3adb134?tpId=13&tqId=2276652&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/2237b401eb9347d282310fc1c3adb134?tpId=13&tqId=2276652&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

```java
1. dp[row][column] 到达该格子所获得的最大价值
2. dp[row][column] = max{左侧格子， 上侧格子} + grid[row][column]
3. dp[0][0] = grid[0][0]
4. 从左至右，从上至右
5. 举例
```

```java
public int maxValue (int[][] grid) {
    if (grid.length==0 || grid[0].length==0) {
        return 0;
    }
    int rows = grid.length;
    int columns = grid[0].length;
    int[][] dp = new int[rows][columns];
    dp[0][0] = grid[0][0];
    for (int row=0; row<rows; row++) {
        for (int column=0; column<columns; column++) {
            if (row==0 && column==0) {
                continue;
            }
            // 注意处理越界情况，上侧格子只能可行越界，左侧格子只能可列越界
            int top = row-1<0 ? 0 : dp[row-1][column];
            int left = column-1<0 ? 0 : dp[row][column-1];
            dp[row][column] = Math.max(left, top) + grid[row][column];
        }
    }
    return dp[rows-1][columns-1];
}
```
#### 最长不含重复字符的字符串

* 双指针
left right指针，右指针逐个向右移动，当[left, right)区间中有值与右指针指向的值重复时，左指针移动到重复值的下一个位置

```java
private int maxLength;
public int lengthOfLongestSubstring (String s) {
    if (s.length() <= 1) {
        return s.length();
    }
    for (int left=0, right=1; right<s.length(); right++) {
        int index = this.indexOfRepeat(left, right, s);
        if (index == -1) {
            continue;
        } else {
            left = index + 1;
        }
    }
    return this.maxLength;
}

private int indexOfRepeat(int start, int target, String s) {
    int i;
    // 查找是否有重复的
    for (i=start; i<target; i++) {
        if (s.charAt(i) == s.charAt(target)) {
            break;
        }
    }
    // 记录最长的长度
    if (target + 1 - start > this.maxLength) {
        this.maxLength = target + 1 - start;
        // i < target 说明找到了重复的，不能包括长度不能包括当前字符
        if (i < target) {
            this.maxLength--;
        }
    }
    // 找到重复的, 返回重复的字符索引，否则返回-1
    if (i < target) {
        return i;
    } else {
        return -1;
    }
}
```
* 双指针，利用substring与indexOf
上述方法，自定义了查找重复字符的索引，避免重复造轮子，使用String内置的方法indexOf()，但是需要利用substring把字符串切割处理，才能使用

```java
public int lengthOfLongestSubstring (String s) {
    // write code here
    if (s.length() <= 1) {
        return s.length();
    }
    int maxLength = 0;
    for (int left=0, right=1; right<s.length(); right++) {
        // 在[left, right)区间查找是否有重复字符
        int index = s.substring(left, right).indexOf(s.charAt(right));
        if (index != -1) {
            // 如果有重复字符，需要加上偏移量left，因为切割了字符串
            index += left;   
        }
        // 保存最长的长度
        if (maxLength < right + 1 - left) {
            maxLength = right + 1 - left;
            // 如果有重复的，长度不能包含当前字符，所以减一
            if (index != -1) {
                maxLength--;
            }
        }
        if (index != -1) {
            // 如果有重复的左指针指向重复的下一个
            left = index + 1;
        }
    }
    return maxLength;
}
```
* 双指针利用set
上述方法都是遍历查找string，看是否存在重复的char，可以利用set判断是否重复，这样在没有重复的时候就不用重复查找了

```java
public int lengthOfLongestSubstring (String s) {
    // write code here
    if (s.length() <= 1) {
        return s.length();
    }
    int maxLength = 0;
    Set<Character> set = new HashSet<>();
    set.add(s.charAt(0));
    for (int left=0, right=1; right<s.length(); right++) {
        char current = s.charAt(right);
        // 使用变量记录添加的结果，而不能每次都通过set.add()判断，因为第一次添加成功的话，再添加就会失败，因为set内容改变了
        boolean isRepeat = !set.add(current);
        // 计算当前的长度，包括了当前字符
        int currentLength = right + 1 - left;
        if (currentLength > maxLength) {
            maxLength = currentLength;
            // 如果当前是重复字符的话，还需减去当前字符
            if (isRepeat) {
                maxLength--;
            }
        }
        // 如果当前是重复字符的话，不停右移left
        if (isRepeat) {
            while (set.contains(current)) {
                // left++，最终会指向被移动的重复字符的右边
                set.remove(s.charAt(left++));
            }
            // set中重复的current已经被移除了，还需要加上current
            set.add(current);
        }
    }
    return maxLength;
}
```
* 动态规划
```java
1. dp[i] 索引为i字符结尾的不重复子串的最大长度
2. 索引为i的字符，距离重复字符距离为d
    if d<=dp[i-1] dp[i] = d
    else dp[i] = dp[i-1] + 1
3. dp[0] = 1
4. 前至后
5. "abcabcbb"
    1 2 3 3 3 3 2 1
```

```java
public int lengthOfLongestSubstring (String s) {
    // write code here
    if (s.length() <= 1) {
        return s.length();
    }
    int maxLength = 1;
    int CHAR_NUMBER = 95;
    int length = s.length();
    // 记录字符上一次出现的索引，32～126(共95个)是字符：32是空格，
    // 其中48～57为0到9十个阿拉伯数字，65～90为26个大写英文字母，
    // 97～122号为26个小写英文字母，其余为一些标点符号、运算符号等。
    int[] previousIndex = new int[CHAR_NUMBER];
    // 索引初始化为-1
    for (int i=0; i<CHAR_NUMBER; i++) {
        previousIndex[i] = -1;
    }
    int[] dp = new int[length];
    // dp数组初始化
    dp[0] = 1;
    previousIndex[(int)s.charAt(0)-(int)' '] = 0;
    for (int i=1; i<length; i++) {
        int current = (int)s.charAt(i) - (int)' ';
        // 当前字符出现过
        if (previousIndex[current] == -1) {
            dp[i] = dp[i-1] + 1;
        } 
        // 当前字符未出现过 
        else { 
            int distance = i - previousIndex[current];
            // 距离小于等于dp[i-1]，说明在上一个字符串中有重复的，dp[i] = distance
            if (distance <= dp[i-1]) {
                dp[i] = distance;
            } 
            // 距离大于dp[i-1]，说明在上一个字符串中没重复的
            else { 
                dp[i] = dp[i-1] + 1;
            }
        }
        if (maxLength < dp[i]) {
            maxLength = dp[i];
        }
        previousIndex[current] = i;
    }
    return maxLength;
}
```


#### 数字翻译成字符串

[https://www.nowcoder.com/practice/046a55e6cd274cffb88fc32dba695668?tpId=13&tqId=1024831&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/046a55e6cd274cffb88fc32dba695668?tpId=13&tqId=1024831&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

* 递归
分类加法

有点类似于爬楼梯，当前可以翻译一个字符，也可以翻译两个字符，把两种情况相加

```java
public int solve (String nums) {
    // write code here
    return dfs(nums.toCharArray(), 0);
}

private int dfs(char[] nums, int startIndex) {
    // 递归终止条件，判断到末尾，这种翻译可能成立
    if (startIndex == nums.length) {
        return 1;
    }
    // 有两种翻译方式，一种是翻译一个字符，一种是翻译两个字符
    // 翻译一个字符，不是'0'都可以翻译，是'0'返回0，表示该种翻译方式不可行
    int single;
    if (nums[startIndex] == '0') {
        return 0;
    } else {
        single = dfs(nums, startIndex+1);         
    }
    // 翻译两个字符，要求至少还剩两个字符，并且 当第一个字符是'1'的时候，肯定能两个字符一起翻译 
    // 当第一个字符是'2'的时候，第二字符小于等于'6'
    // 其他情况不能翻译，返回0
    int double_ = 0;   
    if ((startIndex<nums.length-1) && (nums[startIndex]=='1' || nums[startIndex]=='2' && nums[startIndex+1]<='6')) {
        double_ = dfs(nums, startIndex+2);
    }
    return single + double_;
}
```
* 动态规划
```java
  1. dp[i] 包括i为索引的前面的字符串所有的译码方式
  2. dp[i] = dp[i-1] + dp[i-2] // 当前数字，可以单独变成英文字母，也可以与前一个一起变成英文字母
     dp[i] = dp[i-1] // 当前数字，可以由单独数字变成英文字母
     dp[i] = dp[i-2] // 当前数字与前一个字符，两个字符可以变成英文字母
     dp[i] = 0 // 一个字符，两个字符都不能翻译 当前为0，dp[i]为0，后续dp[i+1]为0，继而dp[i+2]为0
  3. dp[0] = 0 或 1
  4. 前至后
  5. 123
      1 2 3
```

```java
public int solve (String nums) {
    // write code here
    int length = nums.length();
    if (length == 0) {
        return 0;
    }
    int[] dp = new int[length];
    dp[0] = nums.charAt(0)=='0' ? 0 : 1;
    for (int i=1; i<length; i++) {
        int single = 0;
        int double_ = 0;
        char previous = nums.charAt(i-1); 
        char current = nums.charAt(i);
        if (current != '0') {
            single = dp[i-1];
        }
        if (previous=='1' || previous=='2' && current <= '6') {
            if (i == 1) {
                double_ = 1;
            } else {
                double_ = dp[i-2];
            }
        }
        dp[i] = single + double_;
    }
    System.out.println(Arrays.toString(dp));
    return dp[length-1];
}
```

#### 最长递增序列长度

[https://leetcode-cn.com/problems/longest-increasing-subsequence/](https://leetcode-cn.com/problems/longest-increasing-subsequence/submissions/)

```java
/*
    1. 确定下标含义
    dp[i] 以nums[i] 结尾的，前面递增子序列的最长长度
    2. 递推公式
    位置的最长升序子序列，等于之前的最长升序子序列最大值+1
    for j in range(0, i):
        if (nums[i] > nums[j]):
            max_value = max(dp[j], max_value)
        dp[i] = max_value+1
    3. 初始化
    各个位置，至少长度为1，都初始化为1
    4. 从前向后遍历
    5. 举例[10,9,2,5,3,7,101,18]
    1 1 1 2 2 3 4 4
*/
public int lengthOfLIS(int[] nums) {
    int[] dp = new int[nums.length];
    for (int i=0; i<dp.length; i++) {
        dp[i] = 1;
    }
    int result = 1;
    for (int i=1; i<dp.length; i++) {
        for (int j=0; j<i; j++) {
            if (nums[i] > nums[j]) {
                dp[i] = Math.max(dp[i], dp[j]+1);
            }
        }
        result = Math.max(dp[i], result);
    }
    return result;
    
}
```

#### 最长等差数列

[https://leetcode-cn.com/problems/longest-arithmetic-subsequence/](https://leetcode-cn.com/problems/longest-arithmetic-subsequence/)

```java
  1 dp[i][d] 包括i的之前的数组公差为d的子序列长度
  2. dp[i][d1] = dp[i-1][d1] + 1
     dp[i][d2] = dp[i-2][d2] + 1
  3. dp[0][*] = 1
  4. 前后顺序
  5.      [9,   4,   7,   2,   10]
dp[i][-5]  1    2         2
dp[i][3]   1         2         3
dp[i][-2]            2    2
dp[i][-7]                 2
dp[i][8]                       2
dp[i][6]                       2
dp[i][1]                       2
```

```java
    public int longestArithSeqLength(int[] nums) {
        int length = nums.length;
        if (length <= 2) {
            return length;
        }
        Map<Integer, Integer> dp[] = new Map[length];
        for (int i=0; i<length; i++) {
            dp[i] = new HashMap<>();
        }
        int result = 0;
        for (int i=1; i<length; i++) {
            for (int j=0; j<i; j++) {
                int distance = nums[i] - nums[j];
                dp[i].put(distance, dp[j].getOrDefault(distance, 1) + 1);
            }
            for (Integer item : dp[i].values()) {
                result = Math.max(result, item);
            }
        }
        return result;
    }
```
#### 最长等比子序列

```java
public int longestSeqLength(int[] nums) {
    int length = nums.length;
    if (length <= 2) {
        return length;
    }
    Map<Double, Integer> dp[] = new Map[length];
    for (int i=0; i<length; i++) {
        dp[i] = new HashMap<>();
    }
    int result = 0;
    for (int i=1; i<length; i++) {
        for (int j=0; j<i; j++) {
            double ratio = (double)nums[i] / nums[j];
            dp[i].put(ratio, dp[j].getOrDefault(ratio, 1) + 1);
        }
        for (Integer item : dp[i].values()) {
            result = Math.max(result, item);
        }
    }
    return result;
}
```
