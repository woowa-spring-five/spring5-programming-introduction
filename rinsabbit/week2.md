## 스프링 스터디 4-6 장 정리

### 4장 : 의존 자동 주입

#### ▶︎ 자동 주입

의존 대상을 설정 코드에서 직접 주입하지 않고 스프링이 자동으로 의존하는 Bean 객체를 주입해주는 기능.

#### ▶︎ 준비물

`@Autowired` , `@Resource` 

#### ▶︎ 설정 클래스에서의 변화

```java
// injection 될 bean
@Bean
public MemberDao memberDao() {
  return new MemberDao();
}

// Before
@Bean
public ChangePasswordService changePwdSvc() {
  ChangePasswordService pwdSvc = new ChangePasswordService();
  pwdSvc.setMemberDao(memberDao());
  return pwdSvc;
}

// After
@Bean
public ChangePasswordService changePwdSvc() {
  return new ChangePasswordService();
}
```



#### ▶︎ ChangePasswordService 클래스의 변화

```java
import org.springframework.beans.factory.annotation.Autowired;

public class ChangePasswordService {
  
  @AutoWired
  private MemberDao memberDao;
  
  // 설정 클래스에서 기본 생성자를 이용해 객체를 생성하기에 필요하다.
  ChangePasswordService() {
  };
  // ...
}
```



@Autowired 애노테이션을 필드나 Setter 메서드에 붙이면 스프링은 타입이 일치하는 Bean 객체를 찾아서 주입한다. 만약 빈이 없거나 일치하는 타입의 빈이 2개 이상이면 Exception이 발생한다.

#### ▶︎ 의존 객체 선택하기 : @Qualifier

```java
// 설정 클래스
@Bean
@Qualifier("printer") // 해당 어노테이션이 없으면 빈 이름을 한정자로 지정한다.
public MemberPrinter memeberPrinter1() {
  return new MemberPrinter();
}

@Bean
public MemberPrinter memeberPrinter2() {
  return new MemberPrinter();
}

// 객체에서의 사용
@Autowired
@Qualifier("printer")
public void setMemberPrinter(MemberPrintr printer) {
  this.printer = printer;
}
```



|   빈 이름   | @Qualifier |   한정자    |
| :---------: | :--------: | :---------: |
|   printer   |            |   printer   |
|  printer2   |  mprinter  |  mprinter   |
| infoPrinter |            | infoPrinter |



부모, 자식 클래스가 둘 다 빈으로 등록되어 있을 경우 Exception이 발생하지 않으려면 @Qualifier을 사용하거나 자식 클래스를 타입으로 지정한다. (부모 클래스를 타입으로 쓰면 가능한 클래스가 여러 개가 되므로)

#### ▶︎ 자동 주입 대상이 필수가 아니면?

3가지 방법이 있다.

1. @Autowired(required = false) (Bean이 존재하지 않으면 할당 자체를 안 한다.)
2. Optional (JAVA8 이상) (Bean이 존재하지 않으면 값이 없는 Optional을 할당한다.)
3. @Nullable (Bean이 존재하지 않아도 메서드가 호출된다. 이 경우 Null을 할당한다.)

#### ▶︎ 자동 주입은 꼭 해야 할까?

일관성이 중요하다. 하지만 불변성을 유지하고, 순환 참조를 방지하고, 테스트를 용이하게 쓰려면 생성자 주입을 쓰라는 의견도 있다. [여기](https://madplay.github.io/post/why-constructor-injection-is-better-than-field-injection) 참고. 트레이드오프가 있는 듯...



### 5장 : 컴포넌트 스캔

설정 클래스에서 빈으로 등록하지 않아도 클래스에 `@Component` 어노테이션을 붙이면 스캔 대상이 되어 스프링이 빈으로 등록해 준다. @Qualifier와 비슷하게 어노테이션에 값을 주면 그 값을 빈 이름으로 사용한다. 예시) `@Component("listPrinter")`

이 때 설정 클래스에서는 `@ComponentScan` 어노테이션을 붙여야 한다.

(+) `@ComponentScan(basePackages = {"spring"})` 처럼 basePackages 속성을 부여하면 스캔 대상 패키지 목록을 지정할 수 있다. 해당 패키지와 하위 패키지에 속한 클래스 중 @Component 어노테이션이 붙은 클래스를 빈으로 등록해준다.

#### ▶︎ 기본 스캔 대상 (org.springframework.stereotype 패키지)

- @Component
- @Controller / @RestController
- @Service
- @Repository

#### ▶︎ 스캔 대상에서 제외하거나 포함하기

@ComponentScan 에서 exclueFilters 속성을 이용하면 된다. excludeFilter에 사용되는 @Filter 어노테이션의 type 과 pattern 속성을 이용해 제외 대상을 세부적으로 정할 수 있다. (필터는 여러 개 설정할 수 있다.) Type 속성 종류는 다음과 같다.

- FilterType.REGEX : **정규표현식**을 사용해 대상 지정
- FilterType.ASPECTJ : **AspectJ 패턴**을 사용해 대상 지정 (aspectjweaver 모듈 추가해야 함)
- FilterType.ANNOTATION : classes 속성을 사용해 **특정 어노테이션이 붙은 클래스** 지정
- FilterType.ASSIGNABLE_TYPE : **특정 타입과 그 하위 타입**을 지정

```java
@Configuration
@ComponentScan(basePackages = {"spring"},
	excludeFilters = {
    @Filter(type = FilterType.ANNOTATION, classes = ManualBean.class),
    @Filter(type = FilterType.REGEX, pattern = "spring2\\..*")
  })
```



컴포넌트 스캔을 하면 **빈 이름** 과 **수동 등록** 에 따른 충돌이 발생 가능하다.

1. **빈 이름 충돌** : *다른 타입인데 같은 이름의 빈* 을 사용하면 충돌이 나므로 둘 중 하나에 명시적으로 이름을 지정해야 한다.
2. **수등 등록 빈 충돌** : *수동 등록된 빈과 같은 이름의 자동 등록 빈* 이 있으면 수동 등록이 우선으로 사용된다.
   그러나 *수동 등록된 빈과 다른 이름이나 타입이 같은 자동 등록 빈* 이 있으면 충돌이 나므로 @Qualifier 로 알맞은 빈을 선택해야 한다.

### 6장 : 빈 라이프사이클과 범위

#### ▶︎ 스프링 빈 객체의 라이프사이클

1. 빈 객체 생성
2. 의존 설정
3. 빈 객체의 초기화 수행 : 인터페이스 `InitializingBean` 호출
4. 스프링 컨테이너 종료 후, 빈 객체의 소멸 : 인터페이스 `DisposableBean` 호출

초기화와 소멸 과정이 필요한 경우 : DB 커넥션 풀, 채팅 클라이언트 등

InitializingBean 또는 DisposableBean 대신 커스텀 메서드로 초기화와 소멸 메서드를 지정하고 싶다면 Bean에 `initMethod` 와 `destroyMethod` 속성에 값을 부여하면 된다.

```java
@Bean(initMethod = "connect", destroyMethod = "close")
public Client client() {
  // 여기서 초기화 메서드를 쓸 수도 있다.
  return new Client();
}

// Client class
public void connect() {
  // do something...
}

public void close() {
  // do something...
}
```



#### 빈 객체의 생성과 관리 범위

@Bean 과 함께 쓰이는 어노테이션 @Scope를 이용해 빈의 범위를 설정할 수 있다.

- `@Scope("singleton")` 싱글톤 범위 : 별도 설정을 하지 않으면 빈은 싱글톤 범위를 갖는다
- `@Scope("prototype")` 프로토타입 범위 : 빈 객체를 구할 때마다 매번 새로운 객체를 생성한다.

