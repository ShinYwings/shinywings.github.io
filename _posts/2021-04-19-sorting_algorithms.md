---
title: Sorting Algorithm
date: 2021-04-19 12:00:00 -0500
categories: [Algorithms]
tags: [Algorithm, Python]
---

## Bubble Sort

> The bubble sort algorithm compares two adjacent elements and swaps them if they are not in the intended order.

연달아 오는 두 수를 비교하여 정렬하는 방법으로 비교하는 두 값을 오름차순으로 정렬한다.

**Implementation**

```python
def bubbleSort(array):
    for i in range(len(array)):
        for j in range(0, len(array) - i - 1):
            if array[j] > array[j+1]:
                # arr[j], arr[j+1] = arr[j+1], arr[j]
                temp = array[j+1]
                array[j+1] = array[j]
                array[j] = temp
    return array
```

<br/>

**Complexity**

> 1 2 3 4 5 6 7 8 9 10<br/>
> 10 + 9 + 8 + ... + 1 **(Arithmetic scquence, 등차수열)**<br/>
> => 10 \* (10 + 1) / 2<br/>
> => n \* (n + 1) / 2<br/>
> => O (n \* n)<br/>
> => O (n^2)

## Selection Sort

> Selection sort is an algorithm that selects the smallest element from an unsorted list in each iteration and places that element at the beginning of the unsorted list.

---

**Implementation**

```python
def selectionSort(array):
    for i in range(len(array)):
        min = 9999
        for j in range(i, len(array)):
            if min > array[j]:
                min = array[j]
                index = j
        temp = array[i]
        array[i] = array[index]
        array[index] = temp

    return array
```

<br/>

**Complexity**

> 1 2 3 4 5 6 7 8 9 10<br/>
> 10 + 9 + 8 + ... + 1 **(Arithmetic scquence, 등차수열)**<br/>
> => 10 \* (10 + 1) / 2<br/>
> => n \* (n + 1) / 2<br/>
> => O (n \* n)<br/>
> => O (n^2)

## Insertion Sort

> Insertion sort is based on the idea that one element from the input elements is consumed in each iteration to find its correct position.

**Implementation**

```python
def test(array):
    for i in range(1, len(array)):
        selected = array[i]
        j = i - 1

        while j >= 0 and selected < array[j]:
            array[j+1] = array[j]
            j = j - 1

        array[j+1] = selected

    return array
```

<br/>

**Complexity**

> 1 2 3 4 5 6 7 8 9 10<br/>
> 10 + 9 + 8 + ... + 1 **(Arithmetic scquence, 등차수열)**<br/>
> => 10 \* (10 + 1) / 2<br/>
> => n \* (n + 1) / 2<br/>
> => O (n \* n)<br/>
> => O (n^2)

## Quick Sort

> Quicksort algorithm is based on the **divide** and **conquer** approach where
>
> 1. An array is divided into subarrays by selecting a **pivot** element (element selected from the array, 기준값).
>    While dividing the array, the pivot element should be positioned in such a way that elements less than pivot are kept on the left side and elements greater than pivot are on the right side of the pivot.
> 2. The left and right subarrays are also divided using the same approach. This process continues until each subarray contains a single element.
> 3. At this point, elements are already sorted. Finally, elements are combined to form a sorted array.

**Implementation**

```python
def quickSort(array):
    if len(array) < 2:
        return array
    else:
        pivot = array[0]
        ls = [number for number in array[1:] if number <= pivot]
        gt = [number for number in array[1:] if number > pivot]
        return quickSort(ls) + [pivot] + quickSort(gt)
```

<br/>

**Complexity**

- Worst Case

  > O (N^2)

- Best Case

  > O ( n \* log n)

- Average Case

  > O ( n \* log n)

## Merge Sort

**Implementation**

```python
def merge(U, V):
    S = []
    i = j = 0
    while i < len(U) and j < len(V):
        if U[i] < V[j]:
            S.append(U[i])
            i += 1
        else:
            S.append(V[j])
            j += 1

    if i < len(U):
        S += U[i : len(U)]
    else:
        S += V[j : len(V)]
    return S


def mergeSort(S):
    n = len(S)
    if n <= 1:
        return S
    else:
        mid = n // 2
        U = mergeSort(S[0 : mid])
        V = mergeSort(S[mid : n])
        return merge(U, V)
```

<br/>

**Complexity**

## Heap Sort

> Heap sort works by visualizing the elements of the array as a special kind of complete binary tree called a heap.

**Implementation**

```python
def maxHeap(arr, size, i):
    # Find largest among root and children
    largest = i
    lc = 2 * i + 1
    rc = 2 * i + 2

    if lc < size and arr[i] < arr[lc]:
        largest = lc

    if rc < size and arr[largest] < arr[rc]:
        largest = rc

    # If root is not largest, swap with largest and continue heapifying
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        maxHeap(arr, size, largest)


def heapSort(arr):
    size = len(arr)

    # Build max heap
    for i in range(size//2, -1, -1):
        maxHeap(arr, size, i)

    for i in range(size-1, 0, -1):
        # Swap
        arr[i], arr[0] = arr[0], arr[i]

        # Heapify root element
        maxHeap(arr, i, 0)

    return arr
```

<br/>

**Complexity**

## Time Complexity

| Sorting Alorithms | 공간 복잡도 최악 | 공간 복잡도 최선 | 시간 복잡도 평균 | 시간 복잡도 최악 |
| ----------------- | :--------------: | :--------------: | :--------------: | :--------------: |
| Bubble Sort       |       O(1)       |       O(n)       |      O(n2)       |      O(n2)       |
| Selection Sort    |       O(1)       |      O(n2)       |      O(n2)       |      O(n2)       |
| Insertion Sort    |       O(1)       |       O(n)       |      O(n2)       |      O(n2)       |
| Quick Sort        |     O(log n)     |    O(n log n)    |    O(n log n)    |    O(n log n)    |
| Merge Sort        |       O(n)       |    O(n log n)    |    O(n log n)    |    O(n log n)    |
| Heap Sort         |       O(1)       |    O(n log n)    |    O(n log n)    |    O(n log n)    |

## References

- [Sorting and Searching Algorithms](https://www.programiz.com/dsa/bubble-sort)
