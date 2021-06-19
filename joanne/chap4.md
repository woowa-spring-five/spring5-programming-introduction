# Chap4. 의존 자동 주입
> @Autowired의 활용에 대해 알아보자.

## @Autowired 어노테이션이란?
`@Autowired`는 Spring Framework에서 의존성 주입을 위해 제공하는 어노테이션입니다.

`@Autowired`의 동작 원리를 간단히 요약하면 다음과 같습니다.
1. BeanFactory가 BeanPostProcessor 타입의 빈을 찾는다.
2. IoC 컨테이너에 등록되어있는 다른 일반 빈에게 BeanPostProcessor를 적용한다.
3. 다른 Bean에 @Autowired Annotation을 처리하는 AutowiredAnnotationBeanPostProcessor의 로직이 적용된다.
4. 의존성 주입이 일어난다.

## 필수가 아닌 의존을 @Autowired를 활용해서 주입할 때
1. @Autowired(required=false)
   `required=false` 옵션을 활용할 경우에는 주입할 객체가 빈으로 등록되어있지 않거나 존재하지 않는다면 해당 객체에 null을 주입합니다.
   
2. @Autowired with @Nullable
   `@Nullable` 또한 존재하지 않을 경우에는 null을 할당합니다.

## @Autowired와 다른 주입 방법을 동시에 사용하게 된다면?
예를 들어, 세터 주입에서는 @Autowired를 통해서 B라는 객체를 주입, 그리고 @Configuration 내부 메서드 자체에서는 세터 주입을 호출한 뒤 C를 파라미터로 주입한다면,
@Autowired를 통해 명시된 B라는 객체를 주입하게 됩니다. 따라서 자동 주입을 활용한다면 자동 주입 만을 활용하는 것이 가독성 및 코드 파악에도 용이합니다.
