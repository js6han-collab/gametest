# 회사 머신 셋업 가이드

집에서 작업한 `gametest` 프로토타입을 회사에서 이어가는 절차. **순서대로** 따라가면 됩니다.

Repo: <https://github.com/js6han-collab/gametest>

---

## 1. 전제 — 회사 PC 에 깔려 있어야 하는 것

| 도구 | 확인 명령 | 없으면 |
|------|-----------|--------|
| Git | `git --version` | <https://git-scm.com/download/win> 에서 설치 |
| Node.js (v18+) | `node --version` | <https://nodejs.org/> LTS 설치 |
| Claude Code | `claude --version` | <https://claude.com/claude-code> 설치 |
| (선택) VS Code | `code --version` | <https://code.visualstudio.com/> |

> Tip: PowerShell 에서 `Get-Command git, node, claude` 로 한 번에 확인 가능.

---

## 2. 처음 한 번 — 클론 + 의존성 + 실행

```powershell
# 작업 폴더로 이동 (예: C:\Users\<회사ID>\dev)
cd C:\Users\<회사ID>\dev

# 클론 (HTTPS — GitHub 로그인 필요할 수 있음)
git clone https://github.com/js6han-collab/gametest.git
cd gametest

# 의존성 설치 (Vite 만 깔림 — 30초 이내)
npm install

# 개발 서버 실행
npm run dev
```

브라우저에서 자동으로 열리거나, 안 열리면 터미널에 찍힌 `http://localhost:5173/` 로 접속.

> **GitHub 인증이 처음이면**: clone 단계에서 사용자명/PAT(Personal Access Token) 입력 창이 뜹니다. 비밀번호 대신 **PAT** 를 발급해서 넣어야 합니다 (Settings → Developer settings → Personal access tokens → Tokens (classic) → repo 권한).

---

## 3. 회사에서 Claude Code 띄울 때

```powershell
cd C:\Users\<회사ID>\dev\gametest
claude
```

프로젝트 루트의 [CLAUDE.md](../CLAUDE.md) 가 자동으로 컨텍스트로 로드돼서 — 집에서 마지막으로 정해둔 스택/디자인 토큰/슬롯 시각 시스템/인터랙션 규칙이 새 세션에서도 잡힙니다.

> 단, **대화 로그 자체는 머신 간에 안 따라옵니다** (auto-memory 는 로컬에 묶임). 작업 중간에 결정한 내용이 있다면 CLAUDE.md 에 미리 적어두는 게 안전합니다.

---

## 4. 일상 작업 사이클

### 회사에서 작업 시작할 때
```powershell
git pull       # 집에서 push 한 변경 받기
npm run dev    # (이미 설치돼 있으면 install 생략)
```

### 작업 끝나고 push 할 때
```powershell
git status                          # 어떤 파일이 바뀌었는지 확인
git add -A                          # 전부 스테이징 (선택적으로 git add <파일>)
git commit -m "<무엇을 바꿨는지>"
git push                            # GitHub 로 올림
```

### 집에서 다시 시작할 때
```powershell
git pull
npm run dev
```

> **핵심 규칙: 작업 끝나면 무조건 commit + push**. 깜빡하면 다음날 다른 머신에서 변경된 내용이 안 보입니다.

---

## 5. 충돌 (conflict) 났을 때

집과 회사에서 **동시에 같은 줄을 수정**하면 발생. 빈도 낮음.

```powershell
# pull 했더니 충돌 메시지가 뜸
git status                          # 충돌난 파일 확인 (Unmerged paths)
# 해당 파일을 열어서 <<<<<<<, =======, >>>>>>> 마커 사이를 직접 정리
git add <충돌난파일>
git commit                          # 메시지는 자동으로 "Merge..." 채워짐
git push
```

> Claude Code 한테 "충돌 해결해줘" 라고 하면 자동으로 처리 가능합니다.

---

## 6. 자주 쓰는 명령 치트시트

| 하고 싶은 것 | 명령 |
|-------------|------|
| 지금 어떤 파일이 바뀌었는지 | `git status` |
| 변경 내용 보기 | `git diff` (커밋 전) / `git diff HEAD~1` (직전 커밋과) |
| 마지막 커밋 메시지 보기 | `git log -1` |
| 직전 작업 통째로 되돌리기 | `git reset --hard HEAD~1` (push 전에만!) |
| 특정 파일을 마지막 커밋 상태로 | `git checkout HEAD -- <파일>` |
| 임시로 작업 저장하고 pull | `git stash; git pull; git stash pop` |
| 새 브랜치에서 실험 | `git checkout -b try-foo` |

---

## 7. 트러블슈팅

**`npm install` 이 느리거나 실패** → 회사 프록시일 수 있음. `npm config set proxy http://...` 또는 IT 팀 문의.

**`git push` 가 인증 실패** → PAT 만료. GitHub 에서 새로 발급 후 재시도. Windows Credential Manager 에 저장된 인증 정보 갱신 필요할 수 있음.

**`npm run dev` 가 포트 충돌** → `npm run dev -- --port 5174` 처럼 다른 포트 지정.

**한글 파일명이 깨짐** (`시뮬레이터_루팅_시스템_Prototype.html`) → `git config core.quotepath false` 한 번 실행.

**Claude Code 가 CLAUDE.md 를 못 읽음** → repo 루트에서 `claude` 띄웠는지 확인. 하위 폴더에서 띄우면 못 찾을 수 있음.

---

## 8. 프로젝트 구조 빠른 참조

```
gametest/
├── CLAUDE.md                              ← Claude Code 가 자동 로드
├── docs/WORK_SETUP.md                     ← 이 파일
├── index.html                             ← redirect to ↓
├── 시뮬레이터_루팅_시스템_Prototype.html   ← 실제 프로토타입 본체 (7350줄)
├── figma-assets/                          ← SVG/PNG 에셋
│   └── pubg-icons/                        ← PUBG wiki 다운로드 아이콘 23개
├── package.json
└── vite.config.js
```

`Ctrl+F` 로 프로토타입 안에서 검색하는 키워드:
- 슬롯 시각 시스템: `--fig-rarity-*`, `.fi-cell.filled`, `--fi-focus-gradient`
- 인벤토리 DOM: `id="fullinv"`, `id="fiInvGrid"`, `id="fiWeaponPrimary"`
- 드래그/드롭 로직: `onFIDragStart`, `performTransfer`, `quickMove`
- 부착물 분리: `detachAllFromWeapon`
- 카테고리 아이콘: `CAT_ICON_SVG`
- PUBG 아이콘 매핑: `PUBG_ICON`, `iconHtml`
