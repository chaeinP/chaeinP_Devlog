---
title: Nodejs_node + TypeScript + sequelize
date: '2021-11-27'
tags: ['NODEJS', 'TYPESCRIPT']
draft: true
summary: node + TypeScript 환경에 sequelize 연결하기
---

ts/node 환경에서 sequelize를 사용하려면 먼저 `@types/node`, `@types/validator`가 설치되어있어야 한다.

## sequelize 세팅하기

- sequelize 인스턴스 생성

  sequelize를 사용하기 위해선 먼저 새로운 sequelize instance를 생성해야한다. 새로 생성한 instance에는 사용할 sql종류, DB와 관련된 정보 등을 파라미터로 넘겨주어야 한다. 나는 해당 정보를 config.ts파일에 미리 적어놨고 보안이 필요한 요소들은 환경변수를 사용했다. 이를 models/index.ts파일에 불러와 사용했다. 그리고 sql 서버에는 이름이 일치하는 database를 미리 생성해두었다.

```ts
import { Sequelize } from 'sequelize'
import { config } from '../config/config'

const env = 'development'
const { database, username, password, host, dialect } = config[env]
export const sequelize = new Sequelize(database!, username!, password, {
  host,
  dialect: 'mysql',
  dialectOptions: {
    charset: 'utf8mb4',
    dateStrings: true,
    typeCast: true,
  },
  timezone: '+09:00',
})
```

- sequelize 인스턴스 테스트

`.authenticate()`를 사용하면 생성된 인스턴스가 정상적으로 DB와 연결되는지를 확인할 수 있다. 이는 테스트용이기 때문에 배포시에는 없어도 정상 작동한다.

```ts
sequelize
  .authenticate()
  .then(() => {
    console.log('Connection has been established successfully.')
  })
  .catch((err: Error) => {
    console.log('Unable to connect to the database:', err)
  })
```

## model 만들기

- model 속성 타입 interface 만들기

테이블을 생성하기 위해서 model에 그 틀을 짜주어야한다. 타입스크립트에서 모델을 작성할 때에는 모델 속성들의 타입을 지정해주어야한다.

```ts
interface usersAttributes {
  id: number
  username: string
  image: string
  userStatus: string
}
```

- Optional 설정하기

공식문서를 보면 Optional을 다음과 같이 설명하고 있다.

```ts
import { Optional } from 'sequelize'

// These are all the attributes in the User model
interface UserAttributes {
  id: number
  name: string
  preferredName: string | null
}

// Some attributes are optional in `User.build` and `User.create` calls
interface UserCreationAttributes extends Optional<UserAttributes, 'id'> {}
```

Optional은 모델을 build하거나 create할때 반드시 포함시키지 않아도 되는 요소들을 명시하는 작업이다. sequelize에서 build는 해당 테이블에 들어갈 데이터 컬럼을 만드는것을 의미한다. crate는 build후 DB에 저장하는 것까지 포함한다. Optional의 의미는 만약 컬럼을 만들어 DB에 저장할 때 포함되지 않아도 되는 속성들을 따로 지정하라는 뜻이다. 예를들어 보통 id는 pk로 지정되어 autoIncrement, not null 속성이 부여된 경우가 많다. 이 경우 DB에 데이터 컬럼을 저장할 때 id 값을 빼고 전달해도 된다. DB 저장시에 자동으로 id값이 부여되기 때문이다. 만약 이 경우 ts 에서 id를 Optional로 명시해놓지 않으면 DB에 데이터를 저장하고자 할때 id가 빠졌다는 에러가 발생한다. sequelize의 모델 클래스는 interface 를 타입으로 갖기 때문에 interface에서 id를 id?(있을 수도 있고 없을 수도 있는 key라는 뜻)로 해놓지 않는 이상 interface에서 정의된 key가 하나라도 없으면 에러를 발생시킨다. 물론 interface에서 id를 id?로 표시한다면 굳이 Optional로 명시하지 않아도 된다. 하지만 Optional이 따로 있는 이유를 생각해보면 결국 id는 pk로 DB에서 절대 null값을 가질 수 없는 컬럼인데 interface자체에 id?를 작성하면 DB의 속성 규칙과 타입 선언이 어긋나므로 이를 Optional로 따로 명시화해 오해의 여지를 줄이고자 함이 아닐까 추측된다.

따라서 나도 id값을 Optional로 지정해주었다.

```ts
type usersOptionalAttributes = 'id'
// 더 추가하고 싶은 속성이 있다면 "id" | "username" | "image" 처럼 추가하면 된다.

export type usersCreationAttributes = Optional<usersAttributes, usersOptionalAttributes>
```

- 모델 클래스 만들기

```ts
import { Model } from 'sequelize'
export class Users
  extends Model<usersAttributes, usersCreationAttributes>
  implements usersAttributes {
  public readonly id!: number //strict모드에선 !를 붙여주어야한다. 만약 null이 허용되는 경우 number | null처럼 union타입으로 표현해야한다.
  public username!: string
  public image!: string
  public userStatus!: string
}
```

## sequelize와 모델 연결하기

- init함수 실행하기
  sequelize의 init함수를 사용해 DB 속성 값 설정을 추가하고 model에 적용할 sequelize 설정을 적어준다.

```ts
Users.init(
  {
    id: {
      allowNull: false,
      autoIncrement: true,
      type: DataTypes.INTEGER,
      primaryKey: true,
    },
    username: {
      type: DataTypes.STRING(45),
      allowNull: false,
    },
    image: {
      type: DataTypes.STRING(256),
      allowNull: false,
    },
    userStatus: {
      type: DataTypes.STRING(45),
      allowNull: false,
    },
  },
  {
    modelName: 'Users',
    sequelize,
    timestamps: true, //createdAt, deletedAt사용하겠다는 의미
    deletedAt: true, // deletedAt을 추가로 사용하겠다는 의미
    paranoid: true,
  }
)
```

## 마주친 에러

### timestamp 문제

모든 테이블은 속성값으로 createdAt, updatedAt, deletedAt을 가져 데이터 변경이 발생할 때마다 그 시간대를 기록하고자 했다. 서버에서 데이터 생성, 수정, 삭제시 일일히 해당 값을 넣을 수도 있지만 sequelize내장 기능을 활용해 자동을 timestamp가 찍힐 수 있도록 설정하였다. 문제는 해당 timestamp가 한국 시간 기준으로 기록되지 않는다는 것이었다. 문제의 원인은 mysql 서버의 설정 시간이 한국시간을 설정되어있지 않았기 때문이다. 한국 시간대에 맞는 서버 시간을 설정하기 위해 mysql 서버의 시간대를 재설정해주어야 했다.

- mysql 설정된 서버 시간 확인하기

```sql
mysql> select @@global.time_zone, @@session.time_zone;
+--------------------+---------------------+
| @@global.time_zone | @@session.time_zone |
+--------------------+---------------------+
| SYSTEM             | SYSTEM              |
+--------------------+---------------------+
1 row in set (0.00 sec)

```

- 서버시간 한국으로 수정하기
  서버시간을 asia/seoul로 수정하기 위해선 sql이 서버시간 테이블을 가지고 있어야 한다. 서버시간 세팅에는 [공식 문서](https://dev.mysql.com/doc/refman/8.0/en/time-zone-support.html)를 활용했다. 내가 사용한 방법은 다음과 같다. 먼저 [이 페이지](https://dev.mysql.com/downloads/timezones.html)에서 `timezone_2021e_posix_sql.zip`이 파일을 설치해주었고 해당 파일을 sql에서 실행시켜주었다.

```sql
mysql -u root -p mysql < timezone_posix.sql_절대_경로
```

- 서버시간 테이블 확인

```sh
mysql> SELECT COUNT(*) FROM mysql.time_zone_name;
+----------+
| COUNT(*) |
+----------+
|      595 |
+----------+
1 row in set (0.01 sec)
```

만약 위 단계에서 에러가 발생했다면 count는 0이 나온다.

- timezone 변경

```sh
mysql> set global time_zone='Asia/Seoul';

mysql> set time_zone='Asia/Seoul';

mysql> select @@global.time_zone, @@session.time_zone;
+--------------------+---------------------+
| @@global.time_zone | @@session.time_zone |
+--------------------+---------------------+
| Asia/Seoul         | Asia/Seoul          |
+--------------------+---------------------+
1 row in set (0.00 sec)
```

global time_zone과 그냥 session time_zone을 모두 'Asia/Seoul'로 변경하고 변경내용을 다시 확인해보면 SYSTEM 이었던 부분이 Asia/Seoul로 바뀌어있음을 확인할 수 있다.

- sequelize timestamp 설정 변경
  sql 타임존 설정을 변경하니 DB에 한국 시간을 기준으로 데이터가 저장되는 것을 확인할 수 있었다. 그러나 sequelize를 통해 데이터를 불러오니 다시 timestamp가 원하지 않는 형태로 들어왔다.

| sql에 저장된 형태   | sequelize로 불러왔을 때   |
| ------------------- | ------------------------- |
| 2021-11-27 16:12:53 | 2021-11-27T07:51:24.855Z, |

이를 해결하기위해 sequelize의 일부설정을 바꿔주어야했다. 새롭게 추가한 부분은 `dialectOptions`와 `timezone`이다.

dateStrings: DB의 날짜 타입을 자바스크립트 객체형태로 변환할지 문자열로 변환할지 여부 판단, 문자열로 변환하면 형태를 유지해서 가져올 수 있다.

typeCast: 자바스크립트 객체로 변환할지를 선택하는 옵션, true로 해야 date타입 외에 값들은 자바스크립트 객체 형태에 맞게 변환된다.

timezone: sequelize는 UTC를 기준으로 하기 때문에 한국 시간을 표현하기 위해선 여기 9시간을 더해야한다. 이를 timezone 설정으로 추가한다.

이렇게 설정을 하자 데이터 베이스에 있는 시간대로 불러올 수 있었다. 하지만 findOrCreate처럼 데이터 베이스 저장될 객체 데이터를 DB에 전달해서 다시 꺼내오는 경우가 아니라 생성시 바로 사용하는 경우에는 여전히 시간이 다르게 표시됐다... 이건 어떻게 해야할지 모르겠다...

```js
export const sequelize = new Sequelize(database!, username!, password, {
  host,
  dialect: "mysql", //그냥 dialect로 하면 에러남 왜인지 모르겠음
  dialectOptions: {
    dateStrings: true, // ! 데이터 로드시 문자열로 가져와서 형태 변경을 막음
    typeCast: true, // ! 위에서 가져온 데이터가 문자열이기 때문에 타임존을 역으로 계산하지 않음
  },
  timezone: "+09:00", //sequelize는 timezone 설정이 UTC기준이기 때문에 한국시간을 계산하려면 9시간을 더해야한다.
});
```

mysql에서 적용 가능한 dialectOptions는 [이 문서](https://github.com/mysqljs/mysql#connection-options)에서 확인할 수 있다.

참고자료

[sequelize 공식문서](https://sequelize.org/master/manual/typescript.html)
