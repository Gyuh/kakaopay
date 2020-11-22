# kakaopay devops task
## 실행순서 (pwd: /{path}/spring-petclinic-data-jdbc-master)
- ./gradlew jibDockerBuild
- kubectl apply -f ./k8s/mysql-deployment.yml
- kubectl apply -f ./k8s/petclinic-deployment.yml

## 요구사항 설명
- gradle을 사용하여 어플리케이션과 도커이미지를 빌드한다.
  - gradle init 명령어를 통해 maven->gradle 빌드 script 파일 변경
  - build.gradle jib task를 통해 local repo로 docker build,push
  - build.gradle bootjar task 추가하여 /target 경로에 jar파일 생성 가능 
- 어플리케이션의 log는 host의 /logs 디렉토리에 적재되도록 한다.
  - hostpath volumemount
- 정상 동작 여부를 반환하는 api를 구현하며, 10초에 한번 체크하도록 한다. 3번 연속 체크에 실패하 면 어플리케이션은 restart 된다.
  - springboot acturator 사용 (basepath: /manage)
  - livenessProbe 설정 추가
- 종료 시 30초 이내에 프로세스가 종료되지 않으면 SIGKILL로 강제 종료 시킨다.
  - terminationGracePeriodSeconds 설정 추가
- 배포 시와 scale in/out 시 유실되는 트래픽이 없어야 한다.
  - minReadySeconds, rolling update 설정 추가
- 어플리케이션 프로세스는 root 계정이 아닌 uid:1000으로 실행한다.
  - securityContext 설정 추가
- DB도 kubernetes에서 실행하며 재 실행 시에도 변경된 데이터는 유실되지 않도록 설정한다. 어플리케이션과 DB는 cluster domain을 이용하여 통신한다.
  - mysql pv,svc 설정 
- nginx-ingress-controller를 통해 어플리케이션에 접속이 가능하다.
  - nodePort 설정 추가
- namespace는 default를 사용한다.
  - namespace defualt 설정 추가
