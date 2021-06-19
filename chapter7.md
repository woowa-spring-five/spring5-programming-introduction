# AOP 프로그래밍



## 프록시와 AOP

- AOP
  - 공통 기능 구현과 핵심 기능 구현을 분리하는 것

- Proxy 객체 (ExeTimeCalculator)
  - 핵심 기능의 실행은 다른 객체에 위임하고 부가적인 기능을 제공하는 객체
  - 핵심 기능은 구현하지 않는다.
    - 대신 여러 객체에 공통으로 적용할 수 있는 기능을 구현
- 대상 객체 (delegate)
  - 실제 핵심 기능을 실행하는 객체

```java
public class ExeTimeCalculator implements Calculator {

    private Calculator delegate;

    public ExeTimeCalculator(Calculator delegate) {
        this.delegate = delegate;
    }


    @Override
    public long factorial(final long num) {
        long start = System.nanoTime();
        long result = delegate.factorial(num);
        long end = System.nanoTime();

        System.out.printf("%s.factorial(%d) 실행 시간 = %d \n",
                delegate.getClass().getSimpleName(), num, (end-start));
        return result;
    }
}
```



## AOP

- Aspect Oriented Programming
  - 여러 객체에 공통으로 적용할 수 있는 기능을 분리해서 재사요성을 높여주는 프로그래밍 기법
- 핵심 기능과 공통 기능의 구현을 분리함으로써 핵심 기능을 구현한 코드의 수정 없이 공통 기능을 적용 할 수 있게 함



- 핵심 기능에 공통 기능을 삽입하는 방법
  - 컴파일 시점에 코드에 공통 기능을 삽입
  - 클래스 로딩 시점에 바이트 코드에 공통 기능을 삽입
  - 런타임에 프록시 객체를 생성해서 공통 기능을 삽입





## 스프링 AOP 구현

- Aspect로 사용할 클래스에 @Aspect 애노테이션을 붙인다.
  - AOP 공통 기능을 Aspect라고 한다.
- @Pointcut 애노테이션으로 공통 기능을 적용할 PointCut을 정의한다.
  - Pointcut - 실제 Advice가 적용되는 JoinPoint
    - Advice - 언제 공통 관심 기능을 핵심 로직에 적용할 지를 정의
    - JoinPoint - Advice를 적용 가능한 지점
- 공통 기능을 구현한 메서드에 @Around 애노테이션을 적용한다.



```Java
@Aspect
public class ExeTimeAspect {
    
    // chap07 패키지와 그 하위 패키지에 위치한 타입의 public 메서드를 PointCut으로 설정한다.
    @Pointcut("execution(public * chap07..*(..))")
    private void publicTarget() {
    }
    
    //@Around -> Around Advice
    //대상 객체의 메서드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는 데 사용
    @Around("publicTarget()")
    public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.nanoTime();
        
        try {
            Object result = joinPoint.proceed();
            return result;
        } finally {
            long finish = System.nanoTime();
            Signature sig = joinPoint.getSignature();
            
            System.out.printf("%s.%s(%s) 실행 시간 : %d ns \n",
                    joinPoint.getTarget().getClass().getSimpleName(),
                    sig.getName(), Arrays.toString(joinPoint.getArgs()),
                    finish - start);
        }
    }
}

```



## 프록시 생성 방식



```java
// 수정 전
Calculator cal = ctx.getBean("calculator", Calculator.class);

// 수정 후
RecCalculator cal = ctx.getBean("calculator", RecCalculator.class);
```



- Config파일에서의 정의

```java
@Bean
public Calculator calculator() {
	return new RecCaluculator();
}
```



- 수정 후 코드는 에러가 난다.
- 스프링은 AOP를 위한 프록시 객체를 생성할 때 실제 생성할 빈 객체가 인터페이스를 상속하면 인터페이스를 이용해서 프록시를 생성한다.
- 빈의 실제타입이 RecCalculator라고 하더라도 "calcualtor" 이름에 해당 하는 빈 객체의 타입은 Calculator인터페이스를 상속받은 프록시 타입이 된다.



- Bean객체가 인터페이스가 아닌 클래스를 이용해서 프록시를 생성하고 싶다면 다음과 같이 설정

```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = ture)
public class AppCtx {
...
}
```



## Advice 적용 순서

- 한 Pointcut에 여러 Advice를 적용할 수도 있다.
- 순서는 @Order로 정할 수 있다.



- CacheAspect - > ExeTimeAspect -> ReCcalcualtor 실행

```Java
@Configuration
@EnableAspectJAutoProxy
public class AppCtxWithCache {

    @Bean
    public CacheAspect cacheAspect() {
        return new CacheAspect();
    }

    @Bean
    public ExeTimeAspect exeTimeAspect() {
        return new ExeTimeAspect();
    }

    @Bean
    public Calculator calculator() {
        return new RecCalculator();
    }

```



- ExeTimeAspect -> CacheAspect -> RecCalculator 실행

```Java
@Configuration
@EnableAspectJAutoProxy
public class AppCtxWithCache {

    @Bean
    @Order(2)
    public CacheAspect cacheAspect() {
        return new CacheAspect();
    }

    @Bean
    @Order(1)
    public ExeTimeAspect exeTimeAspect() {
        return new ExeTimeAspect();
    }

    @Bean
    public Calculator calculator() {
        return new RecCalculator();
    }
}

```

