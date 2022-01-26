---
title: Algorithm_Hash
date: '2022-01-26'
tags: ['Algorithm']
draft: false
summary: 문제풀이로 이해하는 Hash 알고리즘
---

### 프로그래머스 해시 - 완주하지 못한 선수

[프로그래머스 해시-완주하지 못한 선수 바로 풀러가기](https://programmers.co.kr/learn/courses/30/lessons/42576)

```js
/*
- 문제 설명
수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

- 제한사항
마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
completion의 길이는 participant의 길이보다 1 작습니다.
참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
참가자 중에는 동명이인이 있을 수 있습니다.

- ex
participant	            completion	        return
["leo", "kiki", "eden"]	["eden", "kiki"]	"leo"
*/
```

### 내 풀이

Hash 알고리즘 문제 였지만 hash로 풀지 못했다...

```js
function mySolution(participant, completion) {
  // 배열 문자열을 알파벳 순으로 정렬
  participant.sort()
  completion.sort()

  let count = participant.length
  let i = 0
  let result
  while (count--) {
    if (participant[i] !== completion[i]) {
      // 순서대로 비교할 시 다른 경우 해당 값 리턴
      result = participant[i]
      break
    }
    i++
  }
  return result
}
```

### Hash 알고리즘 이란?

임의의 크기를 가진 데이터(key)를 고정된 크기의 데이터(value)로 변화시켜 저장하는 것으로 변화된 데이터는 그 자체가 index가 되어 효율적인 검색, 삽입, 삭제가 가능해진다. 시간 복잡도는 0(1)이다. key를 value로 바꾸기 위해 사용되는 함수를 해시 함수라고 부르며 key 와 해시 값을 함께 저장하는 자료구조를 해시 테이블이라고 한다.

위 문제는 두 배열에 같은 문자열이 있는지를 검색, 비교하는 문제로 해시 알고리즘을 사용하면 시간 복잡도를 낮출 수 있다.

### 다른 사람 풀이

아래 풀이들은 사람 이름을 키, 값으로는 정수값을 매핑해 존재하는 이름의 횟수를 count를 해 결과를 도출하는 방식으로 푼것 같다.

```js
/**
 * 풀이 1
 */
function solution1(participant, completion) {
  //completion 에 있는 사람이름을 key로 같은 이름이 몇개 있는지 count해서 객체에 저장한다.
  var dic = completion.reduce((obj, t) => ((obj[t] = obj[t] ? obj[t] + 1 : 1), obj), {})

  // participant에 있지만 completion에 없는 사람이름을 찾아내 리턴한다.
  return participant.find((t) => {
    if (dic[t]) dic[t] = dic[t] - 1
    else return true
  })
}

/**
 * 풀이 2
 */
function solution2(participant, completion) {
  const map = new Map() //객체 생성

  for (let i = 0; i < participant.length; i++) {
    let a = participant[i],
      b = completion[i]

    map.set(a, (map.get(a) || 0) + 1) // participant에 이름이 있을 때 +1
    map.set(b, (map.get(b) || 0) - 1) // completion에 이름이 있을 때 -1 , 결국 둘다 존재하는 이름은 0이 될 수 밖에 없는 구조
  }

  for (let [k, v] of map) {
    if (v > 0) return k
  }

  return 'nothing'
}
```
