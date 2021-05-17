# 컴포넌트 스캔

- 자동 주입과 함께 사용하는 추가 기능이 컴포넌트 스캔이다.
- 컴포넌트 스캔은 스프링이 직접 클래스를 검색해서 빈으로 등록해주는 기능이다.
- 설정 클래스에 빈으로 등록하지 않아도 원하는 클래스를 빈으로 등록할 수 있다.



## @Component

- 스프링이 검색해서 빈으로 등록할 수 있으려면 클래스에 @Component 애노테이션을 붙힌다.



## 스캔 대상에서 제외하기

- excludeFilters 속성

```java
@ComponentScan(basePackages = {"spring"}, excludeFilters = @Filter(type = FilterType.REGEX, pattern = "spring\\..*Dao"))
```

- @Filter 옵션에 FilterType.REGEX를 주어 정규표현식을 사용하여 대상을 제외 대상을 지정한다는 것을 의미

```Java
@ComponentScan(basePackages = {"spring", "spring2"},
	excludeFilters = @Filter(type = FilterType.ANNOTATION,
													classes = {NoProduct.class, ManualBean.class}))
```

- 특정 애노테이션을 붙힌 컴포넌트를 대상에서 제외하는 것도 가능하다.



## 컴포넌트 스캔에 따른 충돌 처리

- 빈 이름 충돌
  - spring 패키지와 spring2 패키지에 @Component를 붙힌 MemberRegisterService 클래스가 존재한다면?
    - 충돌발생
- 수동 등록한 빈과 충돌
  - 스캔할 때 사용하는 빈 이름과 수동 등록한 빈 이름이 같은 경우 수동 등록한 빈이 우선한다.
- @Qualifier
  - 사용할 의존 객체를 선택할 수 있도록 해준다.

