# 05. 로그아웃 기능 (#15)

## 작업 개요

세션 무효화를 통한 로그아웃 기능 구현.

## 관련 이슈

- Issue #15

## 관련 커밋

- `8dc611d` [#15] feature: 로그아웃 기능 (#19 PR merge)

## 구현 파일

| 파일 | 역할 |
|------|------|
| `user/service/UserService.java` | `logout()` 메서드 |
| `user/api/UserController.java` | `POST /users/logout` 엔드포인트 |

## API 스펙

### 로그아웃

```
POST /users/logout

Response 200:
{
  "result": {
    "code": 200,
    "message": "Succeed"
  }
}
```

## 로그아웃 처리 흐름

```
1. 현재 요청의 HttpSession 조회
2. session.invalidate() - 세션 무효화 (서버 측 세션 삭제)
3. 성공 응답 반환
```

## 구현 코드

```java
@Transactional
public void logout(HttpServletRequest request) {
    HttpSession session = request.getSession();
    session.invalidate();
}
```

## 상태 코드

| 상황 | StatusCode | HTTP |
|------|------------|------|
| 로그아웃 성공 | CommonStatusCode.Succeed | 200 |

## 주의사항 / 미구현 사항

- 로그아웃 시 User 엔티티의 `sessionKey` 필드를 null로 초기화하는 처리 없음
- 로그인하지 않은 상태에서 로그아웃 요청 시 별도 예외 처리 없음
- 추후 인가(Authorization) 처리 추가 시 로그인 여부 체크 필요
