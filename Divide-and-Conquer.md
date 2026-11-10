# Divide and Conquer 分治法
## BinarySearch 二分搜索技术
```c
int BinarySearch(int arr[], int n, int x){
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == x)
                return mid;
            if (arr[mid] < x)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return -1; // Element not found
    }
```

## 大整数乘法
将n位二进制整数X和Y都分成两段，每段长n/2    
- \(X = A \cdot 2^{n/2} + B\)        
- \(Y = C \cdot 2^{n/2} + D\)       
相乘展开后得：
XY = (A * 2^(n/2) + B)(C * 2^(n/2) + D) = AC * 2^n + (AD + BC) * 2^(n/2) + BD
     
**复杂度分析**       
按此式计算，需要进行四次n/2位整数的乘法、3次不超过2n位的整数加法，以及两次移位。所有加法和移位共用O(n)步运算     
T(n) = 4T(n/2) + O(n) , n>1     
得T(n) = O(n^2) 效率并未提升 

**优化计算**      
写成另一种形式:     
*XY = AC * 2^n  + ( (A-B)(D-C) + AC + BD ) * 2^(n/2) + BD*    
只需要做3次乘法(  AC, BD, (A-B)(D-C) )、6次减法和两次移位    
T(n) = 3T(n/2) + O(n) , n>1   
得T(n) = O(n^log3) = O(n^1.59)   
效率显著提升   

## 棋盘覆盖问题    
原理：四分棋盘，确定一个特殊方格，L形骨牌占据特殊方格以外三个象限，直至简化为1×1棋盘     
# cpp
```c    
void ChessBoard(int tr, int tc, int dr, int dc, int size){     //tr,tc为棋盘左上角坐标，dr,dc为特殊方格的行列，size是棋盘规格
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

## QuickSort快速排序       
```c    
void QuickSort(int arr[], int first, int end){
    if(first < end){
        int pivot = partition(arr, first, end);
        QuickSort(a[], first, pivot-1);
        QuickSort(a[], pivot+1, end);
    }
}
//划分函数（教材装逼写法）
int Partition(int arr[], int first, int end){
    int i = p, j = r + 1;
    int temp = a[p];  //将最左边的元素设为基准值
    while(true){
    //初始：直接从基准值的下一个元素和数组最后一个元素开始比较    
    while (a[++i] < x && i < r) ;   // 找左边大于等于 x 的数（只要条件满足，就一直空转，i++，j--）
    while (a[--j] > x) ;            // 找右边小于等于 x 的数
    if (i >= j) break;  //限制条件
    swap(a[i], a[j]);   //满足条件就交换
    }
    a[p] = a[j]    //此时i=j
    a[j] = temp;   //把基准值放在他最终正确的位置
    return j;      //返回基准值索引
}
//划分函数的人类写法
int Partition(Type a[], int p, int r) {
    int i = p, j = r;
    Type x = a[p]; // 基准值
    
    while (i < j) {
        // 1. 从右向左找小于 x 的数
        while (i < j && a[j] > x) {
            j--;
        }
        // 2. 找到后，把 a[j] 扔到左边的坑 a[i] 里
        if (i < j) {
            a[i] = a[j];
            i++;
        }
        
        // 3. 从左向右找大于 x 的数
        while (i < j && a[i] < x) {
            i++;
        }
        // 4. 找到后，把 a[i] 扔到右边的坑 a[j] 里
        if (i < j) {
            a[j] = a[i];
            j--;
        }
    }
    a[i] = x; // 把基准值填回最后的坑
    return i;
}
```

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
## 教材反人类代码
```c        
void table(int k, int **a){
    int n = 1;
    for (int i = 1; i <= k; i++)
        n *= 2;
    for (int i = 1; i <= n; i++)
        a[1][i] = i;
    int m = 1;
    for (int s = 1; s <=k; s++){
    n /= 2;
    for (int t = 1; t <= n; t++){ 
      for (int i = m+1; i< 2*m; i++){  //扩展行
        for (int j = m+1; j <= 2*m; j++){ //扩展列 
            a[i][j+(t-1)*m*2] = a[i-m][j+(t-1)*m*2-m]; //把左上数据复制到右下角
            a[i][j+(t-1)*m*2-m] = a[i-m][j+(t-1)*m*2]; //把左下数据复制到右上角
}
        m *= 2;
      }
    }
  }
}
```
## 更加清晰的代码
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





