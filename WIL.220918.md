# 2022.09.18의 WIL

## 항해99 실전프로젝트 3주

<br>

### 실전프로젝트 3주
#### - 실전프로젝트 3주차!!
#### - 이번 주차는 프론트에서 요청하는 추가 데이터 및 오류가 생기는 부분을 수정, 작은 추가 기능들을 구현하였고, 마지막으로 refresh 토큰에 대해 공부해보았다.
#### - refresh 토큰에 대한 공부 시작..!
#### - 현재 프로젝트에는 JWT를 활용한 토큰이 현재 6시간으로 토큰 만료 설정을 해두었다.
#### - 하지만, 만약 보안이 뚫려 유저의 토큰이 탈취되어 서버가 유저를 구분하지 못할 뿐만 아니라, 유저의 정보가 노출될 수도 있는 문제가 발생한다.
#### - 그래서 access토큰과 refresh토큰을 따로 설정하고 access토큰의 유효 시간을 짧게 refresh토큰의 유효 시간을 길게 설정하고, access토큰이 만료될 시 db에 저장된 refresh토큰으로 유저가 맞는지 확인한 후, 유저가 맞다면 access토큰을 다시 발급해주어서 로그인을 시켜주는 과정으로 보안을 더 안전하게 하는 것이다.
#### - refresh토큰에 대해 이해를 한 후! 이제 어떻게 적용하는 것이 좋을지 여러 블로그들을 탐색하며 고민을 해보았다.
#### - 가장 먼저 db를 선택하는 것에 대해 제일 많은 고민을 했다..
#### - redis db에 refresh토큰을 저장하는 방법을 사용하여 구현한 정보가 많았다.
#### - 하지만, 현재까지 만든 우리 프로젝트는 db 2개를 가지고 갈 정도로 데이터가 많은 서비스가 아니라고 생각하였고, mysql db 한가지만 사용하면서 user 테이블에 refreshToken이라는 컬럼을 추가하여 db에 저장하는 방식을 선택하였다!
#### - 현재 실전프로젝트의 경우에는 프론트-백 서버를 연결하며 테스트를 진행하고 있기 때문에 혼자 연습해보던 코드에 먼저 연습을 해보기로 했다.
#### - 사용자 인증 미들웨어에 본격적으로 코드를 작성하기 전에 토큰 사용에 대한 경우의 수를 정리하였다.
```
1. access/refresh 토큰이 모두 사용 가능할 때,
2. aceess/refresh 토큰이 모두 만료되었을 때,
3. acccess 토큰은 사용가능하지만/refresh 토큰은 만료되었을 때,
4. access 토큰은 만료되었지만/refresh 토큰은 사용 가능할 때
```
#### - 위와 같이 경우의 수를 4가지로 정리할 수 있었다.
#### - 나와같이 user 테이블에 refreshToken을 컬럼으로 넣어 구현한 이전 기수의 코드를 참고하여 로직을 이해해보는 시간을 가지고, 이를 바탕으로 코드 작성을 시작하였다.
#### - access/refresh 토큰을 헤더에 담고 type과 value로 구분될 수 있도록 split을 통해 나눠주었다.
#### - 그리고 조건문을 걸어 필터링을 해주었고, 각 토큰의 유효성을 검증하여 생성해주었다.
#### - 그 후 위에서 작성했던 경우의 수를 코드로 작성해주었다.
#### -  그렇게 완성된 코드는 아래와 같다.
```
require('dotenv').config();
const jwt = require("jsonwebtoken");
const { User } = require("../models");


module.exports = (req, res, next) => {
  try {
    const { refreshToken, accessToken } = req.headers;
    if (!accessToken) {
      return res.status(401).json({ error: "accessToken이 없습니다." });
    }
    const accessAuthType = accessToken.split(" ")[0];
    const accessAuthToken = accessToken.split(" ")[1];
    const refreshAuthType = refreshToken.split(" ")[0];
    const refreshAuthToken = refreshToken.split(" ")[1];

    if (accessAuthType !== "Bearer" || refreshAuthType !== "Bearer") {
      return res.status(401).json({ error: "토큰 타입이 맞지 않습니다." });
    }

    if (
      accessAuthToken === null ||
      accessAuthToken === undefined ||
      !accessAuthToken ||
      refreshAuthToken === null ||
      refreshAuthToken === undefined ||
      !refreshAuthToken
    ) {
      return res.status(401).json({ error: "토큰이 유효하지 않습니다." });
    }

    let accessVerified = null;
    let refreshVerified = null;

    try {
      accessVerified = jwt.verify(accessAuthToken, process.env.SECRET_KEY);
    } catch (error) {
      accessVerified = null;
    }
    try {
      refreshVerified = jwt.verify(refreshAuthToken, process.env.SECRET_KEY);
    } catch (error) {
      refreshVerified = null;
    }

    try {
      //1.access토큰, refresh토큰 모두 사용 불가
      if (!accessVerified && !refreshVerified) {
        return res.status(401).json({ error: "로그인 후 사용 가능합니다." });
      }
      //2.access토큰은 만료되었지만 refresh토큰이 존재한다면 db에서 토큰을 비교하여 accessToken 발급
      if (!accessVerified && refreshVerified) {
        const existUser = User.findOne({
          where: { refreshToken: refreshAuthToken },
        });
        if (!existUser) {
          res.status(401).json({ error: "존재하지 않은 사용자입니다." });
        }
        // accessToken 발급
        const userId = existUser?.userId; //옵셔널 체이닝

        const newAccessToken = jwt.sign({ userId }, process.env.SECRET_KEY, {
          expiresIn: "1h",
        });

        return res.status(200).json({
          accessToken: newAccessToken,
          userId,
          msg: "acceess 토큰이 재발급 되었습니다.",
        });
      }

      //3.access토큰은 있지만, refresh토큰 사용 불가하다면 refreshToken 발급
      if (accessVerified && !refreshVerified) {
        const { userId } = accessVerified;

        // refreshToken 발급
        const newRefreshToken = jwt.sign({ userId }, process.env.SECRET_KEY, {
          expiresIn: "14d",
        });

        // refreshToken 발급 후 db에 저장
        User.update({ refreshToken: newRefreshToken }, { where: { userId } });

        const existUser = User.findOne({ where: { userId } });
        if (!existUser) {
          res.status(401).json({ error: "존재하지 않은 사용자입니다." });
        }

        return res.status(200).json({
          refreshToken: newRefreshToken,
          userId,
          msg: "refresh 토큰이 재발급 되었습니다.",
        });
      }

      if (accessVerified && refreshVerified) {
        const { userId } = accessVerified;

        const existUser = User.findOne({ where: { userId } });
        if (!existUser) {
          res.status(401).json({ error: "존재하지 않은 사용자입니다." });
        }
        res.locals.user = existUser;
        console.log("미들웨어 확인");

        next();
        return;
      }
    } catch (error) {
      return res.status(400).json({ error: "로그인 후 사용 가능합니다." });
    }
  } catch (error) {
    return res.status(400).json({ error: "error" });
  }
};

```
#### - redis를 사용하는 방법만을 생각했을 때는 우리 프로젝트에 어떻게 접목시킬까라는 것이 가장 큰 고민이었는데, 당장 활용가능하도록 현재 db에 저장하는 것으로 결정하고나서는 코드 작성하는 것은 큰 어려움없이 진행할 수 있었다.
#### - 하지만, 추후 우리 프로젝트도 db를 2가지를 사용하게 된다면! 현재 refresh토큰을 저장하여 비교해주는 db를 redis에 옮겨 메모리를 기억하고, 더 가볍게 사용할 수 있도록 변경해주는 것도 고려해봐야 할 것 같다!
#### `이렇게 차근차근히 공부해가면서 나를 더 성장시켜야겠다!!``