# 산출물

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
     ![webhook-trigger-test](./img/webhook-trigger-test.PNG)
