## 데이터 읽기 (Read)
sequelize를 이용하여 sql 서버에서 데이터를 읽어오는 방법입니다. ```findAll()``` 함수를 이용하고 where 파라미터를 통한 필터링을 통해 원하는 데이터들을 받을 수 있습니다.

<br/>

### 라우트 설정
데이터를 조회할 땐, ```'/get/data'``` 의 경로로 읽을 예정입니다. 따라서 ```'/server/server.js'``` 파일에서 라우트를 연결해야 합니다.

```js
app.get('/get/data', (req, res) => {
    Teacher.findAll()
    .then(result => { res.send(result) })
    .catch(err => { throw err })
});
```

위 코드를 추가해줍니다. Teacher 테이블에 있는 모든 데이터를 불러오고, 성공적으로 불러왔다면 해당 결과(result)를 클라이언트로 전송합니다.

여기서! ```.then().catch()``` 를 보아하니, 결국 ```sequelize```도 ```Promise()``` 를 이용한 비동기 처리를 진행한다는걸 알 수 있겠군요. 

<br/>

### 클라이언드 - 데이터 요청
클라이언트는 서버에 데이터를 요청하고, 요청한 데이터를 받은 후 해당 데이터를 처리해야 하기 때문에 아래 구문을 추가해줍니다.

```jsx
const [list, setList] = useState([]);
const getData = async() => {
    const res = await axios.get('/proxy/5000/get/data');
    if (res.data[0] === undefined) {
        let cover = [];
        cover.push(res.data);
        return setList(cover);
    }
    setList(res.data);
};
```

* (주의) 리액트에서 화면에 표시되는 컴포넌트를 실시간 변경이 반영되도록 하기 위해서는 ```useState()``` 를 이용해야 합니다. 그래서 ```list``` 를 선언해주었고, ```getData()``` 의 마지막에 ```useState()``` 를 통해 선언한 ```setList()``` 로 데이터를 업데이트 해주었습니다.

<br/>

### 클라이언트 - 데이터 표시
서버로 요청하여 받은 데이터를 화면에 표시하는 간단한 페이지를 만들어봅시다. 리액트의 장점은, html 파일 안에 자바스크립트를 끼워넣어서 보다 간편하게 컴포넌트를 추가할 수 있습니다 ..! 바로 이렇게 말이죠

```jsx
<form method='POST' onSubmit={addData}>
    <input type='text' maxLength='10' onChange={(e) => setName(e.target.value)} />
    <input type='submit' value='Add' />
</form>
<p>{name}</p>
{
    list.length === 0?
    null : list.map((element, key) => {
        return (
            <div key={key} style={{display: 'flex'}}>
                <div style={{marginRight: '20px'}}>{element.id}</div> 
                <div>{element.name}</div>
            </div>
        )
    })
}
```

if문 같은 조건문을 사용할 수 없기에, 주로 삼항 연산자를 이용하여 분기를 나눠 다룰 수 있습니다. 위 코드는 ```list```에 데이터를 받아오고, 길이가 0 (받아온 데이터가 없을 경우) 아무것도 추가하지 않고, 데이터가 있을 경우 추가하는 코드입니다.

* (주의) ```.map()``` 을 이용하여 컴포넌트를 추가할 땐, ***항상 고유한 key값***을 가져야 합니다. 

<br/>

### 데이터 업데이트
화면 로드, 데이터 추가 시에도 서버에서 데이터를 받아 업데이트를 해줘야 합니다. 따라서 클라이언트에서 데이터를 받는 부분을 살짝 수정하면


```jsx
import React, { useState, useEffect } from 'react';
import axios from "axios";
import './App.css';


const App = () => {
  const [name, setName] = useState('none');
  const [list, setList] = useState([]);
  
  const addData = async(e) => {
    e.preventDefault();
    const res = await axios('/proxy/5000/add/data', {
      method: 'POST',
      data: {
        'name': name
      },
      headers: new Headers()
    });

    if (res.data) {
      setName("complete!");
      getData();
    }
  };

  const getData = async() => {
    const res = await axios.get('/proxy/5000/get/data');
    if (res.data[0] === undefined) {
      let cover = [];
      cover.push(res.data);
      return setList(cover);
    }
    setList(res.data);
  };

  useEffect(() => {
    getData();
  }, []);

  return (
    <div className="App">
      <form method='POST' onSubmit={addData}>
        <input type='text' maxLength='10' onChange={(e) => setName(e.target.value)} />
        <input type='submit' value='Add' />
      </form>
      <p>{name}</p>
      {
        list.length === 0?
        null : list.map((element, key) => {
          return (
            <div key={key} style={{display: 'flex'}}>
              <div style={{marginRight: '20px'}}>{element.id}</div> 
              <div>{element.name}</div>
            </div>
          )
        })
      }
    </div>
  );
}

export default App;
```

이렇게 ```useEffect()``` 를 이용하여 화면에 컴포넌트가 생성될 때 데이터를 읽어오고, ```addData()``` 의 마지막 부분에도 ```getData()``` 를 추가하여 데이터가 추가될 때에도 업데이트 되도록 수정하였습니다.

<br/>

### 쿼리문
위 내용과 동일한 쿼리문은 ??

> select * from teachers;

입니다. 

<br/>
<br/>
<br/>

## 특정 데이터 조회
전체 데이터가 아닌, 특정 데이터만 받고싶을 때는 ```where``` 을 사용하면 됩니다. 위에서 ```'/server/server.js'``` 에서 ```.findAll()``` 의 인자에 추가해줍니다.

<br/>

### where
앞서 연결한 라우트에서 ```where```을 추가해줍니다.

```js
app.get('/get/data', (req, res) => {
    Teacher.findAll({
        where: {
            name: '11111'
        }
    })
    .then(result => { res.send(result) })
    .catch(err => { throw err })
});
```

위 코드처럼 ```where```을 추가하면, ```name``` 이 **11111** 인 데이터들만 불러옵니다. ```where``` 은 오퍼레이터를 사용할 수 있습니다.

<br/>

### 오퍼레이터 사용해보기
해당하는 조건들을 OR 로 묶어 데이터를 찾습니다. 위 코드에서 오퍼레이터를 넣어보면

```js
app.get('/get/data', (req, res) => {
    Teacher.findAll({
        where: {
            [Op.or]: [{ id: 8 }, { name: '11111' }]
        }
    })
    .then(result => { res.send(result) })
    .catch(err => { throw err })
});
```

이렇게 수정하면 됩니다. ```Op.or``` 은 오퍼레이터 중 ```or``` 을 사용하겠다고 선언하는 것이고, 그 다음 나오는 배열은 ```or``` 로 묶일 조건들 입니다. 

오퍼레이터에는

> eq, ne, is, not, or, and, col, gt, gte, lt, lte, between, notBetween, all, in, notIn, like, notLike, startsWith, endsWith, substring, iLike, notILike, regexp, notRegexp, iRegexp, notIRegexp, any, match

등 매우 많은 종류가 있습니다. 공식 문서에 나온 예시는 

```js
const { Op } = require("sequelize");
Post.findAll({
  where: {
    [Op.and]: [{ a: 5 }, { b: 6 }],            // (a = 5) AND (b = 6)
    [Op.or]: [{ a: 5 }, { b: 6 }],             // (a = 5) OR (b = 6)
    someAttribute: {
      // Basics
      [Op.eq]: 3,                              // = 3
      [Op.ne]: 20,                             // != 20
      [Op.is]: null,                           // IS NULL
      [Op.not]: true,                          // IS NOT TRUE
      [Op.or]: [5, 6],                         // (someAttribute = 5) OR (someAttribute = 6)

      // Using dialect specific column identifiers (PG in the following example):
      [Op.col]: 'user.organization_id',        // = "user"."organization_id"

      // Number comparisons
      [Op.gt]: 6,                              // > 6
      [Op.gte]: 6,                             // >= 6
      [Op.lt]: 10,                             // < 10
      [Op.lte]: 10,                            // <= 10
      [Op.between]: [6, 10],                   // BETWEEN 6 AND 10
      [Op.notBetween]: [11, 15],               // NOT BETWEEN 11 AND 15

      // Other operators

      [Op.all]: sequelize.literal('SELECT 1'), // > ALL (SELECT 1)

      [Op.in]: [1, 2],                         // IN [1, 2]
      [Op.notIn]: [1, 2],                      // NOT IN [1, 2]

      [Op.like]: '%hat',                       // LIKE '%hat'
      [Op.notLike]: '%hat',                    // NOT LIKE '%hat'
      [Op.startsWith]: 'hat',                  // LIKE 'hat%'
      [Op.endsWith]: 'hat',                    // LIKE '%hat'
      [Op.substring]: 'hat',                   // LIKE '%hat%'
      [Op.iLike]: '%hat',                      // ILIKE '%hat' (case insensitive) (PG only)
      [Op.notILike]: '%hat',                   // NOT ILIKE '%hat'  (PG only)
      [Op.regexp]: '^[h|a|t]',                 // REGEXP/~ '^[h|a|t]' (MySQL/PG only)
      [Op.notRegexp]: '^[h|a|t]',              // NOT REGEXP/!~ '^[h|a|t]' (MySQL/PG only)
      [Op.iRegexp]: '^[h|a|t]',                // ~* '^[h|a|t]' (PG only)
      [Op.notIRegexp]: '^[h|a|t]',             // !~* '^[h|a|t]' (PG only)

      [Op.any]: [2, 3],                        // ANY (ARRAY[2, 3]::INTEGER[]) (PG only)
      [Op.match]: Sequelize.fn('to_tsquery', 'fat & rat') // match text search for strings 'fat' and 'rat' (PG only)

      // In Postgres, Op.like/Op.iLike/Op.notLike can be combined to Op.any:
      [Op.like]: { [Op.any]: ['cat', 'hat'] }  // LIKE ANY (ARRAY['cat', 'hat'])

      // There are more postgres-only range operators, see below
    }
  }
});
```

이렇게 예시가 있습니다. 필요할 때 찾아봐서 사용하면 될 것 같습니다.

<br/>

### 하나의 데이터만 받을 때
하나의 데이터를 받는 방법은 ```findOne()``` 을 사용하면 됩니다. 이 때 주의할 점은, 이 함수는 데이터를 **객체(Object)**의 형태로 보냅니다. 따라서 클라이언트에서 데이터를 받을 때, 배열과 관련된 함수를 사용하게 되면 오류가 날 수 있습니다.
(```findAll()``` 의 경우에는 데이터들을 배열의 형태로 전송합니다. 배열의 각 원소는 데이터 객체들이 됩니다.)

```js
app.get('/get/data', (req, res) => {
    Teacher.findOne({
        where : { id : 2 }
    })
    .then(result => { res.send(result) })
    .catch(err => { throw err })
});
```

위 코드는 아이디가 2인 데이터 하나만을 받는 코드입니다. ```findOne()``` 사용 시에는 ```where``` 을 반드시 같이 써야 합니다.

<br/>
<br/>
<br/>

### 출처
- [sequelize 공식문서](https://sequelize.org/docs/v6/core-concepts/model-querying-basics/)