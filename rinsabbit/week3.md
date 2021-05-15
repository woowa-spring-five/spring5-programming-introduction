## 스프링 스터디 7 장 정리

### 7장 : AOP 프로그래밍

#### ▶︎ 프록시

핵심 기능의 실행은 다른 객체(대상 객체)에 위임하고 여러 객체에 공통으로 적용할 수 있는 기능을 제공하는 객체. 핵심 기능은 구현하지 않는다.

#### ▶︎ AOP

Aspect Oriented Programming. 여러 객체에 공통으로 적용할 수 있는 기능을 분리해 재사용성을 높여주는 프로그래밍.

핵심 기능과 공통 기능의 구현을 분리해 핵심 기능을 구현한 코드의 수정 없이 공통 기능을 적용할 수 있게 한다. 적용 방법 종류는 다음과 같다.

- **컴파일 시점** 에 코드에 공통 기능을 삽입하는 방법
- **클래스 로딩 시점** 에 바이트 코드에 공통 기능을 삽입하는 방법
- **런타임**에 프록시 객체를 생성해 공통 기능을 삽입하는 방법

스프링이 제공하는 AOP 방식은 세번째, 런타임에 프록시 객체를 생성하는 방법이다. 스프링 AOP는 자동으로 프록시 객체를 만들어준다.

< AOP 주요 용어 >

| 용어      | 의미                                                         |
| --------- | ------------------------------------------------------------ |
| Advice    | **언제** 공통 관심 기능을 핵심 로직에 적용하는 지 정의       |
| JoinPoint | Advice를 적용 가능한 **지점**을 의미                         |
| PointCut  | JointPoint의 부분 집합. **실제 Advice**가 적용되는 JoinPoint |
| Weaving   | Advice를 핵심 로직 코드에 **적용**하는 것                    |
| Aspect    | 여러 객체에서 **공통**으로 적용되는 기능                     |

Advice 종류는 다음과 같다.

- Before Advice : 대상 객체의 **메서드 호출 전**에 공통 기능 실행
- After Returning Advice : 메서드가 **예외 없이 실행된 이후**에 실행
- After Throwing Advice : **예외가 발생한 경우**에 실행
- After Advice : **예외 상관없이 메서드가 실행된 이후**에 실행
- Around Advice : 메서드 실행 전, 후, 예외 발생 시점에 실행 (주로 이걸 사용한다.)

#### ▶︎ 스프링 AOP 구현

- Aspect로 사용될 클래스에 @Aspect 어노테이션을 붙인다
- @PointCut 어노테이션으로 공통 기능을 적용할 PointCut을 정의
- 공통 기능을 구현한 메서드에 @Around 어노테이션 적용

```java
// @Aspect 어노테이션을 붙인 클래스를 공통 기능으로 적용하려면 @Configuration 클래스에 @EnableAspectJAutoProxy 어노테이션을 붙여야 한다.
// @Order(1) Aspect이 여러 개일 때 적용 순서가 중요하다면 사용하는 어노테이션
@Aspect
public class ExeTimeAspect {
  
  // 공통 기능을 적용할 대상을 설정하는 어노테이션
  // excution 명시자를 통해 Advice를 적용할 메서드를 지정할 수 있다.
  // PointCut을 재사용하려면...
  // 1) public으로 지정하고 다른 Aspect의 클래스의 @Around 어노테이션에서 해당 메서드를 사용하게 한다.
  // 2) 별도 클래스의 PoitCut을 정의하고 각 Aspect 클래스에서 해당 PointCut을 사용하도록 한다.
  @PointCut("execution(public * chap07..*(..))")
  private void publicTarget() {
  }
  
  // Around Advice 설정하는 어노테이션
  @Around("publicTarget()")
  public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
    long start = System.nanoTime();
    try{
      Object result = joinPoint.proceed();
      return result;
    } finally {
      long finish = System.nanoTime();
      // 메서드와 파라미터를 합쳐서 시그니쳐라 한다.
      Signature sig = joingPoint.getString();
      System.out.printf("%s,%s(%s) 실행 시간 : %d ns \n",
                        joinPoint.getTarget().getTarget().getClass().getSimpleName(), sig.getName(),
                       Arrays.toString(joinPoint.getArgs()), (finish-start));
    }
  }
}
```



#### ▶︎ 스프링 AOP에서의 프록시

스프링은 AOP를 위한 프록시 객체 생성시, 실제 생성할 빈 객체가 **인터페이스**를 상속하면 **인터페이스를 이용해 프록시를 생성** 한다.

빈 객체가 상속받는 인터페이스가 아닌 클래스를 이용해 프록시를 생성하고 싶다면 @EnableAspectJAutoProxy의 속성인 **proxyTargetClass**를 true로 설정하면 된다.



