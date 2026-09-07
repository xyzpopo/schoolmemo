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

Firestore Database → 규칙 탭 → 아래 내용 붙여넣기 → 게시

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

## 3. GitHub에 올리기

1. https://github.com → New repository
2. 이름 입력, Public/Private 선택, **"Add a README file" 체크 해제** (직접 올릴 것이므로) → Create repository
3. 저장소 페이지 → **Add file → Upload files**
4. `index.html`, `README.md`, `.gitignore` 끌어다 놓기 (`.gitignore`가 안 보이면 생략 가능, 없어도 무방)
5. 하단 **Commit changes** 클릭

### 업데이트할 때
파일이 바뀔 때마다: 저장소 → **Add file → Upload files** → 같은 이름 파일 다시 올리면 자동 덮어써짐 → Commit changes

## 4. 무료로 배포하기 (GitHub Pages)

1. 저장소 → **Settings → Pages**
2. Source: **Deploy from a branch**, Branch: **main / (root)** → Save
3. 1~10분 후 상단에 뜨는 주소로 접속 (`https://[아이디].github.io/[저장소이름]/`)
4. 파일 업데이트 후에도 반영까지 몇 분 걸릴 수 있음
