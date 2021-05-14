## Dependency Injection

-   의존 주입
-   한 클래스가 다른 클래스의 메서드를 실행할 때 이를 '의존'한다고 한다.
    -   의존은 변경에 의해 영향을 받는 관계를 의미한다.
-   의존 주입은 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식을 사용
-   변경에 유연해 질 수 있다.

```
public MemberRegisterService(MemberDao memberDao) {
    this.memberDao = memberDao
```

-   객체를 생성하고 의존 객체를 주입하는 것은 스프링 컨테이너
-   설정 파일을 정의한 후 AnnotationConfigApplicationContext를 이용해서 스프링 컨테이너를 생성해야 한다.
-   스프링 컨테이너로 부터 getBean()을 사용하여 Bean 객체를 얻어올 수 있다.

```
@Configuration
public class AppCtx {

    @Bean
    public MemberDao memberDao() {
    	return new MemberDao();
    }
    
    @Bean
    public MemberRegisterService memberRegSvc() {
    	return new MemberRegisterService(memberDao());
    }
    
    @Bean
    public ChangePasswordService changePwSvc() {
    	ChangePasswordService pwsSvc = new ChangePasswordService();
        pwdSvc.setMemberDao(memberDao());
        return pwdSvc;
    
    }

}
```

-   memberDao()가 새로운 MemberDao 객체를 생성해서 리턴하므로 memberRegSvc()에서 생성한 MemberRegisterService 객체와 changePwdSvc()에서 생성한 ChangePasswordService객체는 서로 다른 MemberDao 객체를 사용하는 것 아닌가?
    -   스프링 컨테이너가 생성한 Bean은 싱글톤 객체이다.
    -   항상 같은 객체를 리턴한다.

## 의존 자동 주입

@Autowired 애노테이션의 필수 여부

-   @Autowired 애노테이션의 required 속성을 false로 지정하면 매칭되는 빈이 없어도 익셉션이 발생하지 않으며 자동 주입을 수행하지 지 않는다.

```
@Autowired(required = false)
public void setDateFormatter(DateTimeFormatter dateTimeFormatter) {
    this.dateTimeFormatter = dateTimeFormatter;
}
```

-   스프링 5 버전부터는 required 속성을 false로 대신에 자바8의 Optional을 사용해도 된다.
-   자동 주입 대상 타입이 Optional인 경우, 일치하는 빈이 존재하지 않으면 값이 없는 Optional을 전달하고, 일치하는 빈이 존재하면 해당 빈을 값으로 갖는 Optional 인자로 전달한다.

```
@Autowired
public void setDateFormatter(Optional<DateTimeFormatter> formatterOpt) {
    if (formatterOpt.isPresent()) {
        this.dateTimeFormatter = formatterOpt.get();
        return;
    }
    
    this.dateTimeFormatter = null;
}
```

-   @Nullable 애노테이션을 사용
-   스프링 컨테이너는 세터 메서드를 호출할 때 자동 주입할 빈이 존재하면 해당 빈을 인자로 전달하고, 존재하지 않으면 null을 전달

```
@Autowired
public void setDateFormatter(@Nullalbe DateTimeFormatter dateTimeFormatter) {
    this.dateTimeFormatter = dateTimeFormatter;
}
```

-   @Autowired(required = false)와 달리 @Nullable 애노테이션을 사용하면 일치하는 빈이 없을 때 null값을 할당