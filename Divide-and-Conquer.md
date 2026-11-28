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
**核心数学变形（Karatsuba算法）：AD + BC = (A+B) × (C+D) − AC − BD**                
写成另一种形式:             
*XY = AC * 2^n  + ( (A-B)(D-C) + AC + BD ) * 2^(n/2) + BD*  

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
空间复杂度：O(log n)，最坏O(n)        

## 拓展        
快排与归并的区别？        
· 归并排序：先递归划分，然后合并时排序。（ O(n)辅助空间 ）。        
· 快速排序: 划分时做排序，然后递归处理左右（ O(log n)递归栈空间 ）。        
快排是“自顶向下”的排序，归并是“自底向上”的排序。         


# 线性时间选择        
## 算法思路        
问题：给定线性序集中n个元素和一个整数k，要求找出这n个元素中第k小的元素。        
## 代码演示        
```c
// 插入排序 (用于处理小规模子数组)
void insertionSort(vector<int>& arr, int left, int right) {
    for (int i = left + 1; i <= right; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= left && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

// 划分函数 (同快排，返回基准元素最终位置)
int partition(vector<int>& arr, int left, int right, int pivot) {
    int i;
    for (i = left; i < right; i++) {
        if (arr[i] == pivot) break;
    }
    swap(arr[i], arr[right]); // 把基准放到末尾

    int storeIndex = left;
    for (i = left; i < right; i++) {
        if (arr[i] < pivot) {
            swap(arr[i], arr[storeIndex]);
            storeIndex++;
        }
    }
    swap(arr[storeIndex], arr[right]); // 把基准放到正确位置
    return storeIndex;
}

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

# 最接近点对问题        
## 代码演示
```c
#include <algorithm>
#include <cmath>
#include <limits>
#include <numeric>
#include <vector>

struct Point { double x, y; };

static inline double dist2(const Point& a, const Point& b) {
    double dx = a.x - b.x, dy = a.y - b.y;
    return dx * dx + dy * dy;          // 全程比较平方，最后只开一次方
}

// px[l..r) 已按 x 升序；py 存放 px[l..r) 的下标且已按 y 升序。
// 返回该区间内最近点对距离的平方。
double closestPairRec(const std::vector<Point>& px, int l, int r,
                      std::vector<int>& py) {
    const double INF = std::numeric_limits<double>::infinity();
    int n = r - l;

    // 规模小直接暴力，同时把 py 排好（保证返回时契约仍成立）
    if (n <= 3) {
        double best = INF;
        for (int i = l; i < r; ++i)
            for (int j = i + 1; j < r; ++j)
                best = std::min(best, dist2(px[i], px[j]));
        std::sort(py.begin(), py.end(),
                  [&](int a, int b) { return px[a].y < px[b].y; });
        return best;
    }

    int m = l + n / 2;          // px[m] 是右半边的第一个点
    double midx = px[m].x;      // 分割线

    // 按归属拆分 py，两份仍按 y 有序
    std::vector<int> yl, yr;
    yl.reserve(m - l);
    yr.reserve(r - m);
    for (int id : py) (id < m ? yl : yr).push_back(id);

    double d = std::min(closestPairRec(px, l, m, yl),
                        closestPairRec(px, m, r, yr));

    // 归并回 py：整体重新按 y 有序
    std::merge(yl.begin(), yl.end(), yr.begin(), yr.end(), py.begin(),
               [&](int a, int b) { return px[a].y < px[b].y; });

    // 只保留严格落在宽 2d 条带内的点
    std::vector<int> strip;
    strip.reserve(py.size());
    for (int id : py) {
        double dx = px[id].x - midx;
        if (dx * dx < d) strip.push_back(id);
    }

    // 条带内每个点只需向后看常数个（y 差一旦 >= d 就可以停）
    for (size_t i = 0; i < strip.size(); ++i) {
        for (size_t j = i + 1; j < strip.size(); ++j) {
            double dy = px[strip[j]].y - px[strip[i]].y;
            if (dy * dy >= d) break;               // 关键：break 不是 continue
            d = std::min(d, dist2(px[strip[i]], px[strip[j]]));
        }
    }
    return d;
}

// 返回最近点对距离；n < 2 返回 -1
double closestPair(std::vector<Point> pts) {
    int n = (int)pts.size();
    if (n < 2) return -1.0;

    std::sort(pts.begin(), pts.end(),
              [](const Point& a, const Point& b) { return a.x < b.x; });

    std::vector<int> py(n);
    std::iota(py.begin(), py.end(), 0);
    std::sort(py.begin(), py.end(),
              [&](int a, int b) { return pts[a].y < pts[b].y; });

    return std::sqrt(closestPairRec(pts, 0, n, py));
}
```
*算法时间复杂度：O(nlogn)*        

# 循环赛日程表        
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





