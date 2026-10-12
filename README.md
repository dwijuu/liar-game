# 🎭 라이어 게임 - 실시간 온라인 멀티플레이

실시간 웹소켓 기반 온라인 라이어 게임 서비스입니다.

## 🚀 빠른 시작

### 전제 조건
- Node.js v18+ (현재 v24.20.0 설치됨)

### 1. 서버 실행
```powershell
cd server
npm install    # 최초 1회
node src/index.js
```
서버: http://localhost:3001

### 2. 클라이언트 실행 (새 터미널)
```powershell
cd client
npm install    # 최초 1회
npx vite
```
클라이언트: http://localhost:5173

### 3. 한 번에 실행 (PowerShell)
```powershell
# 프로젝트 루트에서
powershell -ExecutionPolicy Bypass -File start.ps1
```

---

## 🌐 네트워크 멀티플레이

같은 와이파이/네트워크의 다른 기기에서 접속:
```
http://<이 PC의 IP>:5173
```

IP 확인:
```powershell
ipconfig | findstr "IPv4"
```

---

## 🏗️ 기술 스택

| 구분 | 기술 |
|------|------|
| 프론트엔드 | React 18 + TypeScript + Vite |
| 백엔드 | Node.js + Express |
| 실시간 통신 | Socket.IO v4 |
| 상태 관리 | Zustand |
| 스타일 | 순수 CSS (다크테마) |
| DB | 인메모리 (재시작 시 초기화) |

---

## 📁 프로젝트 구조

```
라이어게임/
├── server/
│   └── src/
│       ├── index.js          # Express + Socket.IO 서버 진입점
│       ├── socketHandlers.js  # 모든 소켓 이벤트 핸들러
│       ├── gameManager.js     # 게임/방/유저 상태 관리
│       ├── gameConstants.js   # 게임 상태 열거형
│       └── wordData.js        # 제시어 데이터베이스
│
└── client/
    └── src/
        ├── App.tsx
        ├── main.tsx
        ├── pages/
        │   ├── ProfilePage.tsx  # 프로필 설정
        │   ├── LobbyPage.tsx    # 방 목록
        │   ├── RoomPage.tsx     # 대기실
        │   └── GamePage.tsx     # 게임 화면
        ├── components/
        │   └── CreateRoomModal.tsx
        ├── hooks/
        │   ├── useSocketEvents.ts
        │   └── useCountdown.ts
        ├── store/
        │   └── gameStore.ts     # Zustand 상태
        ├── types/
        │   └── index.ts
        └── utils/
            └── socket.ts
```

---

## 🎮 구현된 기능

### ✅ 완전히 동작하는 기능
- 닉네임/아바타/색상 프로필 설정
- 세션 기반 재접속 (새로고침 후 방 복귀)
- 실시간 방 목록 (Socket.IO 브로드캐스트)
- 방 생성/입장/퇴장
- 비밀번호 방
- 방 설정 실시간 변경 (방장)
- 방장 권한 자동 이전
- 일반 모드 / 바보 모드
- 순서대로 발언 / 자유 토론 모드
- 서버사이드 역할 배정 (라이어 비밀 유지)
- 발언 타이머 (서버 기준)
- 투표 시스템 (과반수 조기 종료)
- 투표 타이머 (서버 기준)
- 동률/무효 투표 처리
- 라이어의 마지막 추측
- 게임 결과 (라이어/시민 공개)
- 다시 하기
- 실시간 채팅 (Rate Limiting)
- 참가자 입퇴장 알림
- 반응형 레이아웃 (모바일 지원)
- 13개 주제 × 20-35개 제시어
- 바보 모드용 연관 단어 쌍

### ⚠️ 제한사항
- 인메모리 저장 → 서버 재시작 시 방 초기화
- 배포 시 별도 서버 필요 (Vercel 단독 배포 불가)
- 라이어 복수 지목 시 1명만 추측 허용

---

## 🔧 환경 변수

### server/.env
```
PORT=3001
CLIENT_URL=http://localhost:5173
```

### client/.env
```
VITE_SERVER_URL=http://localhost:3001
```

---

## 🌍 배포 방법 (프로덕션)

### 서버 배포 (Railway / Render / EC2)
```bash
# server/.env 설정
CLIENT_URL=https://your-frontend-domain.com
PORT=3001

# 실행
node src/index.js
```

### 클라이언트 배포 (Vercel / Netlify)
```bash
# client/.env 설정
VITE_SERVER_URL=https://your-server-domain.com

# 빌드
npm run build
```

---

## 🎯 게임 규칙

### 일반 모드
1. 시민: 동일한 제시어를 비밀리에 받음
2. 라이어: 제시어 없음, 시민 발언으로 추측
3. 토론 후 투표
4. 라이어 지목 시 → 마지막 추측 기회
5. 정답 맞히면 라이어 승리, 틀리면 시민 승리

### 바보 모드
1. 시민: 기준 제시어 받음 (예: 사과)
2. 라이어: 연관된 다른 단어 받음 (예: 파인애플)
3. 이후 일반 모드와 동일

### 승패 조건
| 상황 | 결과 |
|------|------|
| 라이어 지목 + 추측 성공 | 라이어 승리 |
| 라이어 지목 + 추측 실패 | 시민 승리 |
| 시민 지목 | 라이어 승리 |
| 투표 동률 (설정에 따라) | 라이어 승리 / 재투표 |
| 추측 시간 초과 | 시민 승리 |
