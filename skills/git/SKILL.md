---
description: 변경사항을 확인 후 git add → commit → push를 한번에 처리합니다.
---

# Git Quick Push

변경사항을 확인하고 git add → commit → push를 한번에 처리한다.

## 절차

### 1단계: 정보 수집 (자동)

다음 정보를 수집한다:
- 현재 브랜치명
- `git status`로 변경/추가/삭제된 파일 목록
- `git log --oneline -5`로 최근 커밋 메시지 스타일 확인
- `git diff --stat`으로 변경 규모 확인

### 2단계: 컨펌 (필수)

수집한 정보를 사용자에게 보여주고 **반드시 AskUserQuestion으로 확인**받는다:

```
📌 현재 브랜치: {브랜치명}
{main 또는 master인 경우: ⚠️ 메인 브랜치입니다! 직접 push해도 괜찮나요?}

📁 변경 파일 ({N}개):
  {git status 결과 — 파일별 M/A/D 표시}

⚠️ 민감 파일 확인:
  {.env, credentials, secret, key 등이 포함된 파일이 있으면 경고}
  {없으면 "민감 파일 없음"}

💬 커밋 메시지 (안):
  {생성한 커밋 메시지}

진행할까?
```

**컨펌 없이 절대 진행하지 않는다.**

### 3단계: 실행

사용자가 수락하면:
1. `git add` — 변경된 파일 추가 (민감 파일 제외)
2. `git commit` — 최근 커밋 스타일에 맞춰 메시지 작성 (한글)
3. `git push` — 현재 브랜치에 push (upstream 없으면 `-u origin {브랜치}`)

## 커밋 메시지 규칙

- 최근 5개 커밋 메시지 스타일을 참고하여 일관성 유지
- 한글로 작성
- 변경 성격이 다르면 커밋을 분리 (예: feat + fix가 섞여있으면 각각 커밋)
- Co-Authored-By 트레일러 포함

## 안전장치

- main/master 브랜치: 추가 경고 문구 표시
- .env, credentials, secret, key, token 등 민감 파일: 경고 + 커밋 대상에서 제외 제안
- 변경 파일이 없으면: "변경사항 없음" 안내 후 종료
