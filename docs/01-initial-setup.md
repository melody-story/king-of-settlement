# 01. 프로젝트 초기 세팅

## 작업 개요

Spring Boot 프로젝트의 기본 환경 구성.

## 관련 커밋

- `7f4a5d7` feature: 스프링부트 초기세팅 추가
- `cbd258d` fix: application 설정 타입 yml로 변경
- `a075538` fix: plain.jar 생기지 않도록 gradle 설정 수정
- `5377796` comment: SpringBootApplication annotation 주석 추가
- `dbca818` chore: jpa 설정 추가, h2 연결
- `ba1c196` chore: junit5 의존성 추가
- `b34d3f3` chore: mockito 의존성 추가
- `3e1da2a` chore: email 의존성 추가
- `3331c61` docs: PR 템플릿 추가
- `7d222cb` docs: 이슈 템플릿 추가

## 설정 내용

### build.gradle.kts 주요 의존성

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    implementation("org.springframework.boot:spring-boot-starter-thymeleaf")
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-validation")
    compileOnly("org.projectlombok:lombok")
    runtimeOnly("com.h2database:h2")
    annotationProcessor("org.projectlombok:lombok")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
    testImplementation("org.springframework.security:spring-security-test")
}
```

### Gradle 설정 포인트

- `tasks.jar { enabled = false }` - plain.jar 생성 방지
- `tasks.bootJar { archiveFileName.set("king-of-settlement.jar") }` - 최종 jar 이름 지정
- 테스트 시 `exclude("user/local/*")` 로 로컬 전용 테스트 제외

### GitHub 템플릿

- `.github/ISSUE_TEMPLATE/bug-report.md`
- `.github/ISSUE_TEMPLATE/feature_request.md`
- `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md`
- `.github/workflows/sonarcloud-analyze.yml` - 자동 코드 분석

## 현재 DB 설정

- **개발/테스트**: H2 in-memory DB
- **JPA**: ddl-auto 설정으로 엔티티 기반 스키마 자동 생성

## 비고

- Spring Security 의존성은 코드리뷰 반영 과정에서 제거됨 (`#7` 작업 시)
- 추후 로그인/인가 정책 확정 후 Spring Security 재도입 필요
