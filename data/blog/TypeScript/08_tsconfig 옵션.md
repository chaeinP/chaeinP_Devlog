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

- 최상위 대표 프로퍼티
  - compileOnSave
  - extends
  - conpilerOptions
  - files
  - include
  - exclude
  - references

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
- files
  - 상대 또는 절대 경로를 요소로 갖는 배열이다.
  - exclude패턴과 상충되는 부분이 있으면 files를 기준으로 컴파일 한다.
- include
  - include는 glob 패턴으로 작성해야 한다.
  - include와 exclude내용이 상충되면 exclude를 기준으로 적용된다.
  - \*를 사용하면 .ts/.tsx/.d.ts만 include 된다. 만약 이 설정에서 js파일을 추가로 포함시키고 싶을 경우 allowJS라는 옵션자를 추가로 설정해주어야 한다.
- exclude
  - 설정하지 않으면 `node_modules, bower_components, jspm_packages, outDir` default로 제외한다.
  - outDir은 어떤 상황에서도 무조건 제외된다.

### compilerOptions : typeRoots와 types

```js
typeRoots: {
        description: "Specify multiple folders that act like `./node_modules/@types`.",
        type: "array",
        uniqueItems: true,
        items: {
        type: "string"
        },
        markdownDescription: "Specify multiple folders that act like `./node_modules/@types`. See more: https://www.typescriptlang.org/tsconfig#typeRoots"
},
types: {
        description: "Specify type package names to be included without being referenced in a source file.",
        type: "array",
        uniqueItems: true,
        items: {
        type: "string"
        },
        markdownDescription: "Specify type package names to be included without being referenced in a source file. See more: https://www.typescriptlang.org/tsconfig#types"
},
```

타입스크립트에서 기존 자바스크립트에서 사용하는 외부 라이브러리를 불러오면 에러가 발생한다. 이유는 해당 라이브러리에 타입캐스팅이 되어있지 않기 때문이다.

만약 react를 불러오려고 하면 해당 라이브러리의 타입을 읽을 수 없다는 에러가 뜬다.

```js
import React from 'react' //에러
```

이떄 `npm i --save-dev @types/react`로 react 타입 모듈을 설치해야한다. 설치이후에 node_modules를 보면 &types폴더 안에 react폴더에 react타입을 정의하는 d.ts파일이 생성되있는 것을 볼 수 있다.

만약 types 관련한 어떤 설정도 해주지 않으면 타입스크립트는 라이브러리 이름을 기준으로 node_modules/@types에서 해당 타입의 index.d.ts를 찾아 자동으로 적용한다.

모든 모듈이 @types로 시작하는 타입정의를 가지고 있는 것이 아니기 때문에 일부 모듈을 사용하기 위해선 직접 index.d.ts파일을 생성해야할 수도 있다. 이 경우 typeRoots에 해당 경로를 추가하거나 types에 해당 모듈 이름을 추가해 타입스크립트가 타입 정의를 찾아 적용할 수 있도록 해주어야 한다.

typeRoots에는 경로를 작성한다면 types에는 모듈의 이름을 작성한다.

- @types는 타입스크립트 2.0부터 사용이 가능해진 내장 type definition 시스템이다.
- types와 typeRoots 옵션은 동시에 함께 쓰지 않는다.

- typeRoots

  - typeRoots를 따로 설정하지 않으면 node_modules/@types라는 경로를 통해 타입 정의를 탐색한다.
  - typeRoots를 사용하면 해당 배열안에 있는 경로만을 탐색한다.
  - 경로는 tsconfig.json이 있는 곳에서부터 상대경로로 작성할 수 있다.
  - 의도한 경우가 아니라면 `node_modules/@types`를 빠뜨리지 않도록 주의한다.
  - include에 이미 포함된 경로는 추가하지 않아도 된다.

- types

  - 전역으로 포함시킬 모듈의 이름을 작성한다.
  - 타입스크립트는 node_modules/@types 또는 @types 경로에서 해당 모듈의 이름을 찾아 타입 정의를 적용한다.
  - 프로젝트 디렉토리에 해당 모듈 타입이 정의되어있어도 types에 모듈 이름을 작성하지 않으면 타입스크립트는 해당 타입 정의를 사용하지 않는다.
  - [] 빈배열은 이 시스템을 사용하지 않겠다는 의미이다.

### compilerOptions: target, lib

```js
target: {
        description: "Set the JavaScript language version for emitted JavaScript and include compatible library declarations.",
        type: "string",
        default: "ES3",
        anyOf: [
                {
                enum: ["ES3","ES5","ES6","ES2015","ES2016","ES2017","ES2018","ES2019","ES2020","ES2021","ESNext"]
                },
                {
                pattern: "^([Ee][Ss]([356]|(20(1[56789]|2[01]))|[Nn][Ee][Xx][Tt]))$"
                }
        ],
        markdownDescription: "Set the JavaScript language version for emitted JavaScript and include compatible library declarations. See more: https://www.typescriptlang.org/tsconfig#target"
},
```

- target

  - 빌드될 자바스크립트 버전을 선택할 수 있다.
  - 선택하지 않으면 ES3가 default
  - 브라우저 환경이라면 기본적으로 es5, node환경은 그 이상의 버전이 사용된다.

- lib
  - 기본으로 내장되어있는 type definition 라이브러리 중 어떤 것을 사용할 것인지를 설정
  - lib를 지정하지 않았을 때 디폴트 라이브러리
    - target이 es3일 때 : lib.d.ts
    - target이 es5일 때 : dom, es5, scripthost
    - target이 es6일 때 : dom, es6, dom.iterable, scripthost
  - 빈배열일 경우 definition이 필요한 내장 함수를 사용했을 때 에러가 발생한다.

> 프로젝트를 개발할 때에는 ECMA스크립트 버전에 대한 이해와 호환되는 환경에 대해서 이해하고 있어야 한다.
> [ecmascript 버전 호환 테이블](https://kangax.github.io/compat-table/es6/)

### compilerOptions : outDir, outFile, rootDir

```js
outFile: {
        description: "Specify a file that bundles all outputs into one JavaScript file. If `declaration` is true, also designates a file that bundles all .d.ts output.",
        type: "string",
        markdownDescription: "Specify a file that bundles all outputs into one JavaScript file. If `declaration` is true, also designates a file that bundles all .d.ts output. See more: https://www.typescriptlang.org/tsconfig#outFile"
},

outDir: {
        description: "Specify an output folder for all emitted files.",
        type: "string",
        markdownDescription: "Specify an output folder for all emitted files. See more: https://www.typescriptlang.org/tsconfig#outDir"
},

rootDir: {
        description: "Specify the root folder within your source files.",
        type: "string",
        markdownDescription: "Specify the root folder within your source files. See more: https://www.typescriptlang.org/tsconfig#rootDir"
},
```

- outFile

  - 컴파일시 여러 파일을 하나의 js파일로 묶는 번들러 역할을 수행
  - 단, 모듈이 None or 시스템 or amd 일 경우에만 가능하다.
  - 이 옵션은 CommonJS 모듈이나 ES6모듈에는 사용할 수 없다.

- outDir

  - 컴파일된 파일들이 위치할 디렉토리 지정

- rootDir
  - 컴파일할 파일들이 위치한 디렉토리 작성
  - rootDir를 설정하지 않으면 프로젝트 루트 기준 폴더 계층 그대로 outDir에 컴파일 된다.

### compileOptions : strict

strict는 true로 설정하고 개발을 진행하는 것이 좋다.

```js
strict: {
        description: "Enable all strict type checking options.",
        type: "boolean",
        default: false,
        markdownDescription: "Enable all strict type checking options. See more: https://www.typescriptlang.org/tsconfig#strict"
},
```

strict를 true로 설정하면 다음과 같은 옵션들이 작동한다.

- noImplicitAny

        타입 추론시 any가 발생하면 명시적으로 표시되지 않는 이상 에러를 발생시킨다. any타입은 타입 체커를 무력화하기 떄문에 사용을 지양해야한다. 따라서 이 옵션은 항상 켜주는것이 바람직하다.

- noImplicitThis

        this 표현식에 any가 타입 추론되는 경우 명시적으로 표시되지 않는 이상 에러를 발생시킨다.

        ```js
        function example(this: any, name: string, age: number){
                this.name = name;
                this.age = age;

                return this;
        }
        ```

        this는 예약어이기 때문에 이러한 문법은 typescript에서만 가능하다. this는 특정한 상황이라면 any가 아니라 구체적인 타입을 부여하기도 한다.

        class에서는 this의 타입을 명시하지 않아도 된다. class 자체가 타입으로 부여되기 때문이다.

        ```js
        class NoImplicitThisTestClass {
                private _name: string;
                private _age: number;

                constructor(name: string, age: number) {
                        this._name = name;
                        this._age = age;
                }

                public print(this) { // 여기서 this는 타입 명시를 해주지 않아도 된다. 타입스크립트에서 NoImplicitThisTestClass로 적절하게 추론해주기 때문
                        console.log(this._name, this._age);
                }
        }
        ```

- strictNullChecks

        이 설정을 체크하지 않으면 모든 타입에 Null과 undefined가 서브타입으로 부여되고 이는 많은 에러를 발생시킬 수 있다. 따라서 이 옵션을 켜주어야만 number는 number, string은 string을 의미한다. 그렇지 않으면 null or undefined값을 가질 수 있게 되고 이 경우 컴파일 과정에서 에러로 걸러지지않아 런타임에서 문제가 될 수 있다. (예외, undefined는 void에 할당 가능)

        이 옵션을 키고 null, undefined 값을 다른 타입과 동시에 가질 수 있게하려면 명시적으로 union타입으로 표시한다. 이 옵션이 켜져있어도 anysms null과 undefined를 갖는다. 정말로 null과 undefined를 가질 수 있는 경우는 union으로 표시하고 조건문으로 분기를 내 처리할 수 있도록 하는 것이 바람직하다.

- strictFunctionTypes

        인자타입이 반공변적인 것만 매개변수로 받을 수 있다.

- strictPropertyInitialization

        strictNullCheckOption과 반드시 함께 사용해야 한다. 클래스의 속성이 생성자에서 초기화되었는지를 확인하고 값이 할당되지 않았을 경우 에러를 발생시킨다.

        그러나 constructor에서 초기화를 하지 않고 비동기함수에서 할당을 진행하는 경우 변수명뒤에 !를 추가하면 constructor 내부에서 초기화가 이루어지지 않더라도 에러를 피할 수 있다.

        ```js
        class Person {
                private _name! : string; //!를 붙여 constructor에서 초기화되지 않아도 에러 감지를 피할 수 있음
                private _age! : number;

                public async initialize(name: string, age: number) {
                        this._name = name;
                        this._age = age;
                }

                public print() {
                        console.log(this._name, this._age);
                }
        }
        ```

- strictBindCallApply

        bind/call/apply를 사용할 때, 엄격하게 체크하도록 하는 옵션

- alwaysStrict

        자바스크립트 strict모드로 컴파일하고 strict모드 기준으로 에러를 감지하는 옵션
