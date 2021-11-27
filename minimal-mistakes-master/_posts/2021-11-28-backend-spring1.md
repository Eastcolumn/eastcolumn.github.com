---
title: Spring 공부 1회차
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
last_modified_at: '2021-11-28 03:30:00 +0800'
---
모든 것이 http 기반 위에서 동작

IP 인터넷 프로토콜

지정한 IP 주소에 데이터 전달
패킷 단위

한계
비연결성
패킷을 받을 대상이 없거나 서비스 불능 상태에서도 전송
비신뢰성
중간에 패킷 손실, 순서 바뀜
프로그램 구분
같은 IP서버에서 통신하는 어플리케이션이 여러개면?

->TCP와 UDP 도입

TCP 특징
전송제어 프로토콜

연결지향 TCP 3 way handshake(가상 연결)
데이터 전달 보증
순서 보장

신뢰할 수 있는 프로토콜!


PORT
한 IP 주소에서 어플리케이션에 따른 패킷 분류
(프로세스 구분)


DNS
도메인 네임 시스템
복잡하고 자주 변경되는 IP 주소
도메인명 <-> IP 주소
사서 등록해야함

URI
uniform resource identifier
로케이터, 이름 또는 둘 다 추가로 분류
리소스를 식별하는 통일된 방식, 제한이 없음

URL(Locator)
로케이터
URN(Name)
이름

거의 URL만 쓴다.

위치는 변할 수 있지만 이름은 변하지 않는다.
그런데 URN 이름만으로 실제 리소스를 찾는 방법이 보편화되어있지는 않는다.
고로
일반적으로 URL은거의 URI와 비슷한 의미로 이해해도 됨.

주로 프로토콜 사용
예) HTTP, HTTPS, FTP
      80     433  
(포트는 생략 가능)

쿼리(쿼리 파라미터, 쿼리 스트링)
key=value의 형태
?로 시작, &로 추가 가능
숫자로 적어도 문자로 넘어감-> 스트링

fragment #
html 내부 북마크
서버로 전송되는 정보는 아님

실제 요청 흐름

1. dns 조회->ip 알아냄
2. http 요청 메세지 생성
3. 소켓 라이브러리를 통해 전달

HTTP 특징
클라이언트 서버 구조->각각 독립적으로 진화 가능
request, response 구조
무상태 프로토콜(스테이스리스)
비연결성
HTTP 메시지로 통신
단순, 확장 가능

무상태 프로토콜
Stateless

서버가 클라이언트의 상태를 보존하지 않는다.
서버 확장이 쉬움
스케일 아웃 - 수평 확장

but 로그인같은 것은 상태를 유지해야함
일반적으로 브라우저 쿠키와 서버 세션을 통해 유지

+단점 : 데이터를 많이 보내줘야함.

그래도 최대한 무상태로 설계해야하고 어쩔 수 없을 때만 상태 유지


http는 기본이 연결을 유지하지 않는 모델
초 단위 빨라짐
수천명이 사용해도 실제 요청은 매우 적음
(연속해서 검색 버튼 누르지는 않으니까)
=>서버 자원이 매우 효율적으로 사용
단점
TCP/IP 연결을 계속 페이지 이동마다 해줘야함 > 3 way handshake 시간 추가
html 뿐만 아니라 js, css, 이미지 등 많은 자원이 함께 다운로드
지금은 http 지속 연결을 통해 해결했다.



요청 메시지
응답 메세지

시작라인+헤더+공백라인(CRLF)-필수+메세지 바디

시작 라인 = request line(요청) + status line(응답)

종류 : GET, POST, PUT, DELETE
절대 경로 / 로 시작하는 경로

url 설계 : "리소스 식별"

등록하고 수정하고 조회하는게 리소스????
No   행위를 분리하자
-->그렇다고 회원으로하면 다 똑같아지는데요???
--->>>>>그렇기 때문에 GET, POST 등으로 구분

post 등록

put 리소스 대체, 없으면 생성 (덮어 씌우기)
post와 차이점? 경로를 다 알고 지정, post의 경우는 어디에 지정될지 모르는데 put은 리소스를 암
->수정이 아니라 갈아치우기임!

patch 부분 변경->이게 수정
http에서 patch 막힐때도 있다 -> 그냥 post 쓰세용




HTTP 메소드 속성
1. 안전
GET, HEAD 호출해도 리소스를 변경하지 않는다.
계속 호출해서 로그가 쌓여서 서버 터지는건? -> 그런건 딱히 고려하는 부분 X

2. 멱등 -> 복구 고려 사항
Idempotent
한 번 호출하던 두번 호출하든 100번 호출하든 결과가 같다.
GET, PUT, DELETE
POST는 아님

재요청 중간에 다른 곳에서 리소스 변경???
그치만 그런거 고려 X

3. 캐시 가능
응답 결과 리소스를 캐시에서 사용 가능?
GET, HEAD, POST, PATCH 가능
그래도 거의 GET이랑 HEAD만 실무에서 캐시로 씀



클라이언트에서 서버로 데이터 전송
1. 정적 데이터 조회
쿼리 파라미터 미사용
그냥 리소스 경로만 이용해서 조회

2. 동적 데이터 조회
쿼리 파라미터 사용
주로 검색, 게시판 목록 정렬 필터(검색어)
조회 조건을 줄여주는 필터, 조회 결과를 정렬
조회는 GET 사용
GET에 쿼리 파라미터 사용해서 데이터 전달

3. HTML Form 데이터 전송
form 태그를 이용
HTTP 메시지 생성됨
Content-Type : application/x-www-form-urlencoded
*함수가 get이면 쿼리 파라미터 (url)
          post면 바디에
인코딩해서 전송 됨

파일 전송할 때
multipart/form-data
바이너리 파일 전송시 사용 됨
여러 종류의 파일과 폼을 함께 전송한다.


참고로 html form 전송은 get, post만 지원


HTTP API 데이터 전송
application/json xml, txt 되지만 요즘은 json
서버 to 서버 백엔드 시스템 통신
앱 클라이언트
웹 클라이언트
자바 스크립트를 통한 통신 AJAX
Reat, vueJS 같은거

POST PUT PATCH 다 됨
GET 조회 쿼리 파라미터 가능



HTTP API 설계 예시

1. 컬렉션 post 기반 등록
회원관리 시스템
DB에 새로 생성하는걸 서버가 해준다.
요청자는 딱히 모름
즉 post에서는 서버에서 리소스 url을 결정하고 생성한다.
클라이언트는 모른다!

컬렉션 : 서버가 관리하는 리소스 디렉토리
           서버가 리소스를 URI를 생성하고 관리
           예를 들어 /members

2. 스토어 put 기반 등록
정적 콘텐츠, 원격 파일 관리

put 기반 -> 클라이언트가 리소스 URI을 알고 있어야한다.
-->Store 클라이언트가 관리하는 리소스 저장소


3. form 
웹 페이지 회원 관리
get, post만 지원
제약이 심함
그래서 동사로 된 경로 사용
/edit, /new, /delete 같은거
HTTP 메서드가 애매한 경우 사용


*컨트롤러 URI : 동사를 직접 사용한다
ex) /members/{id}/delete




상태 코드
클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능

1XX : informational 요청이 수신되어 처리중 자주 안쓰인다
2XX : succcessful 요청 정상 처리
3XX : redirection 요청 완료하려면 추가 행동이 필요
4XX : client error 클라이언트 오류, 잘못된 문법 등 요청 수행 불가
5XX : server error 서버 오류, 서버가 정상 요청을 처리하지 못함

디테일한 뒷자리는 그냥 대충 몇백대인지 파악해서 클라이언트 행동 설계하면 된다.

200 ok
201 created
202 accepted 접수는 됬으나 처리되지 않음 (배치 처리 같은 곳에서 사용)
204 no content 수행 잘 됬으나 응답 페이로드 본문에 보낼 데이터가 없음 (save 버튼 같은거)

리다이렉션 : 요청을 완료하기 위해 유저 에이전트의 추가 조치가 필요할 때
(더 해줘)
웹 브라우저는 3XX응답의 결과에 Location 헤더가 있으면 Location 위치로 자동 이동

1. 영구 리다이렉션 : 특정 리소스의 URI가 영구적으로 이동, 검색 엔진 등에서도 변경 인지
301 요청 메서드가 GET으로 변하고 본문이 제거될 수도 있다 -> 처음부터 입력 시킴
308 본문 유지

2. 일시 리다이렉션 : 일시적, 주문 완료 후 주문 내역 화면으로 이동, PRG: Post/Redirect?get
3. 특수 리다이렉션 : 결과 대신 캐시 사용


3XX 일시적인 리다이렉션 -> 일시적인거라 검색 엔진 등에서 url 변경하면 안된다
302 요청이 GET으로 변하고 본문 제거될 수도
307 반드시 본문 유지
303 리다이렉트시 요청 GET으로 변경


PRG Post redirect get  URL이 POST->GET으로 리다이렉트, 새로고침해도 GET으로 결과화면만
중복 주문 등을 막음

과정 후에 계속 Get 화면만 보게함으로써 POST를 한번하게 한다.

304 : 캐시를 목적으로 사용
       클라이언트에게 리소스가 수정되지 않았음을 알려준다. 따라서 클라이언트는 로컬 PC에 저장된 캐시를 재사용한다. (캐시로 리다이렉트한다) 메세지 바디를 포함X (로컬 캐시 사용)
        조건부 GET, HEAD 요청시 사용

4XX : 네 잘못
오류가 클라이언트에 있기 때문에 아무리 재요청해도 수행할 수 없다.
요청 파라미터나 API 스펙이 맞지 않을 때
401 : 인증(Authentication)이 되지 않음
       WWW-authenticate 헤더와 함께 인증 방법을 설명 해줌
      Authentication 인증 : 누구인가? 로그인
      Authorization 인가 : 권한이 있나?
     
403 forbidden : 인증 자격은 있지만 권한이 부족
404 not found : 요청 리소스가 서버에 없음 또는 숨길때




5XX : 내 잘못  최대한 없게 만들어야함
혹시 서버 고치면 고쳐질 수도 있다. 
null 포인터 예외....DB접근 불가.. 등등

500 일반적인 서버 오류
503 일시적인 과부하나 예정된 작업으로 잠시 요청 불가
      retry-after 헤더 필드로 얼마 뒤에 복구되는지 보낼 수도 있지만..잘 안씀







HTTP 헤더
1. 일반헤더
전송에 필요한 모든 부가 정보
표준 헤더가 너무 많고 필요시 임의 헤더 추가 가능
종류
General 헤더, Request 헤더, Response 헤더, Entity 헤더

스펙 변경
엔티티 -> 표현 Representation
Representation = representation metadata+representation data

메시지 본문 : 페이로드 (payload)


2. 캐시와 조건부 헤더

표현 : 실제 데이터
표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
표현 헤더 = 표현 메타데이터 + 페이로드 메시지



표현
Content-Type
Content-Encoding
Content-Language
Content-Length

협상 (Content Negotiation)
Accept 클라이언트가 선호하는 미디어 타입 전달
Accept-Charset 클라이언트가 선호하는 문자 인코딩
Accpet-Encoding 클라이언트가 선호하는 압축 인코딩
Accept-Language 클라이언트가 선호하는 자연 언어

협상 헤더 : 요청시에만 사용

Quality Values : 우선 순위!!
Accept-Language: ko-KR, ko; q=0.9, en-US=0.8, en; q=0.7
이라고 치면 
ko-KR;q=1 (q 생략)
ko;q=0.9
en-US;q=0.8
en:q=0.7

ko-KR : 한국인들이 쓰는 한국어
ko;q : 공통 한국어

구체적인 것이 우선!
ex)
Accept: text/*, text/plain
이라치면
text/palin 먼저
 
전송방식
단순 : 길이를 알 때 Length까지 주면서
압축 : gzip 압축해서 줄여서 Content-Encoding이랑 같이
분할 : Transfer-Encoding : chunked 짤라서 보내는거 -> Length 넣으면 안된다 (예상이 안되는 거라서)
범위 : content-Range

일반 정보
From : 유저 에이전트의 이메일
referer : 이전 요청
현재 요청된 페이지의 이전 웹페이지
유입 경로 분석 가능
요청에서 사용
user-agent : 클라이언트의 애플리케이션 정보
server : 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보

특별한 정보
HOST : 요청에서 사용, 필수 값
하나의 서버에서 여러 도메인을 다루는 상황에서 호스트를 넣어줘서 구분함
Location :페이지 리다이렉션
Allow : 허용 가능한 HTTP 메소드
Retry-after : 503서비스가 언제까지 불능인지 알려줌

인증
Authorization : 클라이언트 인증 정보를 서버에 전달
WWW-Authenticate : 리소스 접근시 필요한 인증 방법 정의

쿠키

Set-Cookie
서버에서 클라이언트로 쿠키 전달

Cookie
클라이언트가 서버에서 받은 쿠키를 저장하고 HTTP 요청 시 전달

쿠키?
HTTP는 Stateless 무상태 프로토콜
서로 상태를 유지하지 않는다.
로그인을 기억하지 못한다.

웹 브라우저 내부 : 쿠키 내부 저장소
Set-cookie에 입력된 값을 웹 브라우저 내부에 저장한다.
로그인 이후 접근하면
쿠키를 뒤져서 Cookie로 온게 있는지 확인한다.
모든 요청 정보에 쿠키를 자동으로 포함

제약하기도함(보안)

사용자 로그인 세션
광고 정보 트래킹
but 최소한의 정보만 사용하는게 좋다!

서버에 전송하지 않고 웹 브라우저 내부에 데이터를 저장하고 싶으면
웹 스토리지 (localStroage, sessionStorage)
->매번 보내는게 아니고 필요할 때만 보내는 방법

쿠키 생명 주기
Expires, max-age

세션 쿠키 : 웹브라우져 종료시에도 만료
영속 쿠키 : 만료날짜에 만료

쿠키-도메인
명시 : 명시한 문서+서브 도메인 전부 적용
생략 : 현재 문서에만 접근 가능
쿠키-경로
경로를 포함한 하위 경로 페이지만 쿠키 접근 가능

쿠키-보안
Secure
쿠키는 원래 http, https 구분 안하는데 적용시 https에만 적용
HttpOnly
XSS 공격 방지 자바스크립트 접근 불가 HTTP전송에만 사용
SameSite
XSRF 공격 방지
요청 도메인과 쿠키에 설정된 도메인이 같은 경로에만 쿠키 전송

캐시
cache-control : 캐시가 유요한 시간
아예 네트워크를 사용하지 않고 꺼내올 수 있다.
캐시 시간 초과 시 새로 받아와야한다
이 과정에서
검증헤더와 조건부 요청

서버에서 기존 데이터 변경 안됬을 때는
재사용이 가능하다
이를 확인하기 위해 검증 헤더
Last modified
if modified since

요청도 발생하고 응답도 발생하지만 용량을 엄청 줄여준다

검증헤더
Last-Modified 단점 : 1초 미만 단위로 캐시 조절 불가능, 날짜 기반의 정해진 로직을 따름, 서버 별도의 캐시 로직 관리가 힘듬(스페이스바나 주석 같은거는 바껴도 캐시 유지하고 싶다..)-->Etag
Etag
캐시 제어 로직을 서버에서 완전히 관리




캐시 제어 헤더

Cache-Control : 캐시 지시어(제어)
유효 기간
no-cache : 데이터는 캐시해도되지만 항상 원 서버에 검증하고 사용
no-store : 데이터에 민감한 정보가 있으므로 저장하면 안됨

Pragma (하위호환)
Expires(하위호환)

프록시 캣기
기본은 private 응답이 해당 사용자만을 위함
public으로 설정 가능

캐시 무효화





캐시 무효화
no-cache, no-store, must-revalidate
Pragma : no -cache

이러면 거의 다 무효화됨


