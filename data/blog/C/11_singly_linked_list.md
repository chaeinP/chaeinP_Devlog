---
title: C_단일 연결 리스트
date: '2021-10-04'
tags: ['C']
draft: false
summary: C언어로 단일 연결 리스트 구현하기
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 직접 확인하시기를 권합니다.

## 단일 연결 리스트

다른 노드를 가리키는 포인터가 하나씩만 있는 연결리스트를 단일 연결 리스트라고 한다. 연결리스트는 노드의 집합이고 C언어에서 노드는 구조체로 정의된다.

```c++
struct Node {
  struct Node *next; // 다음 노드의 주소를 저장한다.
  int data;
}
```

노드는 역할에 따라 두 가지로 나뉜다.

- 머리 노드 : 단일 연결 리스트의 기준점이며 헤드라고도 부른다. 머리 노드는 첫번째 노드를 가리키는 용도이기 때문에 **데이터를 저장하지 않는다.**

- 노드 : 단일 연결 리스트에서 데이터가 저장되는 실제 노드입니다.

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct NODE {             // 연결 리스트의 노드 구조체
    struct NODE *next;    // 다음 노드의 주소를 저장할 포인터
    int data;             // 데이터를 저장할 멤버
};

int main()
{
    struct NODE *head = malloc(sizeof(struct NODE));    // 머리 노드 생성
                                                        // 머리 노드는 데이터를 저장하지 않음

    struct NODE *node1 = malloc(sizeof(struct NODE));   // 첫 번째 노드 생성
    head->next = node1;                                 // 머리 노드 다음은 첫 번째 노드
    node1->data = 10;                                   // 첫 번째 노드에 10 저장

    struct NODE *node2 = malloc(sizeof(struct NODE));   // 두 번째 노드 생성
    node1->next = node2;                                // 첫 번째 노드 다음은 두 번째 노드
    node2->data = 20;                                   // 두 번째 노드에 20 저장
    node2->next = NULL;                                 // 두 번째 노드 다음은 노드가 없음(NULL)

    struct NODE *curr = head->next;    // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장
    while (curr != NULL)               // 포인터가 NULL이 아닐 때 계속 반복
    {
        printf("%d\n", curr->data);    // 현재 노드의 데이터 출력
        curr = curr->next;             // 포인터에 다음 노드의 주소 저장
    }

    free(node2);    // 노드 메모리 해제
    free(node1);    // 노드 메모리 해제
    free(head);     // 머리 노드 메모리 해제

    return 0;
}
```

매번 노드를 생성해서 연결하는 것은 번거롭고 비효율적이다. 반복 작업은 함수를 통해 간소화시킬 수 있다.

## 노드 추가 함수

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct NODE {    // 연결 리스트의 노드 구조체
    struct NODE *next;    // 다음 노드의 주소를 저장할 포인터
    int data;             // 데이터를 저장할 멤버
};

void addFirst(struct NODE *target, int data)    // 기준 노드 뒤에 노드를 추가하는 함수
{
    struct NODE *newNode = malloc(sizeof(struct NODE));    // 새 노드 생성
    newNode->next = target->next;    // 새 노드의 다음 노드에 기준 노드의 다음 노드를 지정
    newNode->data = data;            // 데이터 저장

    target->next = newNode;    // 기준 노드의 다음 노드에 새 노드를 지정
}

int main()
{
    struct NODE *head = malloc(sizeof(struct NODE));    // 머리 노드 생성
                                                        // 머리 노드는 데이터를 저장하지 않음
    head->next = NULL; // 초기화

    addFirst(head, 10);    // 머리 노드 뒤에 새 노드 추가
    addFirst(head, 20);    // 머리 노드 뒤에 새 노드 추가
    addFirst(head, 30);    // 머리 노드 뒤에 새 노드 추가

    struct NODE *curr = head->next;    // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장
    while (curr != NULL)               // 포인터가 NULL이 아닐 때 계속 반복
    {
        printf("%d\n", curr->data);    // 현재 노드의 데이터 출력
        curr = curr->next;             // 포인터에 다음 노드의 주소 저장
    }

    curr = head->next;      // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장
    while (curr != NULL)    // 포인터가 NULL이 아닐 때 계속 반복
    {
        struct NODE *next = curr->next;    // 현재 노드의 다음 노드 주소를 임시로 저장
        free(curr);                        // 현재 노드 메모리 해제
        curr = next;                       // 포인터에 다음 노드의 주소 저장
    }

    free(head);    // 머리 노드 메모리 해제

    return 0;
}
```

## 노드 삭제 함수

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct NODE {    // 연결 리스트의 노드 구조체
    struct NODE *next;    // 다음 노드의 주소를 저장할 포인터
    int data;             // 데이터를 저장할 멤버
};

void addFirst(struct NODE *target, int data)    // 기준 노드 뒤에 노드를 추가하는 함수
{
    struct NODE *newNode = malloc(sizeof(struct NODE));    // 새 노드 생성
    newNode->next = target->next;    // 새 노드의 다음 노드에 기준 노드의 다음 노드를 지정
    newNode->data = data;            // 데이터 저장

    target->next = newNode;    // 기준 노드의 다음 노드에 새 노드를 지정
}

void removeFirst(struct NODE *target)    // 기준 노드의 다음 노드를 삭제하는 함수
{
    struct NODE *removeNode = target->next;    // 기준 노드의 다음 노드 주소를 저장
    target->next = removeNode->next;     // 기준 노드의 다음 노드에 삭제할 노드의 다음 노드를 지정

    free(removeNode);    // 노드 메모리 해제
}

int main()
{
    struct NODE *head = malloc(sizeof(struct NODE));    // 머리 노드 생성
                                                        // 머리 노드는 데이터를 저장하지 않음
    head->next = NULL;

    addFirst(head, 10);      // 머리 노드 뒤에 새 노드 추가
    addFirst(head, 20);      // 머리 노드 뒤에 새 노드 추가
    addFirst(head, 30);      // 머리 노드 뒤에 새 노드 추가

    removeFirst(head);       // 머리 노드 뒤에 있는 노드를 삭제

    struct NODE *curr = head->next;
    // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장
    while (curr != NULL)                // 포인터가 NULL이 아닐 때 계속 반복
    {
        printf("%d\n", curr->data);     // 현재 노드의 데이터 출력
        curr = curr->next;              // 포인터에 다음 노드의 주소 저장
    }

    curr = head->next;      // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장
    while (curr != NULL)    // 포인터가 NULL이 아닐 때 계속 반복
    {
        struct NODE *next = curr->next; // 현재 노드의 다음 노드 주소를 임시로 저장
        free(curr);        // 현재 노드 메모리 해제
        curr = next;       // 포인터에 다음 노드의 주소 저장
    }

    free(head);    // 머리 노드 메모리 해제

    return 0;
}
```
