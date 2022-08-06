---
title: 模拟-剑指offer
date: 2022-03-02 14:51:20
tags: 
    - 刷题
    - 剑值offer
    - 模拟
---
#### 顺时针打印矩阵

[https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=23279&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=23279&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

使用递归，先处理周围一圈，然后进入内部小矩阵，
<!-- more -->
```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
        ArrayList<Integer> result = new ArrayList<>();
        if (matrix.length==0 || matrix[0].length==0) {
            return result;
        }
        this.addMatrix(result, matrix, 0, matrix.length-1, 0, matrix[0].length-1);
        return result;
    }
    
    private void addMatrix(ArrayList<Integer> result, int[][] matrix, int startRow, int endRow, int startColumn, int endColumn) {
        if (result.size() == matrix.length*matrix[0].length) {
            return;
        }
        for (int i=startColumn; i<=endColumn; i++) {
            result.add(matrix[startRow][i]);
        }
        for (int i=startRow+1; i<=endRow; i++) {
            result.add(matrix[i][endColumn]);
        }
        // 只剩一横或一竖时，防止重复加入
        for (int i=endColumn-1; i>=startColumn && startRow!=endRow; i--) {
            result.add(matrix[endRow][i]);
        }
        for (int i=endRow-1; i>=startRow+1 && startColumn!=endColumn; i--) {
            result.add(matrix[i][startColumn]);
        }
        addMatrix(result, matrix, startRow+1, endRow-1, startColumn+1, endColumn-1);
    }
}
```
#### 连续扑克牌

[https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=23252&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=23252&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

* 直接思路
对数组排序，如果有相同的非零元素，直接返回false

计算0元素的个数，找到非零元素开始的位置

归纳发现，如果非零元素的相邻差小于允许的值，就是连续的

允许的值maxDelta = numbers.length - zeroNumber - 1 + zeroNumber;

允许的值，发现非零元素作差如果比非零元素的数量少1，则正好是连续的，如果有0元素的存在，一个0元素可以当作任意元素，填充到相应位置，弥补一个差值

```java
public boolean IsContinuous(int [] numbers) {
    Arrays.sort(numbers);
    // 对数组排序，如果有相同的非零元素，直接返回false
    if (this.hasRepeat(numbers)) {
        return false;
    }
    // 计算0元素的个数，找到非零元素开始的位置
    int zeroNumber = 0;
    int noZeroIndex = 0;
    while (numbers[noZeroIndex] == 0) {
        zeroNumber++;
        noZeroIndex++;
    }
    // 计算相邻差值
    int delta = 0;
    for (int i=noZeroIndex, j=noZeroIndex+1; j<numbers.length; i++, j++) {
        delta += numbers[j] - numbers[i];
    }
    // 最大允许的差值 发现非零元素相邻差值如果比非零元素的数量少1，则正好是连续的，
    // 如果有0元素的存在，一个0元素可以当作任意元素，填充到相应位置，
    // 可以弥补一个差值，如果没有需要弥补的差值，则可以填充到开头或者结尾
    int maxDelta = numbers.length - zeroNumber - 1 + zeroNumber;
    return delta <= maxDelta;
    
}

private boolean hasRepeat(int[] numbers) {
    for (int i=0, j=1; j<numbers.length; i++, j++) {
        if (numbers[i]==numbers[j] && numbers[j]!=0) {
            return true;
        }
    }
    return false;
}
```
* 优化
零元素的个数，就是非零元素开始索引的位置

发现增加一个0元素，最大允许的相邻差值增加1

但是同时，由于计算相邻差值的非零元素减少了一个，计算的总相邻差值也会减少1，最大允许的相邻差值减少1

所以总体上没变

maxDelta = numbers.length - 1

```java
public boolean IsContinuous(int [] numbers) {
    Arrays.sort(numbers);
    if (this.hasRepeat(numbers)) {
        return false;
    }
    int zeroNumber = 0;
    while (numbers[zeroNumber] == 0) {
        zeroNumber++;
    }
    int delta = 0;
    for (int i=zeroNumber, j=zeroNumber+1; j<numbers.length; i++, j++) {
        delta += numbers[j] - numbers[i];
    }
    int maxDelta = numbers.length - 1;
    return delta <= maxDelta;
}

private boolean hasRepeat(int[] numbers) {
    for (int i=0, j=1; j<numbers.length; i++, j++) {
        if (numbers[i]==numbers[j] && numbers[j]!=0) {
            return true;
        }
    }
    return false;
}
```
#### 字符串转成整数

* 直接思路
1. 先除去空格，除去后如果为空直接返回0
2. 提取符号位，判断是否为负数
3. 提取开头的连续数字
4. 除去开头的0
5. 转成数字
    1. 10位数以上直接截断
    2. 10位数超过整型范围，直接截断
    3. 剩下的都是整型范围内，转成整型
```java
public int StrToInt (String s) {
    // 除去空格
    s = s.trim();
    // 提取空格后如果为空直接返回0
    if (s.length() == 0) {
        return 0;
    }
    // 提取符号位
    boolean isNegative = false;
    if (s.charAt(0)=='-' || s.charAt(0)=='+') {
        if (s.charAt(0) == '-') {
            isNegative = true;
        }
        if (s.length() == 1) {
            return 0;
        }
        s = s.substring(1);
    }
    // 只提取开头连续的纯数字
    s = this.getNumber(s);
    // 除去开头的0
    s = this.removeStartZero(s);
    // 总体上分两种情况 10位数(含)以下和10位数以上
    if (s.length() <= 10) {
        // 等于10位数的时候，利用compareTo方法判断是否在整形范围内，如果不在则返回最大或最小值
        if (s.length() == 10) {
            if (isNegative) {
                String minString = ("" + Integer.MIN_VALUE).substring(1);
                if (s.compareTo(minString) >= 0) {
                    return Integer.MIN_VALUE;
                }
            } else {
                String maxString = "" + Integer.MAX_VALUE;
                if (s.compareTo(maxString) >= 0) {
                    return Integer.MAX_VALUE;
                }
            }
        }
        // 在整型范围内的，转成整形
        if (isNegative) {
            return -this.toInt(s);
        } else {
            return this.toInt(s);
        }
    }
    //10位数以上返回最大值或最小值
    else { 
        if (isNegative) {
            return Integer.MIN_VALUE;
        } else {
            return Integer.MAX_VALUE;
        }
    }
}
// 提取开头的连续数字
private String getNumber(String s) {
    int i=0;
    while (i<s.length() && s.charAt(i)>='0' && s.charAt(i)<='9') {
        i++;
    }
    return s.substring(0, i);
}
// 出去开头的0
private String removeStartZero(String s) {
    int i=0;
    while (i<s.length() && s.charAt(i)=='0') {
        i++;
    }
    return s.substring(i);
}
// 转成整型
private int toInt(String s) {
    int n = 0;
    for (int i=0; i<s.length(); i++) {
        n *= 10;
        n += s.charAt(i) - '0';
    }
    return n;
}
```
