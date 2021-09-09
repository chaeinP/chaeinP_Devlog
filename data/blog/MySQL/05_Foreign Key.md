---
title: MySQL_Foreign Key
date: '2021-08-28'
tags: ['MySQL']
draft: false
summary: 난 Foreign Key, 한 놈만 판다.
---

### FOREIGN KEY

> 한 테이블 내에서 다른 테이블의 특정 row를 식별하게 해주는 컬럼. Foreign Key는 주로 다른 테이블의 Primary Key를 참조한다.

Foreign Key가 있는 테이블을 '자식 테이블(child table)'이나 '참조하는 테이블(referencing table)'이라고 하고 Foreign Key에 의해 참조당하는 테이블을 '부모 테이블(parent table)', '참조당하는 테이블(referenced table)'이라고 한다.

Foreign key 는 두 테이블 간의 `참조 무결성`을 지키기 위해 사용된다.
**! 참조 무결성**

> 두 테이블 간 참조 관계가 있을 때 각 데이터 간에 유지되어야 하는 정확성과 일관성을 의미한다.

### - Foreign Key 설정

**테이블 생성 시 설정할 때**

```sql
CREATE TABLE student (
	id int NOT NULL AUTO_INCREMENT
    course_id INT DEFAULT NULL
    PRIMARY KEY(id)
    CONSTRAINT fk_student_table FOREIGN KEY (course_id)
    REFERNECES course(id)
    ON DELETE RESTRICT
    ON UPDATE RESTRICT
)
```

**테이블 생성 후 설정할 때**

```sql
ALTER TABLE 테이블 이름
ADD CONSTRAINT 제약사항이름 #제약사항이름은 생략가능하다. 이 부분을 생략하면 mysql에서 임의의 제약사항 이름을 부여한다.
	FOREIGN KEY (foreign key로 설정할 컬럼 이름)
    REFERENCES 참조할 테이블(참조할 컬럼)
    ON DELETE RESTRICT
    ON UPDATE RESTRICT;
    #관련 ON DELETE/UPDATE설정값은 4개가 있다. 아래에 설명 참조
```

### - ON DELETE / UPDATE 정책

#### 1. RESTRICT(NO ACTION)

> 자식이 참조하고 있는 로우가 하나라도 있는 **부모 테이블의 로우는 아예 삭제가 불가능**한다. 만약 부모 테이블의 로우를 삭제하고 싶다면 해당 로우를 참조하고 있는 **자식 테이블의 로우들을 먼저 다 삭제하고나서야 가능**하다. 부모 테이블을 수정하는 것도 불가능하다.

#### 2. CASCADE

> 부모 테이블의 로우를 삭제할 때 **참조하고 있던 자식 테이블의 로우들도 함꼐 삭제**된다. 부모 테이블의 로우를 수정할 때에도 참조하고 있던 테이블의 로우들이 자동으로 수정된다.

#### 3. SET NULL

> 부모 테이블의 로우를 삭제하면 **참조하고 있던 자식 테이블의 foreign key 컬럼의 값을 null로 갱신**한다. 만약 부모테이블의 로우를 수정하면 참조하고 있던 자식 테이블의 foreign key 컬럼 값이 null로 변경된다.

### - Foreign Key 확인

```sql
SHOW CREATE TABLE 테이블 이름
# SHOW CREATE TABLE을 사용하면 제약 사항을 확인할 수 있다.
```

### - Foreign Key 삭제

```sql
ALTER TABLE 테이블 이름
	DROP FOREIGN KEY 제약사항이름
```

### - 논리적 / 물리적 Foreign Key

개념상, 논리적으로 성립하는 Foreign Key를 논리적(Logical) Foreign Key라고 하고, DBMS 상에서 실제로 특정 컬럼을 Foreign Key로 설정해서 두 테이블 간의 참조 무결성을 보장할 수 있게 됐을 때, 그 컬럼을 물리적(Physical) Foreign Key라고 한다.

테이블을 설계하다보면 여러 논리적 Foreign Key 들이 생기지만 이를 실무에서 반드시 Foreign Key로 설정해야만 하는 것은 아니다. 여기엔 두가지 이유가 있다.

**첫째, 성능 문제**때문이다.

물리적 Foreign Key가 있는 자식 테이블의 경우에는 INSERT, UPDATE 문 등이 실행될 때 약간의 속도 저하가 발생할 가능성이 있다. 왜냐하면 INSERT, UPDATE 문이 실행될 때 혹시라도 참조 무결성을 깨뜨리는 변화가 발생하지는 않을지 추가적으로 검증해줘야 하기 때문이다. 데이터 무결성 보다 지금 당장의 성능 개선이 우선적이라면 굳이 Foreign Key를 설정하지 않기도 한다. 대신 정기적으로 데이터들을 확인해 참조 무결성을 어기는 데이터를 수정, 삭제한다.

**둘째, 레거시 데이터의 참조 무결성이 이미 깨져있어 이후에 Foriegn Key 설정이 무의미해지는 상황이 있기 때문**이다.

참조 무결성이 중요한 경우 참조 무결성을 어기는 레거시 데이터들을 모두 삭제 후 물리적 Foreign Key를 재설정할 수도 있지만 레거시 데이터 또한 기업의 중요한 자산일 시 데이터 삭제가 불가능한 경우도 있다. 이러한 이유로 굳이 논리적 Foreign Key를 물리적 Foreign Key로 설정하지 않을 수 있다.
