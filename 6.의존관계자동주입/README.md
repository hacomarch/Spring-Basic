> 의존관계 자동 주입

- 다양한 의존관계 주입 방법
  - 생성자 주입
    - 생성자 호출 시점에 딱 1번만 호출되는 것이 보장된다.
    - 불변, 필수 의존관계에 적용
    - **생성자가 1개만 있으면, @Autowired 를 생략해도 자동 주입된다.** (스프링 빈에만 해당)
  - 수정자 주입(setter)
    - 선택, 변경 가능성이 있는 의존관계에 사용
    - ~~~java
      @Autowired
      public void setMemberRepository(MemberRepository memberRepository) {
      this.memberRepository = memberRepository;
    - `@Autowired` 는 주입할 대상이 없으면 오류 발생
      - 주입할 대상 없어도 동작하게 하려면 `@Autowired(required = false)` 로 지정하면 된다.
  - 필드 주입
    - 사용하지말자.
      - 테스트 코드에서는 사용
  - 일반 메서드 주입
    - 잘 사용 안 함.
<br/></br>
- 옵션 처리
  - `@Autowired(required = false)` : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안됨.
  - `@Nullable` : 자동 주입할 대상이 없으면 `null` 이 입력된다.
  - `Optional<>` : 자동 주입할 대상이 없으면 `Optional.empty` 가 입력된다.
<br/></br>
- 생성자 주입을 선택해야 하는 이유
  - **불변**
    - 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.
    - 생성자 주입은 객체 생성 시 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계 가능!

  - 누락
    - 생성자 주입을 사용하면 주입 데이터 누락 시 컴파일 오류가 발생한다. 그러고 바로 어떤 값을 필수로 주입해야 하는지 알 수 있다.

  - 변수에 `final` 키워드
    - 생성자에서만 값을 넣어줄 수 있음 
    - 생성자 주입 방식에서만 사용가능하다.

  - 항상 생성자 주입을 선택하기.
<br/></br>
- 롬복과 최신 트랜드
  - 롬복은 라이브러리다.
  - 게터, 세터를 자동으로 만들어준다. 등등 많은 기능이 있음.
    - `@Getter` , `@Setter`
  - `@RequiredArgsConstructor` : `final`이 붙은 변수들을 가지고 생성자를 자동으로 만들어준다.
<br/></br>
- 조회할 빈이 2개 이상일 경우
  - @Autowired 는 타입으로 조회한다.
  - 따라서, 2개 이상이면 오류 발생

- 해결방법
  - @Autowired 필드명 매칭
    - 필드 명을 빈 이름으로 변경
    - ~~~java
      @Autowired
      private DiscountPolicy discountPolicy;
      ↓
      @Autowired
      private DiscountPolicy rateDiscountPolicy;
    - 타입 매칭 결과가 2개 이상일 때, 필드명, 파라미터 명으로 빈 이름 매칭
    - 먼저 타입 매칭 시도하고, 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능
  - `@Qualifier` 사용
    - 추가 구분자를 붙여주는 방법
    - 주입 시 추가적인 방법을 제공하는 것일 뿐, 빈 이름 변경은 아님.
    - 모든 코드에 @Qualifier 를 붙여줘야 하는 단점이 있다.
    - ~~~java
      @Component
      @Qualifier("mainDiscountPolicy")
      public class RateDiscountPolicy implements DiscountPolicy{}

      @Autowired
      public OrderServiceImpl(@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy){ ... }

      @Autowired
      public DiscountPolicy setDiscountPolicy(@Qaulifier("mainDiscountPolicy") DiscountPolicy discountPolicy){
        return discountPolicy;
      }

      @Bean
      @Qualifier("mainDiscountPolicy")
      public DiscountPolicy discountPolicy(){return new ...}
  - `@Primary` 사용
    - 우선순위를 정하는 방법
    - ~~~java
      @Component
      @Primary
      public class RateDiscountPolicy implements DiscountPolicy{}

      @Component
      public class FixDiscountPolicy implements DiscountPolicy{}
  - @Primary 는 기본값처럼 동작하고, @Qualifier 는 매우 상세하게 동작한다.
  - 스프링은 자동보다는 수동이, 넓은 범위 선택권보다 좁은 범위 선택권이 우선순위가 높다.
  - 따라서, @Qualifier의 우선권이 높다.
<br/></br>
- 애노테이션 직접 만들기
  - `@Qualifier("mainDiscountPolicy")` 는 컴파일 시 타입 체크가 안되기 때문에, 애노테이션을 직접 만들어 문제 해결 가능
<br/></br>
- 조회한 빈이 모두 필요할 때, List, Map
  - `Map<String, DiscountPolicy>`
  - `List<DiscountPolicy>`
<br/></br>
- 자동, 수동의 올바른 실무 운영 기준
  - 편리한 자동 기능을 기본으로 사용하자.
    - OCP, DIP 지킬 수 있음.
  - 수동 빈 등록은 언제 사용할까?
    - 업무 로직 빈 : 비즈니스 요구사항 개발 시 추가 or 변경됨
    - 기술 지원 빈 : 기술적 문제나 공통 관심사를 처리할 때 사용됨
    - 애플리케이션에 광범위하게 영향을 미치는 기술 지원 객체는 수동 빈으로 등록해서 설정 정보에 바로 나타나게 하는 것이 유지보수에 좋다.