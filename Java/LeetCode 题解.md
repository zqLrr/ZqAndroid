# LeetCode 题解

## 1、3的幂

* 题目描述

给定一个整数，写一个函数来判断它是否是 3 的幂次方。如果是，返回 true ；否则，返回 false 。

整数 n 是 3 的幂次方需满足：存在整数 x 使得 n == 3^x

* 个人解题：试除法

  思路：一直是3的倍数，最后一个余数是1；其次需要考虑特殊情况，0不是幂次方的结果，虽然它的余数是0，1是3^0的结果。

  ```java
  public boolean isPowerOfThree(int n) {
          if (n == 0) {
              return false;
          }
          if (n == 1) {
              return true;
          }
          while (n !=1) {
              if (n % 3 != 0) {
                  return false;
              }
              n = n / 3;
          }
          return true;
      }
  //简写后
      public boolean isPowerOfThree(int n) {
          while (n > 0 && (n % 3 == 0)) {
              n = n / 3;
          }
          return n == 1;
      }
  ```

  复杂度分析：T(n) = O(logn) S(n) = O(1)

* 枚举：将int 范围内的所有3的幂存储,用集合set判断

  ```java
   public boolean isPowerOfThree(int n) {
              Set<Integer> set = new HashSet<Integer>() {
                  {
                      add(1);
                      add(3);
                      add(9);
                      add(27);
                      add(81);
                      add(243);
                      add(729);
                      add(2187);
                      add(6561);
                      add(19683);
                      add(59049);
                      add(177147);
                      add(531441);
                      add(1594323);
                      add(4782969);
                      add(14348907);
                      add(43046721);
                      add(129140163);
                      add(387420489);
                      add(1162261467);
                  }
              };
  
              return set.contains(n);
      }
  ```

  复杂度分析：T(n) =O(1) S(n) = O (1)

* 约数法：int范围内的最大质数，除以任何一个3的幂次方，余数必为0

  ```java
   return n > 0 && 1162261467 % 3 ==0;
  ```

  复杂度分析：T(n) = O(1),S(n) = O(1);

  

## 2、两数之和

题目：给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

* 方法一：暴力破解

  思路：两个for 循环相加得到目标值

  ```java
         int []result = new int[2];
         int length = nums.length;
         for(int i = 0;i<length;i++){
             for(int j = i+1;j<length;j++){
                 if(target == nums[i]+nums[j]){
                    result[0] = i;
                    result[1] = j;
                    break;
                 }
             }
         }
         return result;
  ```

  算法复杂度：O(n^2)  空间复杂度 O(1)

* 方法二：哈希查找实现

  思路：利用哈希Map存储数组的值，通过查找 nums[i]和tag的差值是否存在，来实现求两数之和的下标.

  ```java
  Map<Integer,Integer> map = new HashMap<>();
          for(int i = 0;i < nums.length; i++){
              if(map.containsKey(target - nums[i])){
               return new int[]{map.get(target - nums[i]),i};
              }
              map.put(nums[i],i);
          }
          throw new IllegalArgumentException("No two sum solution");
  ```

  T(n) =O(n) S(n) = O(n)

## 3、两数相加

给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。请你将两个数相加，并以相同形式返回一个表示和的链表。你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

* 方法一：不对齐相加

  思路：1、链表一对一相加,轮流往后走

  ​			2、谁停了就让另一个继续走

  ​			3、相加 ，有一个标志位判断是否大于10

  ​			4、存入到新的链表中

  ​			5、最后判断是否还有进一位，有直接在新的链表中加1

  ```java
     //返回的结果
          ListNode resultAdd = new ListNode(-1,null);//移动位
          ListNode result = resultAdd;//结果链表
          ListNode tempNode = null;//临时存放结点
          int temp;//临时存放值
          boolean isAdd = false;//临时
  
          while (l1!=null||l2!=null){
            if(l1!=null&&l2!=null){
                temp = l1.val+ l2.val;
                l1 = l1.next;
                l2 = l2.next;
            }else if(l2 == null){
                temp = l1.val;
                l1 = l1.next;
            }else
            {
                temp = l2.val;
                l2 = l2.next;
            }
              //是否需要加1
              if(isAdd) {
                  temp+=1;
              }
              //是否需要进一
              if(temp>=10){
                  isAdd = true;
                  temp -=10;
              }else{
                  isAdd = false;
              }
              //插入单链表
              tempNode = new ListNode(temp,null);
  
              resultAdd.next = tempNode;
              resultAdd = tempNode;
          }
          if(isAdd){
              tempNode = new ListNode(1,null);
              resultAdd.next = tempNode;
          }
          return  result.next;
  ```

  T(n) = O(n)  S(n) =O(1)

  该题主要考虑的就是什么时候进一

## 4、无重复字符的最长子串

给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长子串** 的长度。

* 方法一：滑动窗口

  思路一：1. 使用hasheset来判断字符是否重复

  2.两个指针实现对最长子串的移动，当右指针遇到重复字符串后，将左指针移到重复字符串

  ```bash
   public int lengthOfLongestSubstring(String s) {
         int result=0,rk = 0;
          Set<Character> set = new HashSet<Character>();
          for(int i = 0;i<s.length();i++){
              if(i!=0){
                  //左指针移动一个，即删除一个重复的
                  set.remove(s.charAt(i-1));
              }
              //将
              while(rk  <s.length() && !set.contains(s.charAt(rk))){
                  set.add(s.charAt(rk));
                  ++rk;
              }
             result = Math.max(result,rk-i);
          }
          return result;
      }
  ```

  思路二：1.使用mapset来判断字符是否重复，并记住位置

  ​       2.两个指针实现对最长子串的移动，当右指针遇到重复字符串后，将左指针通过map记录的位置移到重复字符串

  T(n) =O(n),S(n) = O(1)

## 5、[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

```java
 /**
     * 二分法实现
     * 1、考虑将中位数的两类情况归一 [(m+n+1)/2 + (m+n+2)/2]/2
     * 2、考虑特殊情况 nums为null 或为一
     * 3、考虑边界问题 nums下标
     * @param nums1
     * @param nums2
     * @return
     */
    public double findMidTwoByBin(int[] nums1,int [] nums2){
        int numslength1 = nums1.length;
        int numslength2 = nums2.length;
        int left = (numslength1+ numslength2 +1)/2;
        int right = (numslength1 + numslength2 +2)/2;
        return (double) (findKth(nums1,0,nums2,0,left) + findKth(nums1,0,nums2,0,right))/2;
    }
    /**
     * 找到第k个值
     */
    public int findKth(int [] nums1,int i,int []nums2,int j,int k){
        if(i >= nums1.length) return nums2[j+k-1];
        if(j >= nums2.length) return nums1[j+k-1];
        if(k == 1) return Math.min(nums1[i],nums2[j]);
        //一般情况
        int minValuenums1 = (i + k / 2 - 1) < nums1.length ? nums1[i + k / 2 - 1] : Integer.MAX_VALUE;
        int minValuenums2 = (j + k / 2 - 1) < nums2.length ? nums2[j + k / 2 - 1] : Integer.MAX_VALUE;
        if(minValuenums1 < minValuenums2){
            return  findKth(nums1,i+k/2,nums2,j,k-k/2);
        } else{
            return  findKth(nums1,i,nums2,j+k/2,k-k/2);
        }
    }
```

