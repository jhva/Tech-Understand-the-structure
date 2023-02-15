## Spring IoC - application Context
- 스프링은 스플잉이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트를 빈 이라고함 .
    - 동시에 스프링 빈은 컨테이너가 생성과 관계설정 , 사용 등을 제어해주는 제어의 역전이 적용된 오브젝트.

### Application Context 동작 방식
- 스프링에서는 빈 팩토리라고도 부를수도있다.
    - 이유는 어플리케이션 컨텍스트는 빈팩토리 인터페이스를 상속했기때문에이다.

### 하는역할
- 애플리케이션 컨텍스트는 IoC를 적용해서 관리할 모든 오브젝트에 대한 생성과 관계설정을 담당함.




### 스프링 IoC의 용어정리 
- 빈 
  - 스프링이 IoC방식으로 관리하는 오브젝트. 스프링이 사용하는 애플리케이션에서 만들어지는 모든 오브젝트가 다 빈은 아니다. 그 중 스프링이 직접 그 생성과 제어를 담당하는 오브젝트만을 빈이라고함 .
- 빈 팩토리 Bean factory
  - 스프링의 IoC를 담당하는 핵심 컨테이너 .
    - 빈을 등록, 생성, 조회하고 돌려주고, 그 외 부가적인 빈을 관리하는 기능을 담당함. 
- 애플리케이션 컨텍스트 Application Context
  - 빈 팩토리를 확장한 IoC 컨테이너이고 빈을 등록하고 관리하는 기본적인 기능은 빈팩토리와는 동일한데, 각종 스프링이제공하는 서비스를 추가로 제공함 .
    - 빈팩토리라고부를땐, 빈의 생성과 제어의 관점에서 이야기하는 부분
    - 애플리케이션 컨텍스트라고 할 대는 스프링이제공하는 애플리케이션 지원 기능을 모두 포함해서 이야기하는 것.



### 오브젝트 스코프 
```
Factory factory = new Factory();

UserDao dao1 = factory.userDao();
UserDao dao2 = factory.userDao();

Sys..
Sys.. 
```
- 두 개는 각기 다른 값을가진 동일하지 않는 오브젝트라고 설명이됨 . 
- 하지만 애플리케이션 컨텍스트에 factory를 빈으로설정하고 getbean하여 해당 객체의 주소를살펴보자 
- 매번 호출하면 새로운 오브젝트가 만들어진다.
```
ApplicationContext context = .. .. 

UserDao dao1 = context.getBean("userDao",UserDao.class);
UserDao dao3 = context.getBean("userDao",UserDao.class);

Sys..dao1
Sys..dao3 

둘다 같은 주소를 바라봄!. 이유가 뭘까 ?
```

- 매번 새로운 userDao가 만들어지지않음 .그이유는 ?
  -  내부에서 생성하는 빈 오브젝트를 모두 싱글톤으로 만듦
  - 여기서 싱글톤패턴의란 ? 간단히 요약해서말하면 "하나"의 인스턴스만 생성하여 사용하는 패턴 .
### 싱글톤 레지스트리로서의 애플리케이션 컨텍스트 
- 스프링은 기본적으로 별다른 설정을 하지 않으면 내부에서 생성하는 빈 오브젝트를 모두 싱글톤으로 만듦. 
여기서 싱글톤이라는 것은 디자인 패턴에서 나오는 싱글톤 패턴과비슷한개념이지만 그 구현 방법이랑은 확연히다름.

### 그렇다면 왜 싱글톤으로 빈을 만들까 ? 
- 스프링이 주로 적용되는 대상이 자바 엔터프라이즈 기술을 사용하는 서버환경이기 때문이다. 
  - 스프링은 엔터프라이즈 시스템을 위해 고안된 기술이기 때문에 서버환경에서 사용될 때 그 가치가있음 .
  - 그래서 싱글톤이랑무슨상관인데 ?
  - 스프링이 처음 설계됐던 엔터프라이즈 서버환경은 서버하나당 최대로 초당 수십에서 수백번 씩 브라우저나 여타 시스템으로부터 요청을 받아처리할수있는 높은 성능이 요구되는 환경이였는데, 매번 클라이언트 요청이 올대마다 각 로직을 담당하는 오브젝트가 새로만들어서 사용한다고 생각했을때 1분만경과되도 몇십만개 , 1시간만되도 몇백만개가 새로운 오브젝트가 만들어지기때문에 이렇게 되면 부하거길리고 서버가감당하기힘든부분이생겨서 그렇다.

### 싱글톤 레지스트리 
> 스프링은 직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능을 제공하는 것을 싱글톤 레지스트리라고한다.
- 스프링은 싱글톤으로 만들어졌기때문에 서비스 오브젝트 방식으로 사용되는 것은 적극 지지함.
- 평범한 자바클래스여도 IoC방식을 이용하면 손쉽게 싱글톤방식으로 만들어져 관리되게 할 수 있음.
  - 오브젝트 생성에 대한 모든 권한은 IoC기능을 제공하는 애플리케이션 컨텍스트에게 있기 대문임.


### 스프링 빈의 스코프 
> 빈이 생성 , 존재하고 적용되는 범위.
- 스프링 빈은 경우에 따라서 싱글톤 외의 스코프를 가짐 
  - 프로토타입 스코프 
    - 싱글톤과 달리 컨테이너에 빈을 요청할 대마다 매번 새로운 오브젝트를 만듦.
  - 요청 스코프 
    - 웹을 통해 새로운 HTTP요청이 생길때마다 생성되는 스코프 
  - 웹의 세션과 유사한 session scope
  