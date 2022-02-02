> 싱글톤 컨테이너
- 왜 웹 애플리케이션에서 싱글톤을 사용하는가?
  - 순수한 DI 컨테이너인 AppConfig는 요청을 할 때마다 객체를 새로 생성한다.
  - 메모리 낭비가 심하기 때문에
  - 해당 객체가 딱 1개만 생성되고, 공유하도록 설계 -> **싱글톤 패턴**
<br/></br>
- **싱글톤 패턴**
  - 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
  - 객체 인스턴스를 2개 이상 생성하지 못하게 막아야 함
    - private 생성자를 사용해 외부에서 임의로 new 를 사용하지 못하게 막는다.
    - ~~~java
      public class SingletonService {

        private static final SingletonService instance = new SingletonService();

        public static SingletonService getInstance(){
          return instance;
        }

        private SingletonService(){
        }
        
        public void logic(){
            System.out.println("싱글톤 객체 로직 호출");
        }
      }
    - `getInstance()` 만을 호출해서 싱글톤서비스 객체를 사용할 수 있다.
    - 호출할 때마다 같은 객체 인스턴스를 반환한다.
  - 문제점
    - 클라이언트가 구체 클래스에 의존함
    - OCP, DIP 위반
    - 테스트 어려움
    - 유연성 떨어짐
    - 안티 패턴
<br/></br>
- 싱글톤 컨테이너
  - 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤으로 관리한다.
  - 싱글톤 레지스트리 : 싱글톤 객체 생성, 관리 기능
  - 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다.
  - 고객의 요청이 올 때마다 이미 만들어진 객체(동일한 객체)를 공유해 효율적으로 재사용 가능
<br/></br>
- **싱글톤 방식 주의점**
  - 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에, 싱글톤 객체는 상태유지(stateful)하게 설계 X
  - 무상태(stateless)로 설계해야 함.
    - 특정 클라이언트에 의존적 필드 있으면 안됨.
    - 특정 클라이언트가 값을 변경할 수 있는 필드 있으면 안됨.
    - 가급적 읽기만 가능해야 함.
    - 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
  - 스프링 빈의 필드에 공유값을 설정하면 큰 장애 발생 가능
  - 공유필드는 항상 조심해야 한다.
<br/></br>
- @Configuration 과 바이트코드 조작
  - 스프링 컨테이너에서 내가 만든 AppConfig를 상속받아 , 임의의 다른 클래스를 만들어 싱글톤이 보장되도록 해준다.
  - `AnnotationConfigApplicationContext()` 에 파라미터로 넘긴 값은 스프링 빈으로 등록된다.
  - `@Bean`만 사용해도 스프링 빈으로 등록되지만, 싱글톤 보장 X
  - *스프링 설정 정보는 항상 `@Configuration`을 사용하자.*