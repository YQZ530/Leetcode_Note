Dynamic Programming
先决定DP table 能做什么, 和下标含义
有些情况比如palindromic substring 用来Boolean table 然后查看该table [i][j] 区间是否palindromic
有些时候用来存储数据
2.确认递推公式
3.dp 数组初始化
4.遍历顺序


## 05. Longest Palindromic substring
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

```

```
相似
##  647 Palindromic Substrings 

1. dp table 记录 substRing i..j 是不是回文
2. s[i] == s[j]
   情况1 j-i +1 距离为1，比如aa
   dp[i][j] = true
   情况2 i...j 距离大于二 比如 abba
   check dp[i+1][j-1] >=1
   dp[i][j] = true

    然后数多少 dp[i][j] is T

   
4. intial dp[i][j] =1
5. 下到上， 左到右


  ## 516 Longest Palindromic Subsequence
注意题目要求是subsequence
1. dp table 记录 substRing i..j 的最长subsequence
2. s[i] == s[j]
   情况1 j-i +1 距离<=1，比如aa (但已经在情况2 里面）
   dp[i][j] = +=1

    情况2 i...j 距离大于二 比如 abba  or abca
      dp[i][j]   = dp[i+1][j-1] +2
 # 
   if  s[i] != s[j]
   
      dp[i][j]   = max( dp[i][j-1], dp[i+1][j])

遍历顺序， 下到上，左到右
   

