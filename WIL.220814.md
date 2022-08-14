# 2022.08.14의 WIL

## CORS
> - 교차 출처 리소스 공유 (Cross-origin resource sharing)의 약자

> - 현재 브라우저로 접속중인 페이지에서 자바스크립트를 이용해 다른 도메인 또는 포트를 가진 주소로 요청을 하는 경우, 해당 리소스에 접근을 허용했는지 확인해 보안을 높이는 동작
<br>  -> 동일한 출처가 아닌 다른 출처에서 데이터를 주고 받는 것을 허용하는 정책

### 1. CORS 발생
> - 브라우저가 도메인, 포트 중 하나라도 다르다면, 리소스의 출처가 다르다고 판단하여 발생함.

### `만약! CORS 설정을 하지 않는다면!`
- 웹 브라우저에서 다른 출처와의 요청이 아무런 제약이 없다면, 누구나 서버에 접근할 수 있게 되고 이는 악의적인 사용으로 이어질 수 있음.
<br>


### 2. 해결 방법
#### 1) Access-Control-Allow-Origin 응답 헤더 세팅
- 서버측 응답에서 접근 권한을 주는 헤더를 추가하여 해결
```
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*"); // 모든 도메인
  res.header("Access-Control-Allow-Origin", "https://example.com"); // 특정 도메인
});
```

#### 2) cors 모듈 사용
> npm install cors 설치 후 아래의 방법 사용
- 모든 요청 허용
```
const cors = require("cors");
const app = express();

app.use(cors());
```
<br>

 - 특정 도메인 접근 허용
 ```
 const options = {
  origin: "http://example.com", // 접근 권한을 부여하는 도메인
  credentials: true, // 응답 헤더에 Access-Control-Allow-Credentials 추가
  optionsSuccessStatus: 200, // 응답 상태 200으로 설정
};

app.use(cors(options));
 ```
<br>

 - 특정 요청 접근 허용
 ```
 app.get("/example/:id", cors(), function (req, res, next) {
  res.json({ msg: "example" });
});
 ```
<br>

#### 3) webpack-dev-server proxy 기능
> 리액트 개발환경에서 서버쪽 코드를 수정하지 않고 해결하는 방법<br>
> - 중간의 프록시 서버로 인해 domain.com 서버에서는 같은 도메인에서 온 요청으로 인식함.
```
// 프록시 쓰지 않았을때
// localhost:8080(클라이언트 측) --X (CORS)--> domain.com (서버 측)

// 프록시를 설정 후
// localhost:8080(클라이언트 측) --O 프록시가 설정된 Webpack Dev Server--> domain.com (서버 측)

module.exports = {
  devServer: {
    proxy: {
      "/api": {
        target: "domain.com",
        changeOrigin: true,
      },
    },
  },
};
```
<br>

#### 4) package.json에 proxy값을 설정
> create-react-app 으로 생성한 프로젝트에서는, package.json 에 proxy 값을 설정하여 proxy 기능을 활성화
```
{
    //...
    "proxy": "http://localhost:4000"
}
```

<br>


---
> 이번주를 정리해보자.
### 노드 심화
#### - 항해99 5주_주특기 심화주차..
#### 주특기 마지막 주차가 지났다.
#### 이번 심화주차는 지금까지 모든 주차 중에 가장 힘들었던 것 같다.
#### 과제도 과제이지만, 팀에서 소통의 어려움이 많아 심적으로 너무 힘들었던 주차였다..
#### 금요일부터 목요일까지. 일요일을 포함한 7일 중 앞에 5일은 각자 개념을 이해하고 연습해보는 시간을 가지고 6일째 되는 날부터 함께 과제 진행에 대해 이야기하여 이틀동안 숙련주차 코드를 레이어드 아키텍쳐 패턴으로 적용해보았다.
#### 마지막에 몰아닥쳐 과제를 끝내기는 했지만, 그 과정이 너무 어려워서 힘이 들었던 것 같다.
#### 심화주차에서는 팀이라는 것에 대해 많이 생각해봤던 일주일이되었다.
<br>

#### - 항해99 6주_미니프로젝트
#### 주특기를 나눈 이후 프론트와 처음 함께하는 협업이여서 걱정이 되었는데!! 팀장까지 맡게 되어 시작부터 부담감이 생겼다...!!!!
#### 그래도 여러 의견을 들을 자신은 있어서 팀장에 대해 부담을 내려놓으려하였고, 팀원분들도 너무너무 좋아서 첫날부터 함께 으쌰으쌰 할 수 있었다ㅠ!!
#### 같은 주특기인 node 팀원들과 api도 함께 짜고, db도 같이 생성하고, 깃허브 연동도 함께 테스트해보면서 너무 즐겁게 시작하였고, 이번주 시작부터 너무 행복하다며 서로 힘내자고 의지를 다지기도 하였다.
#### 이제 주어진 업무도 완료하고, 추가적인 기능도 고민해보면서 함께 성장하는 주차가 되었으면 하는 바램이다.
`미니 프로젝트 너무 행복하다ㅠ!`