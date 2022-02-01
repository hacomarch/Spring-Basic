## Spring Basic

#### 객체 지향 설계
- 객체 지향 특징
	- 캡슐화
	- 상속화
	- 추상화
	- 다형성
		- 본질 : 클라이언트를 변경하지 않고, 서버의 구현 기능을 유연하게 변경할 수 있는 것
- 객체 지향 프로그래밍
	- 객체들의 모임으로 파악하고자 하는 것
	- 프로그램을 유연하고 변경이 용이하게 만듦
		- 모든 설계에 역할과 구현을 철저하게 분리
			- 객체 설계시 역할(인터페이스)를 먼저 부여하고, 그 역할을 수행하는 구현 객체 만들기
			- 공연을 설계 하듯이 배역만 만들어두고, 배우는 언제든지 유연하게 변경할 수 있도록
- **SOLID**
	- SRP(Single Responsibility Principle) : 단일 책임 원칙
		- 한 클래스는 하나의 책임만 가져야 한다.
		- 변경이 있을 때 파급 효과가 적으면 잘 따른 것
	- OCP(Open Closed Principle) : 개방 폐쇄 원칙
		- 확장에는 열려있으나, 변경에는 닫혀 있어야 함.
		- 다형성
	- LSP(Liskov Substitution Principle) : 리스코프 치환 원칙
		- 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것
		- 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.
	- ISP(Interface Segregation Principle) : 인터페이스 분리 원칙
		- 특정 클라이언트를 위한 인터페이스 여러 개가, 범용 인터페이스 하나보다 낫다.
			- 자동차 인터페이스 -> 운전, 정비 인터페이스로 분리
			- 분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음
			- 인터페이스가 명확해지고 대체 가능성이 높아짐.
	- DIP(Dependency Inversion Principle) : 의존 관계 역전 원칙 
		- 구현 클래스에 의존하지 말고, 인터페이스에 의존하라
			- 역할에 의존하게 해야 한다는 것
			- 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다.
		- 추상화에 의존해야 하고, 구체화에 의존하면 안된다.
	- *다형성 만으로는 OCP, DIP를 지킬 수 없다.*
- 객체 지향 설계와 스프링
	- 스프링은
		- DI(Dependency Injection) : 의존관계, 의존성 주입
		- DI 컨테이너 제공
	- 위의 기술로 다형성 + OCP, DIP를 가능하게 지원한다.
	- 위의 기술을 제공함으로써 클라이언트 코드의 변경 없이 기능을 확장할 수 있다.
		- 쉽게 부품을 교체하듯이 개발 가능
    
- Enum
	- 요일, 계절과 같이 한정된 데이터만을 가지는 타입을 Enumeration type이라 함.
	- 열거 타입에 들어가는 값(월,화,수,,,)들을 Enumeration constatnt(열거 상수)라 함.
	- 열거 상수를 적을 때, 관례로 대문자로 적음.
	- enum 타입은 == 을 사용해 비교함.
  ~~~java
    public enum Grade{
     BASIC,
     VIP
   }

    public enum Week{
      MON, TUE, WED, THU, FRI, SAT, SUN
    }

    if(member.getGrade() == Grade.VIP){...}
  ~~~
  
***
 - AppConfig
	- 공연 기획자라고 생각하기
	- 객체의 생성과 연결 담당
		- 애플리케이션의 실제 동작에 필요한 구현 객체 생성
		- 생성한 객체 인스턴스의 참조를 생성자를 통해 주입(연결)해준다.
- @BeforeEach
	- 각 테스트를 실행하기 전에 무조건 호출된다.
- **IoC**(Inversion of Control, 제어의 역전)
	- 프레임워크가 내 코드를 대신 호출해주는 것
	- 프로그램에 대한 제어 흐름에 대한 권한을 AppConfig가 가져가는 것
- 프레임워크
	- 내가 작성한 코드를 제어하고 대신 실행하면 그것은 프레임워크다.(JUnit)
- 라이브러리
	- 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 라이브러리다.
- **DI(의존 관계 주입)**
	- *애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고, 클라이언트에 전달해서, 클라이언트와 서버의 실제 의존 관계가 연결되는 것.*
	- 정적인 클래스 의존 관계와 실행 시점에 결정되는 동적인 객체 의존 관계 둘을 분리해서 생각해야 한다.
		- *정적인 클래스 의존관계*
			- 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다
			- 애플리케이션을 실행하지 않아도 분석할 수 있다.
			- 이러한 클래스 의존 관계만으로는 인터페이스에 어떤 객체가 주입될 지 알 수 없다.
		- *동적인 객체 인스턴스 의존 관계* 
			- 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계
		- 의존 관계 주입을 사용하면 정적인 클래스 의존 관계를 전혀 변경하지 않고, 동적인 객체 인스턴스 의존 관계를 쉽게 변경할 수 있다.
- IoC 컨테이너, DI 컨테이너
	- AppConfig 처럼 객체를 생성하고, 관리하면서, 의존 관계를 연결해주는 것
	- 의존 관계 주입에 초점을 맞춰 최근에는 주로 DI 컨테이너라 함.
	- == 어셈블러, 오브젝트 팩토리 
- **@Configuration**
	- 설정을 구성한다.
- **@Bean**
	- 스프링 컨테이너에 스프링 빈으로 등록한다.
	- 이게 적힌 메서드를 모두 호출해 반환된 객체를 스프링 컨테이너에 등록한다.
	- 이게 적힌 메서드의 명을 스프링 빈의 이름으로 사용한다.
- 스프링 컨테이너
	- `ApplicationContext` : 스프링 컨테이너
	- `applicationContext.getBean()` : 메서드를 사용해 스프링 빈을 찾을 수 있다. 
  <br/>
  
  ~~~java
  ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

  MemberService memberService = applicationContext.getBean("memberService", MemberService.class); 

  OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
  ~~~
  
`|출처| 인프런 스프링 핵심 원리 - 기본편 김영한님`
  
