---
title: "Docusaurus"
excerpt: ""

categories:
  - Docs

toc: true
toc_sticky: false
 
date: 2024-01-12
last_modified_at: 2024-01-12
---

## 서론

API 문서 혹은 기술 문서를 작성할 때 유용한 Docusaurus에 대해 알아보자.

<br>

## Docusaurus

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/5b726520-b53e-43a8-a7af-c8c83f1b8989){: width="300px"}

[Docusaurus](https://docusaurus.io/)는 Meta(Facebook)에서 오픈 소스로 개발한 정적 사이트 생성기이다.

Docusaurus를 사용하면 손쉽게 문서 웹사이트를 생성, 유지 및 배포할 수 있다.

<br>

## Docusaurus 특징

- 정적 웹사이트 생성 : React를 사용하여 인터랙티브한 SPA(Single Page Application)를 만들 수 있다.
- 배포 지원 : Github Pages, Vercel, Netlify 등의 호스팅 서비스에 정적 사이트를 배포할 수 있다.
- 마크다운 지원 : 마크다운을 사용하여 문서를 작성할 수 있다. (마크다운의 확장 버전인 [MDX](https://mdxjs.com/)도 지원한다.)
- 버저닝 지원 : 문서를 버전별로 관리할 수 있다.
- 검색 지원 : 문서 내에서 검색 기능을 사용할 수 있다.
- 플러그인 지원 : 다양한 플러그인을 사용하여 기능을 확장할 수 있다.
- 커스터마이징 지원 : CSS, 커스텀 테마 등을 사용하여 웹사이트를 꾸밀 수 있다.
- 활성화된 커뮤니티 : 커뮤니티가 활발하여 지속적인 개선과 지원을 제공한다.

<br>


## Docusaurus 설치 방법

[공식 문서](https://docusaurus.io/docs/installation)에 잘 나와 있지만 설치 방법에 대해 간단히 정리해 보았다.

### Git, cURL 설치

먼저 플랫폼에 맞는 패키지 매니저를 사용해서 Git, cURL을 설치한다.

```bash
# Ubuntu or WSL
$ sudo apt-get update
$ sudo apt-get install git curl

# RHEL or CentOS
$ sudo yum update
$ sudo yum install git curl

# macOS
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
$ brew install git curl
```

### NVM(Node Version Manager) 설치

NVM은 여러 버전의 Node.js를 관리하고 전환할 수 있게 해주는 도구이다.

```bash
# nvm 설치
$ curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
$ source ~/.bashrc
```

### Node.js, NPM(Node Package Manager) 설치

Node.js는 V8 엔진을 사용하여 JavaScript를 서버 환경에서 실행하는 런타임 환경이고 NPM은 JavaScript 라이브러리와 도구를 설치하고 관리하는 Node.js의 패키지 매니저이다.

```bash
# 최신 LTS 버전의 node.js(npm) 설치
$ nvm install --lts

# node.js 버전 확인
$ node -v

# npm 버전 확인
$ npm -v
```

다른 Node.js 버전을 사용하고 싶을 경우, NVM을 사용하여 변경할 수 있다.

```bash
# 원하는 node.js 버전 설치
$ nvm install v18.18.0

# 설치된 node.js 버전 리스트 확인
$ nvm list

# 현재 세션에서 사용할 node.js 버전 선택
$ nvm use v18.18.0

# 기본 node.js 버전 설정
$ nvm alias default v18.18.0
```

### Yarn 설치

Yarn은 NPM 보다 성능과 보완을 향상시킨 Node.js 패키지 매니저이다. Yarn은 NPM과 호환되기 때문에 NPM 명령어 대신 Yarn 명령어를 사용할 수 있다.

```bash
# yarn 설치
$ npm install -g yarn

# yarn 버전 확인
$ yarn -v
```

### Docusaurus 기본 템플릿 설치

다음으로 Docusaurus 기본 템플릿을 설치한다. 설치가 완료되면 프로젝트 이름과 동일한 폴더가 생성된다.

> Node.js(16.14 버전 이상)가 설치되어 있어야 한다.

```bash
$ npx create-docusaurus@latest $(프로젝트 이름) classic
$ cd $(프로젝트 이름)
```

### 패키지 업데이트

Docusaurus 버전을 업데이트하거나 기존에 있는 Docusaurus 프로젝트를 설치한 경우, 패키지를 설치해야 한다.

```bash
$ yarn install # or npm install
```

### 로컬 서버에서 정적 웹사이트 실행

`package.json` 파일에 설정된 [Docusaurus 명령어](https://docusaurus.io/docs/cli)를 사용하여 정적 웹사이트를 로컬 서버에서 실행할 수 있다.

```bash
$ yarn start # or npm run start
```

### 정적 웹사이트 빌드

빌드된 정적 웹사이트는 `/build` 폴더에 저장되며, 정적 파일 호스팅 서비스를 통해 배포할 수 있다.

```bash
$ yarn build # npm run build
```

### [Github Page 배포](https://docusaurus.io/docs/deployment#deploying-to-github-pages)

다음 명령을 통해 Github Page에 배포할 수 있다.

> [Github Actions를 사용한 배포 방법](https://docusaurus.io/docs/deployment#triggering-deployment-with-github-actions)

```bash
$ yarn deploy # or npm run deploy
```

<br>

## [Docusaurus 프로젝트 구조](https://docusaurus.io/docs/installation#project-structure-rundown)

```bash
$(프로젝트 이름)
├── blog
├── docs
├── src
├── static
├── docusaurus.config.js
├── sidebars.js
└── package.json
```

- `/blog/` : 블로그 타입의 마크다운 문서를 저장하는 폴더
- `/docs/` : 마크다운 문서를 저장하는 폴더
- `/src/` : 문서가 아닌 소스 파일을 저장하는 폴더 (React 컴포넌트, 페이지 등)
- `/static/` : 정적인 파일을 저장하는 폴더 (빌드할 때 `/build/` 폴더로 복사된다.)
- `/docusaurus.config.js` : Docusaurus 설정(테마, 프리셋, 플러그인 등)을 관리하는 파일
- `/sidbars.js` : 사이드바에 나타나는 문서 순서를 지정하는 파일
- `/package.json` : Node.js 프로젝트와 모듈 의존성을 관리하는 메타데이터 파일


<br>

## Docusaurus 사용 방법

Docusaurus의 사용 목적은 크게 [문서](https://docusaurus.io/docs/docs-introduction) 혹은 [블로그](https://docusaurus.io/docs/blog)로 나눌 수 있다. 

나는 기술 문서를 목적으로만 사용해 봤기 때문에 해당 내용을 중심으로 정리해 보았다.

### [Docs-only 모드 설정](https://docusaurus.io/docs/docs-introduction#docs-only-mode)

먼저 `docusaurus.config.js` 파일을 수정한다.

```js
export default {
  // ...
  presets: [
    [
      '@docusaurus/preset-classic',
      {
        docs: {
          routeBasePath: '/', // Serve the docs at the site's root
          /* other docs plugin options */
        },
        blog: false, // Optional: disable the blog plugin
        // ...
      },
    ],
  ],
};
```

다음으로 `docs/intro.md` 문서를 홈페이지로 설정한다.

```markdown
---
slug: /
---

This page will be the home page when users visit https://example.com/.
```

> `docs/intro.md` 문서를 홈페이지로 만들기 위해서는 기본 홈페이지로 지정되어 있는 `src/pages/index.js` 파일을 제거해야 한다.

### [문서 작성](https://docusaurus.io/docs/create-doc)

문서를 작성하려면 프로젝트 루트에 있는 `/docs` 폴더 안에 마크다운 파일을 생성하고 최상단에 메타 정보를 입력해야 한다.

```markdown
---
id: { 문서 ID }
title: { 문서 제목 (생략할 경우 문서 ID와 동일) }
slug: { 문서 페이지 경로 설정 (생략할 경우 문서 ID를 기반으로 생성) }
---
```

> 문서 ID는 영어, 한글, 공백을 허용하며 괄호는 안된다.

### [사이드바 설정](https://docusaurus.io/docs/sidebar)

사이드바에 생성한 문서를 연결하려면, 프로젝트 루트에 있는 `sidebars.js` 파일을 수정해야 한다.

`/docs` 폴더를 기준으로 파일의 경로까지 문서 ID에 포함된다. (해당 문서 파일의 폴더 경로도 입력해야 한다.)

마크다운 문서를 생성할 때, 메타 정보 문서 ID에 `테스트 문서`라고 입력한 후 `docs/테스트 폴더/테스트 문서.md` 경로에 파일을 저장한다면, 사이드바에서 인식하는 문서 ID는 `테스트 폴더/테스트 문서`가 된다.

```js
module.exports = {
  docSidebar: [ // 사이드바 ID
    // 문서 링크 일반 표현 방식
    {
      type: 'doc', // 문서 링크 타입
      id: '테스트 폴더/테스트 문서', // 문서 ID
      label: '테스트 문서', // 사이드바에 표시될 이름
    },

    // 문서 링크 축약 표현 방식
    '테스트 폴더/테스트 문서', // 문서 ID (사이드바에 표시될 이름에는 폴더 경로가 빠짐)
  ],
};
```

> 아래와 같은 방법으로 사이드바에 계층 구조를 만들 수 있다.

```js
module.exports = {
  docSidebar: [
    {
      type: 'category', // 문서 링크 타입
      label: '테스트 문서', // 사이드바에 표시될 이름
      collapsible: true, // 카테고리 접을 수 있도록 설정 (설정 안하면 true)
      collapsed: false, // 카테고리를 처음에 접어놓도록 설정 (설정 안하면 true)
      items: [ // 카테고리 내부에 문서 혹은 하위 카테고리 표현 
        '/테스트 폴더/테스트 문서', // 카테고리 내부에 문서 링크 (축약 표현 방식)
        {
          type: 'category', // 이런 식으로 계속 하위 카테고리를 만들 수도 있다.
          label: '테스트 폴더 안의 폴더',
          items: [
            '테스트 폴더/테스트 폴더 안의 폴더/테스트 문서 1', // 카테고리 내부에 문서 링크 (축약 표현 방식)
            '테스트 폴더/테스트 폴더 안의 폴더/테스트 문서 2', // 카테고리 내부에 문서 링크 (축약 표현 방식)
          ],
        },
      ],
    },
  ],
};
```

### [사이트 헤더 설정](https://docusaurus.io/docs/api/themes/configuration)

사이트 헤더에 문서를 연결하고 싶은 경우, `docusaurus.config.js` 파일을 수정해야 한다.

```js
module.exports = {
  themeConfig: {
    navbar: {
      items: [
        {
          type: 'doc', // Navbar 타입
          position: 'left', // 헤더 위치 ("left" or "right")
          sidebarId: 'docSidebar', // 사이드바 ID (sidebarId 대신 docId 사용 가능)
          label: '테스트 문서', // 헤더에 표시될 이름
        },
      ],
    },
  },
};
```

> 아래와 같은 방법으로 헤더에 외부 주소를 연결할 수 있다.

```js
module.exports = {
  themeConfig: {
    navbar: {
      items: [
        {
          href: 'https://github.com/somewhere', // 외부 주소
          position: 'left', // 헤더 위치 ("left" or "right")
          label: '프로젝트 주소', // 헤더에 표시될 이름
        },
      ],
    },
  },
};
```

### 사이드바 접기 버튼 추가

사이브바 접기 버튼을 추가하려면 `docusaurus.config.js` 파일을 수정해야 한다.

```js
module.exports = {
  themeConfig: {
    docs: {
      sidebar: {
        hideable: true,
      },
    },
  },
};
```
