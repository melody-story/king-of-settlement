# 03. 회원가입 암호화 적용 (#7)

## 작업 개요

회원가입 기능에 보안 강화. 이메일 AES 양방향 암호화, 비밀번호 BCrypt 단방향 해싱 적용. UserProfile 분리.

## 관련 이슈

- Issue #7

## 관련 커밋 (주요)

- `3721ac2` feature: 비밀번호/아이디 암호화, Service 이름 변경 SignUp -> User
- `b88ce13` feature: User entity에서 프로필 부분 클래스로 분리
- `22572cf` fix: 비밀번호 암호화 BCrypt 적용
- `c39a7ec` fix: 복호화 예외 생성 및 적용
- `b3e396f` fix: spring boot security 의존성 제거
- `9d8941b` fix: 팩토리 메서드 네이밍 수정
- `87245d8` Merge branch 'feature/#7-encrypt'

## 구현 파일

| 파일 | 역할 |
|------|------|
| `user/service/AESEncryption.java` | 이메일 AES 암호화/복호화 |
| `user/service/BCrypt.java` | 비밀번호 BCrypt 해싱 |
| `user/entity/UserProfile.java` | 프로필 Embeddable 객체 |
| `user/exception/EncryptException.java` | 암호화 실패 예외 |
| `user/exception/DecryptException.java` | 복호화 실패 예외 |

## 암호화 전략

### 이메일 - AES 양방향 암호화

- DB에 `encrypted_email` 컬럼으로 암호화 저장
- 이메일 중복 체크 시 암호화된 값으로 조회
- `User.getEmail()` 호출 시 자동 복호화하여 반환

```java
// AESEncryption 사용 예시
String encrypted = AESEncryption.encrypt(email);
String decrypted = AESEncryption.decrypt(encryptedEmail);
```

### 비밀번호 - BCrypt 단방향 해싱

- BCrypt slow hashing 적용 (cost factor: 10)
- salt 자동 생성 (`BCrypt.gensalt(10)`)
- 로그인 시 `BCrypt.checkpw(password, hashedPassword)` 로 검증

```java
// BCrypt 사용 예시
String salt = BCrypt.gensalt(10);
String hashed = BCrypt.hashpw(password, salt);
boolean match = BCrypt.checkpw(rawPassword, hashed);
```

## UserProfile 분리 배경

`User` 엔티티에서 프로필 관련 필드를 `UserProfile` 값 객체로 분리.

```java
@Embeddable
public class UserProfile {
    private String nickname;
    private String profileUrl;
    private String introduction;
}
```

- `@Embedded` / `@Embeddable` 패턴 적용
- User 테이블에 동일 테이블로 저장 (별도 JOIN 불필요)
- `UserProfile.of()` static factory method 제공

## 코드리뷰 반영 내용

- Spring Security 의존성 제거 (BCrypt를 직접 구현)
- `DecryptException` 별도 예외 클래스 추가
- 팩토리 메서드 네이밍 통일 (`of`)
- `@JsonIgnore` 로 password, sessionKey JSON 직렬화 제외

## 주의사항

- AES 암호화 키는 환경변수 또는 설정 파일로 관리 필요 (운영 환경에서 하드코딩 금지)
- 현재 Spring Security 없이 세션으로만 인증 처리 중
