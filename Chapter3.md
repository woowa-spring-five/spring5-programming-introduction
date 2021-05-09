# Chapter 3 (스프링 DI)

## 1. 의존이란?
```
public class MemberRegisterService {
    
    private MemberDao memberDao = new MenberDao();
    
    public void regist(RegisterRequest req) {
        //이메일로 회원 데이터(Member) 조회
        Member member = memberDao.selectByEmail(req.getEmail());
        if(member != null){
            //같은 이메일을 가진 회원이 이미 존재하면 익셉션 발생
            throw new DuplicateMemberException("dup email " + req.getEmail());
        }
        //같은 이메일을 가진 회원이 존재하지 않으면 DB에 삽입
        Member newMember = new Member(
            req.getEmail(), req.getPassword(),
            req.getName(), LocalDateTime.now());
        memberDao.insert(newMember);
    }
}
```
   - MemberRegisterService 클래스가 회원 데이터 존재 확인, DB에 삽입 하는데 MemberDao 클래스를 사용
   - 이렇게 한 클래스가 다른 클래스의 메서드를 실행할 때 '의존' 한다고 표현
   - 위 코드에선 MemberRegisterService 클래스가 MemberDao에 의존
   - 의존은 변경에 의해 영향을 받는 관계를 의미
 
   
## 2. DI를 통한 의존 처리
 
- DI(Dependency Injection, 의존 주입) : 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식
   
```
public class MemberRegisterService {
	private MemberDao memberDao;

	public MemberRegisterService(MemberDao memberDao) {
		this.memberDao = memberDao;
	}
}
```
   - 처음 코드와 바뀐 부분만 작성했다. 직접 초기화 하던 대신 생성자를 통해 의존 객체를 전달받는다.
   - MemberRegisterService가 의존(Dependency)하고 있는 MemberDao 객체를 주입(Injection) 받은 것
   - 이 코드는 의존 주입 패턴을 따르고 있다.

## 3. DI와 의존 객체 변경의 유연함 
```
public class MemberRegisterService {
	private MemberDao memberDao = new MemberDao();
    ...
}
```
   - memberDao 대신 memberDao를 상속받은 CachedMemberDao를 사용하려면 사용하는 클래스마다 들어가서 변경해줘야 함

```
public class MemberRegisterService {
	private MemberDao memberDao;
    
    public MemberRegisterService(MemberDao memberDao){
        this.memberDao = memberDao;
    }
}
```
   - 생성자를 통애 의존 객체를 주입받도록 변경
   - MemberDao -> CachedMemberDao로 변경할 때 생성자에 인자로 넘어가는 곳만 수정하면 됨
   - 코드가 한 곳으로 집중
   
## 5. 객체 조립기
```
public class Main {
    public static void main(String[] args) {
        MemberDao memberDao = new MemberDao():
        MemberRegisterService regSvc = new MemberRegisterService(memberDao);
        ChangePasswordService pwdSvc = new ChangePasswordService();
        pwdSvc.setMemberDao(memberDao);
        ... // regSvc와 pwdSvc를 사용하는 코드
    } 
}
```
   - 여기서 클래스의 객체를 생성하고 의존 대상이 되는 객체를 주입해주는 조립기 클래스를 적용해볼 수 있다.

```
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
}
```
   - 이 조립기에 있는 필드를 get 함수를 통해 가져오면 원하는 객체를 사용할 수 있다.
   
## 6. 스프링의 DI 설정
#### 6.1 스프링을 이용한 객체 조립과 사용
   - @Configuration 어노테이션은 스프링 설정 클래스를 의미
   - 이 어노테이션을 붙여야 스프링 설정 클래스로 사용 가능
   - @Bean 어노테이션은 메서드가 생성한 객체를 스프링 빈으로 설정
   - memberDao() 메서드가 생성한 빈 객체는 "memberDao"라는 이름으로 스프링에 등록
    
    에러사항
    1. 빈 설정 메서드에 @Bean을 붙이지 않은 경우
    2. @Bean 설정 메서드의 이름과 getBean() 메서드에 전달한 이름이 다른 경우
    
    이 경우에 NoSuchBeanDefinitionException이 발생
    
#### 6.2 생성자를 통한 DI
   - 장점 : 빈 객체를 생성하는 시점에 모든 의존 객체가 주입된다.
   - 단점 : 파라미터 개수가 많아질 경우 각 인자와 의존 객체에 대한 관계를 한 눈에 보기 어렵다.

#### 6.3 setter 메서드를 통한 DI
   - 장점 : 세터 메서드 이름을 통해 어떤 의존 객체가 주입되는 지 알 수 있다.
   - 단점 : 의존 객체를 전달하지 않은 상태로 빈 객체가 생성될 수 있어 NullPointerException의 위험이 있다.

#### 6.4 기본 데이터 타입 값 설정