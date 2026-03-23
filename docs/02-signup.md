# 02. 회원가입 기능 (#6)

## 작업 개요

기본 회원가입 기능 구현. User 엔티티 설계, Repository/Service/Controller 레이어 구성, 입력값 유효성 검증, 테스트코드 작성.

## 관련 이슈

- Issue #6

## 관련 커밋 (주요)

- `6d20a9b` feature: user 생성 기능 추가 (userEntity, UserRepository, Enum UserStatus)
- `0c338c7` feature: 회원가입 - 이메일 중복 여부 확인, 회원 생성
- `b17e862` feature: 회원가입 DTO 내 유효성 검증 추가
- `38e2a92` fix: signUpRequest validation 추가 적용
- `b3a3d44` feature: 회원가입 기능 구현 (#3 PR merge)
- 다수의 코드리뷰 반영 커밋

## 구현 파일

| 파일 | 역할 |
|------|------|
| `user/entity/User.java` | User JPA 엔티티 |
| `user/dto/UserStatus.java` | ACTIVE / INACTIVE Enum |
| `user/dto/SignUpRequest.java` | 회원가입 요청 DTO |
| `user/repository/UserRepository.java` | JPA Repository |
| `user/service/UserService.java` | 회원가입 비즈니스 로직 |
| `user/api/UserController.java` | 회원가입 API 엔드포인트 |

## API 스펙

### 회원가입

```
POST /users
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "Password1!"
}

Response 201:
{
  "result": {
    "code": 1000,
    "message": "User created."
  }
}
```

## 유효성 검증 규칙

| 필드 | 규칙 |
|------|------|
| email | `@NotBlank`, `@Email` |
| password | `@NotBlank`, `@Pattern` - 대문자/소문자/특수문자/숫자 포함, 8~15자 |

## 비밀번호 정규식

```
^(?=.*[A-Z]).(?=.*[a-z]).(?=.*[!"#$%&'()*+,\-./:;<=>?@\[\]^_`{|}~]).(?=.*[0-9]).{8,15}$
```

## 코드리뷰 반영 내용

- static factory method `User.of()` 적용
- `session_id` -> `session_key` 필드명 수정
- RESTful API 설계 적용 (`/users` POST)
- `isDuplicatedUser()` 함수명 명확화
- `join` -> `signUp` 메서드명 통일
- 단위테스트 / 통합테스트 분리
- Mock Repository 적용

## 테스트 코드

- `UserControllerTest.java` - 컨트롤러 통합 테스트
- `UserServiceTest.java` - 서비스 단위 테스트
  - 이메일 중복 케이스
  - 유효성 검증 케이스 (이메일 형식, 비밀번호 패턴, 특수문자 닉네임 등)
