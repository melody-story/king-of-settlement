# 06. 예외처리 (#20)

## 작업 개요

전역 예외 처리 구조 구축. `@RestControllerAdvice` 기반 핸들러 적용, 공통 응답 형식 통일, 유효성 검증 에러 처리 통합.

## 관련 이슈

- Issue #20

## 관련 커밋 (주요)

- `abcf4b0` feature: 예외처리 - exception handler 적용, controller advice, API 응답 리팩토링
- `5f3fb7a` fix: Exception handler를 통한 예외 처리 Controller에 적용
- `8579648` fix: HttpStatusCode Enum값 사용, 입력값 유효성 검증 에러 GlobalExceptionHandler 처리
- `50697ee` fix: GlobalExceptionHandler에서 에러 처리 (코드리뷰 반영)
- `76a86cf` Merge branch 'feature/#20-exception'

## 구현 파일

| 파일 | 역할 |
|------|------|
| `common/CommonResponse.java` | 공통 API 응답 래퍼 |
| `common/Result.java` | result 필드 (code, message) |
| `common/exception/ApiExceptionInterface.java` | 도메인 예외 인터페이스 |
| `common/exception/handler/ApiExceptionHandler.java` | 도메인 예외 핸들러 (높은 우선순위) |
| `common/exception/handler/GlobalExceptionHandler.java` | 전역 예외 핸들러 (낮은 우선순위) |
| `common/statusCode/StatusCodeInterface.java` | StatusCode 공통 인터페이스 |
| `common/statusCode/CommonStatusCode.java` | 공통 상태 코드 Enum |
| `common/statusCode/UserStatusCode.java` | 유저 관련 상태 코드 Enum |
| `user/exception/UserException.java` | 유저 도메인 예외 |

## 예외 처리 구조

```
요청
 |
 v
Controller
 |-- UserException (도메인 예외) --> ApiExceptionHandler  (@Order: HIGHEST_PRECEDENCE)
 |-- BindException (유효성 에러) --> GlobalExceptionHandler
 |-- Exception (기타 예외)      --> GlobalExceptionHandler (@Order: LOWEST_PRECEDENCE)
```

## 공통 응답 형식

```java
// 성공
CommonResponse.success(statusCode)
CommonResponse.success(data, statusCode)

// 실패
CommonResponse.fail(statusCode, exception)
CommonResponse.fail(statusCode, message)
```

### 응답 JSON 구조

```json
{
  "result": {
    "code": 1000,
    "message": "User created."
  },
  "body": { ... }   // null이면 JSON에서 제외 (@JsonInclude NON_NULL)
}
```

## StatusCode 구조

### CommonStatusCode

| Enum | HTTP | code | 설명 |
|------|------|------|------|
| Succeed | 200 | - | 성공 |
| SERVER_ERROR | 500 | - | 서버 에러 |
| INVALID_INPUT_VALUE | 400 | - | 입력값 유효성 에러 |

### UserStatusCode

| Enum | HTTP | code | 설명 |
|------|------|------|------|
| USER_CREATED | 201 | 1000 | 회원가입 성공 |
| LOGIN_SUCCESS | 200 | 1001 | 로그인 성공 |
| USER_NOT_FOUND | 400 | 1400 | 유저 없음 |
| DUPLICATED_USER | 400 | 1402 | 이메일 중복 |
| LOGIN_FAILED | 401 | 1403 | 로그인 실패 |

## 코드리뷰 반영 내용

- `HttpStatus` 대신 `HttpStatus.value()` Enum 값 사용
- 입력값 유효성 검증 에러(`BindException`)를 `GlobalExceptionHandler`에서 통합 처리
- 예외 메시지 영어로 통일
- 사용하지 않는 메서드 제거
- 역할 중복 어노테이션 제거
- 테스트 코드 메시지 영어로 통일
