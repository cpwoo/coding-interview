## HSTS(HTTP Strict Transport Security)
HSTS는 클라이언트가 웹사이트를 HTTP로 접속하려 할때 HTTPS Protocol로 바꿔서 접속하게 강제하는 역할을 한다. <br>
HSTS 기능을 사용하려면, 웹서버와 웹브라우저 둘 다 기능을 지원해야 한다. HSTS 기능을 지원하는 웹서버를 "HSTS enabled Server"라 한다. <br>
HSTS 기능은 웹사이트 보안에 관련된 Policy를 설정하는 기능에 속한다. 또 HSTS는 IETF 표준이며, RFC 6797로 세부내용이 기술되어 있다.

## HSTS 사용 목적
대부분의 사용자들은 http:// 나 https:// 같은 프로토콜을 쓰지 않고 도메인 이름만 입력한다. 그러면 웹 브라우저는 먼저 HTTP Protocol로 해당 도메인에 접속을 시도한다. 해당 도메인이 HTTPS Protocol만을 지원하는 웹사이트라면, "301 Redirect" 또는 "302 Redirect" Response를 보내어, 웹브라우저로 하여금 HTTPS Protocol로 다시 접속하라고 지시한다. <br>
그런데 해커와 같은 공격자가 중간에 Proxy 서버를 두어 사용자와는 HTTP 통신을 하고 웹서버와는 HTTPS 통신을 해도 사용자는 알 수가 없다. 이러한 공격을 SSL Stripping 공격이라 하고, 이러한 공격을 방지하기 위해 HSTS 기능을 사용한다. SSL Stripping은 SSL/TLS Hijacking이라고도 한다. <br>
또한 cookie hijacking을 방지하는 용도로도 사용한다.

## HSTS 기능의 동작
HSTS를 지원하는 웹브라우저는 내부에 HSTS 리스트를 보유하고 있다. 사용자가 웹브라우저 주소창에 도메인 이름을 입력하면, 또한 링크된 URL 주소를 클릭하면, 도메인 주소만 추출하여 HSTS 리스트에 있는 지 확인하여, 있으면 HTTPS Protocol을 사용하여 접속하게 된다. <br>
웹 브라우저가 HSTS 기능이 설정된 사이트에 HTTPS Protocol로 접속할 경우, 웹 서버는 HTTPS Reply Message에 HSTS Policy를 넣어서 보낸다. (암호화 되기 전 packet인, HTTP response header field에 "Strict-Transport-Security" 필드 정보 삽입) <br>
웹 브라우저는 이 정보를 받아서 내부 HSTS 리스트를 구성한다. 참고로, Max Age 값, Subdomain 적용여부 값, Preloaded 리스트에 추가여부 값을 함께 전달한다. Max Age 값은 HSTS 리스트에 존속하는 시간을 나타내며, Subdomain에도 HSTS를 적용할 지 여부를 알려준다. Age 값이 0(zero)인 경우에는 HSTS Policy를 삭제하라는 명령이다. <br>
만약 웹브라우저가 HTTP Protocol로 웹서버에 접속하고, HTTP Reply Message에 HSTS Policy 정보가 들어 있는 경우는, 무시된다고 한다. <br>
참고로, HSTS response header의 Syntax는 다음과 같다.
- Strict-Transport-Security: max-age=`<expire-time`>
- Strict-Transport-Security: max-age=`<expire-time`>; includeSubDomains
- Strict-Transport-Security: max-age=`<expire-time`>; preload

## Preloaded HSTS Lists
이미 만들어져 있는 HSTS Lists를 웹브라우저에 제공하는 기능도 지원하고 있는 데, 이런 HSTS Lists를 "Preloaded HSTS Lists"라고 한다. Preloaded HSTS Lists는 웹브라우저 설치 시 또는 Update 시에 Web Browser Vendor에 의해 제공된다. 참고로 MS사는 분기별로 Update한다. <br>
Preloaded HSTS Lists를 사용하는 목적은, 만약 없다면, 웹브라우저가 HTTPS Protocol을 지원하는 웹사이트를 HTTP Protocol을 사용하여 처음 접속했을 때, SSL Stripping 공격을 받을 가능성이 있기 때문에, 이를 차단하기 위함이다. <br> <br>
참고: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aepkoreanet&logNo=221575708943
