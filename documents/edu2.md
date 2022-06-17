# skill_reader

기술리더 2주차 (2022/06/17)

#기술리더 공통 도출
- 개발표준(네이밍, 포맷터, 개발환경) : 별도 문서 작성
- 메시지 처리 (messagesource, messages.properties)
- 인증/인가: spring security, rbac
- 로깅: slf4j + logback
- 데이터 검증(validation) : javax.validation
- 공통코드관리 - DB 또는 Enum, exceptionCode
- 예외처리: 컨트롤러기반 전역 예외처리
- 트랜잭션 처리: @Transactional
- 파일 핸들링 (up/down/excel) : Restful
- 시큐어코딩 :xss, encrypt/decrypt
- 통신표준 : 데이터타입 : json, 컨버터 : gson, json과 xml을 사용해야할 경우 Jackson추천
gson, jackson이 두개가 있을경우 jackson 우선
- 시스템 관리:메뉴, 프로그램, 권한, 사용자
- API명세화: swagger
- 테스트 규칙 : Junit
- 연계표준 (시스템<->시스템) : restTemplate

spring boot 2.7 버전부터는 사용법이 바뀜
adapter 은 spring boot 에서 사용되는 항목

![image](https://user-images.githubusercontent.com/40287921/174201773-998b22b1-126b-4580-95b4-31ca80d627d9.png)

Authentication Filter이 하는건 UsernamePasswordAuthenticationToken 으로 토큰을 생성해서 Manager로 넘겨줌
Manager -> Provider -> 
UserDetailsService 에서 비교하여 사용자 정보가 들어있는 객체 리턴

SecurityContext


###################################################################################
#Spring Security 주요 흐름 및 객체 설명
특징

Spring Security는 보안에 중점을두어, 인증 및 인가등 여러 옵션을 제공하는 프레임워크이다.
Filter 기반으로 동작하기 때문에 spring MVC 와 분리되어 관리 및 동작한다.
설정파일은 Java, Xml 중 택하여 사용할 수 있다.
회원정보는 인메모리 방식, DB방식 둘다 사용가능하다.

아키텍처 흐름


사용자가 form을 통해 로그인 정보가 담긴 Request를 보냄.


AuthenticationFilter(사용할 구현체 UsernamePasswordAuthenticationFilter)가 HttpServletRequest에서 사용자가 보낸 아이디와 패스워드를 인터셉트한다. 프론트 단에서 유효성검사를 할 수도 있지만, 무엇보다 안전! 안전을 위해서 다시 한번 사용자가 보낸 아이디와 패스워드의 유효성 검사를 해줄 수 있다.(아이디 혹은 패스워드가 null인 경우 등) HttpServletRequest에서 꺼내온 사용자 아이디와 패스워드를 진짜 인증을 담당할 AuthenticationManager 인터페이스(구현체 - ProviderManager)에게 인증용 객체(UsernamePasswordAuthenticationToken)로 만들어줘서 위임한다.


AuthenticationFilter에게 인증용 객체(UsernamePasswordAuthenticationToken)을 전달받는다.


실제 인증을 할 AuthenticationProvider에게 Authentication객체(UsernamePasswordAuthenticationToken)을 다시 전달한다.


인증 절차가 시작되면 AuthenticationProvider 인터페이스가 실행됨 -> DB에 있는 이용자의 정보와 화면에서 입력한 로그인 정보를 비교하게 됨.


AuthenticationProvider 인터페이스에서는 authenticate() 메소드를 오버라이딩 하게 되는데 이 메소드의 파라미터인 Authentication 으로 화면에서 입력한 로그인 정보를 가져올 수 있다.


AuthenticationProvider 인터페이스에서 DB에 있는 이용자의 정보를 가져오려면, UserDetailsService 인터페이스를 사용한다.


UserDetailsService 인터페이스는 화면에서 입력한 이용자의 이름(username)을 가지고 loadUserByUsername() 메소드를 호출하여 DB에 있는 이용자의 정보를 UserDetails 형으로 가져온다. 만약 이용자가 존재하지 않으면 예외를 던진다. 이렇게 DB에서 가져온 이용자의 정보와 화면에서 입력한 로그인 정보를 비교하게 되고, 일치하면 Authentication 참조를 리턴하고, 일치 하지 않으면 예외를 던진다.


인증이 완료되면 사용자 정보를 가진 Authentication 객체를 SecurityContextHolder에 담은 이후 AuthenticationSuccessHandle를 실행한다.(실패시 AuthenticationFailureHandler를 실행한다.)


주요객체 설명
[(UsernamePassword)AuthenticationFilter]
(아이디와 비밀번호를 사용하는 form 기반 인증) 설정된 로그인 URL로 오는 요청을 감시하며, 유저 인증 처리
AuthenticationManager를 통한 인증 실행
인증 성공 시, 얻은 Authentication 객체를 SecurityContext에 저장 후 AuthenticationSuccessHandler 실행
인증 실패 시, AuthenticationFailureHandler 실행
[AuthenticationProvider]
DB 정보와 화면에서 입력한 로그인정보 비교
Spring Security의 AuthenticationProvider을 구현한 클래스로 security-context에 provider로 등록 후 인증절차를 구현
login view에서 login-processing-url로의 form action 진행 시 해당 클래스의 supports() > authenticate() 순으로 인증 절차 진행
[UserDetailsService]
UserDetailsService 인터페이스는 DB에서 유저 정보를 가져오는 역할을 한다.
[UserDetails]
사용자의 정보를 담는 인터페이스, 직접 상속받아 사용하면된다.
UserDetails 인터페이스를 구현하게 되면 오버라이드되는 메소드들이 있다. 이 메소드들에 대해 파악을 해야 된다. 그리고 회원 정보에 관한 다른 정보(이름, 나이, 생년월일, ...)도 추가해도 된다. 오버라이드되는 메소드들만 Spring Security에서 알아서 이용하기 때문에 따로 클래스를 만들지 않고 멤버변수를 추가해서 같이 사용해도 무방하다. 만든 멤버변 수들은 getter, setter를 만들어서 사용하면 된다.




