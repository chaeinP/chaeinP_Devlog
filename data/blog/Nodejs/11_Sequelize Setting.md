---
title: Node.js_TypeScript 환경에 Sequelize 세팅하기
date: '2021-01-11'
tags: ['Munetic', 'NODEJS', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(2)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## 💡ORM 이란?

Object Relational Mapping의 줄임말로 객체와 관계형 DB의 데이터를 자동으로 맵핑해주는 역할을 수행합니다. ORM을 사용하면 RDBMS의 데이터 구조와 node.js 코드 구조가 갖는 간극을 좁힐 수 있습니다.

ORM의 장점

- 개발자는 SQL 쿼리문 작성 없이 데이터를 조작할 수 있기 때문에 객체 지향적으로 프로그래밍하는데 집중할 수 있습니다.
- 디자인 패턴을 해치지 않고 생성한 모델 객체를 재사용할 수 있어 편리합니다.
- DB 모델을 객체로 관리할 수 있기 때문에 직관적이고 가독성 높은 코드를 작성할 수 있게 됩니다.

ORM의 단점

- 작성해야할 코드량이 많아집니다.
- sql 쿼리로는 간단하게 처리할 수 있는 작업을 ORM으로 수행할 시 굉장히 복잡해지는 경우가 발생합니다.
- 쿼리 성능 개선시 ORM을 위한 추가 작업이 필요할 수 있습니다.

[참고 자료][ orm이란](https://gmlwjd9405.github.io/2019/02/01/orm.html)

## 💡 Sequelize 란?

promise 기반 node.js ORM으로 `Postgres, MySQL, MariaDB, SQLite and Microsoft SQL Server`을 지원합니다. npm trend 기준 node.js ORM 중 가장 높은 점유율을 보이고 있습니다.

## 💡 Sequelize 선정 이유

Sequelize를 선정한 이유는 가장 큰 커뮤니티를 보유하고 있다는 점 때문입니다. 구할 수 있는 정보가 많고 원하는 정보를 찾기 수월하기 때문에 아직 공부중인 카뎃들이 사용하기에 적합한 툴이라고 판단하였습니다.

Sequelize외에 타입 스크립트 프로젝트에 최적화된 `typeORM`이라는 툴도 있습니다. 이 툴에 대해서는 프로젝트 시작 이후 알게되어 크게 고려하진 못했습니다. 궁금하신 분은 Sequelize와 typeORM을 둘다 사용해보고 그 차이를 비교해보시면 좋을 것 같습니다. 또한 sequelize-typescript라는 npm 모듈이 있습니다. typescript 환경에서 sequelize를 좀더 간편하게 쓸 수 있도록 지원하는 모듈인데 이 프로젝트에서는 사용하지 않았습니다.

## 💡 Sequelize 세팅 방법

### 0️⃣ 도커로 mariadb 띄우기

로컬에서 개발하는 환경을 기준으로 설명합니다. 현 프로젝트 배포가 도커 컴포즈로 이루어지기 때문에 도커 컴포즈로 프로젝트를 실행시킬 때에는 도커 컴포즈 내부에 설치된 DB 서버를 사용합니다.

- mariadb 이미지 다운로드

```
docker pull mariadb
```

- mariadb 컨테이너 실행

```
docker run --name mariadb -d -p 3306:3306 --restart=always -e MYSQL_ROOT_PASSWORD=root mariadb
```

`--name` : 실행시킬 컨테이너 이름을 지어줍니다.

`-d` : 백그라운드 실행을 의미합니다.

`-p host-port:container-port` : DB를 연결할 host-port와 continer-port를 설정합니다. host-port는 로컬 host에서 연결하는 port,
container-port는 컨테이너 내부에서 사용하는 port입니다. 로컬이나 컨테이너에서 이미 점유하고 있는 포트를 사용하면 충돌이나기 때문에 비어있는 포트 번호를 넣어주어야 합니다.

`--restart=always` : 도커가 실행되는 경우 항상 컨테이너를 실행하는 옵션 (선택)

`e MYSQL_ROOT_PASSWORD=비밀번호` : mariadb에 root권한으로 접속할 때 사용할 비밀번호를 생성합니다.

`mariadb` : 컨테이너 생성에 사용할 이미지 이름

- 도커 mariadb에 접속

```
docker exec -it 컨테이너이름 /bin/bash
```

```
//mariadb접속
mysql -u root -p루트비밀번호입력

//로컬에서 사용할 db생성
create database 데이터 베이스 이름;
```

[참고 자료][ 프로그래머 yd](https://7942yongdae.tistory.com/130)

### 1️⃣ DB 환경변수 설정

다음의 설명은 로컬 개발 시를 기준으로 합니다. 도커 컴포즈로 실행할 때에는 munetic 루트에 있는 .env_template 파일을 참고해 .env 파일을 생성해 사용합니다. 도커 컴포즈가 프로젝트를 빌드하고 컨테이너를 실행시키는 과정에서 환경변수를 부여하도록 설계되었습니다.

- munetic_express .env 파일 생성
  munetic_express 사용중인 환경변수는 다음과 같습니다. 상위 5개 요소가 DB와 관련된 환경 변수 입니다.

```sh
DB_HOST=#DB 호스트 주소를 의미 (ex.localhost)
DB_PORT=#DB 포트, 도커 컨테이너 실행할 때 넣었던 host-port를 적어주세요. (ex.3308)
DB_USERNAME=#DB에 접속할 사용자 이름을 의미합니다. (ex. root)
DB_PASSWORD=#컨테이서 생성 시 MYSQL_ROOT_PASSWORD로 입력했던 문자열을 입력합니다.
DB_NAME=#도커 mariadb에 성생해준 데이터베이스 이름을 입력합니다.
ACCESS_SECRET=
REFRESH_SECRET=
SERVER_HOST=
```

이렇게 입력한 환경 변수들은 `src/config/config.ts`에서 사용하기 편하게 재가공됩니다.

```ts
import * as dotenv from 'dotenv'
dotenv.config()

export const development = {
  host: process.env.DB_HOST as string,
  port: parseInt(process.env.DB_PORT!, 10),
  username: process.env.DB_USERNAME,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  dialect: 'mariadb',
  access_secret: process.env.ACCESS_SECRET,
  refresh_secret: process.env.REFRESH_SECRET,
  domain: process.env.SERVER_HOST,
}
```

### 2️⃣ maria db와 sequelize 연결

- models/index.ts 파일에 환경변수를 불러옵니다.

```ts
import { development } from '../config/config'

const { host, port, database, username, password } = development
```

- Sequelize 인스턴스를 생성합니다.

```ts
export const sequelize = new Sequelize(database!, username!, password, {
  host,
  port,
  dialect: 'mariadb',
  dialectOptions: {
    charset: 'utf8mb4', //문자 인코딩 방식 지정
    dateStrings: true, //date 형태를 'YYYY-MM-DD' 형태가 깨지지 않도록 하는 역할 수행
    typeCast: true,
  },
  timezone: '+09:00', //한국시간 설정을 위해 UTC+9시간을 더해줌
  define: {
    timestamps: true, //모든 테이블에 createdAt, updatedAt 자동 생성, 쿼리 수행시 자동 데이터 입력
    deletedAt: true, //모든 테이블에 deletedAt 자동 생성, 쿼리 수행시 자동 데이터 입력
    paranoid: true, //데이터 destroy()시 soft delete 방식 적용, 데이터가 실제로 삭제되지 않고 deletedAt에 기록 되도록 하는 역할 수행
  },
})
```

- express 실행 후 연결 확인

```ts
sequelize
  .authenticate()
  .then(() => console.log('db connected🚀')) //콘솔에서 이 문구가 뜨면 db연결이 문제없이 수행되었다는 뜻입니다.
  .catch((err) => {
    console.log(host)
    console.log('db connection failed🙀', err)
  })
```

### 3️⃣ 모델 생성

models/lesson.ts 를 기준으로 설명합니다.

- 컬럼 타입 지정

```ts
import { Sequelize, DataTypes, Model, Optional } from 'sequelize'

//레슨 테이블에 필요할 컬럼의 타입을 지정합니다.
export interface lessonAttributes {
  id: number
  tutor_id: number
  category_id: number
  title: string | null
  price: number | null
  location: string | null
  minute_per_lesson: number | null
  content: string | null
  createdAt: Date
  updatedAt: Date
  deletedAt: Date
}

//쿼리 작성 및 수행시 로우에 반드시 포함되지 않아도 되는 컬럼들을 Optional 로 지정합니다.
//nullable한 컬럼, 또는 sequelize에서 자동으로 생성해주는 컬럼 등이 이에 해당합니다.
export type lessonCreationAttributes = Optional<
  lessonAttributes,
  'title' | 'price' | 'location' | 'minute_per_lesson' | 'content'
>
```

- 모델 클래스를 생성합니다.

```ts
// models/Lesson.ts
export class Lesson //Lesson 클래스 생성
  extends Model<lessonAttributes, lessonCreationAttributes>
  //Sequelize에서 제공하는 Model클래스 상속
  implements lessonAttributes {
  // 타입 체크시 적용할 interface 지정
  // 컬럼 타입 지정
  public id!: number
  public tutor_id!: number
  public category_id!: number
  public title!: string
  public price!: number | null
  public location!: string | null
  public minute_per_lesson!: number | null
  public content!: string | null
  public readonly createdAt!: Date
  public readonly updatedAt!: Date
  public readonly deletedAt!: Date

  //모델 생성 함수
  static initModel(sequelize: Sequelize): typeof Lesson {
    return Lesson.init(
      {
        // 컬럼들의 세부 설정들을 정의합니다.
        id: {
          allowNull: false,
          autoIncrement: true,
          type: DataTypes.INTEGER,
          primaryKey: true,
        },
        //...  일부 생략
        content: {
          allowNull: true,
          type: DataTypes.STRING(8192),
        },
        createdAt: {
          field: 'createdAt', // sequelize에서 자동으로 생성하는 timestamp 컬럼들에 맵핑
          type: DataTypes.DATE,
        },
        updatedAt: {
          field: 'updatedAt',
          type: DataTypes.DATE,
        },
        deletedAt: {
          field: 'deletedAt',
          type: DataTypes.DATE,
        },
      },
      { tableName: 'Lesson', sequelize } // 테이블 이름 지정, 연결할 db 인스턴스 지정
    )
  }
}
```

### 4️⃣ 모델 연결

- models/index.ts 모델 생성 함수 만들기

```ts
// models/index.ts
export function Models() {
  Category.initModel(sequelize)
  User.initModel(sequelize)
  Lesson.initModel(sequelize)
  Admin.initModel(sequelize)

  //foreign key 관계 설정
  Category.hasMany(Lesson)
  Lesson.belongsTo(Category, {
    foreignKey: {
      name: 'category_id',
      allowNull: false,
    },
  })
  User.hasMany(Lesson)
  Lesson.belongsTo(User, {
    foreignKey: {
      name: 'tutor_id',
      allowNull: false,
    },
  })
  return sequelize
}
```

- app.ts에 모델 생성함수 실행하기

```ts
//app.ts
Models()
  .sync({ force: true }) //서버 실행시 모델이 계속 새로 생성됩니다. 배포 버전에서는 이 설정을 유지하면 안됩니다.
  .then(() => {
    app.emit('dbconnected')
    console.log('👍 Modeling Successed') // 모델이 성공적으로 생성되었다면 다음 메세지를 콘솔에서 확인할 수 있습니다.
  })
  .catch((err) => console.log(err, '🙀 Modeling Failed'))
```

## 추가 공유 사항

- 프로젝트 1팀에서는 migration 기능을 사용하지 않았습니다. 프로젝트 극 초반이라 db 모델에 작은 수정이 굉장히 잦았고 이를 일일히 migration으로 기록하는 것으로 얻을 수 있는 이점이 아직은 크지 않다고 판단했습니다.
- sequelize 설정으로 timestamp : true를 주어 timestamp 관련 컬럼을 자동생성하도록 하였습니다. 따라서 각 모델 클레스에 createdAt, updatedAt, deletedAt 컬럼을명시해주지 않아도 되었습니다. 그러나 이후 timestamp 컬럼들을 조회 조건으로 활용할 때 class내부에 해당 컬럼들이 정의되어있지 않다는 에러를 만났습니다. 이로 이해 각 클래스별로 timestamp컬럼들을 명시적으로 추가했고 sequelize timestamp설정으로 자동생성되는 컬럼들과 1:1로 필드를 연결해주었습니다.

## 짧은 회고

- ORM을 장점을 제대로 활용하려면 객체 지향적으로 코드를 작성해야합니다. 프로젝트를 진행하면서 객체 지향에 대해 깊게 고민하지 못한 것이, 코드로 풀어내지 못한 것이 아쉽습니다.
