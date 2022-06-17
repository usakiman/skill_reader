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






