**예제로 배우는 스프링 입문-백기선님 강의 내용 정리**
====================================

목차
---
[프로젝트 설정](#프로젝트-설정)    

***


학습포인트

## 프로젝트 설정    
spring-petclinic 프로젝트 사용 방법에는,  
Running petclinic locally 란에 보면 잘 나와있음.

 IntelliJ 사용해서 git clone을 함 => IntelliJ 초기 화면에
 Get from Version Control > Version control : Git으로 설정 ->URL 에 해당 프로젝트 clone하여 넣어준다. 그리고 확인. 만약 폴더가 이미 존재하면 터미널로 해당 디렉토리 삭제할 것 ( rm -rf 프로젝트이름)

 ./mvnw pakage
로컬에 메이븐을 설치하지 않았더라도 사용할 수 있도록 함.
강의에서 백기선님이 오타내서 오류가 났는데, 이 떄 오류메세지를 항상 읽는 습관*
중간에,
의존성을 받는데 시간이 좀 걸린다고 했는데 메이븐 프로젝트 받고 있는데 의존성이 왜나오지?
의존성 - 
일단 PASS.

빌드를 성공했다고 뜸 이상태에서.
메이븐에 패키징 빌드를 실행하면, 이 프로젝트를 빌드해서 패키징 파일을 만듭니다.
근데 이 패키지는 jar 패키지에요 어디서 알 수 있으면 프로젝트 타입에 아무것도 지정되어 있지 않으면 기본적으로 jar 프로젝트임.  구체적으로,
target > pom.xml > 프로젝트이름 태그에 <packaging> 태그가 없을 경우임.
최종적으로 jar 파일이 생성되었다는 것을 터미널 로그에서 확인해보면,
 Building jar: /Users/yunhyeoggi/workspace/spring-petclinic/target/spring-petclinic-2.3.0.BUILD-SNAPSHOT.jar
으로 확인 할 수 있다.

java -jar : jar로 만들어진 파일을 실행하는 명령어
해당 프로젝트를 실행한다.
java -jar target/*.jar
실행하면 톰캣이 8080포트에 떳다는 것을 로그 메세지에서 확인 할 수 있음.
따라서 localhost:8080접속하면 petClinic이라는 페이지가 보이게 됨.
프로젝트 설정 방법이었음.

영어*
Match the requested format  요청된 포맷과 매치해라, 맞게 해라 라는 뜻인 것 같음.

애플리케이션과 실제 코드가 어떻게 맞물려서 동작하는지 살펴보기

애플리케이션 종료 : (터미널에서)ctrl + c

애플리케이션을 실행하는 또 다른 방법
java  > PetClinicApplication 의 main 메소드 실행
주의할점*
: 자바의 메인 메소드를 하기 전에 메이븐 패키징을 꼭 해야 함.
 메이븐 패키징하는 과정 중에 프론트 관련 라이브러리를 생성해 내는 플러그인이 있는데 그 플러그인이 동작해야만 로컬호스트에서 꺠지지 않고 온전하게 동작할 수 있음.
따라서 한 번이라도 메이븐 페키징을 하고 난 뒤로부터는 자바 애플리케이션을 실행/종료 해도 됨.

끝마치며)
스프링 펫클리닉이라는 스프링 예제 프로젝트 깃 클론을 메이븐으로 빌드하고 인텔리 제이로 읽어들여서 메이븐을 사용해서 빌드를 하고 애플리케이션을 실행

애플리케이션 두 가지 방법.
1.콘솔창에 java -jar을 사용하는 방법
2.인텔리J에서 펫 클리닉 에플리케이션이라는 메인 메소드를 실행해서 애플리케이션을 실행하는 방법.

애플리케이션 종료시 찍히는 로그들.
Closing JPA EntityManagerFactory for persistence unit 'default'
 Shutting down ExecutorService 'applicationTaskExecutor'
HikariPool-1 - Shutdown initiated...
 HikariPool-1 - Shutdown completed.
 Cache 'vets' removed from EhcacheManager.

서버포트 변경
java --server.port=하고싶은 포트번호


