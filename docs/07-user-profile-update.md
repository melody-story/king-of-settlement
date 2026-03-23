# 07. 회원 프로필 수정 (#24)

## 작업 개요

회원의 프로필 정보(닉네임, 프로필 이미지 URL, 자기소개)를 수정하는 기능 구현. 회원 단건 조회 기능도 함께 추가.

## 관련 이슈

- Issue #24

## 관련 커밋

- `1225b7b` [#24] feature: 유저수정 개발중
- `5ec5908` [#24] feature: 회원프로필 수정 기능 추가
- `25a6b3a` [#24] feature: 회원프로필수정 기능 추가 (추가 작업)
- `fdf7105` 수정중 (현재 작업 중)

## 현재 브랜치

`feature/#24-userInfoMod` (작업 중)

## 구현 파일

| 파일 | 역할 |
|------|------|
| `user/dto/UserUpdateRequest.java` | 프로필 수정 요청 DTO |
| `user/entity/UserProfile.java` | 프로필 Embeddable 값 객체 |
| `user/service/UserService.java` | `updateProfile()`, `selectOne()` 메서드 |
| `user/api/UserController.java` | `POST /users/{id}`, `GET /users/{id}` 엔드포인트 |

## API 스펙

### 회원 프로필 수정

```
POST /users/{id}
Content-Type: application/json

Request:
{
  "nickname": "홍길동",
  "profileUrl": "https://example.com/profile.jpg",
  "introduction": "안녕하세요"
}

Response 200:
{
  "result": {
    "code": 200,
    "message": "Succeed"
  }
}
```

### 회원 단건 조회

```
GET /users/{id}

Response 200:
{
  "result": {
    "code": 200,
    "message": "Succeed"
  },
  "body": {
    "id": 1,
    "email": "user@example.com",
    "activityStatus": "INACTIVE",
    "profile": {
      "nickname": "홍길동",
      "profileUrl": "https://example.com/profile.jpg",
      "introduction": "안녕하세요"
    },
    "createdAt": "...",
    "updatedAt": "..."
  }
}
```

## UserUpdateRequest 유효성 검증

| 필드 | 규칙 |
|------|------|
| nickname | `@NotNull` |
| profileUrl | `@NotNull` |
| introduction | `@NotNull` |

## 처리 흐름

```
1. PathVariable로 userId 수신 (String 타입)
2. Long.parseLong(userid)로 변환
3. userRepository.findOneById()로 유저 조회
4. UserProfile.of()로 새 프로필 객체 생성
5. user.updateProfile(profile)로 프로필 교체
6. userRepository.save(user)로 저장
7. updatedAt 자동 업데이트
```

## 미완성 사항 / 개선 필요

- `PathVariable` 타입이 `String`으로 선언됨 (`Long`으로 변경 권장)
- `findUser.get()` - Optional 안전 처리 없음 (유저 없을 때 예외 미처리)
- 수정 권한 검증 없음 (본인 외 타인 프로필도 수정 가능한 상태)
- HTTP Method가 `POST`로 되어 있음 (프로필 수정은 `PATCH` 또는 `PUT` 이 적합)
- 현재 브랜치에서 추가 작업 진행 중 (`fdf7105 수정중`)
