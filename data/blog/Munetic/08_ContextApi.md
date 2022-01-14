---
title: Munetic_Context Api를 이용한 전역 상태 관리
date: '2021-12-22'
tags: ['Munetic', 'React', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(7)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## Context Api 사용 이유

admin 페이지 기획이 아직 진행중이기 때문에 프로젝트 1팀이 개발해야할 admin 페이지 기능이 많지 않았습니다. 이로 인해 전역으로 관리가 필요한 상태 역시 많지 않았습니다. 상태 관리 툴로 리덕스가 함께 고려되었지만 현재 필요한 상태 관리 기능에 비해 리덕스를 사용하는 것은 매우 헤비해보였습니다. 리덕스를 사용하면 기본적으로 구축해야하는 상태 관리 플로우로 인해 코드의 양이 많아지고 리덕스가 제공하는 추가 기능들이 현 상황에서는 수요가 없었기 때문입니다. 이에 따라 외부 상태 관리 툴을 도입하기 보다 리액트에서 제공하는 기능선에서 전역 상태 관리를 진행하기로 결정하였습니다.

## Context Api로 관리하고 있는 상태들

현재 admin 페이지에서 전역으로 관리하고 있는 상태는 login과 info 입니다.

- login : login 여부를 관리하는 상태입니다.
- info : user, admin_users, lessons 상세 정보 페이지에 보여지는 info를 담는 상태입니다. 각 상세 페이지는 그리드 컴포넌트로 이루어져있는데 상태를 공유해야하는 컴포넌트들의 레벨이 깊어 context api로 관리하게 되었습니다.

## Context Api 사용시 주의 사항

Context Api는 리덕스에 비해 상태 변화에 따른 성능 최적화가 되어있지 않습니다. 주의를 기울여 사용하지 않으면 이후 렌더 성능에 큰 영향을 미칠 수 있습니다. 따라서 Context Api를 사용할 때는 관련이 없는 상태가 의존성을 갖지 않도록 관심사를 분리해주어야 합니다.

예를 들어, 저는 info context는 info와 setInfo context를 분리해 사용하고 있습니다.
만약 info와 setInfo를 분리하지 않고 하나의 context로 묶는다면 이렇게 작성할 수 있습니다.

```ts
// 타입 정의는 생략
const InfoContext = createContext<InfoType | null>(null)

export default function InfoProvider({ children }: Props) {
  const [info, setInfo] = useState<object>({})

  return <InfoContext.Provider value={{ info, setInfo }}>{children}</InfoContext.Provider>
}
```

이렇게 작성하면 setInfo로 info의 상태를 업데이트 할때 info 상태를 사용하고 있는 컴포넌트 뿐만 아니라 setInfo가 위치한 컴포넌트까지 모두 리렌더링 됩니다. setInfo의 상태값은 변하지 않았으나 해당 컴포넌트가 리렌더링이 된다면 이는 불필요한 리렌더가 됩니다. 지금은 서로 의존하는 상태가 두개 뿐이기 때문에 성능에 미치는 영향이 매우 적지만 만약 의존성을 갖는 상태를 사용하는 컴포넌트가 많아진다면 렌더 성능에 큰 영향을 줄 수 있습니다. 따라서 저는 info와 setInfo를 다음과 같이 분리해주었습니다.

```ts
// 타입 정의는 생략
const InfoContext = createContext<Info | null>(null)
const InfoUpdateContext = createContext<SetInfo | null>(null)

export default function InfoProvider({ children }: Props) {
  const [info, setInfo] = useState<object>({})

  return (
    <InfoContext.Provider value={info}>
      <InfoUpdateContext.Provider value={setInfo}>{children}</InfoUpdateContext.Provider>
    </InfoContext.Provider>
  )
}
```

추가로 createContext default value로 null을 준것은 contextProvider 외부에서 해당 state값에 접근했을 시 값이 없는 잘못된 접근임을 알리기 위함입니다. 이를 통해 Provider가 감싸고 있는 영역 내에서 올바른 state 사용을 유도하기 위해 createContext 기본값을 null로 주었습니다.

이상 끝!

[참고 자료][리덕스 잘 쓰고 계시나요? - 벨로퍼트/리디](https://ridicorp.com/story/how-to-use-redux-in-ridi/)
