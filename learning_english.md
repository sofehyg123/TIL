## Learning-English

1. [Using the “default” Package](#Using-the-default-Package)
  
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
