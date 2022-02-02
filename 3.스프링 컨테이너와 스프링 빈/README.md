## 스프링 핵심 원리 - 기본편
>#### 스프링 컨테이너와 스프링 빈

- 스프링 컨테이너 생성
  - `ApplicationContext` : 인터페이스 , **스프링 컨테이너**
  - `new AnnotationConfigApplicationContext(AppConfig.class)` : 위의 인터페이스의 구현체이다.
  - 생성할 때, 구성정보 `AppConfig.class` 를 지정해주어야 한다.
  - 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해 스프링 빈을 등록한다.
  - *빈 이름은 항상 다른 이름을 부여*해야 한다.
  - 스프링 컨테이너는 설정 정보를 참고해 의존관계를 주입한다.
<br/></br>
- 컨테이너에 등록된 모든 빈 조회  
  - `getBeanDefinition()` : 빈에 대한 메타데이터

  - ~~~java
    if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION)
    ~~~
    - `.ROLE_APPLICATION` : 직접 등록한 애플리케이션 빈
    - `.ROLE_INFRASTRUCTURE` : 스프링이 내부에서 사용하는 빈
<br/></br>
- 스프링 빈 조회
  - 기본
    - `ac.getBean(빈이름, 타입)`
  - `assertThrows` : 예외 처리 테스트
  - 동일한 타입이 둘 이상일 경우
    - 같은 타입의 빈이 둘 이상이면 중복 오류 발생
      - 빈 이름을 지정하자
    - `ac.getBeanOfType()` : 해당 타입의 모든 빈 조회
  - 상속 관계
    - 부모 타입으로 조회하면, 자식 타입도 함께 조회된다.
    - 그래서, 모든 자바 객체의 최고 부모인 `Object` 타입으로 조회하면 모든 스프링 빈을 조회한다.
    - ~~~java
      @Test
      @DisplayName("부모 타입으로 모두 조회하기 - Object")
      void findAllBeanByObjectType(){
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
      }
    
- 스프링 컨테이너
  - BeanFactory
    - 스프링 컨테이너의 최상위 인터페이스
    - 스프링 빈 관리, 조회
  <br/></br>
  - ApplicationContext 
    - 빈팩토리 기능을 모두 상속받아 제공
    - 다른 부가 기능
    <span style="color:grey">
      - MessageSource I
          - 국제화 기능
            - 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
        - EnvironmentCapable(환경변수) I
          - 로컬, 개발, 운영 등을 구분해서 처리
        - ApplicationEventPublisher I
          - 이벤트 발행, 구독하는 모델을 지원
        - ResourceLoader I
          - 파일, 클래스패스, 외부 등에서 리소스 조회
          </span>

  - *거의 ApplicationContext를 사용한다.*
<br/></br>
- 스프링 빈 설정 메타 정보 
  - BeanDefinition
    - 인터페이스
    - 추상화
    - 빈 정보에 대해 추상화 시킨 것
    - 역할과 구현을 개념적으로 나눈 것
    - 빈 설정 메타 정보
    - `@Bean` : 하나씩 붙일 때마다 그거에 대한 메타 정보가 생성된다.
      - 스프링 컨테이너는 이 메타 정보를 기반으로 스프링 빈 생성
    - 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용한다는 것.