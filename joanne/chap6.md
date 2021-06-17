# Chap6. 빈 라이프사이클과 범위

```java
//1. 컨테이너 초기화
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppContext.class);

//2. 컨테이너에서 빈 객체를 구해서 사용
Greeter g = ctx.getBean("greeter", Greeter.class);
String msg = g.greet("조앤");
System.out.println(msg);

//3. 컨테이너 종료
ctx.close();
```

스프링 컨테이너 초기화 시점에서는 설정 클래스에서 정보를 읽어와 알맞은 빈 객체를 생성하고 각 빈을 연결하는 작업을 수행한다.

컨테이너 초기화가 완료되면 컨테이너를 사용할 수 있는데, 컨테이너를 사용한다는 것은 `getBean()`과 같은 메서드를 이용해서 컨테이너에 보관된
빈 객체를 구한다는 것을 뜻한다.

컨테이너 종료 시에는 등록했던 빈 객체를 모두 소멸시킨다.

## 스프링 객체의 빈 라이프사이클
객체 생성 -> 의존 설정 -> 초기화 -> 소멸

## 빈 객체의 초기화와 소멸
스프링 컨테이너는 빈 객체를 초기화하고 소멸하기 위해 빈 객체의 지정한 메서드를 다음 두 메서드를 호출한다.
```java
public interface InitializingBean {
    void afterPropertiesSet() throws Exception;
}

public interface DisposableBean {
    void destroy() throws Exception;
}
```

1. 빈 객체가 `IntializingBean`을 구현한다.
2. 스프링 컨테이너는 초기화 과정에서 빈 객체의 `afterPropertiesSet()` 메서드를 실행한다.
3. 빈 객체가 `DisposableBean`을 구현한다.
4. 스프링 컨테이너는 소멸 과정에서 빈 객체의 `destroy()` 메서드를 실행한다.


## 빈 객체의 초기화와 소멸을 커스터마이즈
```java
@Bean(initMethod = "connect", destroyMethod = "close")
```
위와 같이 등록하면 해당 객체 초기화시에는 connect를, 소멸 시에는 close를 호출한다.
하지만 커스터마이즈 할 때의 주의사항은 `afterPropertiesSet()`이 커스텀화되어 구현하고, 또 설정 파일 코드 내에서 또 실행시키지 않도록 해야한다. 
즉, 초기화 관련 메서드를 빈 설정 코드에서 직접 실행할 때는 이렇게 초기화 메서드가 두 번 호출되지 않도록 주의해야 한다.
또한 `initMethod`, `destroyMethod`는 파라미터가 없어야한다.

## 빈 객체의 생성과 관리 범위
식별자에 대해 스프링은 싱글톤 범위를 갖는다. 하지만 임의로 프로토타입으로 설정해줄 수도 있다. 
프로토타입 범위를 갖는 빈은 스프링의 완전한 라이프사이클을 따르지 않기에 빈 객체의 소멸 처리를 코드에서 직접 처리해야한다.