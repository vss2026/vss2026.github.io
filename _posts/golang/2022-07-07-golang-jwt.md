---
author: <1>
title: Golang Jwt
date: 2022-07-07
categories: [Golang, Jwt]
tags: [Golang]     # TAG names should always be lowercase
---

### JWT
#### What is JWT?
  ~~~
    * json string 형태로 되어있는 토큰
    * 토큰 자체의 정보를 담고있다. (Claim 기반의 Web Token)
    * 로그인, 정보교류시 사용
  ~~~

---
#### # 구조
  <img src="https://velog.velcdn.com/images%2Fdnjscksdn98%2Fpost%2F93750576-b681-4658-ba88-10922ffb4ff3%2Fjwt.png" width="700px" height="200px" title=""/>
  </br>
  </br>

- JWT는 Header, Payload, Signature 3부분으로 이루어짐
    1. **Header**
    - 헤더는 2가지 정보를 담고있음.
    - typ: 토큰의 타입을 지정
    - alg: Signature 해싱 알고리즘 지정, 보통 HMAC-SHA256 || RSA 이용하며 토큰검증시 사용
      </br>
    2. **Payload**
    - 토큰에 담을 정보가 들어있음.
    -  담는 정보의 한 ‘조각’ 을 클레임(Claim)이라 하며 3종류가 존재
        - **registered 클레임**
            -  미리 이름이 정해진 클레임 필수 입력이 아닌 선택적 입력
            - ex) iss(토큰발급자), exp(토큰 만료시간), sub(토큰 제목)
        - **public 클레임**
            - 충돌이 방지된 이름이 필요, 충돌방지를 위해 URL형식
            - ex)
              {
              &nbsp;&nbsp;&nbsp;&nbsp;"https://naver.com/jwt_claims/is_owner": true
              }
        - **private 클레임**
            - 클라이언트, 서버간 협의하에 사용하는 이름들
            - ex)
              {
              &nbsp;&nbsp;&nbsp;&nbsp;"name": "Lionel Messi"
              }
              </br>
    3. **Signature**
    - Header, Payload의 인코딩값을 합친 다음 비밀키로 해싱하여 생성된다.
    - 토큰의 위변조 여부를 판단할수있다.
    - 해커가 header와 payload에 있는 정보를 변조했다 하더라도 서버에서 이를 다시 해싱했을 때 signature 영역과 다르다면 유효한 토큰이 아니라고 판단할 수 있는 것이다.
----

#### 알고리즘

###### HS256 vs RS256
* HS256 (HMAC SHA256)
    * 대칭 암호화 방식
    * JWT를 사용하는 애플리케이션을 개발할 경우 비밀 키를 사용하는 사람을 제어 할 수 있으므로 HS256을 안전하게 사용할 수 있습니다
* RS256 (RSA SHA256)
    * 비대칭 암호화 방식
    * public key로 encrypt된 message는 오직 private key를 가진 주체만 message를 decrypt하여 plaintext를 얻을 수 있다고 말할 수 있다.

----

#### Code

~~~ go
package main

import (
	"fmt"
	"github.com/golang-jwt/jwt"
)

type Claims struct {
	Email string
	Name  string
}

func (Claims) Valid() error {
	return nil
}

func main() {
	claims := Claims{
		Email: "example@example",
		Name:  "Messi",
	}
	jwtGen := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	token, err := jwtGen.SignedString([]byte(SecretKey))
	if err != nil {
		fmt.Printf("%+v\n", err)
	}
	fmt.Println("token = ", token)
}

~~~
