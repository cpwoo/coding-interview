## SOP (Same-Origin Policy)
"같은 출처에서만 리소스를 공유할 수 있다"라는 규칙을 가진 정책이다. 서로 다른 출처가 통신하는 것에 제약이 없다면 XSS나 CSRF 같은 방법으로 우리의 애플리케이션에서 코드가 실행된 것 처럼 꾸며 사용자 정보를 탈취하기 쉬워진다.
```
Cross-Site Scripting (XSS)
1. 사용자의 쿠키 정보를 가져오는 JS 코드(악성코드)를 포함한 게시글을 작성한다.
2. 어떤 사용자가 로그인하면 쿠키에 그 정보가 저장됨
3. 해커가 작성해둔 게시글을 사용자가 클릭했을 때 악성코드가 작동
4. 쿠키에 저장된 사용자 정보가 해커의 웹사이트에 적용됨

Cross-site request forgery (CSRF) - 사이트 간 요청 위조
1. 사용자가 보안이 취약한 서비스에 로그인한다.
2. 사용자 정보가 쿠키에 저장된다.
3. 해커가 만든 피싱 사이트에 접속한다. (클릭 유도, 위조된 광고 등을 통해)
4. 해커가 원하는 대로 사용자 정보가 바뀐다.
```
그러나 웹 개발 중 다른 출처의 리소스를 가져와 사용하는 일은 흔한 일이라 몇 가지 예외 조항을 두고 있다. 이 조항에 해당하는 리소스 요청은 출처가 다르더라도 허용해주고 있는데 그 중 하나가 CORS 정책을 지킨 리소스 요청이다.

## CORS (Cross-Origin Resource Sharing)
CORS는 서로 다른 출처가 리소스를 공유하기 위해 존재한다. <br>
출처(Origin)란 서버의 위치를 찾아가기 위해 필요한 가장 기본적인 것들을 모아둔 것으로 프로토콜, 호스트, 포트 번호까지 모두 합친 것을 의미한다.
```
Origin: http://localhost:3000
```
이때 출처 내 포트 번호는 생략이 가능한데, 이는 각 웹에서 사용하는 HTTPS, HTTPS 프로토콜의 기본 포트 번호(80)가 정해져있기 때문이다. 만약 4000과 같이 출처에 포트 번호가 명시적으로 포함되어 있다면 이 번호까지 모두 일치해야 같은 출처로 인정된다.

### 응답의 파기 여부는 브라우저가 결정한다
이렇게 출처를 비교하는 로직은 서버에 구현된 스펙이 아니라 브라우저에 구현되어 있는 스펙이다. 만약 우리가 CORS 정책을 위반하는 리소스 요청을 하더라도 해당 서버가 같은 출처에서 보낸 요청만 받겠다는 경우가 아니면 서버는 정상적으로 응답한다. 하지만 이 응답을 브라우저가 받아 분석해 봤을 때 CORS 정책 위반이라고 판단되면 그 응답을 버린다. <br>
따라서 브라우저를 통하지 않고 서버 간 통신(ex.Postman)을 할 때는 이 정책이 적용되지 않는다.

## CORS 동작 원리
기본적으로 웹 클라이언트 애플리케이션이 다른 출처의 리소스를 요청할 때는 HTTP 프로토콜을 사용하여 요청을 보내게 되는데, 이때 브라우저는 요청 헤더에 Origin이라는 필드에 요청을 보내는 출처를 함께 담아 보낸다. <br>
이후 서버가 이 요청에 대한 응답을 할 때 응답 헤더의 Access-Control-Allow-Origin이라는 값에 '이 리소스에 접근하는 것이 허용된 출처'를 담아 보내준다. 그러면 브라우저는 자신이 보냈던 요청의 Origin과 서버로부터 받은 응답의 Access-Control-Allow-Origin을 비교해본 후 유효한 응답인지를 결정한다. <br>
이는 기본적인 흐름이고, CORS 동작 방식에는 세 가지 시나리오가 있다.

### 1. Preflight Request
일반적으로 웹 개발 시 자주 마주치게 되는 시나리오로, 이 시나리오에 해당되는 상황일 경우 브라우저는 요청을 예비 요청과 본 요청으로 나누어 서버에 전송하게 된다. <br>
이때 브라우저가 본 요청을 보내기 전에 보내는 예비 요청을 Preflight라고 부른다. 이는 본 요청을 보내기 전 브라우저 스스로 이 요청을 보내는 것이 안전한지 확인하는 과정으로, HTTP 메소드 중 OPTIONS 메소드가 사용된다. <br>
우리가 자바스크립트의 fetch API를 이용해 브라우저에게 리소스를 받아오라는 명령을 내리면 브라우저는 서버에게 예비 요청을 먼저 보내게 되고, 서버는 이 요청에 대한 응답으로 현재 자신이 어떤 것들을 허용하고 금지하는지에 대한 정보를 응답 헤더에 담아 브라우저에게 다시 보내주게 된다. <br>
이후 브라우저는 자신이 보낸 예비 요청과 서버가 응답에 담아준 허용 정책을 비교한 후 요청을 보내도 안전하다고 판단되면 같은 엔드포인트로 다시 본 요청을 보내게 된다. 이후 서버가 본 요청에 대한 응답을 하면 브라우저는 최종적으로 응답 데이터를 자바스크립트에게 넘겨준다.

### 2. Simple Request
대부분 Preflight 방식을 사용하지만, 어떤 경우에는 예비 요청없이 본 요청만으로 CORS 정책 위반 여부를 검사하기도 한다. <br>
단순 요청은 예비 요청없이 바로 서버에게 본 요청부터 보낸 후, 서버가 이에 대한 응답의 헤더에 Access-Control-Allow-Origin과 같은 값을 보내주면 그때 브라우저가 CORS 정책 위반 여부를 검사하는 방식이다. 즉, Preflight와 단순 요청 시나리오는 전반적인 로직 자체는 같고, 예비 요청의 존재 유무만 다르다. <br>
이 시나리오는 아래 조건들을 모두 만족해야만 예비 요청을 생략할 수 있다.
1. 요청의 메소드는 GET, HEAD, POST 중 하나여야 한다.
2. Accept, Accept-Languge, Content-Language, Content-Type, DPR, Downlink, Save-Data, Viewport-Width, Width를 제외한 헤더를 사용하면 안된다.
3. 만약 Content-Type을 사용하는 경우에는 application/x-www-form-urlencoded, multipart/form-data, text/plain만 허용된다.

이 중 2,3번 조건이 까다롭다. 사용자 인증에 사용되는 Authorization 헤더도 사용하면 안 되고, 대부분의 HTTP API에서 사용되는 text/xml이나 application/json 컨텐츠 타입도 가지면 안된다. 따라서 이 시나리오는 현실적으로 조건을 만족시키기 어렵다.

### 3. Credentialed Request
마지막으로 인증된 요청을 사용하는 방법이다. 이 시나리오는 CORS의 기본적인 방식이라기보단 다른 출처간 통신에서 좀 더 보안을 강화하고 싶을 때 사용하는 방법이다. <br>
기본적으로 브라우저가 제공하는 비동기 리소스 요청 API인 XMLHttpRequest 객체나 fetch API는 별도의 옵션 없이 브라우저의 쿠키 정보나 인증과 관련된 헤더를 함부로 요청에 담지 않는다. 이때 요청에 인증과 관련된 정보를 담을 수 있게 해주는 옵션이 바로 credentials 옵션이다. <br>
이 옵션은 총 3가지 값을 사용할 수 있다.
- same-origin (기본값) : 같은 출처 간 요청에만 인증 정보를 담는다.
- include : 모든 요청에 인증 정보를 담는다.
- omit : 모든 요청에 인증 정보를 담지 않는다.

same-origin이나 include와 같은 옵션을 사용하여 리소스 요청에 인증 정보가 포함된다면, 브라우저는 단순히 Access-Control-Allow-Origin만 확인하는 것이 아니라 좀 더 엄격하게 검사하게 된다. <br>
서버측(`http://localhost:4000/api`)에 Access-Control-Allow-Origin이 모든 출처를 허용한다는 *로 설정되어 있다고 가정해보자. 이 경우 다른 출처에서 해당 서버에 리소스를 요청해도 CORS 정책 위반으로 인한 제약을 받지 않는다. 그렇기 때문에 `http://localhost:3000`과 같은 클라이언트 로컬 개발 환겨에서도 fetch API를 사용하여 리소스를 요청하고 받아올 수 있다. <br>
구글 Chrome 브라우저의 credentials 기본값은 same-origin이기 때문에, 포트 3000에서 4000으로 보내는 리소스 요청에는 브라우저의 쿠키와 같은 인증 정보가 포함되지 않는다. 그렇기 때문에 브라우저는 Access-Control-Allow-Origin: *이라는 값만 보고 해당 요청이 안전하다고 결론을 내리게 된다. <br>
하지만 credentials 옵션을 include로 변경한 후 요청을 보내게 되면 어떻게 될까?
```
fetch('http://localhost:4000/api', {
    credentials: 'include',
});
```
이번에는 동일 출처 여부와 상관없이 무조건 요청에 인증 정보가 포함되도록 설정했으므로, 브라우저의 쿠키 정보가 담겨진다. 하지만 서버는 이전과 동일한 응답을 보내주는 반면, 브라우저는 CORS 정책을 위반했다는 반응을 보인다. <br>
이는 요청에 인증 정보가 담겨있는 상태에서 다른 출처의 리소스를 요청하게 되면, 브라우저는 CORS 정책 위반 여부를 검사하는 룰에 다음 두 가지를 추가하여 검사하기 때문이다.
1. Access-Control-Allow-Origin에는 *를 사용할 수 없으며, 명시적인 URL이어야 함
2. 응답 헤더에는 반드시 Access-Control-Allow-Crendentials: true가 존재해야 함

이 경우 서버에서 CORS 설정 시 허용할 origin 값을 *가 아닌 `http://localhost:3000`으로 지정하고, 응답 헤더에 Access-Control-Allow-Credentials: true 를 명시하여 해결할 수 있다.

## CORS 에러 해결하기
작성된 코드는 이해를 돕기 위해 작성된 것으로, 이렇게 작성한다는 것만 보고 넘어가면 된다. (즉, 암기 대상이 아니다. 당연하게도)
### 1. 클라이언트에서 해결하기
- 웹 브라우저 실행 옵션이나 플러그인을 통한 동일 출처 정책 회피하기
  - 동일 출처 정책은 브라우저에서 임의로 하는 것이기 때문에 브라우저에서 동일 출처 정책을 사용하지 않으면 된다.
- jsonp 방식으로 json 데이터 가져오기
  - 자바스크립트 파일이나 css 파일은 동일 출처 정책에 영향을 받지 않고 가져올 수 있다.
  - 이를 이용해서 자바스크립트 파일을 가져와서 이를 json 형식으로 파싱해서 데이터를 사용할 수 있다.

### 2. 서버에서 해결하기
**스프링 CORS** <br>
**@CrossOrigin 어노테이션 사용하기** <br>
메소드 레벨 및 글로벌 레벨에서 spring mvc 애플리케이션에서 spring cors를 지원하는 방법이다. spring mvc는 @CrossOrigin 어노테이션을 제공한다. 이 어노테이션은 어노테이션이 달린 메소드 또는 타입을 교차 출처를 허용하는 것으로 표시된다. <br>
기본적으로 @CrossOrigin은 모든 출처, 모든 헤더, @RequestMapping 주석에 지정된 Http 메소드에 최대 30분을 허용한다. 어노테이션에 속성 값을 넣어 기본 값을 대체할 수 있다. <br>
속성값을 살펴보면,
- origins
  - 허용된 출처, 이 값은 Preflight 응답과 실제 응답 모두에 Access-Control-Allow-Origin 헤더에 배치된다.
- allowedHeaders
  - 실제 요청 중에 사용할 수 있는 요청 헤더 목록이다. Preflight의 응답 헤더인 Access-Control-Allow-Header에 값이 사용된다.
```
@CrossOrigin(origin="*", allowedHeaders="*")
@Controller
public class MainController {
    @GetMapping("/")
    public String main() {
        return "main";
    }
}
```
**CorsFilter 사용하기** <br>
서블릿 필터 인터페이스를 이용하여 개발되었다. 웹 서버의 모든 리소스의 요청을 가로채서 Cross domain request인지 체크하여 실제 요청 페이지에 전달하기 전에 적절한 CORS 정책과 헤더들을 적용한다.
- Access-Control-Allow-Origin
  - 도메인 간 요청을 할 수 있는 권한이 부여된 도메인을 지정한다.
- Access-Control-Allow-Credentials
  - 도메인 간 요청에 credential 권한이 있는지 없는지 지정한다.
- Access-Control-Expose-Headers
  - 노출하기에 안전한 헤더를 나타낸다.
- Access-Control-Max-Age
  - Preflighted 요청이 얼마만큼의 시간동안 게시되는지
- Access-Control-Allow-Methods
  - 리소스에 접근할 때 메소드가 허용되었는지
- Access-Control-Allow-Headers
  - 어떤 헤더 필드 네임이 실제 요청에서 사용할 수 있는지 가리킨다.
```
@Component
public class SimpleCorsFilter implements Filter {

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
        response.setHeader("Access-Control-Allow-Credentials", "true");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT, PATCH");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Content-Type, Accept, X-Requested-With, remember-me");
        chain.doFilter(req, res);
    }

    @Override
    public void init(FilterConfig filterConfig) {
    }

    @Override
    public void destroy() {
    }
}
```

참고:https://velog.io/@nemo/cors <br>
https://bohyeon-n.github.io/deploy/web/cors.html <br>
https://velog.io/@wiostz98kr/CORS%EC%9D%98-%EB%AA%A8%EB%93%A0-%EA%B2%83 <br>
https://developer.mozilla.org/ko/docs/Web/HTTP/CORS
