# Leetcode Top100目录

# 汉明距离

两个整数之间的[汉明距离](https://baike.baidu.com/item/汉明距离)指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 `x` 和 `y`，计算它们之间的汉明距离。

**注意：**
0 ≤ `x`, `y` < 2^31

思路1:每次对比两个数字的最低位(x&1)和(y&1)，若不相等则count++，然后两个数字不断右移动一位，直到两个数都为0为止。

```java
 public int hammingDistance(int x, int y) {
        int count=0;
        while(x!=0||y!=0){
            if((x&1)!=(y&1))
                count++;
            x>>=1;
            y>>=1;
        }
        return count;
    }
```

思路2:首先把两个数字异或，找出两个数字的不同位置，如4(100)^1(001)=5(101)，然后通过sum=sum&(sum-1) 如 5(101)&4(100)=4(100) 可以把sum最右边的1去掉。因为异或后2进制值为1的位，代表的是两个数字的不同位置，所以一直循环，每次循环右移动一位，求有多少个1，直到sum=0。

```java
public int hammingDistance(int x, int y) {
        int count=0;
        int sum=x^y;
        while(sum!=0){
            count++;
            sum&=(sum-1);
        }
        return count;
    }
```

