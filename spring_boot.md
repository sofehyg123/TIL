# Spring_boot

# 목차
---------
0. [자동설정 이해](자동설정-이해)
1. [자동설정 만들기](#자동설정-만들기)  
2. [내장 웹서버의 이해](#내장-웹서버의-이해)  
3. [내장 웹서버의 응용1](#내장-웹서버의-응용1)
4. [내장 웹서버의 응용2](#내장-웹서버의-응용2)
  
***  
  
# 자동설정 이해  
  + 출처  
    1. 백기선님 스프링부트 강좌
    2. [AutoConfig에 대한 이해](https://blog.naver.com/PostView.nhn?blogId=sharplee7&logNo=221591429432&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)  
  + 빈은 두 단계에 걸쳐 읽힘.    
    1. ComponentScan  
        - @ComponentScan 혹은 @SpringBootApplication 애노테이션이 붙은 클래스가 위치한 패키지부터 하위패키지의 클래스들을 Scan한다.  
        - Scan은 아래 애노테이션들이 붙은 클래스를 Spring Bean으로 등록한다.  
        - @Component @Configuration @Repository @Service @Controller @RestController.  
        - 1차적으로 진행하는 스캔임.  
    2. EnableAutoConfiguration  
        - 2차적으로 진행되는 스캔인데 빈으로 등록한다는 개념은 비슷하지만 동작방식이 조금 다름.  
        - Maven의 의존성 관계에 있는 jar 파일을 기반으로 Configuration을 자동설정함.  
        - @EnableAutoConfiguration이 자동으로 등록하려는 Bean들은 External Library 의 spring-boot-auticonfigure:2.x.x.RELEASE > META-INF > spinrg.factories 안에 정의되어 있으므로  이 곳에다 configuration을 정의하면 Bean을 등록해줄 수 있게 @EnableAutoConfiguration이 스캔할 것임.  
        - 근데 factories에 EnableAutoConfiguration 키값을 적어야 동작할 듯?
        - 그래서 EnableAutoConfiguration이 동작할 때 이 키값에 해당되는 configuration을 구동시키는 거지.
        - 근데 전부 다 구동되는 것은 아니고 조건에 맞게 구동이 된다는 것.
  
# 자동설정 만들어 보기
  
  1. pom.xml  
  - <dependencies> 태그 안에 autoconfigure이라는  의존성을 작성하고  
  - <dependencyManagement> 를 작성해서 의존성의 버전을 관리해주는 영역을 추가해줬다.  
  
  ```
  //pom.xml에서..
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure-processor</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.0.3.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
  ```
***  
> 기본개념 파악1([출처](https://maven.apache.org/guides/mini/guide-naming-conventions.html))  
  1.groupId   
  2.artifactId  
  3.version  
***  
  
  2. Scan할 패키지 위치에 클래스 생성(variable, getter/setter, toString())  
  
  3. @Configuration 애노테이션이 붙은 Configuration클래스 작성.    
     @Bean 등록할 메서드 및 객체를 주입할 클래스 인스턴스 생성 후 헤당 인스턴스의 setter 메서드르 호출해서 데이터를 주입 및 객체 리턴해줄 것.  
  
```  
  
  @Configuration
  public class HolomanConfiguration {

    @Bean
    public Holoman holoman(){
        Holoman holoman = new Holoman();
        holoman.setHowLong(5);
        holoman.setName("Hyeokki");
        return holoman;
    }
  }
```
  4. 해당 클래스의 자동 설정을 읽어 들일 수 있도록 resources 밑에 META-INF 디렉토리를 만든 후 spring.factories 파일에  EnableAutoConfiguration과  
  FQCN 작성.  
```
<spring.factories>

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ //자동설정 key
    me.practice.HeymanConfiguration // FQCN (Full Quilified Class Name)
```
  5. maven install : 이 프로젝트를 빌드해서 jar 생성된 것을 다른 매이븐 프로젝트에서 쓸 수 있도로 로컬 메이븐 저장소에 설치.  
     변경이 일어나면 install을 해주고 의존성 받을 프로젝트에는 maven refresh를 해줘야 반영이 됨.  
  
  6. 다른 프로젝트에서 local maven 저장소에 저장된 Bean을 쓰기 위해서 pom.xml에서 자동설정을 작성한 프로젝트의 의존성을 작성해야함.  
     그럼 외부 라이브러리에서 자동으로 추가된다.  
```
<의존성을 받을 프로젝트의 pom.xml>
        <dependency>
            <groupId>me.practice</groupId>
            <artifactId>yun-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```
  
  7. Bean이 있는지 확인 하기 위해서 Runner 클래스를 만들고 ApplicationRunner 을 구현해야 한다. 
  
```  
  @Component
  public class HolomanRunner implements ApplicationRunner {
            //ApplicationRunner는 스프링 부트를 만들고 실행했을 떄 자동으로 빈으로 만들고 싶을 때 쓰는 기능.

  @Autowired
  Holoman holoman;
  //Bean을 등록하지 않았지만 maven에서 의존성을 받아왔기 때문에 Ioc컨테이너에 있는 Bean을 Autowired로 꺼내서 쓸 수 있다.

  @Override
  public void run(ApplicationArguments args) throws Exception {
      System.out.println(holoman);
  }
```  
      
* 해당 프로젝트에서 AutoConfiguration에서 설정된 Bean과 같은 타입으로 직접 @Bean으로 등록한 경우 autoConfiguration에서 설정된 Bean이 우선시 된다.  
* @ComponentScan에서 읽는 @Bean이 우선시 되어야 하기 때문에 이런 현상을 해결해야 한다.  
* 그럴 때 @ConditionalOnMissingBean 애노테이션을 autoConfiguration으로 읽는 @Bean 밑에다가 추가하면 이런 현상을 해결할 수 있다.
* 이 의미는 해당 타입이 @Bean 등록이 되어 있지 않으면 이 @Bean을 등록하라는 뜻.
* @ComponentScan에서 직접 등록한 @Bean이 있기 때문에 다음 단계인 autoConfiguration 스캔할 때에는 해당 타입의 Bean울 등록하지 않게 되는 것.
* 이 때 maven maven 로컬저장소에 다시 install 해서 업데이트를 해줘야 수정된 결과가 maven에 반영된다.
  
***
  * 만약 일일이 직접 등록하지않고 내가 원하는 속성만 변경하고 싶을 때 resource > application.properties 에 해당 속성을 적어주면 됨.  
  
```   
  <resources/application.properties>
  holoman.name = yunhyeokki
  holoman.how-long = 10
```   
  * 이것이 적용되게 할려면 autoconfiguration 설정을 위한 프로젝트에서 
  ```
  1.
  <HoloProperties클래스>
  @ConfigurationProperties("holoman") : holoman으로 정하고 name과 howlong을 getter/setter한다.
  
  2.
  <HolomanConfiguration클래스>
  //이 등록된 properties를 사용할 수 있게 하기 위해서 @EnableConfigurationProperties(HoloProperties.class)을 등록한다.
  @EnableConfigurationProperties(HoloProperties.class)
  public class HolomanConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public Holoman holoman(HoloProperties holoProperties){
        Holoman holoman = new Holoman();
        holoman.setHowLong(holoProperties.getHowLong());
        holoman.setName(holoProperties.getName());
        return holoman;
    }
  }  
    
  ```  
  
  * 그리고 maven install 해서 업데이트.  
  * propertis 자바 파일은 변경하는 안되는 클래스이기 때문에 신중하게 만들어야 함. 맵핑되는 클래스 이름이나 값이 바뀌면 properties값도 바꿔줘야 하는데 그것이 많으면 일일이 수정해야 하기 때문.  
  
  
    
    
* [목차로 돌아가기](#목차)  
***

# 내장 웹서버의 이해  

* 스프링 부트 자체는 일종의 툴임. 내장 서블릿 컨테이너를 쉽게 쓸 수 있도록.  
* ServletWebServerFactoryAutoConfiguration 서블릿 웹서버를 자동 설정으로 생성하는 configuration.  
    + 내장 톰캣을 autoconfiguration에서 만들어서 사용할 수 있음.  
        - 확인: spring.factories > ServletWebServerFactoryAutoConfiguration > embededTomcat 에서 확인.  
    + 이 클래스 안에는 TomcatServletWebServerFactoryCustomize 서버 커스터 마이징 기능을 하는 @Bean도 등록되어 있음.  
* DispatcherServletAutoConfiguration : DispatcherServlet을 생성하고 서블릿 컨테이너에 등록하는 일을 수행.  
  
***
# 내장 웹서버의 응용1

* 웹서버 동작하지 않기  
  + 코드로 작성하기  
  ```
  public class Application (){
      public static void main(String[] args){
          SpringApplication application = new SpringApplication(application.class)
          application.setWebapplicationType(WebApplicationType.NONE);
          application.run(args);
      }
  }
  ```
  + application.properties 에서 작성하기  
  spring.main.web-application-type=none  
  
* 웹 서버 포트 변경  
  + application.properties
    ```  
    server.port = 7070(주고 싶은 포트 번호)
    // server.port = 0 으로 해주면 랜덤 포트로 띄어서 찾아줌.(로그 기록에서 찾을 수 있음).
    ```  
  + 설정한 포트(랜덤 포트 포함)을 어디다 쓸 것인가?  
      -ApplicationListner<ServletWebServerInitializedEvent>  
    
```  
  @Component
    public class PortListener  implements ApplicationListener<ServletWebServerInitializedEvent> {
    @Override
    public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
               
        ServletWebServerApplicationContext applicationContext =         
                                      servletWebServerInitializedEvent.getApplicationContext();

        System.out.println(applicationContext.getWebServer().getPort());
        //서블릿 웹서버 context이기 떄문에 서버 정보를 알 수 있음. 그럼 포트 정보도 알 수 있겠지?  
    }
  }
```   
      - 웹서버가 생성되면 ServletWebServerInitializedEvent 통해 @Override된 메서드가 호출됨.  
      - 이 메서드 안에서 port 정보를 얻을 수 있고, 웹서버거 시작(=생성)되면 포트 정보를 확인 할 수 있다.  
```   
<log 기록>
Jetty started on port(s) 51300 (http/1.1) with context path '/' //1
51300                                                           //2
//1 =>웹서버가 시작(=생성)됨. 이 때 이벤트에 의해 호출된 메서드가 동작하고 거기에 getPort해서 
//    정보를 얻어올 수 있음을 바로 다음에 찍히는 로그를 보면 알 수 있다.
```   
  
***

# 내장 웹서버의 응용2  
  
  
* 내장웹서버에 HTTP1 과 HTTP2를 적용하는 방법을 알아보기.  
    + HTTP1(SSL) 적용
        - 사용하려면 keystore 을 만들어야 함.[참고](https://gist.github.com/keesun/f93f0b83d7232137283450e08a53c4fd)  
        - starting to set with keystore.
        - resources > application.properties 안에 밑 코드 작성.
          ```  
          server.sll.key-store=keystore.p12
          server.ssl,key-store-type=PKCS12
          server.ssl.key-store-password=123456
          server.ssl.key-alias=yun
          ```    
        - 스프링 부트에 톰캣이 사용하는 connector 가 하나만 등록되어 있는데 이 connector에 SSL적용해줌.  
        - 이제 앞으로 모든 요청은 https 로 요청  
        - 클라이언트가 요청하면 서버는 인증서를 보내는데 이 인증서의 key를 모르면 접근 거부됨.  
        - curl -I -http2 http://localhost:8080/hello ->400 error  
        - curl -I -http2 https://localhost:8080/hello  
        - 공인 인증서가 아니기 때문에 아래처럼 메세지가 뜨는데 -k 옵션값을 추가해주면 결과값을 확인할 수 있음.  
        >message
         If this HTTPS server uses a certificate signed by a CA represented in
         the bundle, the certificate verification probably failed due to a
         problem with the certificate (it might be expired, or the name might
         not match the domain name in the URL).
         If you'd like to turn off curl's verification of the certificate, use
         the -k (or --insecure) option.
         HTTPS-proxy has similar options --proxy-cacert and --proxy-insecure.  
         
         ```  
         - curl -I -k --http2 https://localhost:8080/hello  
         ```  
    + HTTP 커넥터, 코딩으로 설정하기  
      기존에 하나였던 connector을 SSL로 덮어서 쓰고 있기 때문에 새롭게 Connector 만들어서 http로 쓰기.  
      [참고출처](https://github.com/spring-projects/spring-boot/blob/v2.0.3.RELEASE/spring-boot-samples/spring-boot-sample-tomcat-multi-connectors/src/main/java/sample/tomcat/multiconnector/SampleTomcatTwoConnectorsApplication.java)    
      - 기존의 SSL 포트를 변경해서 http 포트를 8080 쓰게끔 하자.  
      - application.properties > server.port = 8080  
    + HTTP2 적용  
        - application.properties 에 server.http2.enabled=ture 을 추가해주면 되는데 웹 서버마다 제약사항이 다르니 공홈 확인.  
        - undertow 웹서버 사용하면 별다른 설정이 필요없음.  
        - 그리고 HTTP에 SSL이 기본적으로 적용되어 있어야 함.  
           
    
***
