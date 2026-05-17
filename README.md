# TPS Loot Prototype

PUBG 스타일 3인칭 슈터 루팅/인터랙션 프로토타입.

진실의 원천(source of truth)은 단일 파일 **[시뮬레이터_루팅_시스템_Prototype.html](시뮬레이터_루팅_시스템_Prototype.html)** 입니다. CSS/JS/HTML 모두 인라인이며 Three.js r128을 CDN에서 로드합니다. 이 저장소는 그 파일을 편집하고 공유하기 편하도록 Vite를 dev 서버로 얹어 둔 것일 뿐입니다.

## 실행

```bash
npm install
npm run dev
```

브라우저에서 [http://localhost:5173/](http://localhost:5173/) 열기 → 자동으로 프로토타입 파일로 리다이렉트됩니다.

프로토타입 HTML을 편집하면 Vite가 브라우저를 자동 리로드합니다.

## 동료에게 공유

### 옵션 1 — LAN 시연 (가장 빠름)

```bash
npm run dev -- --host
```

표시되는 Network 주소를 같은 망의 동료에게 전달.

### 옵션 2 — 정적 호스팅용 빌드

```bash
npm run build
```

`dist/` 에 `index.html` 과 `시뮬레이터_루팅_시스템_Prototype.html` 이 함께 생성됩니다. Netlify/Vercel/GitHub Pages/사내 정적 호스트 등에 그대로 업로드.

```bash
npm run preview -- --host
```

로컬에서 빌드 결과물을 LAN으로 미리보기.

### 옵션 3 — 파일만 직접 전달

프로토타입 HTML은 완전 독립형(self-contained)이므로 `시뮬레이터_루팅_시스템_Prototype.html` 한 파일만 동료에게 보내도 브라우저에서 바로 열립니다. (CDN 접속 가능한 환경 한정.)

## 구조

```
시뮬레이터_루팅_시스템_Prototype.html   # 본체 (의도적으로 단일 파일)
index.html                              # localhost:5173 → 본체로 redirect
vite.config.js                          # build 시 두 HTML을 모두 dist에 포함
package.json                            # vite만 devDependency
```

> 본체 파일은 의도적으로 monolithic 구조를 유지하고 있습니다. 모듈 분리/리팩터링은 현재 범위에 포함되지 않습니다.
