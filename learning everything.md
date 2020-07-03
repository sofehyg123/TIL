# 모르는 거 알기  
결국은 타 블로그의 말들을 정리한 것에 불과하다.
## @Mockmvc  
---  
출처  
https://ktko.tistory.com/entry/스프링Spring-MockMvc-테스트  

---  
시작하기 앞서 의문을 갖고 시작해보자.  
* 컨트롤러에 대한 단위 테스트가 필요할까?  
  - 이런 의문은 컨트롤러의 역할들은 스프링MVC의 프레임워크 기능을 사용해야만 처리결과를 재대로 검증할 수 있기 때문에 단위 테스트가 필요 없을껄? 로 이어짐.  
  - 그럼에도 컨트롤러의 단위 테스트를 말함은 일반적인 단위테스트+ 스프링 MVC의 프레임워크 기능의 통합된 형태의 관점으로 봐야 한다는 점.  
  
* MockMvc가 클래스구나!  
  - 서버에 배포하지 않고 스프링MVC의 동작을 재현할 수 있다?  
  - 스프링MVC는 애플리케이션 구현할 때 애플리케이션 서버에 배포해야 하는 구나.  

* 요청 데이터 설정  
  - 스프링MVC 동작에 요청 데이터 설정이 여기선?  
  - MockMvcRequestBuilers 를 사용하기 위해서는 import 해주고~  
* 흐름  
  - MockMvc의 MockMvcRequestBuilders 사용하여 요청데이터 설정  
  - MockMvc는 DispatcherServlet에 요청을 보냄, 이 때 DispatcherServlet은 확장된 TestDispatcherSerlvet이다.  
  - DispatcherServlet으 요청을 받아 매핑정보를 보 그에 맞는 핸들러(컨트롤러) 메서드 호출(test가 아닌 main쪽 컨트롤러를 말함).  
  - @Test 메서드는 MockMvc가 반환하는 실행결과를 받아 실행 결과가 맞는지 검증.  
  

  
