---
title: Munetic_재사용되는 컴포넌트 사용법(Input, Table, Grid)
date: '2021-12-26'
tags: ['Munetic', 'React', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(8)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## 💡 Inputs

어드민 페이지와 비밀번호 변경 페이지에서 사용되고 있는 input 컴포넌트 들입니다. 재사용 및 props를 사용한 제한적인 커스터마이징이 가능합니다. 커스터마이징 가능한 부분들은 props로 값을 지정해주어야 합니다. 사용 예는 다음과 같습니다.

### 0️⃣ Inputs/CustomInput

<img width="217" alt="스크린샷 2022-01-12 오후 7 30 55" src="https://user-images.githubusercontent.com/50102773/149124064-c1c8b9c2-9284-41da-bee6-c9dda235644f.png">
<img width="216" alt="스크린샷 2022-01-12 오후 7 35 50" src="https://user-images.githubusercontent.com/50102773/149124277-74901448-8fcb-452c-b6fd-65580fdc3fa1.png">

```ts
import CustomInput from '../'

const [email, setEmail] = useState('')
;<CustomInput
  width="20rem" //input width값
  text="Email" //input 상단 텍스트
  fontSize="1.5rem" // font-size값
  name="Email" // label for, input id 값
  value={email} // input-value 값, 보통 state 변수
  onChangeEvent={(e) => {
    //changeEvent
    setEmail(e.target.value)
    setButtonText('중복')
  }}
  error={buttonText === '❌'} // input value가 invalid한 경우 true, valid한 경우 false
/>
```

### 1️⃣ Inputs/CustomPasswordInputs

<img width="183" alt="스크린샷 2022-01-12 오후 7 36 57" src="https://user-images.githubusercontent.com/50102773/149124477-5fae1f52-a398-46c6-a9c3-6d7d049bf1af.png">

```ts
import CustomPasswordInput from '../'

const [password, setPassword] = useState('');
const [showPassword, setShowPassword] = useState(false)
<CustomPasswordInput
  width="15rem" // input width 값
  fontSize="1.5rem" // input font-size 값
  showPassword={showPassword} // boolean
  clickEvent={() => setShowPassword(!showPassword)} // password 텍스트로 보여줄지에 대한 이벤트
  value={password} // input value값
  onChangeEvent={e => setPassword(e.target.value)} //onChange event
/>
```

### 2️⃣ Inputs/CustomSelect

<img width="179" alt="스크린샷 2022-01-12 오후 7 37 25" src="https://user-images.githubusercontent.com/50102773/149124562-85ac42f1-3118-40c9-ab4c-bfc830d85c15.png">

```ts
import CustomPasswordInput from '../'

const [auth, setAuth] = useState('Admin')
;<CustomSelect
  width="15rem" //width값
  fontSize="1.5rem" //font-size값
  value={auth} // value값
  items={['Admin', 'Owner']} // 리스트에 들어갈 요소들
  onChangeEvent={(
    e: SelectChangeEvent //changeEvent
  ) => setAuth(e.target.value)}
/>
```

## 💡 Table

유저, 어드민유저, 레슨 페이지에서 재사용되고 있는 Table 사용법입니다. 페이지 별 필요한 테이블 컬럼이 다르기 때문에 페이지 주소에 따라 헤드와 로우 컬럼값을 교체해 사용하고 있습니다.

<img width="600" alt="스크린샷 2022-01-12 오후 8 16 47" src="https://user-images.githubusercontent.com/50102773/149130412-3b0f9a5d-363b-4c50-a95f-c2eeb78de808.png">

### 0️⃣ Table/MUITable.ts

테이블 최상위 컴포넌트입니다. 테이블 모든 요소를 감싸고 있는 컨테이너 컴포넌트 입니다. MUITable을 사용하기 위해서는 다음의 props가 필요합니다.

```ts
export type TableProps = {
  page: number // 현재 페이지 number, 첫번째 페이지는 0부터 시작합니다.
  count: number // 총 row(데이터)의 수
  rows: [] // api로 받아온 데이터 리스트
  rowsPerPage: number // 한 페이지에 노출되는 row의 수
  handleChangePage: (event: React.MouseEvent<HTMLButtonElement> | null, newPage: number) => void // 페이지 전환 이벤트
  handleChangeRowsPerPage: (event: React.ChangeEvent<HTMLInputElement>) => void // 한 페이지에 보여줄 row 수 변경 이벤트
}
```

사용예는 다음과 같습니다.

```ts
export default function LessonListPage() {
  const [page, setPage] = useState<number>(0)
  const [rowsPerPage, setRowsPerPage] = useState<number>(10)
  const [rows, setRows] = useState<[]>([])
  const [count, setCount] = useState(0)

  /**
   * Page 전환
   */
  const handleChangePage = (event: React.MouseEvent<HTMLButtonElement> | null, newPage: number) => {
    setPage(newPage)
  }

  /**
   * 한 페이지에 노출하는 row수
   */
  const handleChangeRowsPerPage = (event: React.ChangeEvent<HTMLInputElement>) => {
    setRowsPerPage(parseInt(event.target.value, 10))
    setPage(0)
  }

  useEffect(() => {
    const limit = 10
    const offset = page * limit
    Api.getAllLessons(offset, limit).then(({ data }: any) => {
      setRows(data.data.rows)
      setCount(parseInt(data.data.count, 10))
    })
  }, [page])

  return (
    <MUITable
      page={page}
      count={count}
      rows={rows}
      rowsPerPage={rowsPerPage}
      handleChangePage={handleChangePage}
      handleChangeRowsPerPage={handleChangeRowsPerPage}
    />
  )
}
```

MUITable 내부에는 크게 3개의 컴포넌트가 존재합니다.

- MUITableToolbar : 테이블 타이틀과 각 로우 선택시 선택 개수를 표시하는 툴바
- MUITableHead : 컬럼과 정렬 아이콘이 있는 테이블 헤드
- MUITableRow : 데이터를 row로 보여주는 테이블 바디

위 3가지 컴포넌트들은 url 경로에 따라 커스터마이징해 사용하고 있습니다.

### 1️⃣ Table/MUITableToolbar.tsx

![스크린샷 2022-01-13 오전 12 03 18](https://user-images.githubusercontent.com/50102773/149165728-e91b7c17-9cfb-47a5-833e-7d146ac30c5d.png)

상단에 Table title을 경로에 따라 다른 텍스트로 보여줄 수 있도록 설계되어있습니다. 아래의 예시를 참고해주세요.

```ts
// Table/MUITableToolbar.tsx line 44~54
<Typography sx={{ flex: '1 1 100%' }} variant="h4" id="tableTitle" component="div">
  {path === '/users' && 'App User'}
  {path === '/admin_users' && 'Admin User'}
  {(path === '/lessons' || path === `/users/${info!.id}`) && 'Lesson'}
</Typography>
```

### 2️⃣ Table/MUITableHead.tsx

![스크린샷 2022-01-13 오전 12 03 23](https://user-images.githubusercontent.com/50102773/149165725-4b86b822-70a7-40fa-abd6-773a780080fa.png)

MUITableHead는 테이블의 컬럼을 표시합니다. 따라서 표시할 컬럼 정보가 담긴 headCells이 필요합니다. 원하는 컬럼이 담긴 headCells을 만들고 url경로에 따라 노출할 headCells 연결해주었습니다. 헤드 정렬 아이콘이 존재하지만 정렬 기능이 부여되어있지는 않습니다.

```tsx
export default function MUITableHead(){
 let headCells:
     | readonly UserHeadCell[]
     | readonly AdminUserHeadCell[]
     | readonly LessonHeadCell[];
   headCells = [];

   if (path === '/users') headCells = userHeadCells;
   if (path === '/admin_users') headCells = adminUserHeadCells;
   if (path === '/lessons' || path === `/users/${info!.id}`)
     headCells = lessonHeadCells;

 ...중략

  return (
         ...중략
        {headCells.map(headCell => (
          <TableCell
            key={headCell.id}
            align={headCell.numeric ? 'center' : 'left'}
            padding={headCell.disablePadding ? 'none' : 'normal'}
            sx={{ fontSize: '1.25rem', fontWeight: 700 }}
          >
            {headCell.label}
            <TableSortLabel direction="asc" /> {/* "asc" , "desc" */}
          </TableCell>
        ))}
        ...중략
)
}
```

headCells은 다음과 같은 형태로 구성되어있습니다. 테이블 사용시에 따라 용도에 맞게 파일을 생성하고 MUITableHead.tsx 에 작성한 headCells리스트를 불러와 경로에 따라 연결해주면 됩니다.

```tsx
//Table/User/userHeadCells.ts

// 컬럼들의 타입
export interface UserData {
  id: number;
  name: string;
  login_id: string;
  type: string;
  lastLogin: string;
  createdAt: string;
  deletedAt: string;
}

// TableCell props 타입
export interface UserHeadCell {
  disablePadding: boolean;
  id: keyof UserData;
  label: string;
  numeric: boolean;
}

export const userHeadCells: readonly UserHeadCell[] = [
  {
    id: 'id', //row의 key값이 됩니다.
    numeric: false, //align style을 결정합니다 false는 left, true는 center를 의미
    disablePadding: true, //패딩 사용 여부
    label: 'No.', //화면에 컬럼으로 표시될 텍스트
  },
  {
    id: 'login_id',
    numeric: false,
    disablePadding: false,
    label: '아이디',
  },
...
}
```

### 3️⃣ Table/MUITableRow.tsx

![스크린샷 2022-01-13 오전 12 03 28](https://user-images.githubusercontent.com/50102773/149165718-f44909e3-d903-4c97-829b-645b237e5e02.png)

MUITableRow 파일에서는 한 row에 보여줄 데이터의 종류와 각 row를 클릭했을 때 해당하는 상세 정보 페이지로 이동하는 경로를 커스터마이징 할 수 있습니다. 데이터는 <\*\*TableCell>이라는 새로운 컴포넌트를 생성해 경로별로 연결해주었습니다.

```ts
// Table/MUITableRow.tsx
export default function MUITableRow() {
  //line 59 ~ 62
  {
    path === '/users' && <UserTableCell row={row} />
  }
  {
    path === '/admin_users' && <AdminUserTableCell row={row} />
  }
  {
    ;(path === '/lessons' || path === `/users/${info!.id}`) && <LessonTableCell row={row} />
  }
}
```

연결된 TableCell의 형태는 다음과 같습니다. 아래와 같이 필요한 정보를 담은 TableCell파일을 만들어 MUITableRow에 연결합니다.

```ts
//Table/User/UserTableCell.tsx

export default function UserTableCell({ row }: any) {
  return (
    <>
      <TableCell align="left" sx={{ fontSize: '1.25rem' }} padding="none">
        {row.login_id}
      </TableCell>
      <TableCell sx={{ fontSize: '1.25rem' }}>{row.name}</TableCell>
      <TableCell align="left" sx={{ fontSize: '1.25rem' }}>
        {row.type}
      </TableCell>
      <TableCell align="left" sx={{ fontSize: '1.25rem' }}>
        {row.lastLogin}
      </TableCell>
      <TableCell align="left" sx={{ fontSize: '1.25rem' }}>
        {row.createdAt}
      </TableCell>
      <TableCell align="left" sx={{ fontSize: '1.25rem' }}>
        {row.deletedAt}
      </TableCell>
    </>
  )
}
```

각 로우는 클릭하면 해당하는 상세페이지로 이동하는 moveInfoPage이벤트가 부여되어 있습니다. 이 이벤트 또한 테이블이 렌더되는 경로에 따라 다른 경로로 이동하도록 설계하였습니다.

```tsx
// Table/MUITableRow.tsx
export default function MUITableRow() {
  const moveInfoPage = () => {
    if (path === '/users') navigate(`/users/${row.id}`)
    if (path === '/admin_users') navigate(`/admin_users/${row.id}`)
    if (path.slice(0, 7) === `/users/`) navigate(`/lessons/${row.id}`)
    if (path === '/lessons') navigate(`/lessons/${row.id}`)
  }
}
```

## 💡 grid

그리드는 users/:id, admin_users/:id, lessons/:id 에서 사용되고 있습니다.
<img width="600" alt="스크린샷 2022-01-12 오후 8 15 10" src="https://user-images.githubusercontent.com/50102773/149130133-ed3486f5-6bdb-4682-af89-cd88807f7126.png">

### 0️⃣ Info/InfoGird.tsx

그리드를 담고 있는 컨테이너입니다. 경로에 따라 다른 그리드를 렌더하도록 설계되어 있습니다.

```tsx
export default function InfoGrid() {
  const path = useLocation().pathname
  const info = useInfo() as any

  return (
    <InfoContainer>
      {path === `/users/${info!.id}` && <UserGrid />}
      {path === `/admin_users/${info!.id}` && <UserGrid />}
      {path === `/lessons/${info!.id}` && <LessonGrid />}
    </InfoContainer>
  )
}
```

### 1️⃣ Info/**/**Grid.tsx

material-ui에서 제공하는 그리드는 다음과 같이 사용할 수 있습니다. Grid contianer의 spacing은 각 그리드 카드 간의 간격을 의미합니다. Grid width는 12기준입니다. 아래와 같이 그리드를 3, 9로 나누었다면 3:9의 비율로 화면에 렌더됩니다. sx의 mb는 margin-bottom을 의미합니다. Grid내부의 Item은 common폴더의 공통 스타일 컴포넌트 입니다. 그리드 카드 안의 컨텐츠를 감싸는 컨테이너 컴포넌트 입니다.

```tsx
//ex
<Grid container spacing={2} sx={{ flexGrow: 1 }}>
 <Grid item xs={3} sx={{ mb: 3 }}>
        <Item>
          <OverView />
        </Item>
 </Grid>
 <Grid item xs={9} sx={{ mb: 3 }}>
        <Item>
          <UserInfo />
        </Item>
 </Grid>
<Grid container spacing={2} sx={{ flexGrow: 1 }}>
```

### 2️⃣ Info/common/\*\*.tsx

그리드 컴포넌트에서 자주쓰는 스타일 컴포넌트를 모아놓은 폴더입니다.

- `<Itme/>` 은 그리드 콘텐츠를 감싸는 컨테이너 입니다.

- `<Title />` 각 컴포넌트의 타이틀 요소의 스타일 컴포넌트입니다.

![스크린샷 2022-01-13 오전 12 21 21](https://user-images.githubusercontent.com/50102773/149168988-8cb00098-9fbe-4d49-945d-aba422fa5cc0.png)

- `<TextFields/>` 는 두 열을 감싸는 텍스트 필드 컨테이너이며 `<TextField>`는 왼쪽 텍스트, `<TextField_>`는 오른쪽 텍스트를 의미합니다.
  ![스크린샷 2022-01-13 오전 12 21 37](https://user-images.githubusercontent.com/50102773/149168978-e39fcac5-fdef-4d83-af88-26e9ac01ceb7.png)

이상 끝!
