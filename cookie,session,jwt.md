## 쿠키, 세션, JWT에 대해서 알아보자

- 이러한 쿠키,세션,JWT가 왜 필요할까?
- `HTTP`는 `Stateless`이기 때문이다.
- 상태값을 저장하지 않는다. 내가 서버에 30을 보내줬는데, 다른 페이지로 넘어가면 30을 기억하지 못한다. 어딘가에 저장을 하고, 그걸 안전하고, 정확하게 확인하기 위해서는 쿠키,세션,jwt들이 필요하다.

### 쿠키

- 정의
  - 쿠키란 클라이언트가 어떤 웹 사이트를 방문할때, 그 사이트가 사용하는 서버를 통해 클라이언트 브라우저에 설치되는 정보를 말한다.
- 특징
  - 쿠키는 key,value로 이루어진 데이터이다.
  - 유효시간을 정할 수 있고, 브라우저를 꺼도 쿠키가 유지된다.
- 동작 방식

  - 1. 클라이언트가 로그인 요청
  - 2. 서버에서 쿠키 생성 후 클라이언트로 전달
  - 3. 클라이언트가 서버로 요청을 보낼 때 쿠키 전송
  - 4. 쿠키를 이용해 유저 인증을 진행

- 망상을 통한 단점찾기
  - http 요청을 누군가가 패킷을 가로채서 본다고 한다면?
  - 쿠키에 내 주민번호를 기억하기 위해 넣어놨는데.. 누가 훔쳐본다면?
- 어떻게 하란건가? (해결방법)

  - 민감한 정보를 저장하지 마라. 장바구니 or 자동로그인 여부 등을 저장하면 된다. 이거 훔쳐도 쓸 곳 없다..

- 브라우저가 어떤 요청을 보낼때 쿠키는 자동으로 set해서 보내주지만, jwt를 사용할때에는 우리가 bearer token을 직접 추가해줘야한다.
  - 이 방식에는 하자가 있다. CSRF attack에 취약하다.
  - 만약 www.naver.com은 인증된 유저가 비밀번호를 www.naver.com/changepassword를 유저이름이나, 예전 비밀번호 없이 POST요청으로 바꿀 수 있다고 해보자.
  - 이 www.naver.com에 방문하면 changepassword로 POST를 하는 페이지를 불러온다고 해보자. 내 브라우저는 authentication cookie를 attach할거고, attcker가 내 비밀번호를 바꿀 수 있게 된다.
  - (내 브라우저 쿠키가 자동으로 들어가니까, 지 멋대로 비밀번호를 나를 인증하고 비번을 바꿀 수 있다.)
  - 물론 same-origin-policy를 통해서, 다른 domain에는 cookie를 보내지 않게 할 수 있다. 하지만 (5년전에도 bearer token을 더 많이 쓴다.)
- 브라우저가 아닌 어플리케이션에서 좋지 않다. 모바일이나 태블릿 어플에서 API를 사용하기 힘들다.

### 세션

- 특징
  - 세션은 쿠키를 기반으로 하지만, 클라이언트에 저장하는것과 달리 `서버에 저장`한다.
  - 서버에서는 클라이언트를 구분해야한다. 따라서 세션ID를 클라이언트마다 부여해서 구분한다.
  - 클라이언트가 종료되기 전까지 유지한다.
  - 쿠키보다는 보안이 좋다. 왜? 내가 안가지고 있고, 보안이 더 좋은 서버에 저장을 하기 때문에. (일반적으로 서버가 더 보안이 좋다는 얘기임.. 반박 금지)
- 동작 방식
  - 서버에서는 클라이언트에게 세션ID를 부여하고(JSESSIONID) 세션 저장소에 저장하고 클라이언트에 발급
  - 클라이언트는 서버에서 발급받은 세션ID를 쿠키에 저장하고, 보낼때마다 쿠키를 보낸다. (자동저장 된다고 했었습니다)
  - 서버는 쿠키에 담겨있는 세션 ID와 세션 저장소에 있는 정보와 비교하고, 데이터를 가져옵니다.
- 망상을 통한 단점 찾기

  - `세션 하이재킹`을 당할 수 있다고 한다.
    - 세션 하이재킹이란? 로그인 상태를 가로채는 방법이 있다고 한다. TCP를 통한 컴퓨터간의 연결을 할 때 sequence number를 이용해서 SYN, ACK를 보내면서 연결을 한다. 근데 이걸 seq num을 위조해서 그 사이에 끼어들어서 (클라이언트 | 공격자 | 서버) 이런 구조를 갖게 하는것이다.
  - 세션 스토리지가 필요하므로, 사용자가 많아진다는것은? -> 용량의 증가를 의미한다. 배달의민족에 회원가입자 1000만명이 넘을텐데, 일단 이 사람들을 구별하기 위해서, 1000만개 이상의 row를 만들어야 한다. 거기다가 확인까지 해야한다. 제길;; `서버는 부하가 많이 걸리게 된다`.(당연)

### 토큰 인증방식

- JWT란?

  - JWT는 `Json Web Token`의 약자이다. 인증에 필요한 정보들을 암호화 한 토큰이다
  - `Authorization: <type> <credentials>`
  - `세션`처럼 토큰을 쿠키에 담아서 보낼 수 있고, HTTP Header에 "Authorization : Bearer Token~~" 으로 넣어서 보낼수도 있다.

- Type에 해당하는 인증 Type
  - Bearer
    - JWT 혹은 OAuth에 대한 토큰을 사용한다는 인증타입이다. (RFC 6750)
- 토큰의 구성요소

  - `Header, Payload`는 Decode를 아무나 할 수 있으므로, 개인정보를 함부로 담으면 안된다.
  - Header

    - 암호화 알고리즘과, Token type을 입력한다.
    - ```js
      {
        "alg" : "HS256",
        "typ" : "JWT",
      }
      ```

  - Payload

    - Data를 의미한다
    - ```js
      {
        "name" : "hkm",
        "auth: : "superUser",
      }
      ```

  - Verify Signature
  - 이걸 통해서 나는 서버가 보내준게 맞다고 증명을 할 수 있다. Secret Key를 입력하는데, 이건 서버만 알 수 있다. 모르면 복호화가 불가능 하기에 토큰을 변조해도 통하지 않는다.

- 동작 방식
  - 클라이언트가 로그인 요청
  - 서버에서 유저의 ID와 다른 인증 정보들을 포함해 Payload에 담는다
  - JWT의 유효기간 및 옵션을 설정한다
  - Secret Key를 정하고 토큰을 발급한다
  - 발급된 토큰은 클라이언트의 쿠키나 로컬스토리지에 저장해서 요청을 보낼때마다 같이 보낸다. (헤더에 넣든지.. 쿠키에 담던지..)
  - 서버는 토큰을 Secret Key로 복호화해서 검증하는 과정을 거친다.
  - 검증이 완료되면 대응하는 데이터를 보내준다.
- 장점
  - 스토리지 얘기가 없다. 즉 세션같이 별도의 저장소가 필요없다
  - 토큰을 발급하고 클라이언트에 전송 후 검증하는 과정만 있으면 된다
  - 모바일 어플에서도 잘 작동합니다.
  - 토큰 기반으로 다른 로그인 시스템에 접근 및 권한공유 가능
  - 확장성 우수
  - 클라이언트 인증정보를 저장하는 세션과는 달리 서버가 stateless 가능.
- 단점

  - JWT는 삭제가 안된다.. 세션은 뺏기면 그냥 그 ID를 지우면 된다. 하지만 JWT는 토큰 만료시간까지 프리패스다.. 이건 어떻게 막을 수 있을까?
  - 토큰의 길이가 굉장히 깁니다. 인증요청이 많을수록 네트워크 부하가 심해집니다.
  - Payload는 암호화가 안되므로 기밀정보는 담기 힘듭니다.

- 토큰 탈취 해결 방법

  - 만료시간을 짧게 한다.
    - 뺏었는데 이미 만료가 되어있으면 의미가 없다. 하지만 정상적인 사람이 했다면, 토큰을 잦은 주기로 발급 받아야하는 단점이 있다.
  - Sliding Session
    - 폼이나 결제중에 시간이 지나서, 제출을 하고보니 토큰이 만료됐다고 나오면 눈이 뒤집힌다. 글 작성이나 결제 시작시에 토큰 만료 기한을 늘려주는 방식을 이용하면, 사용자는 조금 더 편한 경험을 할 수 있다.
  - Refresh Token
    - Access Token보다 만료시간이 더 긴 Refresh Token을 발급하는 전략이다. 클라이언트가 Access Token이 만료되었을때 Refresh Token을 이용해서 Access Token의 재발급을 요청한다.
    - 이렇게 한다면, Access token의 만료 기한을 짧게 설정해도 되고, 사용자가 자주 로그인 할 필요도 없다. 또한 강제로 서버가 Refresh Token을 만료시킬 수 있다.
    - 사실 이 방식에는 `단점`이 있다. Refresh Token을 `storage에 저장`해야 한다. 또한 클라이언트도 Refresh Token이 탈취당하면 안되므로 `보안이 유지되는곳에 저장`해야한다. (세션대신 좋은 이유가 I/O작업이 필요가 없던건데..)
    - 방식 : AccessToken 만료 오류가 나면, RefreshToken을 이용해서 서버에 재발급을 요청한다. Refresh도 만료되어 재발급이 실패한경우 로그인을 다시 해달라고 요청을 하면 된다. (access 30분, Refresh 2주~)
    - 자주 로그인을 할 필요도 없고, accessToken이 뺏겨도 금방 만료되어 괜찮다.

![2021-05-29-jwt-cells](https://user-images.githubusercontent.com/60285506/150404308-2d12d8c1-18be-4dd7-bfec-0375b286cd63.png)

- 위 이미지는 https://tecoble.techcourse.co.kr/post/2021-05-22-cookie-session-jwt/ 에서 가져왔습니다.

- 더 필요한 내용들

  - LocalStorage, web Cookie 등등 더 찾아야할게 많다.
  - 시간이 부족해서 더 하지 못했다. 토큰을 보다보니 OAuth도 궁금하고..

- Reference
  - [용욱님의 게시글 훔치기..](https://velog.io/@dnr6054/Cookie-Session-vs-JWT)
  - [JWT vs cookies for token-based authentication](https://stackoverflow.com/questions/37582444/jwt-vs-cookies-for-token-based-authentication)
  - [쿠키 세션 그리고 JWT](https://velog.io/@stampid/%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-%EA%B7%B8%EB%A6%AC%EA%B3%A0-JWT)
  - [세션 하이재킹](https://blog.naver.com/PostView.nhn?blogId=wnrjsxo&logNo=221114275533&parentCategoryNo=&categoryNo=7&viewDate=&isShowPopularPosts=true&from=search)
  - [테코블](https://tecoble.techcourse.co.kr/post/2021-05-22-cookie-session-jwt/)
  - [bearer](https://velog.io/@cada/%ED%86%A0%EA%B7%BC-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D%EC%97%90%EC%84%9C-bearer%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)
  - [JWT 보안전략](https://blog.ull.im/engineering/2019/02/07/jwt-strategy.html)
