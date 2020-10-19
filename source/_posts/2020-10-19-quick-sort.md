---
title: quick sort
categories:
  - 排序
tags:
  - 快速排序
---
## 快速排序思想

> 1、选取一个基准元素（pivot）
> 2、比pivot小的放到pivot左边，比pivot大的放到pivot右边
> 3、对pivot左边的序列和右边的序列分别递归的执行步骤1和步骤2

确定第一位为基准数，两头向中间进行遍历，如果找到左边数大于基准数且右边的数小于基准数，那么进行交换。直到左右遍历节点相遇，交换基准数和相遇节点
### 复杂度分析
时间复杂度: 最坏情况；O(n^2) 最好情况：O(nlog(n)) 平均情况:O(nlog(n))

```
    public void sort(int[] array) {
        quickSort(array, 0, array.length - 1);
    }

    public void quickSort(int[] array, int start, int end) {
        if (start > end) {
            return;
        }
        System.out.println(Arrays.toString(array));
        int i = start;
        int j = end;
        int index = array[start];
        while (i < j) {
            //寻找需要交换的元素,在升序中左侧节点如果小于基准值，则i++
            while (i < j && array[i] < index) {
                i++;
            }
            while (i < j && array[j] > index) {
                j--;
            }
            if (i < j) {
                int temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
        //基准位的值和中间值交换
        array[i] = index;
        quickSort(array, start, i - 1);
        quickSort(array, i + 1, end);
    }
```


## 参考链接
[csdn-快速排序](https://blog.csdn.net/jianyuerensheng/article/details/51258374)

[极客学院-马桶上学算法](https://wiki.jikexueyuan.com/project/easy-learn-algorithm/fast-sort.html)