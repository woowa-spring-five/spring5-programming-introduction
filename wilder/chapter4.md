# 초보 웹 개발자를 위한 스프링 5 프로그래밍 입문

핵심이 되는 부분을 발췌 혹은 정리한 포스팅 입니다.

## Chapter 04 의존 자동 주입

- @Autowired 를 이용한 의존 자동 주입

### 2. @Autowired 애너테이션을 이용한 의존 자동 주입

자동 주입 기능을 사용하면 스프링이 알아서 의존 객체를 찾아서 주입한다. 의존을 주입할 대상에 @Autowired 애너테이션을 붙이기만 하면 된다. 빈 객체의 메서드에 @Autowired 애너테이션을 붙이면 해당 메서드를 호출한다. 이때 메서드 파라미터 타입에 해당하는 빈 객체를 찾아 인자로 주입한다.

일치하는 빈이 없을 경우에는 익셉션이 발생한다. 보통 의존을 충족하지 않는다는 내용(Unsatisfied dependency expressd through filed '오류가 발생한 메서드명')과 함께 적용할 수 있는 차입의 빈이 없다고 나온다.

반대로 @Autowired 애너테이션을 붙인 주입 대상에 일치하는 빈이 두 개 이상이면 빈을 한정할 수 없다(No qualifying bean of thpe '오류가 발생한 메서드명')고 나온다. 해당 타입 빈이 한 개가 아니라 적용된 @Autowired 개수 만큼 발견된 빈을 알려준다.

### 3. @Qualifier 애너테이션을 이용한 의존 객체 선택

자동 주입 가능한 빈이 두 개 이상이면 자동 주입할 빈을 @Qualifier 애너테이션을 이용해 주입 대상 빈을 한정할 수 있다. 사용 가능한 위치로는 @Bean 애너테이션을 붙인 빈 설정 메서드다.

```java
@Configuration
public class AppCtx {
	@Bean
	@Qualifier("printer")
	public MemberPinter memberPrinter1() {
		return new MemberPrinter();
	}
}
```

위의 설정을 통해 해당 빈의 한정 값으로 "printer"를 지정한다. 이렇게 지정한 한정 값은 @Autowired 애너테이션에서 자동 주입할 빈을 한정할 때 사용한다.

```java
public class MemberListPrinter {
	private MemberDao memberDao;
	private MemberPrinter printer;
	
	@Autowired
	@Qualifier("printer")
	public void setMemberPrinter(MemberPrinter printer) {
		this.printer = printer;
	}
}
```

위와 같이 설정 해주면 @Qualifier 애터테이션 값이 "printer" 이므로 한정 값이 "printer"인 빈을 의존 주입 후보로 사용한다. 앞서 스프링 설정 클래스에서 @Qualifer 애너테이션 값으로 "printer"를 준 MemberPrinter 타입의 빈(memberPrinter1)을 자동 주입 대상으로 사용한다.

@Qualifier 애너테이션이 없으면 빈의 이름을 한정자로 지정한다.

```java
@Bean // 한정자 "printer"
public MemberPrinter printer() {
	return new MemberPrinter();
}

@Bean // 한정자 "mPrinter"
@Qualifier("mPrinter")
public MemberPrinter printer2() {
	return new MemberPrinter();
}
```

### 4. 상위/하위 타입 관계와 자동주입

두 가지 방법으로 자동 주입할 타입을 지정할 수 있다. 첫 번째는 이전 설명과 같이 @Qualifier 애너테이션을 사용하는 것이다. 두 번째 방법은 사용할 빈 메서드에 @Qualifier 로 한정자를 지정해주고 반환 값을 다른 클래스를 보내준다. 그리고 사용할 곳에 앞서 설정해둔 한정자와 같게 @Qualifier 를 사용한다. -> (이 부분은 이해가 잘안된다.)

### 5. @Autowired 애너테이션의 필수 여부

자동 주입할 대상이 필수가 아닌 경우에는 @Autowired 애너테이션의 required 속성을 false 로 지정하면 된다.

```java
@Autowired(required = false)
public void setDataFormatter(DataTimeFormatter dateTimeFormatter) {
	this.dateTimeFormatter = dateTimeFormatter;
}
```

위와 같이 속성을 지정해두면 매칭되는 빈이 없어도 익셥션이 발생하지 않으며 자동 주입을 수행하지 않는다. 스프링 5 버전부터는 @Autowired 애너테이션의 required 속성을 false 로 하는 대신에 다음과 같이 의존 주입 대상에 자바 8의 Optional 을 사용해도 된다.

```java
public class MemberPrinter {
	private DateTimeFormatter dateTimeFormatter;
	
	@Autowired
	public void setDateFormmater(Optional<DateTimeFormatter> formatterOpt) {
		if (formatterOpt.isPresent()) {
			this.dateTimeFormatter = formatterOpt.get();
		}
		else {
			this.dateTimeFormatter = null;
		}
	}
}
```

주입 대상이 Optional 인 경우 일치하는 빈이 존재하지 않으면 값이 없는 Optional 을 인자로 전달하고 일치하는 빈이 존재하면 해당 빈을 값으로 갖는 Optional 을 인자로 전달한다.

필수 여부를 지정하는 세 번째 방법은 @Nullable 애너테이션을 사용하는 것이다.

```java
public class MemberPrinter {
	private DateTimeFormatter dateTimeFormatter;
	
	@Autowired
	public void setDateFormatter(@Nullable DateTimeFormatter dateTimeFormatter) {
		this.dateTimeFormatter = dateTimeFormatter;
	}
}
```

### 6. 자동 주입과 명시적 의존 주입 간의 관계

의존을 주입했는데 자동 주입 대상이면 어떻게 될까? 설정 클래스에서 세터 메서드를 통해 의존을 주입해도 해당 세터 메서드에 @Autowired 애너테이션이 붙어 있으면 자동 주입을 통해 일치하는 빈을 주입한다. 따라서 @Autowired 애너테이션을 사용했다면 설정 클래스에서 객체를 주입하기보다는 스프링이 제공하는 자동 주입 기능을 사용하는 편이 낫다.