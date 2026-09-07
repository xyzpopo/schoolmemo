# 메모장

날짜별 메모 + 캘린더 보기를 지원하는 단일 페이지 앱. 데이터는 Firebase Firestore에 저장됨.

## 1. Firebase 설정

1. https://console.firebase.google.com → 프로젝트 추가 → 이름 입력 → Google Analytics는 꺼도 됨 → 프로젝트 만들기
2. 왼쪽 메뉴 **빌드 → Firestore Database** → 데이터베이스 만들기
   - 데이터베이스 모드: **Standard** (Enterprise 아님)
   - 운영 모드: **Native mode**
   - 위치 선택 → 보안 규칙 시작 모드는 아무거나 선택 (뒤에서 직접 덮어씀)
3. 프로젝트 개요 옆 **⚙ 프로젝트 설정** → 하단 "내 앱" → **</> (웹)** 아이콘 클릭 → 앱 등록 (Firebase Hosting 체크는 꺼도 됨)
4. 표시되는 `firebaseConfig` 값 전체 복사
5. `index.html`에서 `firebaseConfig = { ... }` 부분을 방금 복사한 값으로 교체 (약 8번째 줄 근처)

## 2. Firestore 보안 규칙

`firestore.rules` 파일 내용 (이미 저장소에 포함됨):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /memo-app/notes {
      allow read, write: if true;
    }
  }
}
```

로그인 기능이 없는 개인용 앱이라 이 문서 하나만 누구나 읽고 쓸 수 있게 열어둔 규칙. URL을 남에게 공유하지 않으면 사실상 안전함.

**최초 1회는 콘솔에서 직접 붙여넣기**: Firestore Database → 규칙 탭 → 위 내용 붙여넣기 → 게시

**이후 자동배포 설정 (선택)**: `firestore.rules` 파일을 고치고 push만 하면 깃헙 액션이 알아서 반영해줌.
1. 터미널에서 `npm install -g firebase-tools` (최초 1회)
2. `firebase login:ci` 실행 → 브라우저에서 로그인 → 터미널에 뜨는 토큰 복사
3. GitHub 저장소 → **Settings → Secrets and variables → Actions → New repository secret**
4. Name: `FIREBASE_TOKEN`, Secret: 방금 복사한 토큰 → Add secret
5. 이후 `firestore.rules`를 고쳐서 main에 push하면 자동 배포됨 (`.github/workflows/firebase-rules-deploy.yml`이 이미 포함되어 있음)

## 3. GitHub에 올리기

1. https://github.com → New repository
2. 이름 입력, Public/Private 선택, **"Add a README file" 체크 해제** (직접 올릴 것이므로) → Create repository
3. 저장소 페이지 → **Add file → Upload files**
4. 다음 파일 끌어다 놓기: `index.html`, `README.md`, `.gitignore`, `firestore.rules`, `firebase.json`, `.firebaserc`
   (점으로 시작하는 파일이 파일탐색기에 안 보이면 생략 가능, 없어도 동작엔 문제없음)
5. 하단 **Commit changes** 클릭
6. 워크플로 파일은 폴더가 있어서 드래그가 번거로울 수 있음 → **Add file → Create new file** → 파일명 칸에 `.github/workflows/firebase-rules-deploy.yml` 통째로 입력(자동으로 폴더 생성됨) → 내용 붙여넣기 → Commit

내가 깃헙 토큰 받아서 한 번에 커밋해줄 수도 있음 (직전 답변 참고)

### 업데이트할 때
파일이 바뀔 때마다: 저장소 → **Add file → Upload files** → 같은 이름 파일 다시 올리면 자동 덮어써짐 → Commit changes

## 4. 무료로 배포하기 (GitHub Pages)

1. 저장소 → **Settings → Pages**
2. Source: **Deploy from a branch**, Branch: **main / (root)** → Save
3. 1~10분 후 상단에 뜨는 주소로 접속 (`https://[아이디].github.io/[저장소이름]/`)
4. 파일 업데이트 후에도 반영까지 몇 분 걸릴 수 있음
