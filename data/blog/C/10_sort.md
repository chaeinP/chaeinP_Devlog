---
title: C_정렬 알고리즘
date: '2021-10-04'
tags: ['C']
draft: false
summary: 정렬 알고리즘, C 언어로 이해하기
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 직접 확인하시기를 권합니다.

## 선택 정렬

정렬 알고리즘 중에서도 비효율적인 방법 중 하나지만 가장 간단하게 구현할 수 있는 알고리즘, 가장 작은 값을 찾아 맨 앞으로 보내는 정렬방법이다. 시간복잡도는 O(N^2)이다.

```c++
#include <stdio.h>
#include <limits.h>
int main(void){
    int array[10] = {1, 9, 4, 10, 6, 2, 5, 3, 7, 8};
    int i, j, min, index, temp;
    for(i=0; i<10; i++){
        min = INT_MAX; // int중 가장 큰 수로 초기화
        for(j=i; j<10; j++){
            if(min > array[j]){
                min = array[j];
                index = j;
            }
        }
        temp = array[i];
        array[i] = array[index];
        array[index] = temp;
    }
    for(i=0; i<10; i++){
        printf("%d ", array[i]);
    }
    return 0;
}​
```

## 버블 정렬

버블 정렬은 처음부터 끝까지 요소를 순회하면서 현재 값과 다음 값을 비교하여 큰 값을 다음으로 보낸다. 시간복잡도는 O(n^2)으로 효율이 좋지 않다. 따라서 널리 쓰이는 알고리즘은 아니다.

```c++
#include <stdio.h>

void bubble_sort(int arr[], int count)  // 매개변수로 정렬할 배열과 요소의 개수를 받음
{
    int temp;
    for (int i = 0; i < count; i++)    // 요소의 개수만큼 반복
    {
        for (int j = 0; j < count - 1 - i; j++)   // 요소의 개수 - 1 - i만큼 반복
        {
            if (arr[j] > arr[j + 1])          // 현재 요소의 값과 다음 요소의 값을 비교하여
            {                                 // 큰 값을
                temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;            // 다음 요소로 보냄
            }
        }
    }
}

int main()
{
    int numArr[10] = { 8, 4, 2, 5, 3, 7, 10, 1, 6, 9 };   // 정렬되지 않은 배열
    bubble_sort(numArr, sizeof(numArr) / sizeof(int));    // 거품 정렬 함수 호출
    for (int i = 0; i < 10; i++)
    {
        printf("%d ", numArr[i]);    // 1 2 3 4 5 6 7 8 9 10
    }
    printf("\n");

    return 0;
}
```

## 삽입 정렬

삽입 정렬은 앞의 원소보단 크고, 뒤에 원소보단 작은 위치에 삽입하는 방법이다. 시간복잡도는 O(N^2)이다.

```c++
#include <stdio.h>
int main(void) {
    int i, j, temp;
    int array[10] = {4, 5, 2, 7, 9, 1, 8, 3, 6, 10};
    for(i=0; i< 9; i++){
        j = i;
        while(array[j] > array[j+1]){
            temp = array[j];
            array[j] = array[j+1];
            array[j+1] = temp;
            j--;
        }
    } // 결과 확인
    for(i=0 ; i<10; i++){
        printf("%d ", array[i]);
    }
    return 0;
}
```

> 선택정렬, 버블정렬, 삽입정렬 모두 시간복잡도는 O(N^2)로 같지만, 특정한 경우(거의 정렬이 다 된 상태)에서는 삽입정렬이 다른 두 정렬보다 훨씬 빠르다.

## 퀵정렬

퀵정렬은 O(N\*logN) 의 시간복잡도를 갖는다. 최악의 경우, 이미 모든 데이터가 정렬되어있는 경우 O(N^2)이다.

```c++
#include <stdio.h>
int data[10] = {4, 1, 2, 3, 9, 7, 8, 6, 10, 5};

void quick_sort(int *data, int start, int end){
    if(start >= end){ // 원소가 1개인 경우
        return;
    }

    int pivot = start;
    int i = pivot + 1; // 왼쪽 출발 지점
    int j = end; // 오른쪽 출발 지점
    int temp;
    while(i <= j){ // 포인터가 엇갈릴때까지 반복
        while(i <= end && data[i] <= data[pivot]){
            i++;
        }
        while(j > start && data[j] >= data[pivot]){
            j--;
        }

        if(i > j){ // 엇갈림
            temp = data[j];
            data[j] = data[pivot];
            data[pivot] = temp;
        } else { // i번째와 j번째를 스왑
            temp = data[i];
            data[i] = data[j];
            data[j] = temp;
        }
    }
    // 분할 계산
    quick_sort(data, start, j - 1);
    quick_sort(data, j + 1, end);
}

int main(void){
    quick_sort(data, 0, 9); // 결과 확인
    for(int i=0; i<10; i++){
        printf("%d ", data[i]);
    }
    return 0;
}
```

C 언어에는 퀵정렬을 사용할 수 있는 내장 함수가 있다.

```c++
#include <stdio.h>
#include <stdlib.h>    // qsort 함수가 선언된 헤더 파일

int compare(const void *a, const void *b)    // 오름차순 비교 함수 구현
{
    int num1 = *(int *)a;    // void 포인터를 int 포인터로 변환한 뒤 역참조하여 값을 가져옴
    int num2 = *(int *)b;    // void 포인터를 int 포인터로 변환한 뒤 역참조하여 값을 가져옴

    if (num1 < num2)    // a가 b보다 작을 때는
        return -1;      // -1 반환

    if (num1 > num2)    // a가 b보다 클 때는
        return 1;       // 1 반환

    return 0;    // a와 b가 같을 때는 0 반환
}

int main()
{
    int numArr[10] = { 8, 4, 2, 5, 3, 7, 10, 1, 6, 9 };    // 정렬되지 않은 배열

    // 정렬할 배열, 요소 개수, 요소 크기, 비교 함수를 넣어줌
    qsort(numArr, sizeof(numArr) / sizeof(int), sizeof(int), compare);
    for (int i = 0; i < 10; i++)
    {
        printf("%d ", numArr[i]);    // 1 2 3 4 5 6 7 8 9 10
    }
    printf("\n");

    return 0;
}
```

추가 정리예정

## 병합정렬

## 힙정렬

## 계수정렬

참고자료

- [코딩 도장-C언어](https://dojang.io)
- [IT에 취.하.개.](https://hongku.tistory.com/146?category=804730)
