# BinarySearch 二分搜索技术        
## 算法思想        
每次将搜索区间缩小一半。通过比较目标值与区间中间值，直接舍弃不可能存在目标值的那一半。        
前提条件：                
· 数据必须存储在数组中。        
· 数据必须是**有序**的。        

## 代码演示        
```c
int BinarySearch(int arr[], int n, int x){
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2; //防止left + right溢出
            if (arr[mid] == x)
                return mid;
            if (arr[mid] < x)
                left = mid + 1; //目标在右半区
            else
                right = mid - 1; //目标在左半区
        }
        return -1; // 没找到
    }
```

## 复杂度分析         
O(log n)        

                
# 大整数乘法                
将n位二进制整数X和Y都分成两段，每段长n/2            
- \(X = A \cdot 2^{n/2} + B\)                
- \(Y = C \cdot 2^{n/2} + D\)               
相乘展开后得：                        
XY = (A * 2^(n/2) + B)(C * 2^(n/2) + D) = AC * 2^n + (AD + BC) * 2^(n/2) + BD        
                   
按此式计算，需要进行4次n/2位整数的乘法；3次不超过2n位的整数加法，以及两次移位，所有加法和移位共用O(n)步运算          
T(n) = 4T(n/2) + O(n) , n>1                     
得T(n) = O(n^2) 效率并未提升                 

## 优化计算                            
核心数学变形（Karatsuba算法）：AD + BC = (A-B) × (C-D) + AC + BD                
写成另一种形式:                     
**XY = AC * 2^n  + ( (A-B)(D-C) + AC + BD ) * 2^(n/2) + BD**        

## 复杂度分析        
只需要做3次乘法( AC, BD, (A-B)(D-C) )、6次减法和两次移位            
T(n) = 3T(n/2) + O(n) , n>1           
得T(n) = O(n^log3) = O(n^1.59)   
效率显著提升   

## 代码演示        
```c
// 简化版：假设传入的是两个相同长度的非负整数型数据（或前缀0对齐的字符串转化）
long long karatsuba(long long x, long long y) {
    // 递归终止条件：如果位数很少，直接相乘（或者用普通乘法）
    if (x < 10 || y < 10) return x * y;

    // 计算位数 n
    int n = 1;
    while (n < max(to_string(x).length(), to_string(y).length())) n *= 2;
    int half = n / 2;

    long long power = pow(10, half);

    // 拆分高位和低位
    long long a = x / power;  // 高位
    long long b = x % power;  // 低位
    long long c = y / power;
    long long d = y % power;

    // 核心：3 次递归乘法
    long long z0 = karatsuba(b, d);             // BD
    long long z2 = karatsuba(a, c);             // AC
    long long z1 = karatsuba(a + b, c + d) - z0 - z2; // (A+B)(C+D) - AC - BD

    // 合并结果：AC * 10^n + (AD+BC) * 10^(n/2) + BD
    return z2 * pow(10, 2 * half) + z1 * power + z0;
}
```


# 棋盘覆盖问题    
原理：四分棋盘，确定一个特殊方格，L形骨牌占据特殊方格以外三个象限，直至简化为1×1棋盘

## 代码演示
```c    
void ChessBoard(int tr, int tc, int dr, int dc, int size){        
//tr,tc为棋盘左上角坐标，dr,dc为特殊方格的行列，size是棋盘规格        
if (size == 1)        
return;            
int t = tile++;            
s = size/2; //好快的切割        
//覆盖左上角棋盘        
if (dr < tr+s && dc < tc+s)
ChessBoard(tr, tc, dr, dc, s);
else{
    Board[tr+s-1][tc+s] = t; //L形骨牌覆盖右下角
    ChessBoard(tr, tc, tr+s-1, tc+s-1, s);
}
//右上角
if(dr < tr+s && dc >= tc+s)
    ChessBoard(tr, tr+s, dr, dc, s);
else{
    Board[tr+s-1][tc+s] = t;
    ChessBoard(tr, tc+s, tr+s-1, tc+s, s);
}
//左下角
if(dr >= tr+s && dc < tc+s)
    ChessBoard(tr+s, tr, dr, dc, s);
else{
    Board[tr+s][tc+s-1] = t;
    ChessBoard(tr+s, tc, tr+s, tc+s-1, s);
}
//右下角
if(dr >= tr+s && dc >= tc+s)
    ChessBoard(tr+s, tr+s, dr, dc, s);
else{
    Board[tr+s][tc+s] = t;
    ChessBoard(tr+s, tc+s, tr+s, tc+s, s);
    }
}
```

## 复杂度分析
递归方程 T(n) = 4T(n/2) + O(1)。                
主定理解得：T(n) = O(n^2)。        
空间复杂度：O(n^2)。需要一个二维数组来存储整个棋盘。        


# 归并排序    
## 算法思想        
把数组一分为二，递归地对子数组进行归并排序，把排好序的子数组合并成一个新的有序数组。        

## 代码演示        
```c
// 合并两个有序数组 (核心代码)
void merge(vector<int>& arr, int left, int mid, int right, vector<int>& temp) {
    int i = left;     // 左半部分的起始指针
    int j = mid + 1;  // 右半部分的起始指针
    int k = 0;        // 辅助数组的指针

    // 1. 双指针比较，谁小就把谁放进临时数组
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[k++] = arr[i++];
        } else {
            temp[k++] = arr[j++];
        }
    }

    // 2. 把左边剩余的元素塞进去
    while (i <= mid) temp[k++] = arr[i++];
    
    // 3. 把右边剩余的元素塞进去
    while (j <= right) temp[k++] = arr[j++];

    // 4. 把临时数组的数据拷贝回原数组
    for (int p = 0; p < k; p++) {
        arr[left + p] = temp[p];
    }
}

// 递归拆分
void mergeSort(vector<int>& arr, int left, int right, vector<int>& temp) {
    if (left >= right) return; // 边界条件：只有一个元素，自然是有序的

    int mid = left + (right - left) / 2; // 从中间切开
    
    // 递归排序左半边
    mergeSort(arr, left, mid, temp);
    // 递归排序右半边
    mergeSort(arr, mid + 1, right, temp);
    
    // 合并左右两边
    merge(arr, left, mid, right, temp);
}
```
## 复杂度分析
· 时间复杂度：O(n logn) 非常稳定。                
· 空间复杂度：O(n)，需要temp作为辅助数组。        
## 拓展                
求**逆序对**数量：求数组中有多少对数字满足arr[i] > arr[j]且i<j。        
在merge函数中，当右边数组元素arr[j]被放入temp时，说明左边剩下的所有元素都比arr[j]大。此时逆序对数量直接加上mid-i+1。时间复杂度依然是O(n logn)。        


# 快速排序
## 代码演示（双指针法）
```c    
// Partition 函数：将数组划分为左右两部分（确定基准值）
int partition(vector<int>& arr, int left, int right) {
    // 1. 选取最左边的元素作为基准（Pivot）
    int pivot = arr[left];
    int i = left, j = right;
    
    while (i < j) {
        // 2. 从右往左找第一个小于 pivot 的元素
        while (i < j && arr[j] >= pivot) j--;

        // 当while循环停止时，找到了小于pivot的元素（撞到i直接退出外层循环）
        if (i < j) arr[i++] = arr[j]; // 如果是找到了，就放到左边坑位，i右移一位
        
        // 3. 从左往右找第一个大于 pivot 的元素
        while (i < j && arr[i] <= pivot) i++;
        if (i < j) arr[j--] = arr[i]; // 放到右边坑位，j左移一位
    }
        //当循环停止时，即有i == j
        
    // 4. 把基准值填入最后的坑位        
    arr[i] = pivot;        
    return i; // 返回基准值的最终位置
}

void quickSort(vector<int>& arr, int left, int right) {
    if (left >= right) return; // 边界条件：子数组长度为0或1
    
    // 获取基准值的位置
    int pivotIndex = partition(arr, left, right);
    
    // 递归排序左半边和右半边
    quickSort(arr, left, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, right);
}
```
## 复杂度分析
时间复杂度：最好/平均: O(n logn)
最坏：O(n^2)，即数组本身已经完全有序
空间复杂度：原地排序，只需要O(log n)栈空间，最坏O(n)        

## 拓展        
快排与归并的区别？        
· 归并排序：先递归划分，然后合并时排序。（ O(n)辅助空间 ）。        
· 快速排序: 划分时做排序，然后递归处理左右（ O(log n)递归栈空间 ）。        
快排是“自顶向下”的排序，归并是“自底向上”的排序。         


# 线性时间选择（BFPRT算法——中位数的中位数）        
## 问题描述        
给定线性序集中n个元素和一个整数k，要求找出这n个元素中第k小的元素。     

## 算法思路
核心BFPRT算法：        
1. 将n个元素每5个一组，分成[n/5]组。        
2. 对每组进行插入排序，找出每组的中位数。        
3. 取出这[n/5]个中位数。        
4. 递归调用BFPRT，找出这些中位数的中位数，作为最终的pivot。        
5. 用这个pivot划分数组，只递归包含第k小元素的那一半。        

## 代码演示        
```c
// 插入排序 (用于处理小规模子数组) = 扑克牌！        
void insertionSort(vector<int>& arr, int left, int right);        
// 划分函数 (同快排，返回基准元素最终位置)        
int partition(vector<int>& arr, int left, int right, int pivot);

// 核心：线性时间选择
int Select(vector<int>& arr, int left, int right, int k) {
    if (right - left < 75) { //设置一个阈值，小于该阈值直接用简单排序
        insertionSort(arr, left, right);
        return arr[left + k]; 
    }

    // 1. 每 5 个一组，找中位数
    for (int i = 0; i < (right - left - 4) / 5; i++) {
        insertionSort(arr, left + i * 5, left + i * 5 + 4);
        swap(arr[left + i], arr[left + i * 5 + 2]); // 把中位数换到前面
    }

    // 2. 递归寻找中位数的中位数 (完美的基准)
    int mid = (right - left - 4) / 10;
    int pivot = Select(arr, left, left + mid, mid);

    // 3. 以 pivot 为基准划分
    int pos = partition(arr, left, right, pivot);

    // 4. 判断目标在左边还是右边
    int num = pos - left + 1; // 基准的排名
    if (k == num) return arr[pos];
    else if (k < num) return Select(arr, left, pos - 1, k);
    else return Select(arr, pos + 1, right, k - num);
```

## 复杂度分析
通过不完全排序，将时间复杂度降到了线性；只递归处理目标所在的那一边。                        
Quickselect算法：复杂度是O(n)，最坏O(n^2)，即数组已经有序。                    
BFPRT算法：绝对O(n)。        


# 最接近点对问题
## 问题描述
给定平面上n个点，找出其中距离最近的两个点。        

## 算法思想                        
分治法：                
1. 用一条直线将点集分为左右两半（SL，SR）。保证两边点数大致相等。                
2. 递归地在左半边找最小距离dL，在右半边找最小距离dR。                
3. 令d = min(dL,dR)。                
**如何找跨越中线的点对？**        
1、以中线L为中心，划分出一个宽度为2d的带状区域。                
2、只有落在这个区域内的点，才有可能产生小于d的距离。                
3、鸽巢原理：将带状区域划分为便成为d/2的网格。每个网格内最多只能有 1 个点（否则内部距离就小于d了）。对于带状区域内的任意一个点p，它只需要与按y坐标排序后的紧邻的 7 个点进行比较，就能找到最小距离。        

## 代码演示
```c
#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

struct Point {
    double x, y;
};

// 按 x 坐标排序
bool cmpX(const Point& a, const Point& b) { return a.x < b.x; }
// 按 y 坐标排序
bool cmpY(const Point& a, const Point& b) { return a.y < b.y; }

// 计算两点距离
double dist(const Point& a, const Point& b) {
    return sqrt((a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y));
}

// 分治法核心
double closestPair(vector<Point>& px, vector<Point>& py, int left, int right) {
    // 1. 基础情况：2个或3个点，直接暴力算
    if (right - left <= 2) {
        double min_d = 1e18;
        for (int i = left; i <= right; i++) {
            for (int j = i + 1; j <= right; j++) {
                min_d = min(min_d, dist(px[i], px[j]));
            }
        }
        return min_d;
    }

    // 2. 分：用中线把点集分成左右两半
    int mid = left + (right - left) / 2;
    double mid_x = px[mid].x;

    // 递归求解左右两边的最小距离
    double d_left = closestPair(px, py, left, mid);
    double d_right = closestPair(px, py, mid + 1, right);
    double d = min(d_left, d_right);

    // 3. 合：收集落在带状区域 [mid_x - d, mid_x + d] 内的点
    vector<Point> strip;
    for (int i = left; i <= right; i++) {
        if (abs(px[i].x - mid_x) < d) {
            strip.push_back(px[i]);
        }
    }

    // 将带状区域内的点按 y 坐标排序
    sort(strip.begin(), strip.end(), cmpY);

    // 4. 检查带状区域内的点对（最多检查 7 个相邻点）
    for (int i = 0; i < strip.size(); i++) {
        for (int j = i + 1; j < strip.size() && (strip[j].y - strip[i].y) < d; j++) {
            d = min(d, dist(strip[i], strip[j]));
        }
    }

    return d;
}

int main() {
    // 测试数据
    vector<Point> points = {{2, 3}, {12, 30}, {40, 50}, {5, 1}, {12, 10}, {3, 4}};
    int n = points.size();

    vector<Point> px = points;
    // py 其实不需要每次从头排，但为了简单先用全局按 x 排
    sort(px.begin(), px.end(), cmpX);
    
    vector<Point> py = px;

    cout << "最近点对距离: " << closestPair(px, py, 0, n - 1) << endl;
    return 0;
}
```

## 复杂度分析        
时间复杂度：O(nlogn)。递归树深度O(log n)，每一层需要O(n)的扫描。                       
空间复杂度：O(n)。主要是递归栈和辅助数组。        


# 循环赛日程表        
## 问题描述
有n = 2^k个选手参加网球循环赛。
要求：每个选手必须与其他n-1个选手各赛一场；每个选手每天只能赛一场；循环赛一共进行n-1天。        

## 代码演示
```c
#include <iostream>
using namespace std;

int a[100][100]; // 定义二维数组，a[i][j]表示第i个选手在第j天遇到的对手

// 核心函数：从左上角起点(x, y)开始，把一个边长为 m 的方阵复制扩展成边长为 2m 的方阵
void copy(int x, int y, int m) {
    // 对角线复制：左上 -> 右下
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < m; j++) {
            a[x + i + m][y + j + m] = a[x + i][y + j];
        }
    }
    // 右上角复制：左上 -> 右上（加上 m）
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < m; j++) {
            a[x + i][y + j + m] = a[x + i][y + j] + m;
        }
    }
    // 左下角复制：右上 -> 左下
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < m; j++) {
            a[x + i + m][y + j] = a[x + i][y + j + m];
        }
    }
}

// 递归分治
void solve(int x, int y, int n) {
    if (n == 2) {
        a[x][y] = x + 1; a[x][y+1] = x + 2;
        a[x+1][y] = x + 2; a[x+1][y+1] = x + 1;
        return;
    }
    solve(x, y, n / 2);              // 左上角
    solve(x, y + n / 2, n / 2);      // 右上角
    solve(x + n / 2, y, n / 2);      // 左下角
    solve(x + n / 2, y + n / 2, n / 2); // 右下角
    copy(x, y, n / 2);               // 合并
}
```

## 复杂度分析
时间复杂度：T(n) = 4T(n/2) + O(n^2) 解得：O(n^2)——最终填满一个n × n的表格，达到了理论最优。                      空间复杂度：O(n^2)表格。          
