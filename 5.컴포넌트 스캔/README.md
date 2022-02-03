> 컴포넌트 스캔
- 컴포넌트 스캔과 의존관계 자동 주입 시작하기

  - **컴포넌트 스캔** : 설정정보가 없어도 자동으로 스프링 빈 등록하는 기능
    -  `@Component`가 붙은 모든 클래스를 스캔해 스프링 빈으로 등록
    - 스프링 빈 기본 이름은 클래스 명을 사용하는데, 맨 앞글자만 소문자로 사용한다.
    - 스프링 빈 이름 직접 지정도 가능하다.
      - `@Component("memberService2")`
  - `@Autowired` : 의존 관계 자동 주입 기능
    - 생성자에서 여러 의존관계도 한번에 주입받을 수 있음.
    - 보통, 타입이 같은 빈을 찾아서 주입한다.
  - ~~~java
    @ComponentScan(
      excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class))
    public class AutoAppConfig{...}
    ~~~
    - `@ComponentScan` : 컴포넌트 스캔을 사용하겠다.
    - `Configuration`이 붙은 클래스들을 뺄거다.
    - `excludeFilters`를 이용해 설정 정보는 컴포넌트 스캔 대상에서 제외
<br/></br>
- 탐색 위치와 기본 스캔 대상

  - 탐색할 패키지 시작 위치 지정
    - ~~~java
      @ComponentScan(basePackages = "hello.core.member", "hello.core.order")
      ~~~
    - 패키지 위치를 설정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이 좋은 방법.
      - `hello.core` 에 AppConfig 같은 메인 설정 정보를 두고( *프로젝트 시작 루트 위치에 두는 것이 좋음* ), @ComponentScan 을 붙이고, basePackages 지정 생략

  - 컴포넌트 스캔 기본 대상
    - `@Controller`
    - `@Service`
    - `@Repository`
    - `@Configuration`
    - 을 붙여놓으면 자동으로 컴포넌트 스캔의 대상으로 들어간다.
    - 사실, 애노테이션에는 상속 관계라는 것은 없다. (스프링이 지원하는 기능일뿐, 자바 언어가 지원하는 기능은 아니다.)

- 필터 
  - `includeFilters` : 컴포넌트 스캔 대상을 추가로 지정
  - `excludeFilters` : 컴포넌트 스캔에서 제외할 대상 지정
  - FilterType 옵션
    - ANNOTATION : 기본값, 애노테이션을 인식해서 동작
    - ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식해서 동작
    - ASPECTJ
    - REGEX
    - CUSTOM
<br/></br>
- 중복 등록과 충돌
  - 자동 빈 등록 한 것 끼리 이름이 중복된다면,
    - 예외 발생 `ConflictingBeanDefinitionException`
  - 수동 빈 등록 vs 자동 빈 등록 끼리 이름이 중복된다면,
    - 수동 빈 등록이 우선권을 가진다.( *수동 빈이 자동 빈을 오버라이딩 해버림* )
    - 최근에는, 오류가 발생하도록 바뀜.
      - `CoreApplication`을 실행해보면 오류를 볼 수 있다.