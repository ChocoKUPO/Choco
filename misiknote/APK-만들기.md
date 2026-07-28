# 미식노트 APK 만들기

이 앱은 PWA(설치형 웹앱)로 만들어져 있어서, 안드로이드 APK로 포장할 수 있습니다.
아래 **방법 1(PWABuilder)** 이 가장 쉽고 빠릅니다. 컴퓨터에 아무것도 설치할 필요가 없습니다.

> 먼저 준비: 앱을 `https://chocokupo.github.io/Choco/` 에 올려 두어야 합니다.
> (GitHub Pages에 web 폴더 내용을 올린 상태. APK는 이 주소를 감싸는 껍데기입니다.)

---

## 방법 1 — PWABuilder (권장)

주소만 넣으면 서명된 APK를 만들어 줍니다.

1. 크롬에서 **https://www.pwabuilder.com** 접속
2. 입력칸에 `https://chocokupo.github.io/Choco/` 넣고 **Start**
3. 점수 화면이 나오면 아래 **Android** 카드에서 **Generate Package**
4. 옵션 화면:
   - Package ID: `kr.chocokupo.choco` (그대로)
   - App name: `미식노트`
   - 나머지 기본값 그대로 두어도 됩니다
5. **Generate** → zip 파일을 받습니다.

### 받은 zip 안에 들어 있는 것
- `app-release-signed.apk` — **바로 폰에 설치할 수 있는 APK**
- `app-release-bundle.aab` — 구글 플레이 스토어 올릴 때 쓰는 파일
- `signing.keystore` 와 `signing-key-info.txt` — **서명 키. 절대 잃어버리면 안 됩니다.**
  (다음에 업데이트 APK를 만들 때 같은 키가 있어야 "같은 앱"으로 인정됩니다. 안전한 곳에 보관하세요.)
- `assetlinks.json` — 딥링크 검증용 (아래 참고)

### 마지막 한 단계 — assetlinks.json 올리기 (중요)
APK가 웹사이트와 "같은 앱"임을 증명해서, 앱을 열 때 주소창 없이 전체화면으로 뜨게 하는 파일입니다.

1. 받은 zip 안 `assetlinks.json` 을 엽니다. (SHA256 지문이 채워져 있습니다)
2. 이 파일을 GitHub 저장소의 `.well-known/assetlinks.json` 위치에 **덮어씁니다.**
   (지금 저장소에 있는 건 지문이 비어 있는 자리표시자입니다. PWABuilder가 만든 걸로 교체)
3. 다시 올리면 끝. 몇 분 뒤부터 앱이 주소창 없이 전체화면으로 뜹니다.

> 이 단계를 건너뛰어도 APK는 설치·실행됩니다. 다만 상단에 주소창이 잠깐 보일 수 있습니다.

### 폰에 설치
- APK 파일을 폰으로 보내 실행 → "출처를 알 수 없는 앱" 허용 → 설치.
- 또는 구글 플레이에 올리려면 `.aab` 파일과 개발자 계정(1회 25달러)이 필요합니다.

---

## 방법 2 — Bubblewrap (본인 PC에서 직접)

안드로이드 스튜디오나 JDK가 깔린 PC에서 명령어로 만드는 방법입니다.
이 프로젝트의 `android/twa-manifest.json` 이 이미 준비돼 있습니다.

```bash
npm install -g @bubblewrap/cli
cd android
bubblewrap init --manifest=https://chocokupo.github.io/Choco/manifest.webmanifest
bubblewrap build
```

- 처음 실행하면 JDK 17과 안드로이드 SDK를 자동으로 내려받습니다(수백 MB).
- `bubblewrap build` 가 끝나면 `app-release-signed.apk` 가 생깁니다.
- 서명 키(`android.keystore`)가 만들어지니 잘 보관하세요.
- 딥링크를 원하면 `bubblewrap fingerprint` 로 SHA256 지문을 얻어
  `web/.well-known/assetlinks.json` 에 넣고 다시 배포합니다.

---

## 둘 중 뭘 고를까
- **그냥 APK 하나 빨리 받고 싶다** → 방법 1 (PWABuilder). 설치할 것 없음.
- **PC에 안드로이드 개발환경이 있고 직접 관리하고 싶다** → 방법 2.

두 방법 모두 결과물(APK)은 같은 일을 합니다: `chocokupo.github.io/Choco/` 를
전체화면 앱으로 감쌉니다. 앱 내용을 고치면 웹사이트만 다시 올리면 되고,
APK를 매번 다시 만들 필요는 없습니다. (앱 버전을 올리거나 아이콘을 바꿀 때만 다시 만듭니다.)
