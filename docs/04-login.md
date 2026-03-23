# 04. 로그인 기능 (#8)

## 작업 개요

세션 기반 로그인 기능 구현. 이메일/비밀번호 검증 후 HttpSession에 사용자 정보 저장.

## 관련 이슈

- Issue #8

## 관련 커밋

- `7e81296` [#8] 로그인 기능 구현 (#18 PR merge)
- `ed7e405` fix: 유저객체 조회시 복호화된 이메일로 표시

## 구현 파일

| 파일 | 역할 |
|------|------|
| `user/dto/LogInRequest.java` | 로그인 요청 DTO |
| `user/service/SessionConst.java` | 세션 키 상수 (`LOGIN_MEMBER`) |
| `user/service/UserService.java` | `login()` 메서드 |
| `user/api/UserController.java` | `POST /users/login` 엔드포인트 |

## API 스펙

### 로그인

```
POST /users/login
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "Password1!"
}

Response 200:
{
  "result": {
    "code": 1001,
    "message": "Login successful."
  },
  "body": {
    "id": 1,
    "email": "user@example.com",
    "activityStatus": "INACTIVE",
    "profile": null,
    "createdAt": "...",
    "updatedAt": "..."
  }
}
```

## 로그인 처리 흐름

```
1. 이메일 AES 암호화
2. 암호화된 이메일로 DB 조회
3. 유저 없으면 -> UserException(USER_NOT_FOUND) 발생
4. HttpSession 생성 (maxInactiveInterval: 1800초 = 30분)
5. 세션에 유저 정보 저장 (key: LOGIN_MEMBER)
6. BCrypt.checkpw()로 비밀번호 검증
7. 유저의 sessionKey 업데이트
8. 유저 객체 반환 (password, sessionKey는 @JsonIgnore)
```

## 세션 설정

```java
HttpSession session = request.getSession();
session.setMaxInactiveInterval(1800);  // 30분
session.setAttribute(SessionConst.LOGIN_MEMBER, existUser);
existUser.updateSessionKey(session.getId());
```

## 상태 코드

| 상황 | StatusCode | HTTP |
|------|------------|------|
| 로그인 성공 | LOGIN_SUCCESS (1001) | 200 |
| 유저 없음 | USER_NOT_FOUND (1400) | 400 |

## 주의사항 / 미구현 사항

- 비밀번호 불일치 시 별도 예외 처리 없음 (현재 세션은 생성되고 sessionKey만 미업데이트)
- Spring Security 없이 직접 세션 관리 중
- 추후 JWT 또는 Spring Security 세션 관리로 전환 검토 필요
