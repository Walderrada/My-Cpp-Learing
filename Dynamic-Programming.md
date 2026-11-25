# 基本要素                        
## 1、最优子结构                    
问题的最优解包含子问题的最优解                            
## 2、重叠子问题                
在用递归算法自顶而下解问题时，每次产生的子问题并不总是新问题，有些子问题被反复计算。动规利用此重叠性质，对每个子问题只解一次，然后将其解保存在一个表格中，当再次需要解此子问题时，用常数时间查看结果。通常不同的子问题个数随问题的大小呈多项式增长。因此，动规只需要多项式时间。            
## 3、备忘录方法                
与动规一样用表格保存已解决的子问题的答案。                
不同：备忘录方法的递归方式是自顶而下的，而动规算法自底向上递归            


# 矩阵连乘问题
## 状态定义
dp[i][j]表示将矩阵Ai-Aj连乘所需的最少乘法次数，                
边界条件dp[i][i] = 0。                
区间从底向上、长度逐渐变大，最终结果为dp[1][n]。                        
            
## 转移方程            
假设从k处断开，                
总代价由左右两部分代价以及左右两部分相乘代价一共三部分组成            
dp[i][j] = min{dp[i][k] + dp[k+1][j] + 左边矩阵的行数×中间边界的维度×右边结果矩阵的列数}            
中间边界的维度 = 左边矩阵的列数 = 右边矩阵的行数            

## 算法实现
```c
int matrixChain(const vector<int>& p, int n) {  // p 数组表示维度（其实就是各个矩阵的行，最后是最后一个矩阵的列），n 为矩阵个数
    vector<vector<int>> dp(n, vector<int>(n, 0));

    // len 是连乘的矩阵长度（区间长度），从 2 开始到 n
    for (int len = 2; len <= n; len++) {
        for (int i = 1; i <= n - len + 1; i++) {  // i 是区间起点
            int j = i + len - 1; // j 是区间终点
            dp[i][j] = INT_MAX;  // 初始设为极大值
            
            // k 是断点，从 i 到 j-1
            for (int k = i; k < j; k++) {
                int cost = dp[i][k] + dp[k+1][j] + p[i-1] * p[k] * p[j];
                if (cost < dp[i][j]) {
                    dp[i][j] = cost;
                }
            }
        }
    }
    return dp[1][n];
}
```
## 复杂度分析
三层循环len， i， k，所以时间复杂度为O(n^3)                        
空间复杂度O(n^2)，二维数组dp            

## 拓展            
如何输出最优的加括号顺序？                
增加一个二维数组s[i][j]，在每次找到更小的dp[i][j]时，记录当前的断点k，最后用一个递归函数，从s[1][n]开始一层层切开打印括号。            
```c
// 递归打印最优加括号方式
void printOptimalParens(const vector<vector<int>>& s, int i, int j) {
    if (i == j) {
        cout << "A" << i; // 只有一个矩阵，直接输出
    } else {
        cout << "(";
        // 左边部分
        printOptimalParens(s, i, s[i][j]);
        // 右边部分
        printOptimalParens(s, s[i][j] + 1, j);
        cout << ")";
    }
}
```


# 最长公共子序列 LCS                    
## 概念辨析                    
子序列：不要求连续，相对顺序不变即可                    
子串：必须是连续的一段                    

## 性质分析                
1、状态定义：dp[i][j]表示：序列 X 的前 i 个字符与序列Y的前 j 个字符的最长公共子序列的长度。            
2、状态转移：                
·如果xi == yi（尾巴相同）：这个字符必然在LCS中，只需要看它前面的部分。                
    dp[i][j] = dp[i-1][j-1] + 1            
·如果xi != yi（尾巴不同）：说明xi和yi至少有一个不在LCS中，所以分别砍掉他们取最大值。            
    dp[i][j] = max{dp[i-1][j], dp[i][j-1]}                    
3、边界条件：dp[0][j]=0, dp[i][0]=0                    
    
```c
int LCS(string X, string Y) {
    int m = X.length();
    int n = Y.length();
    
    // 定义 dp 数组，大小为 (m+1) x (n+1)，初始全为 0
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    
    // 从前往后填表
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            // 注意：字符串下标从 0 开始，所以第 i 个字符是 X[i-1]
            if (X[i - 1] == Y[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    
    return dp[m][n]; // 最终结果
}
```

## 复杂度分析            
时间复杂度：O(m × n)，m×n的二维表格                
空间复杂度：O(m × n)，二位dp数组                    

## 拓展：            
1. 求一个字符串S的最长回文子序列            
将S翻转的得到S`，求二者LCS即可                
2. **与矩阵连乘的对比：**                
矩阵连乘是一个区间划分问题，需要将区间切分，也是所有区间DP的通用模板：第一层枚举区间长度，第二层枚举起点，第三层枚举分割点。                
LCS的子问题是从字符串尾部删减得到的，适合用双重循环。                

## 打印序列                
```c
// 反向回溯找序列
    string lcs = "";
    int i = m, j = n;
    while (i > 0 && j > 0) {
        if (X[i - 1] == Y[j - 1]) {
            lcs += X[i - 1]; // 找到公共字符，加入结果
            i--; j--;        // 往左上角移动
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--;             // 从上方来的，往上走
        } else {
            j--;             // 从左方来的，往左走
        }
    }
    // 因为是从后往前找的，所以要反转
    reverse(lcs.begin(), lcs.end());
    return lcs;
```


# 最大子段和
## 问题描述            
给定一个由 n 个整数组成的数组 A（注意：数组中可能包含负数），求该数组的一个连续子数组，使得其元素之和最大。          

## 性质分析                
· 状态定义：dp[i]表示以第 i 个元素结尾的最大子段和            
· 状态转移方程：dp[i] = max{dp[i-1] + A[i], A[i]}                
· 解释：对于当前元素A[i]，要么接在前面子段的后面，要么自己另起炉灶。        
· 最终结果：遍历所有dp[i]，取最大值            

## 代码实现（Kadane算法）                                
算法思想：如果前面的累加和变成了负数，那它不仅帮不上忙，还会拖累后面的数，不如直接扔掉，从当前数字重新开始算。        
```c            
int maxSubArray(vector<int>& nums) {
    if (nums.empty()) return 0;
    
    int current_sum = nums[0]; // 以当前元素结尾的最大和
    int max_sum = nums[0];     // 全局最大和
    
    for (int i = 1; i < nums.size(); i++) {
        // 如果当前前缀和大于0，就加上当前元素；否则从当前元素重新开始
        current_sum = max(nums[i], current_sum + nums[i]);
        max_sum = max(max_sum, current_sum);
    }
    return max_sum;
}
```


## 拓展                                    
1. 如何输出具体的“子数组”位置？                                    
在更新`max_sum`时，记录当前的结束下标`right = i`。当`current_sum`从当前元素重新开始时，记录下新的起点`temp_left = i`。当`max_sum`被更新时，把`temp_left`赋给最终的`left`。                        
2. 为什么状态定义要以 i 为结尾？
如果定义成“前 i 个元素的最大和”，无法确定第 i 个元素有没有被包含进去，无法判断连续性。“以 i 结尾”强制保持了连续性
3. 如何求最大子段积？
在记录max的同时，还要记录一个min，维护负数相乘的结果，遇到新的负数时，交换max和min再乘。


# 凸多边形最优三角部分（和“矩阵连乘”高度相似）                                    
## 问题描述                                    
给定一个凸多边形，用不相交的对角线分割成若干个三角形，这个过程叫三角部分。
· n边形的三角部分，必然会产生n - 2个三角形，并且使用了n - 3条对角线。                        
                                    
## 算法思路                                    
如果随便找一条对角线把多边形切成两半，这两半的形状和顶点数不一样，无法用同一个递归函数处理，也就无法使用分治法。因此我们需要采用`区间 DP`的思想。                                    
                                    
**最优子结构**                        
假设多边形顶点逆时针编号为v1-vn。                        
看底边v1vn，假设其顶点为vk，那么这个三角形将多边形切成了三部分：三角形、左半子多边形、右半子多边形。                 
如果整个大部分最优，那么左半和右半都必然是最优。满足最优子结构性质。                        

## 状态定义与转移方程
dp[i][j]表示：将顶点vi到vj加上他们的弦vivj构成的凸多边形进行最优三角划分后，能得到的最小总权值。            
枚举第三个顶点k，尝试把多边形分成三部分：                        
dp[i][j] = min{dp[j][k] + dp[k][j] + w(i,k,j)}                        
· dp[i][k]：左边子多边形的代价                        
· dp[k][j]：右边子多边形的代价                        
· w(i,k,j)：当前这个三角形vivkvj的权值。                        

*边界条件：*            
当多边形只有两个或三个顶点时，无法再切分：            
· 如果j <= i + 1，dp[i][j] = 0。（不足三个顶点）                        
· 如果j = i + 2，dp[i][j] = w(i,i+1,j)。（刚好是一个三角形）              


## 代码实现            
```c
#include <iostream>
#include <vector>
#include <cmath>
#include <iomanip>

using namespace std;

// 计算两点之间的欧氏距离（边长）
double dist(int i, int j, const vector<double>& x, const vector<double>& y) {
    return sqrt((x[i] - x[j]) * (x[i] - x[j]) + (y[i] - y[j]) * (y[i] - y[j]));
}

// 计算三角形 i, j, k 的周长
double weight(int i, int j, int k, const vector<double>& x, const vector<double>& y) {
    return dist(i, j, x, y) + dist(j, k, x, y) + dist(k, i, x, y);
}

int main() {
    int n = 6; // 六边形
    // 假设顶点按顺时针或逆时针给出
    vector<double> x = {0, 2, 5, 6, 4, 1};
    vector<double> y = {0, 3, 4, 1, -2, -1};

    // dp[i][j] 表示顶点 i 到 j 构成的子多边形的最小三角剖分代价
    // 顶点从 0 到 n-1
    vector<vector<double>> dp(n, vector<double>(n, 0.0));

    // 区间长度 len 从 3 开始（因为至少要 3 个顶点才能构成三角形）
    for (int len = 3; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            dp[i][j] = 1e9; // 初始化为极大值

            // k 是切分点，从 i+1 到 j-1
            for (int k = i + 1; k < j; k++) {
                double cost = dp[i][k] + dp[k][j] + weight(i, k, j, x, y);
                if (cost < dp[i][j]) {
                    dp[i][j] = cost;
                }
            }
        }
    }

    // 最终结果是整个多边形 [0, n-1] 的剖分代价
    cout << "最小三角形周长之和: " << fixed << setprecision(2) << dp[0][n-1] << endl;
    return 0;
}
```

## 复杂度分析                        
· 三重循环（区间长度、起点、切分点），时间复杂度O(n^3)。                        
· 二维数组存状态，空间复杂度O(n^2)。                        

## 拓展            
为什么和矩阵连乘高度相似？            
· 矩阵连乘：在序列中找一个切分点，左右算完，然后加上两边相乘产生的额外代价。            
· 多边形部分：找三角形的顶点k，左右算完，加上中间三角形的代价。            
· 如果把矩阵的维度看作多边形的顶点权值，他们的动态规划表格结构一模一样。            


# 多边形游戏            
## 问题描述             
给定一个由 n 个顶点和 n 条边组成的多边形。                        
每个顶点上有一个整数，每条边上有一个运算符+或*，所有边从整数0到1编号。
规则：首先删除一条边，变成一条n个顶点的链，每次选择链上相邻的两个顶点，用它们之间的边运算，将结果合并成一个新的顶点，直至最终只剩一个顶点。
目标：求最后剩下的顶点的最大值。

## 算法思想            
1、环状区间DP：将长度为n的数组复制一份，拉长为2n的数组。                        
2、状态定义：                        
有乘法运算，要同时记录最大值最小值。            
设dp_max[i][j]：表示将区间[i,j]内的顶点合并成一个顶点时，能得到的最大值。            
设dp_min[i][j]：表示将区间[i,j]内的顶点合并成一个顶点时，能得到的最小值。            
3、状态转移方程（分运算符讨论）            
假设切分点 k 将区间划分为两部分。            
· 如果是`+`
最大值：max(max左 + max右)
最小值：min(min右 + min右)
· **如果是`*`**
最大值：从四种组合中找最大值：max左 * max右、max左 * min右、min左 * max右、min左 * min右。
最小值：从上面4种组合中找最小的。

## 代码演示                         
```c
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

int main() {
    // 示例：n 个顶点，边运算符号。
    // 注意：为了代码简洁，假定第 i 条边连接的是顶点 i 和 i+1。输入顺序为：顶点1, 边1, 顶点2, 边2...
    // 这里用 0 代表 '+', 1 代表 '*'
    int n = 4;
    vector<int> v = {1, -2, 3, 4}; // 顶点值
    vector<int> op = {1, 1, 1, 0}; // 边上的操作符 (v[0] op[0] v[1] ... v[3] op[3] v[0])

    // 破环成链：数组长度变为 2n
    vector<int> val(2 * n);
    vector<int> ops(2 * n);
    for (int i = 0; i < 2 * n; i++) {
        val[i] = v[i % n];
        ops[i] = op[i % n];
    }

    // 初始化 dp 数组
    // dp_max[i][j] 表示从第 i 个顶点合并到第 j 个顶点的最大值
    vector<vector<int>> dp_max(2 * n, vector<int>(2 * n, INT_MIN));
    vector<vector<int>> dp_min(2 * n, vector<int>(2 * n, INT_MAX));

    // 初始化长度为 1 的区间
    for (int i = 0; i < 2 * n; i++) {
        dp_max[i][i] = val[i];
        dp_min[i][i] = val[i];
    }

    // 区间 DP 遍历
    // len 是区间长度，最大为 n (因为只要找 n 个顶点的合并结果)
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i < 2 * n - len + 1; i++) {
            int j = i + len - 1;
            
            // 枚举断点 k
            for (int k = i; k < j; k++) {
                int op_k = ops[k];
                
                // 如果是加法
                if (op_k == 0) {
                    dp_max[i][j] = max(dp_max[i][j], dp_max[i][k] + dp_max[k+1][j]);
                    dp_min[i][j] = min(dp_min[i][j], dp_min[i][k] + dp_min[k+1][j]);
                } 
                // 如果是乘法
                else if (op_k == 1) {
                    int a = dp_max[i][k] * dp_max[k+1][j];
                    int b = dp_max[i][k] * dp_min[k+1][j];
                    int c = dp_min[i][k] * dp_max[k+1][j];
                    int d = dp_min[i][k] * dp_min[k+1][j];
                    
                    dp_max[i][j] = max({dp_max[i][j], a, b, c, d});
                    dp_min[i][j] = min({dp_min[i][j], a, b, c, d});
                }
            }
        }
    }

    // 统计最终结果
    int ans = INT_MIN;
    for (int i = 0; i < n; i++) {
        ans = max(ans, dp_max[i][i + n - 1]);
    }
    
    cout << "最大得分: " << ans << endl;
    return 0;
}
```

## 复杂度分析            
时间复杂度O(n^3)，区间DP三循环。
空间复杂度O(n^2)，两个二维数组记录最大值和最小值。            
                        
## 拓展                        
如何输出最优的删除方案？                                    
在最终的统计过程中，如果发现当前值比`ans`大，就记录下此时的起点`i`，意味着首先删除的是`i-1`条边。             


# 0-1背包            
## 问题描述            
一个容量为 W 的背包，以及 N 个物品。第 i 个物品的重量是weight[i]，价值是value[i]。            
约束：每个物品最多选一次。                        
目标：在不超过容量的情况下，求背包能装入的最大总价值。            

## 算法思想
dp[i][j]表示：从前i个物品中挑选，放入容量为 j 的背包种所能获得的最大价值。
对于第 i 个物品，在容量为 j 时，我们有两种选择：
· 不装：dp[i][j] = dp[i-1][j]
· 装：if (j >= weight[i])。
dp[i][j] = dp[i-1][j - weight[i] + value[i]]
取两者最大值：dp[i][j] = max(dp[i-1][j]， dp[i-1][j - weight[i] + value[i])
目标：dp[W][N]            

*边界条件*：                        
dp[0][j] = 0（没有物品可选）            
dp[i][o] = 0 （背包容量为0）           

## 代码实现
```c
    // dp[i][j] 初始化全为 0
    vector<vector<int>> dp(N + 1, vector<int>(W + 1, 0));

    for (int i = 1; i <= N; i++) {
        for (int j = 1; j <= W; j++) {
            if (j >= weight[i]) {
                // 能装下：比较“不装”和“装”的最大值
                dp[i][j] = max(dp[i-1][j], dp[i-1][j - weight[i]] + value[i]);
            } else {
                // 装不下：那就不装
                dp[i][j] = dp[i-1][j];
            }
        }
    }
```

## 复杂度分析：            
· 时间复杂度：O(N × W)，二维表格。                        
· 空间复杂度：O(N × W)，二维数组。                        
            
## 拓展            
**空间优化（一位滚动数组）**                        
设dp[j]表示：当前容量为 j 的背包，能装入的最大价值。                        
状态转移方程：dp[j] = max(dp[j], dp[j - weight[i]] + value[i])            
右边的dp[j]代表上一层的值（不装），另一项则代表装入当前物品后的值。            

**关键细节**
内层循环必须从大到小（逆序）遍历！保证每个物品只被放入一次。            
```c
for (int i = 1; i <= N; i++) {
    for (int j = W; j >= weight[i]; j--) { // 必须逆序！
        dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);
    }
}
```            
优化后复杂度：时间O(N×W)；空间O(W)。            






