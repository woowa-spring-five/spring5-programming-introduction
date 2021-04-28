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
   
   