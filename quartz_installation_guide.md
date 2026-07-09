# Obsidian + Quartz + Cloudflare Pages 무료 블로그 구축 매뉴얼 (2026년 최신 기준)

이 문서는 Obsidian(옵시디언) 노트를 Quartz 4/5 프레임워크와 Cloudflare Pages를 결합하여 웹상에 완전히 무료로 배포하고 자동화하는 통합 설치 매뉴얼입니다. 구글 드라이브(가상 드라이브)에서의 파일 충돌 및 Windows PowerShell 실행 보안 정책 등 발생할 수 있는 주요 에러 예방책을 포함하여 검증된 단계로 정리했습니다.

---

## 🛠️ 준비 사항
시작하기 전에 아래 도구들이 PC에 설치되어 있어야 합니다.
1. **Node.js** (LTS 버전 권장, v20 이상)
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

## 3단계: Obsidian 연동 및 로컬 테스트
실제 노트 편집 도구인 Obsidian을 프로젝트 폴더와 연동하고 로컬 컴퓨터에서 사이트를 띄워 검증합니다.

1. **Obsidian에서 보관소 열기**:
   * Obsidian 앱을 실행한 후 **[보관소로 폴더 열기 (Open folder as vault)]**를 선택합니다.
   * 방금 생성하여 초기화한 로컬 폴더 **`D:\깃 클론(Clone)`**을 찾아 선택하여 엽니다.

2. **첫 페이지 작성**:
   * Obsidian 좌측 파일 내비게이터에서 **`content`** 폴더를 엽니다.
   * `content` 내에 자동으로 생성된 **`index.md`** 파일을 엽니다.
   * 파일 맨 상단에 메타데이터(Frontmatter)와 함께 원하는 소개글을 작성합니다:
     ```markdown
     ---
     title: 내 블로그 홈
     ---

     안녕하세요! Obsidian과 Quartz로 만든 블로그에 오신 것을 환영합니다!
     ```

3. **로컬 개발 서버 실행**:
   * 명령 프롬프트(cmd)에서 아래 명령어를 실행하여 로컬 서버를 구동합니다.
     ```cmd
     npx quartz build --serve
     ```
   * 빌드가 완료되면 브라우저를 열고 **`http://localhost:8080`**에 접속하여 내 블로그가 화면에 예쁘게 나오는지 확인합니다.
   * 확인이 끝났다면 터미널 창에서 `Ctrl + C`를 눌러 서버를 종료합니다.

---

## 4단계: 변경사항 GitHub 업로드
로컬에서 설정이 끝난 파일들을 GitHub에 저장하여 배포 준비 상태로 만듭니다.

```cmd
git add -A
git commit -m "Initialize Quartz 5"
git push origin v4
```
*(브랜치 명이 `v4` 또는 `main`일 수 있으니, 터미널에 명시된 기본 브랜치 이름을 사용해 주세요.)*

---

## 5단계: Cloudflare Pages 배포 및 자동화 설정 (최신)
GitHub에 소스코드가 업데이트될 때마다 Cloudflare가 자동으로 빌드하여 웹사이트를 갱신하도록 구성합니다.

1. **Cloudflare 대시보드 진입**:
   * [Cloudflare 대시보드](https://dash.cloudflare.com/)에 로그인합니다.
   * 좌측 메뉴에서 **[Workers & Pages]** ➔ **[Create]**를 클릭합니다.
   * 상단 탭에서 **[Pages]**를 누르고 **[Connect to Git]**을 클릭하여 본인의 GitHub 계정을 연동합니다.
   * 연동된 레포지토리 목록에서 방금 생성했던 `my-quartz-blog` 레포지토리를 선택하고 **[Begin setup]**을 누릅니다.

2. **프로젝트 빌드 설정**:
   * **프로젝트 이름**: 원하는 서브도메인을 결정합니다. (예: `my-blog` 입력 시 `my-blog.pages.dev` 주소 획득)
   * **프로덕션 분기**: `v4` (또는 GitHub에 푸시했던 브랜치 명 선택)
   * **프레임워크 사전 설정 (Framework preset)**: **`None (없음)`** 선택
   * **빌드 명령 (Build command)**: 
     ```bash
     npx quartz build
     ```
   * **출력 디렉토리 (Build output directory)**: 
     ```text
     public
     ```

3. **환경 변수 지정 (매우 중요)**:
   * 빌드 최적화 및 에러 방지를 위해 Node.js 환경변수를 추가해야 합니다.
   * 아래 **[환경 변수 (Environment variables)]** 섹션을 펼치고 **[+ Add variable]**을 누릅니다.
     * **변수 이름(Variable name)**: `NODE_VERSION`
     * **값(Value)**: `20` (또는 `22`)

4. **저장 및 배포**:
   * **[Save and Deploy]** 버튼을 클릭하여 최초 배포를 시작합니다.
   * 빌드가 완료되면 화면에 출력되는 고유 `pages.dev` 도메인으로 접속하여 블로그가 전 세계에 무료로 배포된 것을 확인합니다.
   * 앞으로 Obsidian에서 글을 작성하고 GitHub에 `git push`만 해주면 Cloudflare가 자동으로 감지해 2~3분 내에 사이트를 새롭게 업데이트합니다.

---

## 💡 자주 발생하는 트러블슈팅
### Q1. PowerShell에서 `npm i` 입력 시 스크립트를 실행할 수 없다는 보안 에러가 발생합니다.
* **원인**: 윈도우 PowerShell의 기본 스크립트 실행 제한 정책 때문입니다.
* **해결**: 명령 프롬프트(cmd) 창을 실행해 작업하거나, 현재 PowerShell 창에 아래 임시 정책 완화 명령어를 입력하고 다시 시도하세요.
  ```powershell
  Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process
  ```

### Q2. 구글 드라이브(G:) 내에서 빌드할 때 파일 관련 에러가 발생합니다.
* **원인**: Cloudflare/Quartz 빌드 툴체인 및 Node.js 패키지 시스템은 구글 드라이브의 가상 파일 마운트 시스템과 호환성이 좋지 않아 파일 잠금 충돌을 일으킵니다.
* **해결**: 반드시 D드라이브나 C드라이브 등 순수 로컬 물리 드라이브 폴더에서 전체 작업을 세팅하여 진행하세요.
