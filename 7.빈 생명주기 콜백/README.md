> 빈 생명주기 콜백

- 시작
  - 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 종료 시점에 연결을 모두 종료하는 작업을 하려면, 객체 초기화와 종료 작업이 필요하다.
  - 스프링을 통해 이런 작업을 진행할 수 있다.
  - 스프링 빈은 객체생성 후, 의존관계 주입을 한다.
    - 이 과정이 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 된다.
    - 따라서, 초기화 작업은 의존관계 주입 후에 호출해야 한다.
    - 스프링은 의존관계 주입이 완료되면, 스프링 빈에게 콜백 메서드를 통해 초기화 시점을 알려주는 다양한 기능 제공 + 스프링 컨테이너 종료되기 직전에 소멸 콜백을 준다.
  - 스프링 빈의 이벤트 라이프사이클
    - 스프링 컨테이너 생성
    - 스프링 빈 생성
    - 의존관계 주입
    - 초기화 콜백
      - 빈 생성, 빈 의존관계 주입 완료된 후 호출
    - 사용
    - 소멸 전 콜백
      - 빈 소멸 직전 호출
    - 스프링 종료
    - 순으로 진행된다.

  - 객체 생성과 초기화는 분리하자.

  - 인터페이스
    - `InitializingBean` 
      - `afterPropertiesSet()` : 의존관계 주입이 끝나면 호출해주겠다. (초기화)
    - `DisposableBean`
      - `destroy()` :  소멸
    - 스프링 전용 인터페이스에 의존한다.
    - 초기화, 소멸 메서드의 이름 변경 X
    - 외부 라이브러리에 적용 X
    - 거의 사용 안 함.

  - 빈 등록 초기화, 소멸 메서드 지정
    - `@Bean(initMethod = "init", destroyMethod = "close")`
    - 메서드 이름 자유롭게 지정
    - 스프링에 의존 X
    - 외부 라이브러리에도 적용 가능

  - **애노테이션**
    - `@PostConstruct`
    - `@PreDestroy`
    - ~~~java
      @PostConstruct
      public void init() {
      System.out.println("NetworkClient.init");
      connect();
      call("초기화 연결 메시지");
      }

      @PreDestroy
      public void close() {
      System.out.println("NetworkClient.close");
      disConnect();
      }
    - 이 방법을 쓰자.
    - 외부 라이브러리 적용은 안된다.
      - @Bean 의 initMethod, destroyMethod 사용 하자.