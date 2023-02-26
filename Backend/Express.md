# React + Express

2023.02.22-24

<br/>
<br/>
<br/>

## Node.js, Express란 무엇인가 ??

Express.js 또는 익스프레스라고 불린다. Node.js 를 위한 웹 프레임워크 중 하나로, 사실상의 표준 서버 프레임워크로 불린다.

<br/>
<br/>
<br/>

## Node.js란 ??

- **서버사이드 자바스크립트**로 구글의 자바스크립트 엔진인 V8을 기반으로 구성된 일종의 소프트웨어 시스템이다.

- 2009년 유럽에서 만들어짐. 역사가 길진 않다.

- **이벤트 기반으로 개발** 가능하며, **Non-Blocking I/O 를 지원**하기 때문에 **비동기식 프로그래밍**이 가능하다. 이 때문에 I/O 부하가 심한 **대규모 서비스를 개발하는데 적합**하다고 할 수 있다.

- 짧은 시간에 대량의 클라이언트 요청을 처리하는 웹 앱은 적합하지만, CPU의 사용이 높게 필요한 앱의 경우 오히려 성능 저하가 발생할 수도 있음.

- 서버에서 Javascript를 동작하도록 하는 환경(플랫폼) 이기 떄문에 Node.js 자체로는 구현하는데 한계가 있다 !

  → 그래서 주로 웹 = 리액트 / 서버 = Node.js 이런 형식으로 개발한다.

  > (정리)

  1. 매우 빠른 고성능 서버 : 비동기 처리로 성능 증가
  2. 한가지 언어(자바스크립트) 만으로 개발 가능 : 서버-클라이언트 모두 가능
  3. 프론트엔드 개발자들이 직접 서버를 개발할 수 있다
  4. 광범위한 커뮤니티
  5. 탁월한 생산성 : 일반적으로 더 빠르게 개발 가능함

  > 

  > (주의사항)

  1. 싱글스레드 → 하나의 작업 자체가 시간이 많이 걸리면 전체 시스템의 성능이 낮아짐.
  2. 코드 가독성이 좋지 않음
  3. 실행해봐야 에러를 확인할 수 있음 (컴파일시 확인 불가)
  4. 서버사이드 컨셉과는 달라서 적응 시간이 필요 : 코드를 순차적으로 실행하는 것이 아닌, 비동기 방식으로 이벤트를 보내고 그 응답에 대한 이벤트가 오면 핸들러를 통해서 처리하는 방식이기 때문에, 기존 서버 프로그래밍 모델과는 많은 차이가 있다.

  > 

→ 간단하지만 많은 양의 처리를 요구하는 서버를 구축할 때 효율이 좋다.

<br/>
<br/>
<br/>

## Express.js 의 개념

- Express.js 는 Node.js를 위한 빠르고 간편한 웹 프레임워크 이다.
- 다양한 웹 프레임워크가 있지만 현재 가장 많이 사용되는 것이 익스프레스.

> Express.js는 Node.js의 핵심모듈 **HTTP 와 Connect 컴포넌트를 기반**으로 하는 웹프레임워크로, 그러한 컴포넌트를 미들웨어(middleWare)라고 하며, 설정보다는 관례(convention over configuration) 같은 프레임워크의 철학을 지탱하는 주춧돌에 해당합니다.

즉, Node.js 를 빠르고 쉽게 할 수 있도록 도와주는 역할을 한다. 이건 middleware 구조 때문에 가능한 것이다. 자바스크립트 코드로 작성된 다양한 기능의 미들웨어는 개발자가 필요한 것만 가지고 익스프레스를 결합해 사용할 수 있다.

: 질문) 그럼 약간 babel 과 같은 기능을 하는건가 ?? 필요한 기능들만 뽑아서 결합해 사용한다는 개념이 똑같아서

<br/>
<br/>
<br/>

## 개발 환경에서

블로그를 만들면서 nodemon 과 concurrently 를 설치한 이유

- nodemon : 개발시 변경사항을 실시간으로 업데이트 해주기 위해서

- concurrently : 리액트 서버와 노드 서버를 동시에 실행하기 위해서

  → 이거 없으면 노드 백그라운드에서 실행시키고 다시 리액트 실행시키고 종료시에도 둘 다 종료하는 불상사가 벌어짐

```bash
npm install nodemon --save
npm install concurrently --save
```

이후 package.json 에 추가적인 설정을 해준다.

```json
{
  "scripts": {
    "server": "cd server && nodemon server",
    "client": "cd client && npm start",
    "start": "concurrently --kill-others-on-fail \"npm run server\" \"npm run client\""
  },
  "dependencies": {
    "concurrently": "^7.6.0",
    "express": "^4.18.2",
    "nodemon": "^2.0.20"
  }
}
```

<br/>
<br/>
<br/>

## 설치

1. 프로젝트 폴더 생성

   : React 에서 제공하는 CRA (create-react-app) 을 이용해 클라이언트 생성

```bash
yarn create react-app react-express
```

1. 프로젝트 시작

   ```bash
   yarn add express
   ```

2. 프로젝트 터미널에서 express 라이브러리 설치

위 과정을 마무리 하면 프로젝트 폴더 안에 client와 server 폴더 두개가 생긴다. 다음으로 Express 를 사용하기 위한 기본구조 & 테스트 파일을 작성해본다. 리액트가 3000번 포트를 사용하므로 다른 포트를 사용하는데 일반적으로 5000번 포트를 주로 사용한다.

`server.js` 파일

```jsx
const express = require("express");
const app = express();
const test = require("./Router/test");

app.use("/", test);

const port = 5000; // Node.js server 가 작동할 포트
app.listen(port, () => console.log(`현재 포트 넘버는 ${port} 입니다`));
```

`test.js` 파일

```jsx
const express = require("express");
const router = express.Router();

router.get("/", (req, res) => {
    res.send("접속 성공");
});

module.exports = router;
```

최상단 폴더 (프로젝트 루트 위치) 에서 아래 명령어를 실행해서 제대로 실행되는지 확인

```bash
node ./server/server.js
```

이후 [localhost:5000](http://localhost:5000) 으로 들어가서 직접 확인해본다.
