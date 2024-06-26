> 빈 스코프
- 빈 스코프란?
  - 스코프 : 빈이 존재할 수 있는 범위
  - 스프링 빈은 기본적으로 싱글톤 스코프로 생성됨.
  - **싱글톤 스코프** : 스프링 컨테이너 시작, 종료까지 유지되는 가장 넓은 범위의 스코프
    - 스프링 컨테이너 생성 시점에 초기화 메서드 실행됨.
  <br/></br>
  - **프로토타입 스코프**
    - 항상 새로운 인스턴스 생성
      - 싱글톤은 항상 같은 인스턴스 스프링 빈 반환
    - 클라이언트한테 반환하고, 그 이후 관리 X 
      - ( 클라이언트가 관리해야 함. )
    - 프로토타입 빈의 생성, 의존관계 주입까지만 관여
    - 매우 짧은 범위의 스코프
    - **스프링 컨테이너는**
      - 프로토타입 빈 생성
      - 의존관계 주입
      - 초기화
    - 까지만 처리한다.
    - **그래서, `@PreDestroy` 같은 종료 메서드 호출 X**
      - 직접 클라이언트가 메서드를 호출해야 한다.
    - 스프링 컨테이너에서 **빈 조회 시** 생성, 초기화 메서드 실행
    - `@Scope("prototype")` : 자동 등록
    - 수동 등록
      - ~~~java
        @Scope("prototype")
        @Bean
        PrototypeBean HelloBean(){
          return new HelloBean();
        }
<br/></br>
- 프로토타입 빈과 싱글톤 빈을 함깨 사용 시 문제점
  - 스프링은 디폴트가 싱글톤 빈을 사용하므로, 싱글톤 빈이 프로토타입 빈을 사용하게 된다.
    - 그런데, 싱글톤 빈은 생성 시점에만 주입 받기 때문에, 프로토타입 빈이 새로 생성되기는 하지만, 싱글톤 빈가 함께 계속 유지되는 것이 문제다.
<br/></br>
- 문제 해결
  - DL(Dependency Lookup, 의존관계 조회)
    - 의존관계를 외부에서 주입받는게 아니라, 직접 필요한 의존관계를 찾는 것.
  - `ObjectProvider`
    - 지정한 빈을 컨테이너에서 대신 찾아주는(DL) 기능 정도만 제공.
    - ~~~java
      private ObjectProvider<PrototypeBean> p;

      public int logic(){
        PrototypeBean prototypeBean = p.getObject();
        prototypeBean.add();
        return p.getCount();
      }
    - `getObject()` : 내부에서 스프링 컨테이너를 통해 해당 빈을 찾아 반환한다. ( **DL** )
      - 항상 새로운 프로토타입 빈이 생성됨.

  - JSR-330 Provider
    - `Provider.get()` : 자바 표준

  - 보통 싱글톤 빈을 사용하기는 함. 프로토타입 빈 거의 사용 안 함.
<br/></br>
- 웹 스코프
  - 웹 환경에서만 동작
  - 스프링이 해당 스코프의 종료시점까지 관리( 종료 메서드 호출됨 )
  - 종류
    - request
      - HTTP 요청 하나가 들어오고 나갈 때까지 유지되는 스코프
      - **각각의 HTTP 요청마다 별도의 빈 인스턴스 생성, 관리**
    - session
      - HTTP Session과 동일한 생명주기를 가지는 스코프
    - application
      - ServletContext 와 동일한 생명주기를 가지는 스코프
    - websocket
      - 웹소켓과 동일한 생명주기를 가지는 스코프

  - request 스코프
    - UUID
      - Universally Unique IDentifier
      - 네트워크 상에서 고유성이 보장되는 id를 만들기 위한 표준 규약
    - `@Scope(value = "request")`
<br/></br>
- 스코프와 프록시
  - `@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)`
    - 적용 대상이 클래스면 TARGET_CLASS 선택
    - 적용 대상이 인터페이스면 INTERFACES 선택
    - (내 클래스를 상속 받은) 가짜 프록시 클래스를 만들어두고 request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입가능
    - **가짜 프록시 객체는**
      - 내부에 진짜 내 클래스를 찾는 방법을 알고 있다.
      - 요청이 오면 그때, 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.
      - 원본 클래스를 상속받아 만들어졌기 때문에 이 객체를 사용하는 클라이언트는 원본인지 아닌지도 모르게 , 동일하게 사용 가능 ( 다형성 )
      - 마치 싱글톤처럼 동작한다.(결국 다르게 사용되기 때문에 주의해야 함.)
    - 꼭 필요한 곳에만 최소화해서 사용하자, 유지보수 어려워짐
