## Learning-English

1. [Using the “default” Package](#using-the-default-Package)  
2. [Use another Web Server](#use-another-web-sever)
  
***
    
## Using the “default” Package
  
  
>When a class does not include a package declaration, it is considered to be in the “default package”.  
* 패키지 선언이 포함되어 있지 않은 클래스일 때, 기본 패키지로 간주된다.  
  + be considered to : ~로 간주되다.  
    
>The use of the “default package” is generally discouraged and should be avoided.  
* 기본 패키지의 사용은 일반적으로 좌절되고(?) 피해야 한다.  
* 구글번역: 기본 패키지의 사용은 일반적으로 권장되지 않으므로 피해야합니다.  
  + discouraged : 의욕을 꺾다,좌절시키다,낙담시키다,단념시키다,막다  
* 기본 패키지의 사용은 일반적으로 (좌절되니까) 권장하지 않으므로 피해야 한다. and 뒤에 동사가 나왔다는 것은 어떤 이유의 결과로 해석해야 하는 건가?  

>It can cause particular problems for Spring Boot applications that use the @ComponentScan,  
* 특정 문제들을 야기합니다. / 스프링 부트 애플리케이션에 대해 / that이하의 애노테이션(@)들을 사용하는  
>@ConfigurationPropertiesScan, @EntityScan, or @SpringBootApplication annotations, since every class from every jar is read.  
* 모든 jar로부터 모든 클래스 읽는 이후로(?)  
  
* 구글번역:  
클래스에 패키지 선언이 포함되어 있지 않으면 "기본 패키지"로 간주됩니다. "기본 패키지"의 사용은 일반적으로 권장되지 않으므로 피해야합니다.  
모든 jar의 모든 클래스를 읽으므로 @ComponentScan, @ConfigurationPropertiesScan, @EntityScan 또는 @SpringBootApplication  
주석을 사용하는 Spring Boot 애플리케이션에 특정 문제가 발생할 수 있습니다.  
* 내 해석:
패키지 선언이 포함되어 있지 않은 클래스일 때, 기본 패키지로 간주된다. 기본 패키지의 사용은 일반적으로 좌절되고(?) 피해야 한다.  
모든 jar로부터 모든 클래스 읽는 이후로 @ComponentScan, @ConfigurationPropertiesScan, @EntityScan,    
or @SpringBootApplication annotations 사용하는 스프링 부트 애플리케이션들에게 특정 문제를 야기할 수 있다.  

***

## Use another Web Server  
  

Many Spring Boot starters include default embedded containers.  
  
For servlet stack applications, the spring-boot-starter-web includes Tomcat by including spring-boot-starter-tomcat, but you can use spring-boot-starter-jetty or spring-boot-starter-undertow instead.  
  
For reactive stack applications, the spring-boot-starter-webflux includes Reactor Netty by including spring-boot-starter-reactor-netty, but you can use spring-boot-starter-tomcat, spring-boot-starter-jetty, or spring-boot-starter-undertow instead.  
  
When switching to a different HTTP server, you need to exclude the default dependencies in addition to including the one you need. Spring Boot provides separate starters for HTTP servers to help make this process as easy as possible.  
  
The following Maven example shows how to exclude Tomcat and include Jetty for Spring MVC:  
  
<배운 점>  
1. 문장 구조 파악. => 부사라서 intead 위치를 마지막에 둘 수 있구나. 이런 표현 방식이 부담스럽지 않은 건가? 자연스러운 표현방식인건가? 메모.
2. When 다음에 동사가 오네? 뒤에 목적어 전치사로 to가 올 수 있구나. need 다음에도 to가 많이 오네? to는 목적을 얘기할 때 많이 쓰는 전치사인가?
3. separate 형용사로 쓰임. 
4. to help와 한 묶음, 그래서 뒤에 동사인 make를 쓸 수 있는건가?
<단어 정리>  
  -For : ~겅우, ~에서 이런 식으로 의역.
  
