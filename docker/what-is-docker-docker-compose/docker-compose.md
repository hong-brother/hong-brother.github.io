---
description: 'Gitlab, jenkins, nexus, postgis Service에 관련된 Compose'
---

# Docker-compose\(작성중\)

## Docker-compose 

```yaml
####################################################################################
#### Gitlab, Jenkins, Nexus, PostGIS
####################################################################################
version: '3'
services: 
  gitlab: 
    container_name: vcs-gitlab
    image: 'gitlab/gitlab-ce:latest'
    ports: 
      - '80:80'
      - '443:443'
      - '222:22'
    privileged: true
    restart: always
    environment: 
      GITLAB_OMNIBUS_CONFIG: |
          external_url 'https://gitlab.hsnam.co.kr'
          nginx['enable'] = true
          nginx['client_max_body_size] = '1048m' #Proxy에서 올리는 파일 용량 제한 조
          # Add any other gitlab.rb configuration here, each on its own line
    volumes: 
      - '/home/hsnam/docker/docker-compose/data/gitlab/config:/etc/gitlab'
      - '/home/hsnam/docker/docker-compose/data/gitlab/logs:/var/log/gitlab'
      - '/home/hsnam/docker/docker-compose/data/gitlab/data:/var/opt/gitlab'
      - '/home/hsnam/docker/docker-compose/data/gitlab/backups:/var/opt/gitlab/backups'
  jenkins: 
    container_name: ci-jenkins
    image: 'jenkins/jenkins:latest'
    ports: 
      - '8080:8080'
      - '50000:50000'
    privileged: true
    restart: always
    volumes: #Jenkins에서 Host의 Docker를 이용하기 Volumes를 공
      - '/var/run/docker.sock:/var/run/docker.sock'
      - '/home/hsnam/docker/docker-compose/data/jenkins/jenkins_home:/var/jenkins_home'
      - '/usr/bin/docker:/usr/bin/docker'
    environment: 
      TZ: Asia/Seoul
    depends_on: 
      - gitlab #Gitlab이 로드 된이후에 Jenkins기
  nexus: 
    image: 'sonatype/nexus3:latest'
    container_name: repository-nexus
    privileged: true
    restart: always
    environment: 
      TZ: Asia/Seoul
    ports: 
      - '8081:8081'
      - '8082:8082'
    volumes: 
      - '/home/hsnam/docker/docker-compose/data/nexus/nexus-data:/nexus-data'
    depends_on: 
      - gitlab
      - jenkins
  postgis: 
    container_name: db-postgis
    image: mdillon/postgis
    ports: 
      - '5432:5432'
    privileged: true
    restart: always
    environment: 
      TZ: Asia/Seoul
    volumes: 
      - '/home/hsnam/docker/docker-compose/data/postgresql:/var/lib/postgresql'
```

DevOps를 하면서 필요한 GitLab, Jenkins, Nexus를 필수적으로 커스텀해서 Docker-Compose으로 구성하였다. Docker-Compose 파일만 있으면 언제든지 기동 할 수 있고 해당 Container 업데이트 까지 할 수가 있다.  run명령어 없이 compose up -d 명령어면 여러개 서비스도 쉽게 올릴 수가 있다.

### How To Use? 

**Docker-Compose 실행\(yml에 선언된 서비스들이 전부 컨테이너로 실행 된다.\)**

```bash
docker-compose up -d [options] [--scale SERVICE=NUM...] [SERVICE...]
```

-d, --detach : Detached Mode\(컨테이너를 background에서 run한다\)

-

**Docker-Compose 종료\(yml에 선언된 서비스의 Container를 삭제한다.\)**

```bash
docker-compose down [option]
```

--rmi : 종료 되면서 모든 이미지를 삭제 한다.

--volumes, -v

--remove-grphans

--timeout, -t

[Docker-Compose **reference**](https://docs.docker.com/compose/reference/)\*\*\*\*

