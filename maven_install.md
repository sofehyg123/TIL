# [출처](https://araikuma.tistory.com/444)
* 리눅스 공부하자..http://throughkim.kr/2016/12/22/linux-4/
## Build tool  
  + 프로젝트 생성, 테스트 빌드, 배포 등 기본적인 작업을 위하 개발도구  
  + 같으 빌드도구로 만든 다양한 프로젝트들도 쉽게 마이그레이션이 가능함.  
## maven?  
  + 중앙 저장소 역할  
  + Java에서 사용되는 다양한 라이브러리와 프로그램들을 집중 관리하는 서버에서 Maven은 이 서버에 접속하여,  
    필요한 프로그램을 다운로드하여 프로젝트에 설치한다. 즉, 개발자가 라이브러리 등을 직접 설치하거나 관리할 필요가 없다.  
  + maven은 XML로 빌드 파일 만든다.  


  + Download  
    - https://maven.apache.org > Download > File > Binary tar.gz archive "LINK 클릭"  
    - 어느 경로에서든 실행할 수 있게 환경변수 설정을 해줘야 함.  
  + 환경변수설정  
    - terminal > vi ~/.bash_profile
    - 비정상 종료될 때 그 파일을 수정하고 저장하기 전에 x.swp으로 저장되기 때문에 rm으로 삭제하자.
    ```  
    #JAVA
    export jAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home
    export PATH:$PATH:$JAVA_HOME/bin
    #MAVEN
    export MVN_HOME=/Users/yunhyeoggi/Downloads/apache-maven-3.6.3
    export PATH=$PATH:$MVN_HOME/bin
    
    명령모드:wq
    ```  
    - 버전 변경한것 적용하기  
    ```  
    $ source .bash_profile
    ```  
    - 설치된 mvn 버전확인하기
    ```  
    mvn -version
    
    //결과
    Apache Maven 3.6.3
    Maven home: 어쩌구저쩌구
    Java version: 어쩌구저쩌구
    OS name: "mac os x", family: "mac"
    ```  
