# Recursion
## 1.Factorial
```c
int factorial (int n)
{
    if (n == 0) return 1;
    return n * factorial(n-1);
}
```
## 2.Fibonacci
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

## 4.排序问题
```c
   


