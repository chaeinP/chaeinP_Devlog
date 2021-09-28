---
title: Error Log_Sequelize findOrCreate 에러
date: '2021-09-28'
tags: ['Error_Log', 'Project']
draft: false
summary: utterence github OAUTH는 canonical url을 중심으로 작동한다.
---

> 이 포스트는 토이프로젝트 'signup/signin 간단 구현' 에서 발생한 에러를 기록한 것입니다.

### 문제상황

> Cannot convert undefined or null to object at create.then.catch.err (.../node_modules/sequelize/lib/model.js:2138)

회원가입 API 구현을 위해 sequelize의 findOrCreate로 기존 회원과 신규 회원 정보를 구분하여 DB USER 테이블에 추가하는 코드를 작성하였다. 신규 회원 정보를 클라이언트의 요청에 실어보내자 DB테이블에 데이터가 추가되긴 하였으나 위와 같은 에러 메세지가 발생해 의도한 성공 메세지가 클라이언트에 전송되지 않았다. (의도한대로 DB추가는 됐으나 에러 발생으로 이후 코드가 실행되지 않는 상황)

```js
// server/controllers/users/signup.js
const { User } = require('../../models')

module.exports = async (req, res) => {
  // 이미 등록된 이메일인지 확인하고 없으면 등록
  try {
    const [user, created] = await User.findOrCreate({
      where: { email: req.body.email },
      defaults: {
        password: req.body.password,
        username: req.body.username,
        mobile: req.body.mobile,
      },
    })

    //...생략
  } catch (err) {
    console.log(err)
  }
}
```

### 해결

> USER 테이블 정보를 담고있는 `User.js/models` 파일에 id 속성은 `autoIncrement:true` 를 추가하자 정상적으로 작동했다.

초기 작성한 User 테이블 모델은 다음과 같다. 그리고 모델을 완성한 뒤 이를 migrations에 저장했다.

```js
// models/User.js
const { Model } = require('sequelize')
module.exports = (sequelize, DataTypes) => {
  class User extends Model {
    static associate(models) {}
  }
  User.init(
    {
      id: {
        type: DataTypes.NUMBER,
        primaryKey: true,
      },
      email: DataTypes.STRING,
      password: DataTypes.STRING,
      username: DataTypes.STRING,
      mobile: DataTypes.STRING,
    },
    {
      sequelize,
      modelName: 'User',
    }
  )
  return User
}
```

모델을 작성할 땐 id의 autoIncrement 설정을 따로 설정해 주지 않았다. 디폴트 값으로 설정된다고 알고 있었기 때문

```js
// migrations/user.js
module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.createTable('Users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER,
      },
      ///생략
  down: async (queryInterface, Sequelize) => {
    await queryInterface.dropTable('Users')
  },
}
```

마이그레이션 파일에도 보면 모델에서 설정해주진 않았지만 id컬럼에 autoIncrement 속성이 추가된 것을 알 수 있다.

이리저리 몇시간에 걸쳐 검색을 해본 결과 stackoverflow에서 나와 똑같은 이슈를 해결한 사람의 답변을 찾을 수 있었다. 답변은 다음과 같다.

> after a lot of digging, I found that the problem was due to autoIncrement not being set in the model definition. So even while the database row got an ID (because the table was created with auto_increment), the row did not get that id when running create/save operations in Sequelize. Hard problem to find, I would suggest more intuitive error messages.

해석/요약해보면

> 테이블의 id column은 autoIncrement로 설정이 되어있지만 model에는 autoIncrement 설정이 되어 있지 않아 model을 거쳐 db로 들어온 데이터에 id 값이 없어서 오류가 발생한 것 (때문에 id는 autoIncrement되지 않고 null이나 undefined 값을 갖고 있었던 듯 하다.)

데이터 모델인 user.js에 `autoIncrement:true`를 설정하니 에러 없이 정상적으로 작동했다.

```js
// models/user.js
User.init(
    {
      id: {
        type: DataTypes.NUMBER,
        primaryKey: true,
        autoIncrement:true,
      },
    }
  } ///생략
)
```

참조

- [Sequelize FindOrCreate ERROR](https://github.com/sequelize/sequelize/issues/9003)
