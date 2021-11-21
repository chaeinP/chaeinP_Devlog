---
title: TypeScript_tsconfig 옵션 정리
date: '2021-11-21'
tags: ['TypeScript']
draft: false
summary: 타입스크립트 컴파일 옵션 모음
---

> 이 포스트는 [타입스크립트 deep dive(원문)](https://basarat.gitbook.io/typescript/) 페이지를 참고하였습니다.
> ([한국어 번역본은 여기](https://radlohead.gitbook.io/typescript-deep-dive/))

## Compilation context

```sh
The compilation context is basically just a fancy term for grouping of the files that TypeScript will parse and analyze to determine what is valid and what isn't. Along with the information about which files, the compilation context contains information about which compiler options are in use. A great way to define this logical grouping (we also like to use the term project) is using a tsconfig.json file.
```

위 원문을 직역해보면,

> compilation context란 타입스크립트가 컴파일 할 수 있는지 없는지를 판단할 수 있는 파일들의 묶음을 의미하며, 각 파일들이 사용하는 컴파일 옵션을 포함한다. 이 context는 tsconfig.json 파일에 정의하는 것이 가장 바람직하다.

정도가 될 수 있겠다. 프로젝트 별 적합한 ts 컴파일 환경을 만들기 위해선 tsconfig.json 파일에 compilation context를 정의하는 방법을 알아야한다.

## tsconfig 스키마

> [ts.config 전체 스키마](http://json.schemastore.org/tsconfig) 확인하기

- 최상위 대표 프로퍼티 - compileOnSave - extends - conpilerOptions - files - include - exclude - references

아래의 명령어를 실행하면 tsconfig.json 파일을 생성할 수 있다.

```sh
npx tsc --init
```

### compileOnSave

```
compileOnSave: {
        description: "Enable Compile-on-Save for this project.",
        type: "boolean"
}
```

저장 할때마다 자동으로 compile 하는 옵션이다. 디폴트 값은 false이며 Visual Studio 2015 w/t TypeScript 1.8.4이상이거나 atop-typescript 플러그인이 설치되어있는 경우에만 true가 동작한다.

```json
"compileOnSave" : "true" / "false"
```

### extends

```
extends: {
        description: "Path to base configuration file to inherit from. Requires TypeScript version 2.1 or later.",
        type: "string"
}
```

tsconfig도 상속개념이 존재한다. extends 속성에 상속을 받아올 부모의 경로를 입력할 수 있다.

```json
"extends": "./base.json"
```

[tsconfig/bases 깃레포](https://github.com/tsconfig/bases)에 들어가면 ts에서 공식적으로 배포한 tsconfig 설정들을 확인할 수 있다. 필요한 tsconfig 파일을 가져와 내 프로젝트에 extends 옵션으로 쉽게 상속할 수 있다.

사용방법은 다음과 같다.

```
npm i --save-dev @tsconfig/base설정이름
```

위 명령어로 관련 tsconfig 설정을 설치하고 tsconfig.json 파일에 아래와 같이 상속한다.

```json
{
  "extends": "@tsconfig/base설정이름/tsconfig.json"
}
```

### files, include, exclude

기본적으로 files와 include 설정이 없으면 ts컴파일러는 프로젝트 내 모든 파일을 컴파일 하려고 한다. 만약 exclude로 제외된 파일들이 files 내에 포함되어있으면 files내에 정의되어있는 파일들을 기준으로 컴파일 된다.

```js
files: {
        description: "If no 'files' or 'include' property is present in a tsconfig.json, the compiler defaults to including all files in the containing directory and subdirectories except those specified by 'exclude'. When a 'files' property is specified, only those files and those specified by 'include' are included.",
        type: "array",
        uniqueItems: true,
        items: {
                type: "string"
        }
}

exclude: {
        description: "Specifies a list of files to be excluded from compilation. The 'exclude' property only affects the files included via the 'include' property and not the 'files' property. Glob patterns require TypeScript version 2.0 or later.",
        //include에 속한 파일들에는 영향을 미치지만 files 속성에 포함된 파일들에는 영향을 미치지 않는다.
        //Glob pattern이란 .gitignore와 같은 파일에서 사용되는 문법 패턴을 의미한다.
        type: "array",
        uniqueItems: true,
        items: {
                type: "string"
        }
}

include: {
        description: "Specifies a list of glob patterns that match files to be included in compilation. If no 'files' or 'include' property is present in a tsconfig.json, the compiler defaults to including all files in the containing directory and subdirectories except those specified by 'exclude'. Requires TypeScript version 2.0 or later.",
        type: "array",
        uniqueItems: true,
        items: {
                type: "string"
        }
}
```

- 세 설정이 없으면 모든 파일이 컴파일 된다.
- files는 상대 또는 절대 경로를 요소로 갖는 배열이다. exclude패턴과 겹치면 files를 기준으로 컴파일 한다.
- include와 exclude는 glob 패턴으로 작성해야 한다. 만약 \*와
