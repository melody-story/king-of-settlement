# 09. 프론트엔드 개발 계획 (Figma 연동)

## 현재 상태

- 프론트엔드 화면 없음
- 백엔드 API만 구현된 상태
- Thymeleaf 의존성은 추가되어 있으나 미사용

## Figma 연동 방식 선택

프론트엔드 개발 방식에 따라 연동 방법이 다름. 아래 옵션 중 선택 필요.

### 옵션 A: Figma + HTML/CSS/JS (Thymeleaf 서버사이드 렌더링)

- 현재 `build.gradle.kts`에 Thymeleaf 의존성 존재
- Figma 디자인을 Thymeleaf 템플릿으로 변환
- `src/main/resources/templates/` 하위에 HTML 파일 작성
- 별도 프론트 서버 불필요

**장점**: 빠른 개발, 별도 프로젝트 불필요
**단점**: 프론트-백엔드 결합도 높음, 현대적 UI 라이브러리 사용 어려움

### 옵션 B: Figma + React/Vue (SPA 별도 프로젝트)

- 프론트엔드 별도 프로젝트 생성 (React / Vue / Next.js 등)
- 백엔드 API 호출로 연동
- CORS 설정 필요

**장점**: 프론트-백엔드 완전 분리, 현대적 개발 방식
**단점**: 별도 프로젝트 관리 필요, CORS 설정 추가

### 옵션 C: Figma Dev Mode → 코드 자동 생성

- Figma Dev Mode에서 CSS/HTML 코드 추출
- 선택한 프레임워크(React 등)와 연동 플러그인 활용

## 현재 API 엔드포인트 정리 (프론트 연동용)

| 화면 | Method | URL | 요청 Body |
|------|--------|-----|-----------|
| 회원가입 | POST | `/users` | `{email, password}` |
| 로그인 | POST | `/users/login` | `{email, password}` |
| 로그아웃 | POST | `/users/logout` | - |
| 프로필 수정 | POST | `/users/{id}` | `{nickname, profileUrl, introduction}` |
| 회원 조회 | GET | `/users/{id}` | - |

## CORS 설정 (프론트 별도 개발 시 필요)

현재 CORS 설정 없음. 프론트엔드 별도 개발 시 아래 설정 추가 필요.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("http://localhost:3000")
            .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE")
            .allowCredentials(true);
    }
}
```

## 작업 재개 전 결정 사항

- [ ] 프론트엔드 개발 방식 결정 (Thymeleaf vs SPA)
- [ ] Figma 디자인 파일 준비
- [ ] 화면 목록 및 사용자 플로우 정의
- [ ] 운영 DB 선택 (H2 -> MySQL/PostgreSQL 마이그레이션 계획)
