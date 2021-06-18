# 컴포넌트 스캔
자동 주입과 함께 사용하는 추가 기능인 컴포넌트 스캔은 스프링이 직접 클래스를 검색해서 빈으로 등록해주는 기능이다.


## @Component 애노테이션으로 스캔 대상 지정
@Component 애노테이션은 해당 클래스를 스캔 대상으로 표시한다.
@Component 애노테이션에 값을 주게되면 빈으로 등록할 때 사용할 이름으로 사용된다.


## @ComponentScan 애노테이션으로 스캔 설정
@Component 애노테이션을 붙인 클래스를 스캔해서 스프링 빈으로 등록하기 위해 설정 클래스에 @ComponentScan 애노테이션을 적용한다.

@ComponentScan 애노테이션에도 속성 값을 줄 수 있는데 이 속성은 스캔 대상 패키지 목록을 지정한다.
```java
@ComponentScan(basePackages = {"spring"}) // spring 패키지와 하위 패키지에 속한 클래스가 스캔 대상
```

## 스캔 대상에서 제외하거나 포함하기
excludeFilters 속성을 사용해 스캔할 때 특정 대상을 자동 등록 대상에서 제외할 수 있다.
```java
@ComponentScan(basePackages = {"spring"}, 
    excludeFilters = @Filter(type = FilterType.REGEX, pattern = "spring\\..*Dao"))
```
@Filter 애노테이션의 type 속성값으로 정규표현식을 사용해 제외 대상을 지정한다.
위의 예제에서는 "spring."으로 시작하고 Dao로 끝나는 정규표현식을 지정했다.

이에 따라 spring.MemberDao 클래스는 컴포넌트 스캔대상에서 제외된다.


### 기본 스캔 대상
- @Component
- @Controller
- @Service
- @Repository
- @Aspect
- @Configuration

## 컴포넌트 스캔에 따른 충돌 처리
컴포넌트 스캔 과정에서 서로 다른 타입인데 같은 빈 이름을 사용하는 경우 : 둘 중 하나에 명시적으로 빈 이름을 지정해서 충돌을 피한다.

스캔할 때 사용하는 빈 이름과 수동등록한 빈 이름이 같은 경우 수동 등록한 빈이 우선한다. 그렇다면 두 이름이 같은 경우는?

같은 타입의 빈이 두 개 생성되므로 자동 주입하는 코드는 @Qualifier 애노테이션을 사용해 선택해야 한다.