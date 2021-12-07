---
title: Spring 공부 2회차
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- backend
toc: true
toc_sticky: true
toc_label: 목차
description: 스프링 공부 일단 대충 업로드
article_tag1: 스프링
article_tag2: backend
article_section: 스프링 공부
meta_keywords: Spring
last_modified_at: '2021-12-02 23:15:00 +0800'
---
스프링

프로젝트 생성
https://start.spring.io

스프링 initializr

Maven vs Gradle
라이브러리 땡겨오고 빌드하는 life cycle도 관리해주는 툴
과거에는 Maven을 많이 사용했으나 이제는 Gradle을 많이 쓴다.

dependencies
어떤 libraray 갖다 쓸거냐

spring web : 톰캣 & 스프링 MVC
thymeleaf : html을 만들어주는 탬프릿 엔진, 딱히 좋은게 없어서 이거 쓴다..


java 파일 제외 전부 다 resources에 들어감


톰캣이라는 웹 서버 자체적으로 내장


sl4j, logging : 로그 관련 라이브러리



welcome page : static/index.html

웹 어플리케이션 첫 진입점 : controller

웹 개발
1. 정적 컨텐츠
2. MVC와 템플릿 엔진 -> jsp, php
   html을 동적으로 바꿔서 주는거
3. API

정적 컨텐츠

요청이 들어옴
컨트롤러에서 찾아봄
->없네!
그러면 resources/static에서 찾아보고 있으면 바로 그냥 반환!


MVC 템플릿 엔진
템플릿 엔진이 변환을 해서 넘김


@ResponseBody
응답 body부에 직접 데이터를 넣어주겠다는 annotation.

static으로 class를 class 내부에서 선언하면 안에서 쓸 수 있다.


api방식
json 넘긴다
property 방식, getter setter 방식 등등..
@ResponseBody라는 어노테이션이 있어야함
객체를 주고 받음
viewResolver 대신에 HttpMessageConverter가 동작

스프링에서는 객체가 오면 API 방식으로 작동하게 된다.
(문자일 떄는 StringConverter가 작동했지만 객체일때는 jsonConverter가 작동하게된다)


개발 순서

1. 비즈니스 요구사항 정리

도메인 : 비즈니스 도메인 객체
리포지토리 : 데이터베이서 접근, 도메인 객체를 db에 저장하고 관리
서비스 : 핵심 비즈니스 로직 구현
컨트롤러

MemberService -> MemberRepostiory(interface) <---MemoryMemberRepository

일단 인터페이스로 만들어서 실제 구현 담당 memoryMemberRepo로 짜긴하는데
언제든 대체 가능

Optional : null을 처리하는 방법 java 8에 들어가있음

자바 람다 숙지하기

junit test

Assertions.assertThat()
Assertions.assertEquals()

@AfterEach
테스트 하나하고 클리어하는 코드 작성하는 법
콜백 메소드, 함수 하나 실행하고 꼭 거치는 부분
을 이용해서 테스트를 한다.(서로 의존관계, 순서 상관 없이 테스트가 설계되어야한다.)




Service
실제 비즈니스 로직 서비스


회원 존재 여부 확인
Optional과 같이 사용하는 법
result.ifPresent(m->{throw new IllegalException("이미 존재");});

테스트 문법 추천
//given
//when
//then
주석 깔아주기
@Transactional 을 이용해서
반복테스트 가능하다 -> DB에서 작업하고 rollback을 하기 때문에 commit 이 안되서 다음 테스트 케이스에 영향을 주지 않는다.




DI

Dependency Injection

의존성 주입

Spring container 안에 넣고 관리하는 개념 -> "Spring bean이 관리된다"

@Controller @Service로 빈에 등록하기
@Autowired로 자기들끼리 연결
같은 Annotation으로

빈에 등록하는 법
1. 컴포넌트 스캔과 자동 의존관계 설정 -> 해당 패키지 하위만 보게된다
@Component(@Service, @Controller, @Repository)


@Autowired
1) 필드주입 -> 수정 어려움 (활용도 낮음)
@Autowired private MemberService memberService;
2) Setter 주입
@Autowired -> public으로 노출 (과거에 많이 씀)
public void setMemberService(MemberService memberService){
	this.memberService=memberService;
}
3) 생성자로




2. 자바 코드로 직접 스프링 빈 등록
SpringConfig 파일을 생성해서
@Configuration 어노테이션을 이용해서
@Bean으로 선언하면된다.

교체가 편하다!!!
컴포넌트 스캔은 DB 변경 같은거 하면 다 바꿔줘야함...


3. Xml도 되기는한다..


@Configuration
public class SpringConfig{
	@Bean
	public MemberService memberService() {
		return new MemberService(memberRepository());
	@Bean
	public MemberRepository memberRepository() {
		return new MemberRepository();
	}
}
이런 느낌으로다가


스프링은 스프링 컨테이너에 스프링 빈을 등록할 때 기본적으로 싱글톤으로 등록한다.
(같은 것은 하나만 등록해서 공유한다!)
따라서 같은 스프링 빈이면 모두 같은 인스턴스이다.
물론 설정으로 싱글톤이 아니게 할 수 있기도하다

DB 변경하는거는
SOLID의 OCP 개방 폐쇄 원칙
확장에는 열려있고 수정에는 닫혀있다.
기존 코드 수정 없이 교체 가능

데이터 관리 하는 법
1. JDBC
제일 옛날 방법

2. JdbcTemplate 실무에서 꽤 사용한다

MyBatis 라이브러리랑 유사
JDBC API에서 본 반복 코드를 대부분 제거해줌
but SQL 직접 작성해야함

3. JPA	
최근 방법
SQL문이 필요없음
자동으로 처리해준다~

데이터 중심 설계에서 객체 중심의 설계로 패러다임을 전환할 수 있다.
개발 생산성이 크게 늘어난다.

strategy=generationType.Identity
자동 생성해주는부분

EntityManager
Jpa는 entitymanager로 다 관리됨
db랑 통신하고 관리하고 다 해줌
.persist() : 영구 저장하기
.find() : 조회하기
.createQuery() : 쿼리 생성해서 검색하기 -> 전체 검색 또는 findByName일 때

CRUD 
SQL짤 필요없음 
찍어서 찾는거 pk기반 아닌거만 createQuery

Jpa는 항상 @transaction이 있어야한다!

스프링 데이터 JPA : JPA를 잘 다룰 수 있게 해주는 도구!! (별개다)


인터페이스를 extends만 해놓으면 알아서 다 해준다...
findByname 이런거 알아서 구현해서 해버림...
기본 메소드들 전부 다 이미 재현되어있습니다... 
save 같은 것도

but 불가능한거
모든 비즈니스 요구 사항이 findByName을 하려는게 아닐 수 있잖아 그래서 다 통일은 아님



인터페이스만으로 구현이 거의 다 되어버렸습니다...



AOP

공통 관심 사항 (예를 들어 비즈니스 로직마다 소요 시간을 측정해준다던지..)
@Aspect

가짜 Spring bean : 프록시를 만들어서
거치게 만든다.







