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




