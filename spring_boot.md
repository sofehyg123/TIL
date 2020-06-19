# Spring_boot

# 목차
---------
0. [자동설정 이해](자동설정-이해)
1. [자동설정 만들기](#자동설정-만들기)  
2. [내장 웹서버의 이해](#내장-웹서버의-이해)  
  
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
  - <dependencyManagement> 를 작성해서 의존성을 관리해준다.  
  
  ```
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

* 기본개념 파악1([출처](https://maven.apache.org/guides/mini/guide-naming-conventions.html))  
  1.groupId   
  2.artifactId  
  3.version  

  
  2. Scan할 패키지 위치에 클래스 생성(variable, getter/setter, toString()  
  
  3. @Configuration 애노테이션이 붙은 클래스 작성.  @Bean 등록할 메서드, 데이터가 담기는 객체를 생성 후 주입 및 리턴해줄 것.
  
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
  
  4. maven install : 이 프로젝트를 빌드해서 jar 생성된 것을 다른 매이븐 프로젝트에서 쓸 수 있도로 로컬 메이븐 저장소에 설치.  
  
  5. 다른 프로젝트에서 이 것을 쓰고 싶을 때 pom.xml에서 받을 의존성을 작성. 그럼 외부 라이브러리에서 자동으로 추가된다.
  
  6. Bean이 있는지 확인 하기 위해서 Runner 클래스를 만들고 ApplicationRunner 을 구현해야 한다. 
  
```  
  @Component
  public class HolomanRunner implements ApplicationRunner {
            //ApplicationRunner는 스프링 부트를 만들고 실행했을 떄 자동으로 빈으로 만들고 싶을 때 쓰는 기능.

  @Autowired
  Holoman holoman;

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

    

  
