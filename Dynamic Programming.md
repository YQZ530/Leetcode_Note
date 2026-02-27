Dynamic Programming
先决定DP table 能做什么, 和下标含义
有些情况比如palindromic substring 用来Boolean table 然后查看该table [i][j] 区间是否palindromic
有些时候用来存储数据
2.确认递推公式

3.dp 数组初始化
4.遍历顺序

0005. Longest Palindromic substring
   参考思路 https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0005.%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E4%B8%B2.md
For i .. len(s)-1
   for j .. start from i...len(s)
if s[i] !=s[j] 
   dp[i][j] = false
if s[i] == s[j]
   情况1： a or aa
   j - i <1 , 也是substring
   情况2   abba， i,j 指向a ;   abcba, ij 指向最左最右a
     -check看dp[i+1][j-1] 是不是回文

然后需要更新最长距离 
if(dp[i][j] is true and j-i+1 > max length
   update maxleng - j-i +1

遍历顺序很重要
这次我们需要根据 dp[i+1][j-1] 来update [i][j]， 所以下到上


