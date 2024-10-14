# Leetcode

## 二分查找

### 24.1.31 704 二分查找

```java
class Solution {
    public int search(int[] nums, int target) {
            int left= 0,right = nums.length;。//length不用加括号
            while(left<right){
                int mid = left + ((right - left) >> 1); //mid条件

                if(nums[mid]==target){
                    return mid;
                }
                else if(nums[mid]<target){
                    left= mid + 1;
                }
                else {
                    right = mid;//左开右闭
                }
            }
            return -1;
    }
}
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240131090033950.png" alt="image-20240131090033950" style="zoom:50%;" />

### 24.1.31 35 搜索插入位置

```Java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length;
        while(left<right){
            int mid = left+((right-left)>>1);

            if(nums[mid]==target){
                return mid;
            }
            else if(nums[mid]<target){
                left = mid+1;
            }
            else right = mid;
        }
        return right;//分别处理如下四种情况
        // 目标值在数组所有元素之前 [0,0)
        // 目标值等于数组中某一个元素 return middle
        // 目标值插入数组中的位置 [left, right) ，return right 即可
        // 目标值在数组所有元素之后的情况 [left, right)，因为是右开区间，所以 return right
    }

```

### 24.1.31  69. x 的平方根 

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0 || x == 1) return x;

        long left = 0, right = x;//long 类型 我也不知道为什么 long就完了
        int ans = -1;

        while (left < right) {
            long mid = left + (right - left) / 2;

            if (mid * mid == x) return (int) mid;
            else if (mid * mid < x) {
                left = mid + 1;
                ans = (int) mid;
            } else {
                right = mid;
            }
        }

        return ans;
    }
}
```

###  24.2.1 34. 在排序数组中查找元素的第一个和最后一个位置

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
            int left = searchLeft(nums,target);
            int right = searchRight(nums,target);

            return new int[]{left,right};//返回的时候new一个
    }
      public int searchLeft(int[] nums, int target){
            int left = 0,right = nums.length;
            int ans = -1;
            while(left<right){
                int mid = left+(right-left)/2;
                if(nums[mid]==target){
                    ans = mid;
                    right = mid;
                }
                else if(nums[mid]<target){
                    left = mid +1;
                }
                else if(nums[mid]>target){
                    right = mid;
                }
            }
            return ans;
        }

        public int searchRight(int[] nums, int target){
            int left = 0, right = nums.length;
            int ans = -1;
            while(left<right){
                int mid = left+(right-left)/2;
                if(nums[mid]==target){
                    ans = mid;
                    left = mid+1;
                }
                else if(nums[mid]<target){
                    left = mid +1;
                }
                else if(nums[mid]>target){
                    right = mid;
                }
            }
            return ans;
        }
}
```

## 移除元素

### 24.2.2 [27. 移除元素](https://leetcode.cn/problems/remove-element/)

```java
class Solution {
    public int removeElement(int[] nums, int val) {
            int fastIndex=0,slowIndex=0;

            for(;fastIndex<nums.length;){
                if(val == nums[fastIndex]){
                    fastIndex++;
                }
                else{
                    nums[slowIndex]=nums[fastIndex];
                    slowIndex++;
                    fastIndex++;
                }
            }
            return slowIndex;
    }
}


//双指针法
https://code-thinking.cdn.bcebos.com/gifs/27.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0-%E5%8F%8C%E6%8C%87%E9%92%88%E6%B3%95.gif
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240202193846782.png" alt="image-20240202193846782" style="zoom:33%;" />

### 24.2.3 [26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
      int slowIndex = 0;
      int fastIndex = 1;

      for(;fastIndex<nums.length;fastIndex++){
          if(nums[slowIndex]==nums[fastIndex]){
              
          }
          else{
              slowIndex++;
              nums[slowIndex]=nums[fastIndex];
          }
      }
      return slowIndex+1;
        //return index+1而不是index
    }
}
```

### 24.2.4 [283. 移动零](https://leetcode.cn/problems/move-zeroes/)

```java
class Solution {
     public static void moveZeroes(int[] nums) {
            int slowIndex =0 , fastIndex= 0;
            int count = 0;
            while(fastIndex<nums.length){
                if(nums[fastIndex]==0){
                    fastIndex++;
                    count++;

                }
                else{
                    nums[slowIndex]=nums[fastIndex];
                    slowIndex++;
                    fastIndex++;
                }
            }
            while(count>0){     
                nums[fastIndex-1]=0;
                fastIndex--;
                count--;
            }

        }
}
```

### 24.2.5 977.有序数组的平方

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int i = 0, j = nums.length -1, k = j;

        int[] result = new int[nums.length];

        while(i<=j){
            int result1 = nums[i]*nums[i];
            int result2 = nums[j]*nums[j];
            if(result1 <= result2){
                result[k--] = result2;
                j--;
            }
            else{
                result[k--] = result1;
                i++;
            } 
        }
        return result;
    }
}
```

双指针法

<img src="D:\2024\Notes\Typora\Pictures\image-20240205085907382.png" alt="image-20240205085907382" style="zoom: 33%;" />

## 长度最小的子数组

### 24.2.6 209.长度最小的子数组

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int sum = 0;
        int result = Integer.MAX_VALUE;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (sum >= target) {
                result = Math.min(result, right - left + 1);
                sum -= nums[left++];
            }
        }
        return result == Integer.MAX_VALUE ? 0 : result;
    }
}
```

接下来就开始介绍数组操作中另一个重要的方法：**滑动窗口**。所谓滑动窗口，**就是不断的调节子序列的起始位置和终止位置，从而得出我们要想的结果**。在暴力解法中，是一个for循环滑动窗口的起始位置，一个for循环为滑动窗口的终止位置，用两个for循环 完成了一个不断搜索区间的过程。那么滑动窗口如何用一个for循环来完成这个操作呢。首先要思考 如果用一个for循环，那么应该表示 滑动窗口的起始位置，还是终止位置。如果只用一个for循环来表示 滑动窗口的起始位置，那么如何遍历剩下的终止位置？此时难免再次陷入 暴力解法的怪圈。所以 只用一个for循环，那么这个循环的索引，一定是表示 滑动窗口的终止位置。那么问题来了， 滑动窗口的起始位置如何移动呢？这里还是以题目中的示例来举例，s=7， 数组是 2，3，1，2，4，3，来看一下查找的过程：https://code-thinking.cdn.bcebos.com/gifs/209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.gif

<img src="D:\2024\Notes\Typora\Pictures\20210312160441942.png" alt="leetcode_209" style="zoom:50%;" />

### 24.2. 7 [904.水果成篮](https://leetcode.cn/problems/fruit-into-baskets/)

```JAVA
class Solution {
    public int totalFruit(int[] fruits) {
        int n = fruits.length;
        Map<Integer, Integer> cnt = new HashMap<Integer, Integer>();
        //用集合记录 键为树的种类 键为个数

        int left = 0, ans = 0;
        for (int right = 0; right < n; ++right) {
            //直接将右边新树放入 无则加入，有则加一
            cnt.put(fruits[right], cnt.getOrDefault(fruits[right], 0) + 1);
             //直至集合中只有两种树
            while (cnt.size() > 2) {
               //将左侧树删除
                cnt.put(fruits[left], cnt.get(fruits[left]) - 1);
               
                if (cnt.get(fruits[left]) == 0) {
                    cnt.remove(fruits[left]);
                }
                ++left;
            }
            //每次加入一颗右边树记录最大值
            ans = Math.max(ans, right - left + 1);
        }
        return ans;
    }
}
```

### 24.2.8  76.最小覆盖子串

```java
 public String minWindow(String s, String t) {
           if (s.length() == t.length()) {
            if (s.equals(t)) return s;//相等
        } else if (s.length() < t.length()) {
            //s长度小于t
            return "";
        }

        //s长度大于t
        int slowIndex = 0, fastIndex = 0;
        HashMap<Character, Integer> targetmap = new HashMap<>(); //键为字符 值为目标子序列中出现的次数

        HashMap<Character, Integer> nowmap = new HashMap<>(); //键为字符 值为现在子序列中出现的次数

        for (int i = 0; i < t.length(); i++) { //遍历 将t中字符录入集合
            targetmap.put(t.charAt(i), (targetmap.getOrDefault(t.charAt(i), 0) + 1));
        }

        //遍历s序列，每次fastindex后移，并判断slowindex是否能后移，
        // 判断条件为 targetmap中的键都能在nowmap中找到 且nowmap中的值大于targetmap
        // 记录长度及两个位置

        int minlength = Integer.MAX_VALUE;
        int left = -1, right = -1;

        for (fastIndex = 0; fastIndex < s.length(); fastIndex++) {
            nowmap.put(s.charAt(fastIndex), (nowmap.getOrDefault(s.charAt(fastIndex), 0) + 1)); //先将fastindex入

            boolean flag = true;
            do {
                Set<Map.Entry<Character, Integer>> entries = targetmap.entrySet();

                for (Map.Entry<Character, Integer> entry : entries) {
                    Character key = entry.getKey();
                    int value = entry.getValue();
                    if (!nowmap.containsKey(key) || nowmap.containsKey(key) && nowmap.get(key) < value) {
                        flag = false;
                        break;
                    }

                }
                if (flag) {
                    //如果都能找到 判断是否更新答案
                    int length = fastIndex - slowIndex;
                    if (minlength > length) {
                        minlength = length;
                        left = slowIndex;
                        right = fastIndex;
                    }
                    //后移slowindex

                    //对nowmap中键为slowindex的值进行删减
                    if (nowmap.containsKey(s.charAt(slowIndex)) && nowmap.get(s.charAt(slowIndex)) == 1) {
                        nowmap.remove(s.charAt(slowIndex));
                    } else {
                        Integer value = nowmap.getOrDefault(s.charAt(slowIndex), 0);
                        if (value == null) {
                            value = 0;
                        }
                        nowmap.put(s.charAt(slowIndex), value - 1);
                    }
                    slowIndex++;
                }
            } while (flag && slowIndex <= fastIndex);
        }

        if (left == -1 && right == left) return "";
        //System.out.println(minlength);
        return s.substring(left, right+1);
    }
```

**用1个map就好了，先把t的词频加进去，然后再减掉，只要保证里面每个值都不大于0**

## 螺旋矩阵

### 24.2.11 59.螺旋矩阵II

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int loop = 0;  // 控制循环次数
        int[][] res = new int[n][n];
        int start = 0;  // 每次循环的开始点(start, start)
        int count = 1;  // 定义填充数字
        int i, j;

        while (loop++ < n / 2) { // 判断边界后，loop从1开始
            // 模拟上侧从左到右
            for (j = start; j < n - loop; j++) {
                res[start][j] = count++;
            }

            // 模拟右侧从上到下
            for (i = start; i < n - loop; i++) {
                res[i][j] = count++;
            }

            // 模拟下侧从右到左
            for (; j >= loop; j--) {
                res[i][j] = count++;
            }

            // 模拟左侧从下到上
            for (; i >= loop; i--) {
                res[i][j] = count++;
            }
            start++;
        }

        if (n % 2 == 1) {
            res[start][start] = count;
        }

        return res;
    }
}
```

![image-20240211101907872](D:\2024\Notes\Typora\Pictures\image-20240211101907872.png)

```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> res(n, vector<int>(n, 0)); // 使用vector定义一个二维数组
        int startx = 0, starty = 0; // 定义每循环一个圈的起始位置
        int loop = n / 2; // 每个圈循环几次，例如n为奇数3，那么loop = 1 只是循环一圈，矩阵中间的值需要单独处理
        int mid = n / 2; // 矩阵中间的位置，例如：n为3， 中间的位置就是(1，1)，n为5，中间位置为(2, 2)
        int count = 1; // 用来给矩阵中每一个空格赋值
        int offset = 1; // 需要控制每一条边遍历的长度，每次循环右边界收缩一位
        int i,j;
        while (loop --) {
            i = startx;
            j = starty;

            // 下面开始的四个for就是模拟转了一圈
            // 模拟填充上行从左到右(左闭右开)
            for (j = starty; j < n - offset; j++) {
                res[startx][j] = count++;
            }
            // 模拟填充右列从上到下(左闭右开)
            for (i = startx; i < n - offset; i++) {
                res[i][j] = count++;
            }
            // 模拟填充下行从右到左(左闭右开)
            for (; j > starty; j--) {
                res[i][j] = count++;
            }
            // 模拟填充左列从下到上(左闭右开)
            for (; i > startx; i--) {
                res[i][j] = count++;
            }

            // 第二圈开始的时候，起始位置要各自加1， 例如：第一圈起始位置是(0, 0)，第二圈起始位置是(1, 1)
            startx++;
            starty++;

            // offset 控制每一圈里每一条边遍历的长度
            offset += 1;
        }

        // 如果n为奇数的话，需要单独给矩阵最中间的位置赋值
        if (n % 2) {
            res[mid][mid] = count;
        }
        return res;
    }
};
```

[LeetCode 算法刷题目录 (Java)_leetcode 代码题-CSDN博客](https://blog.csdn.net/weixin_43004044/article/details/122477673?ops_request_misc=%7B%22request%5Fid%22%3A%22171426941216800182188084%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=171426941216800182188084&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-122477673-null-null.142^v100^control&utm_term=leetcode&spm=1018.2226.3001.4187)



后来在《看不见的大猩猩》，看到了一句特别应景的话:

> 人类的自信错觉：表现好是因为自己本身实力强，表现不好是运气差，或者是因为一时的粗心大意。总之，人们经常忽略能力这个最重要的因素。
> 解决方法只有一个，提高能力。
