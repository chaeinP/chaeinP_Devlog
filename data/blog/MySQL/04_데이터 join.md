---
title: MySQL_INNER JOIN, OUTER JOIN
date: '2021-08-28'
tags: ['MySQL']
draft: false
summary: Foreign Key로 여러 테이블을 한번에 조회하는 INNER JOIN, OUTER JOIN
---

### Foreign Key

> Foreign Key는 두개의 테이블을 연결해주는 키로 다른 테이블의 특정 row를 식별하는 역할을 한다. 다른 테이블의 컬럼을 참조하는 테이블을 자식 테이블, 참조 당하는 테이블을 부모 테이블이라고 하며 부모 테이블의 컬럼을 참조한 자식 테이블의 컬럼을 Foreign Key 라고 한다.

Foreign Key 설정 예

```sql
ALTER TABLE stock
ADD INDEX fk_stock_item_idx (item_id ASC) VISIBLE;

ALTER TABLE stock
ADD CONSTRAINT fk_stock_item
    FOREIGN KEY (item_id)
    REFERENCES  item(id)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION;
```

**연계참조 무결성 제약 조건**

연계 참조 무결성 제약 조건을 사용하면 사용자가 기존 외래 키가 가리키는 키를 삭제 또는 업데이트하려고 할 때 데이터 무결성을 위해 에러를 발생시키거나 미리 설정해놓은 설정값에 따라 처리된다.

**1) ON DELETE NO ACTION**

다른 테이블에 있는 기존 행의 외래 키에서 참조하는 키가 있는 행을 삭제하려고 할 때 오류를 발생시키고 DELETE가 롤백되도록 지정합니다.

**2) ON UPDATE NO ACTION**

다른 테이블에 있는 기존 행의 외래 키에서 참조하는 키가 있는 행에서 키 값을 업데이트하려고 할 때 오류를 발생시키고 UPDATE가 롤백되도록 지정합니다.

> CASCADE는 키 값의 삭제 또는 업데이트를 허용하여 수정 내용이 있는 테이블로 역추적할 수 있는 외래 키 관계를 갖도록 정의된 테이블에서 연계되도록 합니다. timestamp 열이 있는 외래 키나 기본 키에 대해 CASCADE를 지정할 수 없습니다.

**3) ON DELETE CASCADE**

다른 테이블의 기존 행에 있는 외래 키가 참조하는 키가 있는 행을 삭제하려고 할 때 해당 외래 키가 들어 있는 모든 행도 삭제되도록 지정합니다. 대상 테이블에서도 연계 참조 작업이 정의되어 있다면 테이블에서 삭제된 행에 대해서도 지정된 연계 동작이 수행됩니다.

**4) ON UPDATE CASCADE**

다른 테이블의 기존 행에 있는 외래 키가 참조하는 키 값이 있는 행에서 키 값을 업데이트하려고 할 때 모든 외래 키 값도 키에 지정된 새 값으로 업데이트되도록 지정합니다. 대상 테이블에서도 연계 참조 작업이 정의되어 있다면 테이블에서 업데이트된 키 값에 대해서도 지정된 연계 동작이 수행됩니다.

### 결합연산 JOIN

> 결합연산은 테이블을 가로 방향으로 합치는 연산을 말한다. 결합연산은 같은 컬럼 값을 공유하는 컬럼 구조가 다른 테이블끼리의 연산을 의미한다.

- LEFT / RIGHT OUTER JOIN
- INNER JOIN

**1. LEFT / RIGTH OUTER JOIN**

LEFT OUTER JOIN은 왼쪽 테이블에 오른쪽 테이블을 조건에 따라 결합한 형태로 데이터를 출력한다. 왼쪽 테이블은 모든 컬럼의 값이 출력되지만 조건에 부합하지 않는 오른쪽 테이블의 컬럼값은 null로 나타난다.

```sql
SELECT
    item.id,
    item.name,
    stock.item_id,
    stock.inventory_count
FROM item LEFT OUTER JOIN stock
ON item.id = stock.item_id;
```

| id  | name    | item_id | inventory_count |
| --- | ------- | ------- | --------------- |
| 1   | 바지    | 1       | 300             |
| 2   | 순면 티 | 2       | 200             |
| 3   | 치마    | null    | null            |
| ..  | ...     | ..      | ...             |

RIGHT OUTER JOIN은 그 반대로 오른쪽 테이블에 조건에 맞는 왼쪽 테이블 컬럼 값을 결합한 형태로 출력한다.

```sql
SELECT
    item.id,
    item.name,
    stock.item_id,
    stock.inventory_count
FROM item Right OUTER JOIN stock
ON item.id = stock.item_id;
```

| id  | name    | item_id | inventory_count |
| --- | ------- | ------- | --------------- |
| 1   | 바지    | 1       | 300             |
| 2   | 순면 티 | 2       | 200             |
| 4   | 원피스  | 4       | 250             |
| ..  | ...     | ..      | ...             |

위와는 달리 null 값이 보이지 않는 이유는 현재 stock의 item_id값이 item의 id값을 참조하고 있는 foreign key이기 때문이다. stock의 item_id 값에는 item의 id 값 이외의 값은 들어올 수 없기 때문에 위 쿼리문의 조건에 부합하지 않는 row가 없다.

조인을 할 때에는 테이블에 Ailas 설정을 해주는 것이 좋다. 테이블 이름까지 모두 작성하면 쿼리문이 길어지기 때문이다.

```sql
SELECT
    i.id,
    i.name,
    s.item_id,
    s.inventory_count
FROM item AS i Right OUTER JOIN stock AS s
ON i.id = s.item_id;
```

**2. INNER JOIN**

INNER JOIN은 양쪽 테이블 모두 조건에 부합하는 row만 출력한다. 따라서 left/right outer join 과는 달리 기준이 되는 테이블이 없다. 집합에서 교집합에 해당한다.

```sql
SELECT
    item.id,
    item.name,
    stock.item_id,
    stock.inventory_count
FROM item INNER JOIN stock
ON item.id = stock.item_id;
```

만약 ON 뒤에 오는 조건절의 비교하는 컬럼 이름이 같으면 ON 대신 USING을 사용할 수 있다.

```sql
SELECT old.id, old.name, new.id, new.name
FROM item INNER JOIN item_new
USING (id)
# = ON item.id = item_new.id
```

JOIN은 세개이상의 테이블을 JOIN할 때도 사용할 수 있다.

```sql
SELECT
    i.name, i.id,
    r.item_id, r.star, r.comment, r.mem_id,
    m.id, m.email
FROM
    item AS i LEFT OUTER JOIN review AS r
        ON r.item_id = i.id #item 테이블과 review 테이블을 조인하고
    LEFT OUTER JOIN member AS m #조인된 테이블을 member 테이블과 한번 더 조인한다.
        ON r.mem_id = m.id
```

### 집합연산 UNION

> 집합연산은 컬럼 구조가 같은 테이블끼리 세로로 연산하는 것으로 합집합, 교집합, 차집합을 의미한다. MySQL에서는 합집합을 수행하는 UNION연산자만 지원한다. 결합연산인 JOIN으로도 집합연산을 수행할 수 있다.

UNION으로 합집합을 나타낼 때는 두 집합이 공통적으로 갖고 있는 원소는 중복을 제거하고 하나만 표시된다.
만약 자동으로 수행되는 중복 제거를 하지않고 합집합 연산을 하고 싶다면 UNION ALL 연산자를 사용하면 된다.

```sql
SELECT * FROM member_A
UNION
SELECT * FROM member_B
```

서로 다른 컬럼 구조를 가지고 있는 테이블이라도 공통적으로 갖고 있는 컬럼들만 조회할 경우엔 집합연산이 가능하다.
