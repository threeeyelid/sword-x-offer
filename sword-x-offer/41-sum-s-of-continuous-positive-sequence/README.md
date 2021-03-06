# 和为S的连续正数序列

小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序

## 双指针

- 时间复杂度：O(N)

```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> res;
        if (sum<3) return res;
        int start = 1;
        int end = 2;
        while(start<end && start<=(sum/2)) {
            int cur_sum = (start+end)*(end-start+1)/2;
            if(cur_sum==sum) {
                vector<int> sub_res;
                for(int e = start; e<=end; e++)
                    sub_res.push_back(e);
                res.push_back(sub_res);
                start++;
            }
            else if(cur_sum>sum) start++;
            else end++;//cur_sum<sum
        }
        return res;
    }
};
```

## 等差数列

- 时间复杂度：O(sqrt(N))
- 数列长度n有两种情况：
    - n为奇数，数列中间的数即平均值，条件为：`sum%n==0 && (n&1)==1`  
    - n为偶数，数列中间的两个数的均值为平均值(小数部分为0.5)，条件为：`(sum%n)/2==n`  
- n的下限，由于连续数列，因而长度至少为两个数，因而n>=2  
- n的上限，公差为1的等差数列求1-N(假设起始为1)的和: `s = (1+n)*n/2`，则有n的上限：`n=sqrt(2*s)`  
- 题目要求正数序列，因而第一个元素可以从1开始遍历  


```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> res;
        for(int n=sqrt(sum*2); n>=2; n--) {
            if((n&1)==1 && sum%n==0 || //case1 奇数长度，中位数即平均数，sum可被n整除
               (sum%n)*2==n) { //case2 偶数长度，中位数为中间两数平均值。sum%n得到中间两数多出来的部分，n个0.5
                res.emplace_back(vector<int>());
                vector<int>& vi = res[res.size()-1];
                for (int s=0, e=(sum/n)-(n-1)/2;
                     s<n; s++, e++)
                    vi.emplace_back(e);
            }
        }
        return res;
    }
};
```

## 解方程

- 时间复杂度：O(sqrt(N))

### 解方程1

- (a + b)(b - a + 1) = sum * 2, y\*y<=sum (a最小是1,则有y^2<=sum，下面有提到y = b - a + 1)  
- 设x = a + b, y = b - a + 1, y >= 2  (至少2个数)  
- 枚举y，得x，解出a,b  

```cpp
class Solution {
    inline static bool cmp(vector<int>& a, vector<int>& b) {
        return a[0]<b[0];
    }
public:
    vector<vector<int>> FindContinuousSequence(int sum) {
        vector<vector<int>> res;
        for(int y=2; y*y<=(sum<<1); y++) if((sum<<1)%y==0) {//优先级：！ > 算术运算符 > 关系运算符 > && > || > 赋值运算符
            int x = (sum<<1)/y, bx2 = x+y-1;// x+y-1=(a+b)+(b-a+1)-1=2b
            if(!(bx2&1)) {//判断奇偶
                int b = bx2>>1;
                int a = x - b;
                res.emplace_back(vector<int>());
                vector<int>& vi = res[res.size()-1];//引用方式取出最后一个元素
                for(int n=a; n<=b; n++) vi.emplace_back(n);
            }
            sort(res.begin(), res.end(), cmp);
        }
        return res;
    }
};
```

### 解方程2

```cpp
class Solution {
    static inline bool cmp(vector<int>& a, vector<int>& b) {
        return a[0]<b[0];
    }
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> res;
        for(int k = 2; k*k < 2*sum; k++) {
            float m = ((2.0*sum)/k-k+1)/2.0;
            int temp = (int)m;
            if(m == temp) {
                res.emplace_back(vector<int>());
                vector<int>& vi = res[res.size()-1];
                for(int i = 0; i < k; i++) vi.emplace_back(i+temp);
            }
        }
        sort(res.begin(), res.end(), cmp);
        return res;
    }
};
```
