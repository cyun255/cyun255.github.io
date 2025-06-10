---
title: 算法复杂度与简单排序算法
date: 2023-05-30
comments: false
categories: 算法
tags:
	- 算法
	- 复杂度
	- 二分法
	- 排序算法
	- 选择排序
	- 冒泡排序
	- 插入排序
description: |
---

## 算法与复杂度

算法的核心是创建问题抽象的模型和明确求解目标，之后可以根据具体的问题选择不同的模式和方法完成算法的设计。
针对同一问题，不同的算法在执行效率或内存空间上可能存在差异，复杂度是评判算法好坏的一个指标。

- 时间复杂度：指算法所需要消耗的时间资源，评估算法的执行效率。
- 空间复杂度：指算法所需要消耗的空间资源，评估算法对内存空间的使用情况。

一个算法通常存在最好、平均、最差三种情况，一般使用大写字母$O$表示算法在最差情况下的复杂度，所以也称作 **大$O$表示法**

## 常数阶

简单理解，只要代码里没有循环迭代、递归等复杂的操作，就一行代码能够完成的动作，就是常数操作。
不管多少行代码，只要是常数操作，时间复杂度统一使用 $O(1)$ 表示

```c
int a = 10;
int b = a << 2;
int c = list[b];
```

算法实现过程中仅使用现有资源、几个简单的变量，没有额外创建内存空间的算法，空间复杂度使用 $O(1)$ 表示

## 简单排序算法

### 选择排序

选择排序的思想很简单，将整个数组分成两个部分
- 前面一部分已经完成排序
- 后面一部分等待排序
	- 遍历这一部分数据，找出最小值的位置
	- 将最小值跟排序完成部分后一位数据位置交换

![选择排序](https://images.rescld.cn/selectionSort.gif)

```c
void selection_sort(int arr[], int len) {
	if (len <= 1) return;

	for(int i = 0; i < len - 1; i++) {  \\ O(N)
		int min = i;
		for(int j = i + 1; j < len; j++) { \\ O(N)
			min =  arr[min] < arr[j] ? min : j;
		}
		swap(&arr[i], &arr[min]);
	}
}
```

选择排序需要使用两层循环实现，总体时间复杂度$O(N^2)$
- 外层循环，选择未排序的第一个元素，一共需要执行$N-1$次，时间复杂度$O(N)$
- 内层循环，遍历未排序的部分找出最小值，也需要执行$N-1$次，时间复杂度$O(N)$

由于选择排序整个操作都是在原数组上完成，并没有申请额外内存空间，所以空间复杂度$O(1)$

### 冒泡排序

冒泡排序操作步骤很简单
- 直接遍历数组对比每一对相邻两个元素，如果第二位比第一位大就交换两个元素
- 完整遍历一遍数组之后，能够将数组中最大的值放到最后
- 撇开最后一个元素，其他元素重复以上操作，直到排序完成为止

![冒泡排序](https://images.rescld.cn/bubbleSort.gif)

```c
void bubble_sort(int arr[], int len) {
	if(len <= 1) return;

	for(int end = len; end > 0; end--) {   \\ O(N)
		for(int i = 0; i < end - 1; i++) { \\ O(N)
			if(arr[i] > arr[i+1]) swap(&arr[i], &arr[i+1]);
		}
	}
}
```

冒泡排序也是需要两层循环实现，总体时间复杂度$O(N^2)$
- 外层循环，通过 `end--` 实现每一次遍历后忽略最后一个元素，需要循环$N$次，时间复杂度$O(N)$
- 内层循环，对比每一对相邻两个元素的大小，每次循环最多需要对比$N-1$次，时间复杂度$O(N)$

冒泡排序跟选择排序一样，都是直接在原数组上操作没有申请额外内存空间，空间复杂度$O(1)$

### 插入排序

插入排序就像打扑克摸牌时，会将新拿到的排从后往前对比，直到找到合适的位置后插入手牌。
- 数组中每一位元素向前遍历对比
- 如果前一位更大，两个元素交换，否则结束向前遍历

![插入排序](https://images.rescld.cn/insertionSort.gif)

```c
void insertion_sort(int arr[], int len) {
	if(len <= 1) return;

	for (int i = 1; i < len; i++) {  \\ O(N)
		for(int j = i-1; j >= 0 && arr[j] > arr[j+1]; j--) { \\ O(N)
			swap(&arr[j], &arr[j+1]);
		}
	}
}
```

插入排序根据不同的数据情况，执行的时间效率是不同的。
- 在最好的情况下（数据已经排序完成）只需要向后遍历一次，没有交换动作，时间复杂度$O(N)$
- 在最坏的情况下（数据完全倒叙排序），除了向后遍历一遍$O(N)$外，还需要向前遍历交换数据$O(N)$，整体时间复杂度$O(N^2)$

插入排序也是在原数组上完成的，空间复杂度$O(1)$

## 二分法

比如在一个已经排序完成的数组中查找一个数，需要怎么操作？
直接从头到尾遍历一遍当然是可以的，时间复杂度$O(N)$

```c
int get_index(int arr[], int len, int num) {
	if (len < 0) return -1;

	for (int i = 0; i < len; i++) {
		if(arr[i] == num) return i;
	}
	return -1;
}
```

也可以使用二分法，选择数组中间的元素
- 查找的数大于中间的元素，左边的数据再次使用二分查找
- 否则，右边的数据再次使用二分查找


每次使用二分法都会放弃一半的数据，整体时间复杂度$O(logn)$
在计算机科学中如果没有特别强调对数底数，都默认以2为底，即$O(log_2n)$

![二分法](https://images.rescld.cn/Binary_search_into_array.png)

```c
int binary_search(int arr[], int l, int r, int num) {
	if(l > r) return -1;

	int mid = l + ((r-l) >> 2);
	if(arr[mid] == num) return mid;
	else if(arr[mid] > num) return binary_search(arr, l, mid, num);
	else return binary_search(arr, mid+1, r, num);
}
```















