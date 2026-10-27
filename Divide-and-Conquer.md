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

