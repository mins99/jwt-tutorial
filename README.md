## Spring Boot 환경에서의 JWT 실습
## 참고
+ [inflearn 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-jwt)
+ [github code](https://github.com/SilverNine/spring-boot-jwt-tutorial)

## 프로젝트 구성
+ Project: Gradle Project
+ Language: Java11
+ Spring Boot: 2.7.1
+ Packaging: Jar
+ Dependencies : Spring Web, Spring Security, Spring Data JPA, H2 Database, Lombok, Validation

## IntelliJ에서 Lombok 사용시 설정 사항
+ Preferences > Annotation Processors 검색 > Enable annotation processing 활성화

## @EnableWebSecurity
+ config/SecurityConfig.class
+ 기본적인 Web 보안을 활성화하기 위한 어노테이션
+ 추가적인 설정이 필요한 경우 `WebSecurityConfigurer`를 implements 하거나 `WebSecurityConfigurerAdapter`를 extends 함
  + 강의에서는 `WebSecurityConfigurerAdapter`를 extends 했으나 Spring Boot 버전이 업그레이드 되면서 해당 클래스가 deprecated 됨
    + HttpSecurity에 대한 configure → `SecurityFilterChain`를 `@Bean`으로 등록
    + WebSecurity에 대한 configure → `WebSecurityCustomizer`를 `@Bean`으로 등록
+ 참고 
  + [Deprecated된 WebSecurityConfigurerAdapter, 어떻게 대처하지?](https://velog.io/@pjh612/Deprecated%EB%90%9C-WebSecurityConfigurerAdapter-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8C%80%EC%B2%98%ED%95%98%EC%A7%80#httpsecurity-configure)
  + [[Spring Security] Config Refactoring](https://velog.io/@csh0034/Spring-Security-Config-Refactoring)

## 이슈 사항
### 1. DB table 생성이 정상적으로 되지 않음
+ 테이블명 user → h2 2.1.212 버전 이후 예약어로 지정됨
  1) h2 버전을 1.4.200 이하로 변경
  2) properties.yml → `spring.datasource.url`에 `NON_KEYWORDS=USER` 설정 추가
  3) 테이블 이름 user를 users로 바꾸기
     1) @Table(name = "users")
     2) data.sql의 user -> users로 변경
+ 참고 : https://www.inflearn.com/questions/546219
+ 추가로 h2 console 접속 주소 : http://localhost:8080/h2-console

### 2. DB table 생성 전 query insert 가 진행 됨
+ Spring Boot 2.5 이후 부터 `data.sql` 이 먼저 실행됨
  + `spring.jpa.defer-datasource-initialization: true` 옵션 추가
+ 참고 : https://zzang9ha.tistory.com/371