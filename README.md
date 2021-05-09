# 김김 2주차 정리

## Chapter 4. 의존 자동 주입

### `@Autowired` 를 이용한 의존 객체 자동 주입

- `@Autowired`를 이용하면 스프링이 컨테이너로부터 알아서 맞는 타입의 의존 객체를 찾아 주입한다.
- `@Autowired`를 이용한 주입 방법은 다음과 같이 3가지가 존재한다.

#### 1. 생성자 주입
- 생성자 위에 Autowired 어노테이션을 붙인다.
```
public class Kimkim {
    private final SomeClass someClass;
    
    @Autowired
    public Kimkim(SomeClass someClass) {
        this.someClass = someClass;
    }
}
```
- 생성하는 시점에 1번 주입받는다.
- 불변, 필수(final이 붙은 인스턴스 변수, 생성자에 할당 코드가 없으면 컴파일이 되지 않는다) 의존관계에 사용한다.

#### 2. 필드 주입 
- 인스턴스 변수 위에 Autowired 어노테이션을 붙이는 방식이다.
```
public class Kimkim {
    @Autowired
    private final SomeClass someClass;
   
}
```
- 외부에서 변경(setter를 이용한다든가)이 불가능해서 테스트하기 힘들다는 치명적 단점이 존재한다.
- 테스트 코드(외부에서 테스트 클래스 객체를 생성할 일이 없음) 외에는 잘 사용하지 않는다.

#### 3. setter 메소드 주입
- setter 메소드 위에 Autowired 어노테이션을 붙인다.
```
public class Kimkim {
    private SomeClass someClass;
   
    @Autowired
    void setSomeClass(SomeClass someClass) {
        this.someClass = someClass;
    }
}
```
- 런타임 중에 선택, 변경 가능성이 있는 경우 사용한다.
- 그럴 일이 많지 않아 마찬가지로 잘 사용되지 않는다.

### 예외 상황들

#### 1. 일치하는 빈이 없을 때
- `NoSuchBeanDefinitionException`이 발생된다.
  
- 빈이 없는 상황에 대한 예외 처리가 잘 되어있다면 다음과 같은 옵션을 통해 비필수 객체로 등록할 수 있다.
  
1-1. `@Autowired(required = false)` 옵션을 준다.

```
@Autowired(required = false)
void setSomeClass(SomeClass someClass) {
    this.someClass = someClass;
}
```
  
1-2. `@Nullable` 옵션을 준다.
  

```
@Autowired
void setSomeClass(@Nullable SomeClass someClass) {
    this.someClass = someClass;
}
```

1-3. `Optional`로 받는다.
```
@Autowired
void setSomeClass(Optional<SomeClass> someClass) {
    if (someClass.isPresent) {
        ...
    }
}
```

#### 2. 같은 타입의 빈이 여러개일 때
- `NoUniqueBeanDefinitionException`이 발생된다.

2-1. `@Qualifier` 어노테이션을 이용해 빈에 수동으로 이름을 주고, 주입 받을 곳에서 직접 지정한다.

```
@Configuration
public class Config {
    @Bean
    @Qualifier("targetSomeClass")
    SomeClass someClass1() {
        return new SomeClass();
    }
    
    @Bean
    SomeClass someClass2() {
        return new SomeClass();
    }
}
public class

public class Kimkim {
    private final SomeClass someClass;
    
    @Autowired
    @Qualifier("targetSomeClass")
    public Kimkim(SomeClass someClass) {
        this.someClass = someClass;
    }
}
```

2-2. 상속 관계에 있는 경우, 자식 클래스를 명시해 하위 클래스를 받을 수 있다.

## Chapter 5. 컴포넌트 스캔

- 설정 클래스(@Configuration)에 빈(@Bean)으로 등록하지 않더라도 스프링 빈으로 등록할 수 있는 방법이다.
- 설정 클래스에서 `@ComponentScan`을 통해 기본 스캔 위치를 지정해주어야 한다. 지정하지 않는다면, `@ComponentScan`이 붙은 설정 파일의 위치부터 하위 디렉토리로 탐색을 시작한다.
  - Tip. `@SpringBootApplication` 어노테이션은 `@ComponentScan` 옵션을 포함한다. 일반적으로 스프링부트 프로젝트의 main 함수가 있는 클래스에해당 어노테이션이 붙어있기 때문에, 하위 프로젝트의 컴포넌트들이 스캔 대상이 되는 것이다. 

### 기본 스캔 대상이 되는 어노테이션
- 스프링 빈에 등록하기 위한 가장 기본적인 어노테이션이다.
- 후술할 어노테이션들은 이 어노테이션을 포함한다. 따라서 `@Component`는 DTO, VO 용도의 클래스에 주로 사용된다.

#### @Service

- 비즈니스 로직을 수행하며 영속성 계층(Respository Layer)의 객체를 호출하는 클래스에 사용된다.
- 기능적으로 Component와 다를 것은 없다.

#### @Repository

- DB처리를 위한 DAO(Data Access Object)에 주로 사용된다.
- `Component`의 기능에 더불어, `Unchecked Exception`을 Spring의 `Runtime Exception`인 `DataAccessException`으로 처리할 수 있게 해준다. 데이터베이스를 바꾸면, 그곳에서 발생하는 예외의 종류도 바뀌고, 결국 상단 레이어의 코드가 예외를 잡을 수 없는 상황이 발생할 수 있다. 따라서 데이터베이스 계층에서 발생하는 런타임 에러를 DataAccessException으로 다시 뒤바꾸어 던져주기 때문에, 그 위의 계층의 코드가 바뀌지 않더라도 예외를 처리할 수 있게 된다.

#### @Controller

- 스프링 MVC 컨트롤러에서 사용한다.

#### @Bean

- 메소드 위에 선언할 수 있는 어노테이션으로, 메소드 단위로 스프링 빈으로 등록할 수 있다.

#### @Configuration

- 설정 정보로 인식된다.