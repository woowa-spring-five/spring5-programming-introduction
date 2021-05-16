# 초보 웹 개발자를 위한 스프링 5 프로그래밍 입문

핵심이 되는 부분을 발췌 혹은 정리한 포스팅 입니다.

## Chapter 03 스프링 DI

- 객체 의존과 의존 주입(DI)
- 객체 조립
- 스프링 DI 설정



### 1. 의존이란?

한 클래스가 다른 클래스의 메서드를 실행할 때 이를 <b>의존</b>한다고 표현한다.

```java
public class MemeberRegisterService {
  // MemberDao 는 MemberRegisterService 클래스의 의존 객체가 된다.
	private MemberDao memberDao = new MemberDao();
}

public static void main(String[] args) {
  // 의존하는 MemberDao 의 객체도 함께 생성한다.
  MemberRegisterService mrs = new MemberRegisterService();
}
```

클래스 내부에서 직접 의존 객체를 생성하는 것이 쉽긴 하지만 유지보수 관점에서 문제점을 유발할 수 있다.

그렇기 때문에 직접 의존 객체를 생성하는 것이 아닌 의존 객체를 주입 받는 방법을 사용하면 위의 문제를 해결할 수 있다.



### 2. DI를 통한 의존 처리

DI(Dependency Injection, 의존 주입)는 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식을 사용한다.

위에 예시로 구현해둔 MemberRegisterService 클래스에 DI 를 적용하면 아래와 같이 구현할 수 있다.

```java
public class MemeberRegisterService {
	private MemberDao memberDao = new MemberDao();
  
  public MemberRegisterService(MemberDao memberDao) {
    this.memberDao = memberDao;
  }
}
```

직접 의존 객체를 생성했던 코드와 달리 바뀐 코드는 의존 객체를 직접 생성하지 않고 생성자를 통해서 의존 객체를 전달받는다.

위와 같이 생성자(혹은 Setter)를 통해 의존하고 있는 객체를 주입 받는 것을 DI 패턴을 따르고 있다고 한다.



### 3. DI와 의존 객체 변경의 유연함

MemberDao 클래스는 회원 데이터를 데이터베이스에 저장한다고 가정한다. 이 상태에서 회원 데이터의 빠른 조회를 위해 캐시를 적용해야 하는 상황이 발생했다. 그래서 MemberDao 클래스를 상속받은 CachedMemberDao 클래스를 만들었다.

여러 객체에서 MemberDao 를 사용 중이라면 사용 중인 모든 객체의 필드를 CachedMemberDao 로 수정해 줘야 한다. 하지만 DI 를 사용하면 실제 객체를 생성하는 한 곳만 CachedMemberDao 로 수정해주면 나머지 객체를 생성할 때 자동으로 생성자에 변경된 CachedMemberDao 인스턴스가 주입되어 생성된다.

앞서 의존 객체를 직접 생성했던 방식에 비해 변경할 코드가 한 곳으로 집중되는 것을 알 수 있다.



### 5. 객체 조립기

앞서 DI 를 설명할 때 객체 생성에 사용할 클래스를 변경하기 위해 객체를 주입하는 코드 한 곳만 변경하면 된다고 했다. 그렇다면 실제 객체를 생성하는 코드는 메인 메서드에서 객체를 생성하면 될 것 같다.

```java
public static void main(String[] args) {
  MemberDao memberDao = new MemberDao();
  MemberRegisterService mrs = new MemberRegisterService(memberDao);
  ChangePasswordService cps = new ChangePasswordService();
  cps.setMemberDao(memberDao);
}
```

위의 방법보다 더 나은 방법은 객체를 생성하고 의존 객체를 주입해주는 클래스를 따로 작성하는 것이다. 의존 객체를 주입한다는 것은 서로 다른 두 객체를 조립한다고 생각할 수 있는데, 이런 의미에서 이 클래스를 조립기라고도 표현한다.

```java
public class Assembler {
	private MemberDao memberDao;
	private MemberRegisterService regSvc;
	private ChangePasswordService pwdSvc;
	
	public Assembler() {
		memberDao = new MemberDao();
		regSvc = new MemberRegisterService(memberDao);
		pwdSvc = new ChangePasswordService();
		pwdSvc.setMemberDao(memberDao);
	}
	
	public MemberDao getMemberDao() {
		return memberDao;
	}
	
	public MemberRegisterService getMemberRegisterService() {
    return regSvc;
  }
  
  public ChangePasswordService getChangePasswordService() {
    return pwdSvc;
  }
}
```

Assembler 클래스를 사용하는 코드는 다음처럼 Assembler 객체를 생성한다. 그리고 get 메서드를 이용해서 필요한 객체를 구하고 그 객체를 사용한다.



### 6. 스프링의 DI 설정

스프링은 DI 를 지원하는 범용 조립기와 유사한 기능을 제공한다.

에너테이션

- @Configuration: 스프링 설정 클래스를 의미한다. 이 애너테이션을 붙여야 스프링 설정 클래스로 사용할 수 있다.
- @Bean: 메서드가 생성한 객체를 빈이라고 설정한다.

AnnotationConfigApplicationContext 클래스를 이용해서 스프링 컨테이너를 생성할 수 있다.

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(AppCtx.class);

// 컨테이너에서 이름이 memberRegSvc 인 빈 객체를 구한다.
MemberRegisterService regSvc = 
	ctx.getBean("memberRegSvc", MemberRegisterService.class);
```

위 코드에서 구한 MemberRegisterService 객체는 내부에서 memberDao 빈 객체를 사용한다.



### 7. @Configuration 설정 클래스의 @Bean 설정과 싱글톤

스프링 컨테이너가 생성한 빈은 싱글톤 객체다.

@Bean이 붙은 메서드에 대해 한 개의 객체만 생성한다. 같은 메서드를 여러 번 호출하더라도 항상 같은 객체를 리턴하는 것을 의미한다.



### 8. 두 개 이상의 설정파일 사용하기

스프링은 한 개 이상의 설정 파일을 이용해서 컨테이너를 생성할 수 있다.

#### @Autowired

- 스프링의 자동 주입 기능을 위한 것이다.
- 이 설정은 의존 주입과 관련이 있다.
- 해당 애너테이션을 붙이면 해당 타입의 빈을 찾아서 필드에 할당한다.
- 스프링 빈에 의존하는 다른 빈을 자동으로 주입하고 싶을 때 사용한다.

#### @Import(className.class)

- 함께 사용할 설정 클래스를 지정한다.



### 9. getBean() 메서드 사용

첫 번째 인자는 빈의 이름, 두 번째 인자는 빈의 타입이다.

빈 이름을 지정하지 않고 타입만으로도 빈을 구할 수도 있다. 이 때는 해당 타입의 빈 객체가 한 개만 존재해야 한다.



### 10. 주입 대상 객체를 모두 빈 객체로 설정해야 하나?

주입할 객체가 꼭 스프링 빈이어야 할 필요는 없다.

객체를 스프링 빈으로 등록할 때와 등록하지 않을 때의 차이는 스프링 컨테이너가 객체를 관리하는지 여부이다. 스프링 컨테이너는 자동 주입 라이프사이클 관리 등 단순 객체 생성 외에 객체 관리를 위한 다양한 기능을 제공하는데 빈으로 등록한 객체에만 기능을 적용한다.

최근에는 의존 주입 대상은 스프링 빈으로 등록하는 것이 보통이다.

