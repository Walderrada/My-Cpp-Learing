# Recursion
## 1.Factorial阶乘函数
```c
int factorial (int n)
{
    if (n == 0) return 1;
    return n * factorial(n-1);
}
```
## 2.Fibonacci斐波那契数列
```c
int Fibonacci(int n)
{
    if (n <= 2) return 1;
    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```
*记忆化搜索*
```c
vector<int> save(n,0); //存储计算过的数据，解决重复性计算的性能问题
int Fibonacci(int n)
{
    if (n <= 2) return 1;
    if (save[n] == -1) 
    {
        return save[n] = Fibonacci(n - 1) + Fibonacci(n - 2);
    }
    return save[n];
}
```
## 3.Hanoi塔问题
```c
void hanoi （int n, int a, int b, int c) //From a to b
{
    if (n > 0)
    {
        hanoi(n-1, a, c, b); //从a移至c，b为辅助塔座
        move(a, b);
        hanoi(n-1, c, b, a);
    }
}
```
**焚诀：不要思考“先有鸡还是先有蛋”的问题，把函数体当成库中已有函数直接调用，写完即逻辑自洽**

## 4.排序问题(全排列)
```c
void Perm(int list[], int k, int m)
{
    if (k == m)
    {
         for(int i = 0; i <= m; i++)
         {
              cout << list[i] << " ";
         }
         cout << endl;
    }
    else
    {
         for(int i = k; i < m; i++)
         {
              Swap(list[k], list[i]); 
              Perm(list, k + 1, m); 
              Swap(list[k], list[i]); //回溯
         }
    }
}
```

# 主定理
**固定格式： T(n) = a * T(n/b) + f(n)**    
1.把一个规模为n的问题，分成了a个规模为n/b的子问题  
2.分解和合并这些子问题所花费的时间（非递归问题）是f（n）  

**基准值 n ^ logb a**  
与f(n)比较大小，谁大谁主导T(n)  

*三种主定理无法使用的情况：  
1.多项式差距不成立：f(n)只比基准值大一点，比如logn的倍数  
2.格式不符：比如T(n) = T(n-1) + n 线性递推  
3.a和b不固定，即a和b随n变化*  
