# 08. 회원 프로필 삭제 (#25)

## 작업 개요

회원 프로필 삭제 기능 구현. 커밋 이력에는 존재하나 현재 브랜치(`feature/#24-userInfoMod`)에 미merge 상태.

## 관련 이슈

- Issue #25

## 관련 커밋

- `0bf0f7d` [#25] feature: 회원프로필 삭제 기능 추가

## 현재 상태

- 해당 커밋이 main 브랜치에 포함되어 있지 않을 수 있음
- `feature/#24-userInfoMod` 브랜치 기준으로 확인 필요
- UserController에 삭제 엔드포인트가 존재하지 않는 상태 (확인 필요)

## 예상 API 스펙

### 회원 프로필 삭제

```
DELETE /users/{id}

Response 200:
{
  "result": {
    "code": 200,
    "message": "Succeed"
  }
}
```

## 확인 필요 사항

- [ ] `0bf0f7d` 커밋의 변경 내용 확인 (`git show 0bf0f7d`)
- [ ] 삭제가 "회원 탈퇴(User 삭제)"인지 "프로필 정보만 null로 초기화"인지 명확화 필요
- [ ] 삭제 권한 검증 로직 필요
- [ ] main 브랜치 merge 여부 확인

## 작업 재개 시 체크리스트

- [ ] `git show 0bf0f7d` 로 구현 내용 확인
- [ ] `#24` 브랜치 작업 완료 후 merge
- [ ] `#25` 이슈 재오픈 또는 신규 브랜치 생성
- [ ] 삭제 범위 정의 (회원 탈퇴 vs 프로필 초기화)
