## REST(REpresentational State Transfer)
자원을 이름(자원의 표현)으로 구분해 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다. <br>
즉, 자원(resource)의 표현(representation)에 의한 상태 전달을 뜻한다.
- 자원: 해당 소프트웨어가 관리하는 모든 것 (문서, 그림, 데이터, 해당 소프트웨어 자체 등)
- 표현: 그 자원을 표현하기 위한 이름 (DB의 학생 정보가 자원이면, 'students'를 자원의 표현으로 정함)
- 상태 전달: 데이터가 요청되는 시점에 자원의 상태를 전달한다. (JSON 또는 XML을 통해 데이터를 주고 받는 것이 일반적)

REST는 기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에, 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다. REST는 네트워크 상에서 클라이언트와 서버 사이의 통신 방식 중 하나다.

어떤 자원에 대해 CRUD 연산을 수행하기 위해 URI(Resource)로 GET, POST 등의 방식(Method)을 사용하여 요청을 보내며, 요청을 위한 자원은 특정한 형태(Representation of Resource)로 표현된다.

## REST 구성 요소
- 자원(Resource) - URI
  - 모든 자원에는 고유한 ID가 존재하고, 이 자원은 서버에 존재한다.
  - 자원을 구별하는 ID는 '/exgroups/:exgroup-id'와 같은 HTTP URI다.
  - 클라이언트는 URI를 이용해 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 서버에 요청한다.
- 행위(Verb) - Method
  - HTTP 프로토콜의 Method를 사용한다.
  - HTTP 프로토콜은 GET, POST, PUT, PATCH, DELETE의 Method를 제공한다.
- 표현(Representation of Resource)
  - 클라이언트와 서버가 데이터를 주고받는 형태로 JSON, XML, TEXT, RSS 등이 있다.
  - JSON, XML을 통해 데이터를 주고 받는 것이 일반적이다.

## REST 특징
1. Server-Client (서버-클라이언트 구조)
- 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
  - REST Server는 API를 제공하고 비즈니스 로직 처리 및 저장을 책임지고,
  - Client는 사용자 인증이나 context(세션, 로그인 정보) 등을 직접 관리하고 책임진다.
  - 역할을 확실히 구분시킴으로써 서로 간의 의존성을 줄인다.
2. Stateless (무상태)
- HTTP 프로토콜은 Stateless Protocol이므로 REST 역시 무상태성을 갖는다.
- Client의 context를 Server에 저장하지 않는다.
  - 즉, 세션과 쿠키와 같은 context 정보를 신경쓰지 않아도 되므로 구현이 단순해진다.
- Server는 각각의 요청을 완전히 별개의 것으로 인식하고 있다.
  - 각 API 서버는 Client의 요청만을 단순 처리한다.
  - 즉, 이전 요청이 다음 요청의 처리에 연관되어서는 안 된다. (DB에 의해 바뀌는 것은 허용)
  - Server의 처리 방식에 일관성을 부여하기 때문에 서비스의 자유도가 높아진다.
3. Cacheable (캐시 처리 기능)
- 웹 표준 HTTP 프로토콜을 그대로 사용하므로 웹에서 사용하는 기준의 인프라를 그대로 활용할 수 있다.
  - 즉, HTTP가 가진 가장 강력한 특징 중 하나인 캐싱 기능을 활용할 수 있다.
  - HTTP 프로토콜 표준에서 사용하는 Last-Modified Tag 또는 E-Tag를 이용해 캐싱을 구현한다.
- 대량의 요청을 효율적으로 처리할 수 있다.
4. Layered System (계층 구조)
- Client는 REST API Server만 호출한다.
- REST Server는 다중 계층으로 구성될 수 있다.
  - 보안, 로드 밸런싱, 암호화 등을 위한 계층을 추가하여 구조를 변경할 수 있다.
  - Proxy, Gateway와 같은 네트워크 기반의 중간매체를 사용할 수 있다.
  - 하지만 Client는 Server와 직접 통신하는지, 중간 서버와 통신하는지는 알 수 없다.
5. Uniform Interface (인터페이스 일관성)
- URI로 지정한 Resource에 대한 요청을 통일되고, 한정적으로 수행하는 아키텍처 스타일을 의미한다.
- HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하며, Loosely Coupling (느슨한 결합) 형태를 갖는다.
  - 즉, 특정 언어나 기술에 종속되지 않음
6. Self-Descriptiveness (자체 표현)
- 요청 페이지만 보고도 쉽게 이해할 수 있는 자체 표현 구조로 되어있다.

## REST API
**정의** <br>
- REST의 특징을 기반으로 서비스 API를 구현한 것
- 최근 OpenAPI, 마이크로서비스 등을 제공하는 기업 대부분은 REST API를 제공한다.

**특징** <br>
- REST API의 가장 큰 특징은 각 요청이 어떤 동작이나 정보를 위한 것인지를 그 요청의 모습 자체로 추론이 가능한 것이다.

**REST API 디자인 가이드** <br>
- URI는 정보의 자원을 표현해야 한다.
- 자원에 대한 행위는 HTTP Method로 표현한다.
  - 행위(Method)는 URI에 포함되지 않는다.

**REST API 설계 규칙** <br>
1. URI는 명사를 사용한다. (리소스명은 동사가 아닌 명사를 사용해야 한다.)
- 아래와 같은 동사를 사용하지 말 것
  - /getAllUsers
  - /getUserById
  - /createNewUser
  - /updateUser
  - /deleteUser
2. 슬래시(/)로 계층 관계를 표현한다.
3. URI 마지막 문자로 슬래시(/)를 포함하지 않는다.
4. 밑줄(_)을 사용하지 않고, 하이픈(-)을 사용한다.
5. URI는 소문자로만 구성한다.
6. HTTP 응답 상태 코드 사용
- 클라이언트는 해당 요청에 대한 실패, 처리완료 또는 잘못된 요청 등에 대한 피드백을 받아야 한다.
7. 파일확장자는 URI에 포함하지 않는다.

## RESTful API
RESTful API는 REST의 설계 규칙을 잘 지켜서 설계된 API다. <br>
즉, REST의 원리를 잘 따르는 시스템을 RESTful이란 용어로 지칭된다.

## 결론
URI는 정보의 자원만 표현해야 하며, 자원의 행위는 HTTP Method에 명시한다.
<br> <br>
참고: https://dev-coco.tistory.com/97 <br>
https://www.youtube.com/watch?v=iOueE9AXDQQ
