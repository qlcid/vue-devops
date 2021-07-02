1. [Vue CLI](#vue-cli-프로젝트-기반-devops-개발환경-실습)
2. [Jenkins-docker를 활용한 CI/CD 구축](#jenkins-docker를-활용한-ci/cd-구축)
3. [Github Actions와 Jenkins의 차이점](#github-actions와-jenkins의-차이점)

# Vue CLI 프로젝트 기반 DevOps 개발환경 실습

> :bulb: Vue CLI로 프로젝트를 생성하여 GitHub Pages에 정적 페이지 호스팅 하기 & GitHub Actions로 자동 배포 설정해서 DevOps 개발환경을 구성하는 실습하기

### 1. Github에 vue-devops 프로젝트 생성

### 2. 원격 저장소 설정 및 코드 푸시

![devops-remote-repo-setting](./img/devops-remote-repo-setting.PNG)
![devops-remote-repo-push](./img/devops-remote-repo-push.PNG)

### 3. Github Pages로 배포하기 위한 라이브러리 추가 & pagckage.json에 배포 관련 정보 추가

```json
{
	"name":  "vue-devops",
	"version":  "0.1.0",
	"private":  true,
	"homepage":  "https://qlcid.github.io/vue-devops",
	"scripts":  {
		"serve":  "vue-cli-service serve",
		"build":  "vue-cli-service build",
		"predeploy":  "vue-cli-service build",
		"deploy":  "gh-pages -d dist",
		"clean":  "gh-pages-clean",
		"test:unit":  "vue-cli-service test:unit",
		"lint":  "vue-cli-service lint"
	}
...
```

### 4. 배포용 publicPath 설정

```javascript
// vue.config.js
moduel.exports = {
  publicPath: '/vue-devops/',
  outputDir: 'dist',
};
```

### 5. yarn deploy 명령 실행

![devops-yarn-deploy](./img/devops-yarn-deploy.PNG)

- 원격 저장소에 gh-pages를 생성해 푸시함

## 6. GitHub Actions workflow로 자동 배포

### 6-1. GitHub Actions의 workflow 파일을 작성

```yml
# deploy.yml
name: Deployment

on:
	push:
		branches: [main]
	pull_request:
		branches: [main]

	workflow_dispatch:

jobs:
	deploy:
		runs-on: ubuntu-latest

	steps:
		- name: Checkout source code
		  uses: actions/checkout@master

		- name: Set up Node.js
		  uses: actions/setup-node@master
		  with:
			node-version: 14.x

		- name: Install dependencies
		  run: yarn install

		- name: Build page
		  run: yarn build
		  env:
			NODE_ENV: production

		- name: Deploy to gh-pages
		  uses: peaceiris/actions-gh-pages@v3
		  with:
			github_token: ${{ secrets.GITHUB_TOKEN }}
			publish_dir: ./dist
```

### 6-2. Github Actions Workflow 동작

![vue-workflow](./img/vue-workflow.PNG)

### 6-3. 변경된 내용 commit & push

![devops-remote-repo-push](./img/devops-remote-repo-push.PNG)

### 6-4. 배포 완료된 Vue 프로젝트 사이트

![vue-site](./img/vue-site.JPG)




# Jenkins-docker를 활용한 CI/CD 구축

> :bulb: Jenkins-docker를 활용한 CI/CD 구축

### 1. Docker로 Jenkins 설치

![docker-jenkins-install](./img/docker-jenkins-install.PNG)

- http://localhost:9090/으로 접속

### 2. Jenkins와 GitLab 연동

- 1. Jenkins에서 GitLab Repository에 접근하기 위해 Access Token을 발행

  - https://lab.ssafy.com/profile/personal_access_tokens에 접속 후 발행
    ![access-token](./img/access-token.PNG)

- 2. Jenkins에서 플러그인 설치

  - DashBoard > Manager > Jenkins > Plugin Manager

- 3. Jenkins에서 GitLab 연동
  - Dashboard > Manager Jenkins > configure System
    ![jenkins-gitlab-connection](./img/jenkins-gitlab-connection.PNG)

### 3. Nginx 설치 후 설정

![nginx-success](./img/nginx-success.PNG)

### 4. Pipeline 생성

- 1. GitLab -> Jenkins 트리거 전송을 위해 Jenkins에서 Secret Token 발행
     ![build-trigger](./img/build-trigger.PNG)
     ![secret-token](./img/secret-token.PNG)

- 2. GitLab에서 push 이벤트에 대한 trigger 테스트

  - GitLab Repository > Settings > Integrations
  - URL(Jenkins Item URL)/Secret Token/Trigger 설정
    ![integration](./img/integration.PNG)

- 3. Add Webhook 후 push 테스트
  - ![webhook-trigger-test](./img/webhook-trigger-test.PNG)

### c.f) 오류

- webhook trigger test error
  - 👉ngrok 사용
- webhook 402 error
  - docker toolbox를 사용할 경우 docker 머신의 ip와 localhost 주소가 다름
  - 👉ngork을 사용하면서 포워딩 할 주소를 localhost가 아닌 docker 머신의 주소로 포워딩( ngrok http [url]:[port])
    ![webhook-402-error](./img/webhook-402-error.PNG)
- 파이프라인 credentialsId access denied error
  - 👉 pipeline syntax > snippet generotor > git: Git 선택 > 암호화 된 id를 포함한 pipeline script 생성
  - 👉 를 하면 해결될 줄 알았으나 안됨....😇




# Github Actions와 Jenkins의 차이점

> :bulb: Github Actions와 Jenkins의 차이점을 알아보자!

## 빌드를 자동화 해야하는 이유
* 개발을 하면서 수동으로 빌드를 하게 될 때 매번 반복되는 과정의 번거로움을 줄여줌


## 1. Jenkins
* CI/CD를 무료로 제공
* 파이프라인을 사용해 거의 모든 언어의 조합이 가능

* 장점
	+ 자동화 테스트 수행
	+ 코딩 규약 준수 여부 체크
	+ 개발 업무를 도와주는 다양한 플러그인을 제공
	+ ...


## 2. Github Actions
* workflow를 자동화 할 수 있는 도구
* 깃허브에서 발생하는 build, test, release, deploy 등 다양한 이벤트 기반으로 workflow를 작성 가능

* 장점
	+ 복잡한 절차 없이 Github를 통해 사용 가능
	+ workflow 복제 용이
	+ ...

## 3. Jenkins vs Github Actions
|Jenkins|Github Actions|
|--|--|
|서버 설치 필요|별도의 설치 필요없음(클라우드 有)|
|작업이 동기화 되기 때문에 많은 시간 소요|  비동기 CI/CD|
|환경 호환성을 위해 도커 이미지에서 실행해야 함|모든 환경과 호환|
|공유 불가능|gitub 마켓 플레이스를 통해 공유 가능|
|참고 문서 多|참고 문서 少|
|ex> 페이스북, 넷플릭스, 쿠팡...|ex> 업스테이지 AI...|

* jenkins에 익숙하지 않은 사람에겐 처음에는 github actions가 접근하기는 쉬워보임(도커 빌드가 자동으로 되는 점..?과 따로 설치가 필요없다는 이유 등)
* jenkins는 다양한 IDE를 지원하고 커스터마이징이 다양하지만 호스팅을 직접 하나부터 열까지 해야하기 때문에 상대적으로 난이도가 있음, 전세계적으로 많이 사용되고 있기 때문에 참고할 문서가 많음