## URI (Uniform Resource Identifier)
통합 자원 식별자. URI는 인터넷상의 리소스 자원 자체를 식별하는 고유한 문자열 시퀀스다.

## URL (Uniform Resource Locator)
네트워크상에서 통합 자원의 "위치"를 나타내기 위한 규약. 즉, 자원 식별자와 위치를 동시에 보여준다. 특정 웹 페이지의 주소에 접속하기 위해서는 웹 사이트의 주소뿐만 아니라 프로토콜(https, http, sftp, smp ...)을 함께 알아야 접속이 가능한데, 이들을 모두 나타내는 것이 URL이다.

## URI와 URL의 차이점
URI = 식별자, URI = 식별자+위치
google.com은 URI다. 리소스의 이름만 나타내기 때문이다. <br>
https://www.google.com은 URL이다. 이름과 더불어, 어떻게 도달할 수 있는지 위치까지 함께 나타내기 때문이다.

- URL은 일종의 URI다.
- URL은 프로토콜과 결합한 형태다.
- URI는 그 자체로 이름이 될 수 있다.

## URI URL 구조
`https://github.com/cpwoo/coding-interview`를 보면 `https://`은 Scheme, `github.com/`은 Host, `cpwoo/coding-interview`는 Path다.
- Scheme: 리소스를 접근하는 데 사용할 프로토콜. 웹에서는 http 또는 https 사용
- Host: 접근할 대상(서버)의 호스트명
- Path: 접근할 대상(서버)의 경로에 대한 상세 정보

참고로, Path에 해당하는 부분은 URN이라 한다.
- URI/URL : Scheme + Host + Path
- URI : Host + Path
- URN : Path

## URN (Uniform Resource Name)
URN은 리소스의 위치, 프로토콜, 호스트 등과는 상관없이 각 자원에 이름을 부여한 것이다. 즉, URL은 어떤 특정 서버에 있는 웹 문서를 가리키는 반면, URN은 웹 문서의 물리적인 위치와 상관없이 웹 문서 자체를 나타낸다. <br>
이처럼 개별 자원에 식별자를 부여하게 되면 해당 정보에 대한 URN은 일정하게 유지되며 리소스의 위치, 프로토콜, 호스트와 관계없이 위치를 파악할 수 있다는 장점이 있다. <br>
예를 들어, 웹 문서가 다른 웹 서버로 이동하거나 주소가 바뀌는 등 물리적 위치가 변경되더라도 해당 문서를 찾을 수 있다. <br> <br>

참고: https://www.elancer.co.kr/blog/view?seq=74
