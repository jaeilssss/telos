# Spec-First Kit — 글로벌(~/.claude) 버전

우로보로스의 **명세 우선** 방법론 핵심만 뽑아 Claude Code 사용자 레벨(`~/.claude/`)에
설치하는 버전. 어떤 레포도 건드리지 않고 **회사·개인 프로젝트 전부에 자동 적용**된다.

## 설치 (한 줄)

```bash
unzip spec-first-kit-global.zip
cd spec-first-kit-global
bash install.sh
```

그다음 **Claude Code 세션을 완전히 재시작**한다 (settings/hook 은 프로세스 재시작해야 적용).
`/clear` 로는 안 된다.

설치되는 위치:
```
~/.claude/
  CLAUDE.md                 전역 규칙 (모든 프로젝트에 적용)
  SPEC.template.md          계약 문서 템플릿
  skills/spec/SKILL.md      /spec  — 소크라테스식 인터뷰
  skills/impl/SKILL.md      /impl  — 모델 선택 후 서브에이전트 구현
  skills/eval/SKILL.md      /eval  — 3단계 평가 게이트
  agents/spec-evaluator.md  Stage 2 의미 평가 (Sonnet)
  settings.json             PostToolUse 훅
  scripts/spec_gate.py      $0 게이트 (작업 중인 프로젝트의 SPEC.md 를 확인)
  scripts/ambiguity_score.py (선택) 모호성 자동 채점 (Haiku)
```

> 기존에 `~/.claude/CLAUDE.md` 나 `settings.json` 이 있으면 install.sh 가 덮어쓰지 않는다.
> CLAUDE.md 는 끝에 append, settings.json 은 `settings.json.spec-first` 로 따로 떨궈 수동 병합을 안내한다.

## 사용

작업할 프로젝트 디렉터리에서 Claude Code 를 띄우고:

```
/spec  "주문 목록 API 를 만들고 싶어"   # 질문으로 모호함 제거 → 그 프로젝트 루트에 SPEC.md 생성
/impl                                   # 모델 선택(Sonnet/Opus) → 서브에이전트 구현
/eval                                   # 3단계 게이트로 인수 기준 채점
# 실패 항목 되먹이고 /impl → /eval … 수렴까지 반복
```

SPEC.md 는 **각 프로젝트 루트**에 생긴다. 훅은 현재 작업 디렉터리의 SPEC.md 를 본다.

## 동작 범위 / 끄기

- 전역이라 모든 프로젝트에 규칙이 걸린다. 단, **게이트 훅은 차단이 아니라 경고**라
  일회성 스크립트·탐색 작업이면 그냥 무시하고 진행해도 된다.
- 특정 프로젝트에서 전역 규칙을 덮고 싶으면, 그 프로젝트 루트에 `CLAUDE.md` 를 두면
  프로젝트 설정이 우선한다.
- 완전히 빼려면: `~/.claude/skills/{spec,impl,eval}/`, `~/.claude/agents/spec-evaluator.md`,
  `~/.claude/scripts/{spec_gate,ambiguity_score}.py`, `SPEC.template.md` 삭제 +
  `settings.json` 의 해당 훅 제거 + CLAUDE.md 의 appended 블록 삭제.

## 토큰 비용 레버

- Stage 1(테스트·린트)은 LLM 0원으로 먼저 거른다.
- 채점 Haiku / 의미 평가 Sonnet / Opus 는 정말 어려운 추론에만.
- Consensus 기본 OFF, 고위험·불확실 시에만.
- 무한 진화 루프 없음 — 간단한 작업엔 가볍게.

## 커스터마이즈

- `~/.claude/commands/eval.md` 의 Stage 1 명령을 본인 스택 명령으로 교체.
- `~/.claude/agents/spec-evaluator.md` 의 `model:` 티어 조정.
- 게이트를 강제 차단형으로 바꾸려면 `spec_gate.py` 가 exit code 로 거부하도록 수정(주의).
