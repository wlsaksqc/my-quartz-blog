# Obsidian + Quartz + Cloudflare Pages 무료 블로그 구축 매뉴얼 (2026년 최신 기준)

이 문서는 Obsidian(옵시디언) 노트를 Quartz 5 프레임워크와 Cloudflare Pages를 결합하여 웹상에 완전히 무료로 배포하고 자동화하는 통합 설치 매뉴얼입니다. 구글 드라이브(가상 드라이브)에서의 파일 충돌 및 Windows PowerShell 실행 보안 정책 등 발생할 수 있는 주요 에러 예방책과 2026년 최종 배포에 성공한 실제 해결 내역을 포함하여 정리했습니다.

---

## 🛠️ 준비 사항
시작하기 전에 아래 도구들이 PC에 설치되어 있어야 합니다.
1. **Node.js** (v22.0.0 이상 필수, Quartz 5 빌드 최저 요구사항)
2. **Git** (로컬 소스 관리 및 GitHub 업로드용)
3. **Obsidian** (노트 작성용 마크다운 에디터)
4. **GitHub 계정** 및 **Cloudflare 계정**

---

## 1단계: GitHub 저장소(Repository) 준비 및 로컬 클론
Cloudflare Pages가 내 노트를 자동으로 감지하여 빌드할 수 있도록 소스코드 저장소를 생성하고 로컬 PC로 가져옵니다.

1. **Quartz GitHub 템플릿 복사**:
   * [Quartz 공식 GitHub 레포지토리](https://github.com/jackyzha0/quartz)에 접속합니다.
   * 우측 상단의 **[Use this template]** ➔ **[Create a new repository]**를 선택합니다.
   * 레포지토리 이름을 입력하고 (예: `my-quartz-blog`), **Public** 혹은 **Private**으로 저장소를 생성합니다.

2. **로컬 PC 물리 드라이브에 폴더 생성 및 클론**:
   > [!IMPORTANT]
   > 구글 드라이브(G:)나 OneDrive 등 실시간 동기화 드라이브 내에서 `npm install` 실행 시 파일 잠금 에러(`EBADF`)가 발생합니다. 반드시 **로컬 물리 드라이브(C: 또는 D:)** 상에 폴더를 생성해야 합니다.

   * 윈도우 **명령 프롬프트(cmd)**를 열고 아래 명령어를 순서대로 실행합니다. (여기서는 `D:\깃 클론(Clone)` 폴더를 기준으로 작성했습니다.)
   ```cmd
   D:
   cd \
   mkdir "깃 클론(Clone)"
   cd "깃 클론(Clone)"
   git clone https://github.com/자신의_깃허브_닉네임/생성한_레포지토리_이름.git .
   ```
   *(마지막 한 칸 띄우고 입력하는 마침표`.`는 현재 폴더 내에 깃 파일을 바로 풀겠다는 뜻입니다.)*

---

## 2단계: 패키지 설치 및 Quartz 초기화
로컬 폴더에서 Quartz를 구동하기 위한 의존성 파일들을 설치하고 환경을 구성합니다.

1. **의존성 패키지 설치**:
   ```cmd
   npm install
   ```

2. **Quartz 프로젝트 초기화**:
   ```cmd
   npx quartz create
   ```
   * 명령어를 실행하면 대화형 질문(마법사)이 순서대로 나타납니다. 키보드 방향키와 엔터를 사용하여 다음과 같이 선택합니다.
   
   | 질문 단계 (Question) | 권장 선택지 (Selection) | 설명 |
   | :--- | :--- | :--- |
   | **1. Choose a template** | `Default (clean Quartz setup)` | 기본 템플릿 사용 (그대로 **엔터**) |
   | **2. Choose how to initialize the content** | `Empty Quartz` | 비어있는 새 블로그용 파일 세팅 |
   | **3. Choose how Quartz should resolve links** | `Treat links as shortest path` | Obsidian의 기본 링크 방식과 동기화 |
   | **4. Enter the base URL** | `자신의닉네임.pages.dev` | 배포될 Cloudflare Pages의 도메인 입력 |

   * 초기화가 끝나면 다시 일반 명령어 입력 대기 상태(`D:\깃 클론(Clone)>`)로 돌아옵니다.

---

## 3단계: 구글 드라이브(G:) - 로컬 물리드라이브(D:) 실시간 연동 자동화
실제 작업은 기존 구글 드라이브인 `G:\내 드라이브\MY Obsidian`에서 자유롭게 작성하고, 업로드할 때만 D드라이브로 파일을 안전하게 덤프하여 복제하는 **1초 자동화 스크립트**를 구성하여 배포 안정성을 확보합니다.

1. **윈도우 바탕화면**에 `블로그_업로드.bat` 파일을 생성합니다.
2. 스크립트 내용은 아래와 같이 적용합니다:
   ```batch
   @echo off
   chcp 65001 >nul
   title Obsidian Quartz Blog Auto-Uploader
   
   set SOURCE_DIR=G:\내 드라이브\MY Obsidian
   set DEST_DIR=D:\깃 클론(Clone)\content
   
   echo 1. 구글 드라이브에서 최신 마크다운 노트 복사 중...
   robocopy "%SOURCE_DIR%" "%DEST_DIR%" /MIR /XD .obsidian
   
   echo 2. 복사 완료! Git 업로드 및 블로그 배포 시작...
   d:
   cd "D:\깃 클론(Clone)"
   git add -A
   git commit -m "Auto-published Obsidian notes from Google Drive"
   git push origin v5
   
   echo 업로드 성공!
   pause
   ```
3. 앞으로 글 작성이 끝나면 바탕화면의 **`블로그_업로드.bat`** 파일만 더블 클릭해 주면 자동으로 깃허브 업로드까지 논스톱 완료됩니다.

---

## 4단계: Cloudflare Pages 배포 및 언어 설정
GitHub에 소스코드가 업데이트될 때마다 Cloudflare가 자동으로 빌드하여 웹사이트를 갱신하도록 구성합니다.

1. **Cloudflare 대시보드 진입 및 Pages 연결**:
   * [Cloudflare 대시보드](https://dash.cloudflare.com/) 로그인 후 **[Workers & Pages]** ➔ **[Create]** ➔ **[Pages]** ➔ **[Connect to Git]**을 클릭하여 본인의 GitHub 계정을 연동합니다.
   * `my-quartz-blog` 레포지토리를 선택하고 **[Begin setup]**을 누릅니다.

2. **프로젝트 빌드 설정**:
   * **프로덕션 분기**: **`v5`** (v5 브랜치를 기본값으로 지정합니다.)
   * **프레임워크 사전 설정 (Framework preset)**: **`None (없음)`** 선택
   * **빌드 명령 (Build command)**: `npx quartz build`
   * **출력 디렉토리 (Build output directory)**: `public`

3. **환경 변수 지정 (필수 ⚠️)**:
   * **변수 이름(Variable name)**: `NODE_VERSION`
   * **값(Value)**: **`22`** (Quartz 5 빌드 안정성을 위해 반드시 22 이상 지정)

4. **한국어 로캘(Locale) 변경 설정**:
   * 블로그 UI가 중국어나 영어로 깨져서 나오는 것을 방지하기 위해 로컬 컴퓨터의 [quartz.config.yaml](file:///d:/깃 클론(Clone)/quartz.config.yaml#L9) 파일 내 **`locale: ko-KR`** 로 지정한 뒤 다시 푸시합니다.

---

## 💡 자주 발생하는 트러블슈팅 및 해결 이력
### Q1. `fatal: No url found for submodule path...` 에러와 함께 복제가 터집니다.
* **원인**: Quartz 프레임워크가 외부 플러그인을 가져올 때 가상 링크(Submodules) 정보를 참조하다가, 외부 주소가 깨지거나 만료되어 발생합니다.
* **해결**: 로컬 `.gitmodules` 파일을 삭제하고, `.quartz/plugins/` 내부 하위 디렉토리에 몰래 숨어있던 외부 깃 설정 디렉토리(`.git` 폴더)들을 모두 찾아서 강제 박멸합니다. 그 후 일반 물리 폴더 상태로 인덱스에 강제 스테이징(`git add .quartz/`)하여 푸시하면 해결됩니다.

### Q2. `.quartz` 폴더 내부의 설정 및 파일이 깃에 올라가지 않아 빌드가 깨집니다.
* **원인**: 기본 설정된 `.gitignore` 파일에 `.quartz/` 폴더가 추적 제외(Ignored)로 잡혀 있어 원격지에는 빈 껍데기만 푸시되어 그렇습니다.
* **해결**: `.gitignore` 파일을 메모장이나 에디터로 열어 `.quartz/` 라고 적힌 제외 룰 라인을 삭제한 뒤 다시 푸시해 줍니다.
