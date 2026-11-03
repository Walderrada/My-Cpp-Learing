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

## 线性时间选择



