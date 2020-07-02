# HTML & CSS  
[출처](https://www.edwith.org/htmlcss/lecture/16610/)

## 목차  
* [css](#css)  
***  

## CSS    
* CSS 구성
  + selector, property, value  
  ```  
  span { //selector 
    color:red; // property, value  
    };
  ```  
* CSS를 HTML에서 사용하는 방법  
  + inline > internal > external 의 우선순위 순서로 적용
    - inline  
      HTML태그 안에다가 적용  
      다른 CSS파일에 적용한 것 보다 가장 먼저 적용  
    - internal  
      style 태그로 지정  
      구조와 스타일이 섞이게 되므로 유지보수의 어려움  
      별도의 CSS파일을 관리하지 않아도 되며 서버에 CSS파일을 부르기 위해 별도의 브라우저가 요청을 보낼 필요가 없음  
    - external  
      외부파일(.css)로 지정  
