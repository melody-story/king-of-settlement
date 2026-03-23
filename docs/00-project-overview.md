# King of Settlement - 프로젝트 개요

## 프로젝트 정보

- **프로젝트명**: King of Settlement (정산 왕)
- **패키지**: `son.kingofsettlement`
- **기술 스택**: Spring Boot 3.2.2 / Java 21 / JPA / H2 / Lombok / Thymeleaf

## 아키텍처 구조

```
src/main/java/son/kingofsettlement/
├── KingOfSettlementApplication.java
├── common/
│   ├── CommonResponse.java          # 공통 API 응답 래퍼
│   ├── Result.java                  # 응답 결과 객체
│   ├── exception/
│   │   ├── ApiExceptionInterface.java
│   │   └── handler/
│   │       ├── ApiExceptionHandler.java      # 도메인 예외 핸들러
│   │       └── GlobalExceptionHandler.java   # 전역 예외 핸들러
│   └── statusCode/
│       ├── StatusCodeInterface.java
│       ├── CommonStatusCode.java
│       └── UserStatusCode.java
└── user/
    ├── api/
    │   └── UserController.java
    ├── dto/
    │   ├── SignUpRequest.java
    │   ├── SignUpResponse.java
    │   ├── LogInRequest.java
    │   ├── UserUpdateRequest.java
    │   └── UserStatus.java          # ENUM: ACTIVE / INACTIVE
    ├── entity/
    │   ├── User.java
    │   └── UserProfile.java         # @Embeddable: 닉네임/프로필URL/소개
    ├── exception/
    │   ├── UserException.java
    │   ├── EncryptException.java
    │   └── DecryptException.java
    ├── repository/
    │   └── UserRepository.java
    └── service/
        ├── UserService.java
        ├── AESEncryption.java       # 이메일 암호화/복호화
        ├── BCrypt.java              # 비밀번호 해싱
        └── SessionConst.java
```

## 구현된 API 목록

| Method | URL | 기능 | 이슈 |
|--------|-----|------|------|
| POST | `/users` | 회원가입 | #6, #7 |
| POST | `/users/login` | 로그인 | #8 |
| POST | `/users/logout` | 로그아웃 | #15 |
| POST | `/users/{id}` | 회원 프로필 수정 | #24 |
| GET | `/users/{id}` | 회원 단건 조회 | #24 |

## 공통 응답 형식

```json
{
  "result": {
    "code": 1000,
    "message": "User created."
  },
  "body": { ... }
}
```

## User 엔티티 구조

| 필드 | 컬럼 | 설명 |
|------|------|------|
| id | id | PK (auto increment) |
| email | encrypted_email | AES 암호화된 이메일 (unique) |
| password | hashed_password | BCrypt 해싱된 비밀번호 |
| sessionKey | session_key | 세션 ID |
| profile | (embedded) | nickname / profileUrl / introduction |
| activityStatus | status | ACTIVE / INACTIVE |
| createdAt | created_at | 생성일시 |
| updatedAt | updated_at | 수정일시 |

## 완료된 작업 목록

| 이슈 | 작업 | 파일 |
|------|------|------|
| - | 프로젝트 초기 세팅 | `docs/01-initial-setup.md` |
| #6 | 회원가입 기능 | `docs/02-signup.md` |
| #7 | 회원가입 암호화 적용 | `docs/03-signup-encrypt.md` |
| #8 | 로그인 기능 | `docs/04-login.md` |
| #15 | 로그아웃 기능 | `docs/05-logout.md` |
| #20 | 예외처리 | `docs/06-exception-handling.md` |
| #24 | 회원 프로필 수정 | `docs/07-user-profile-update.md` |
| #25 | 회원 프로필 삭제 | `docs/08-user-profile-delete.md` |

## 프로젝트 재개 시 주요 확인 사항

### 즉시 해결 필요한 버그 (#24 브랜치)

- `UserService.updateProfile()`의 `findUser.get()` - Optional 예외 처리 없음 (유저 없을 때 NoSuchElementException 발생)
- 프로필 수정 HTTP Method `POST` → `PATCH` 또는 `PUT` 으로 변경 권장
- `@PathVariable("id")` 타입이 `String` → `Long` 으로 변경 권장
- 수정/삭제 권한 검증 없음 (본인 외 타인 프로필도 수정 가능)

### 브랜치 상태 확인

- `feature/#24-userInfoMod` 브랜치에 미완성 커밋(`fdf7105 수정중`) 존재 → 마무리 후 main merge 필요
- `#25` 회원 프로필 삭제 커밋(`0bf0f7d`) 내용 확인 필요 (`git show 0bf0f7d`)

### 프론트엔드 시작 전 결정 사항

- 개발 방식 선택: Thymeleaf 서버사이드 렌더링 vs React/Vue 등 SPA
- SPA 선택 시 CORS 설정 추가 필요
- Figma 디자인 준비 및 화면 목록 확정

### 운영 환경 전환 전 필요 작업

- H2 → MySQL / PostgreSQL 마이그레이션
- AES 암호화 키 환경변수 분리 (현재 하드코딩 여부 확인)
- Spring Security 재도입 검토 (현재 세션 직접 관리 중)

## 다음 작업 계획

- [ ] 프론트엔드 연동 (Figma 기반 UI 개발)
- [ ] 정산 관련 핵심 도메인 구현 (정산 그룹, 정산 항목 등)
- [ ] 데이터베이스 H2 -> 운영 DB 마이그레이션 (MySQL / PostgreSQL)
- [ ] Spring Security 적용 (현재 의존성 제거된 상태)
- [ ] 회원 삭제 기능 완성
