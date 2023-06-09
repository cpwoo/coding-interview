## I. JVM (Java Virtual Machine)
Java를 실행하기 위한 가상 기계, JVM은 Java 바이트코드(.class 파일)을 OS에 특화된 코드로 변환하여 실행해준다. JVM 덕분에 Java는 OS에 무관하게 코드를 짤 수 있다.


JVM의 특징은 다음과 같다.
- 스택 기반의 가상 머신: 인텔x86 아키텍처나 ARM 아키텍처와 같은 하드웨어가 레지스터 기반으로 동작하는데 비해 JVM은 스택 기반으로 동작한다.

- 심볼릭 레퍼런스: 기본 자료형을 제외한 모든 타입(클래스와 인터페이스)를 명시적인 메모리 주소 기반의 레퍼런스가 아니라 심볼릭 레퍼런스를 통해 참조된다.

- 가비지 컬렉션(GC): 클래스 인스턴스는 사용자 코드에 의해 명시적으로 생성되고 가비지 컬렉션에 의해 자동으로 제거된다.

- 기본 자료형을 명확하게 정의하여 플랫폼 독립성 보장: C/C++ 등의 전통적인 언어는 플랫폼에 따라 int형의 크기가 변하지만 JVM은 기본 자료형을 명확하게 정의하여 호환성을 유지하고 플랫폼 독립성을 보장한다.

- 네트워크 바이트 오더(network byte order): 자바 클래스 파일은 네트워크 바이트 오더를 사용한다. 인텔x86 아키텍처가 사용하는 `리틀 엔디안`이나, RISC 계열 아키텍처가 주로 사용하는 `빅 엔디안` 사이에서 플랫폼 독립성을 유지하려면 고정된 바이트 오더를 유지해야 하기에 네트워크 전송 시에 사용하는 바이트 오더인 네트워크 바이트 오더를 사용한다. 네트워크 바이트 오더는 `빅 엔디안`이다.

(+) 스택 기반 아키텍처와 레지스터 기반 아키텍처의 차이 

가상 기계: 소프트웨어로 구현된 하드웨어를 뜻하는 넓은 의미의 용어

물리적인 CPU에 의해 처리되는 동작들을 흉내낼 수 있어야 한다.
- 소스 코드를 VM이 실행가능한 바이트 코드로 변환
- 명령어와 피연산자를 포함하는 데이터구조를 가지고 있어야 한다.
- 함수를 실행하기 위한 콜 스택
- IP(Instruction Pointer): 다음 실행할 곳을 지정하는 포인터
- 가상 CPU: 다음 명령어를 패치 & 명령어를 해석 & 명령을 실행

위와 같은 명세를 만족하는 VM을 구현할 수 있는 방법은 2가지,
스택 기반 아키텍처와 레지스터 기반 아키텍처다.

스택 기반
- 피연산자들을 스택에서 뽑아서 연산 후 결과를 스택에 넣어 저장
- 하드웨어(레지스터, CPU)에 대해 직접적으로 다루지 않으므로 하드웨어에 덜 의존적이다.
- 다음 피연산자는 스택의 TOP에 존재하므로 피연산자의 메모리 주소를 사용할 필요가 없고 명령어에 메모리 주소를 적을 필요가 없으므로 명령어의 길이가 짧아진다.
- 명령어의 수가 많아지고, 스택을 사용하는 오버헤드가 존재하고, 명령어 최적화를 할 수 없다는 단점이 있다.

레지스터 기반
- 피연산자를 레지스터에서 가져와 연산 후 결과를 레지스터에 저장
- 스택과 다르게 뽑았다가 넣는 게 아니기 때문에 명령어 수가 적다.
- 스택을 사용하지 않아 오버헤드가 적다.
- 코드에 동일한 연산식이 존재하면 처음 계산한 결과를 레지스터에 넣어 여러번 계산이 가능하기 때문에 명령어 최적화를 할 수 있다.
- 메모리 주소를 저장하기 때문에 명령어 길이가 길어지는 단점이 있다.

대다수의 VM이 하드웨어에 덜 의존적이란 이유로 스택 기반 VM을 채택하고 있다.

```
심볼릭 레퍼런스: 참고하는 클래스와 특정 메모리 주소를 참조 관계로 구성하지 않고 이름만 가지고 있는 것
```

```
바이트 오더: 시스템이 어떠한 데이터를 표현하는 방법
빅 엔디안: 상위 바이트의 값이 메모리상에 먼저 표시
리틀 엔디안: 하위 바이트의 값이 메모리상에 먼저 표시
```

## II. Java Compile 과정
플랫폼으로부터 독립적인 언어와 Java는 바로 컴파일을 할 수는 없다. 대신, 2단계를 실행해서 할 수 있는데
1. OS에 독립적인 컴파일러를 통해서
2. 모든 운영체제에 맞게 만들어진 JVM으로 컴파일 할 수 있다.

### JVM 컴파일 과정
`*.java` 파일이 컴파일러를 통해 전달된 다음 소스코드를 ByteCode로 인코딩한다. 소스파일에 포함된 각 클래스의 내용은 별도의 `.class` 파일에 저장된다. 소스코드를 ByteCode로 변환하는 동안 컴파일러는 다음 단계를 따른다.

- Parse: `*.java` 소스파일을 읽은 뒤 결과 토큰을 AST(Abstract Syntax Tree) 노드에 매핑한다.

- Enter: 정의된 심볼들을 심볼테이블(Symbol table)에 저장한다.

```
심볼 테이블: 하나의 함수가 호출될 때 필요한 메모리 크기를 미리 계산해 둔 메모리 지도
컴파일 할 때 코드 세그먼트(Code Segement)에 생성된다.

함수가 호출되면 해당 함수의 심볼 테이블을 이용해서 스택 공간에 함수가 필요한 메모리를 확보하여 실행한다.
함수가 종료되면 함수가 사용하던 메모리를 제거하게 된다.
```

- Process annotations: 요청된 경우 지정된 컴파일 위치에서 찾은 어노테이션을 처리한다.

- Attribute: 구문 트리에 속성을 부여하며, 이름 확인, 유형 검사 및 상수 정의가 포함된다.

- Flow: 이전 단계의 트리에 대한 데이터 흐름을 분석한다. 여기에 할당 및 도달 가능성에 대한 검사도 포함된다.

- Desugar: AST를 다시 작성하고 몇몇 syntactic sugar들을 번역한다.

```
syntactic sugar: 사람이 이해하고 표현하기 쉽게 디자인된 프로그래밍 언어 문법
```

- Generate: `*.class` 파일을 생성한다.

## III. Terminal에서 컴파일 및 실행
1. 작성한 코드가 있는 위치로 이동해 `javac 파일명.java` 명령어 작성
2. `.class` 파일 생성 확인 -> 컴파일 완료
3. 이전 컴파일 과정에서 생성한 `.class` 파일을 java 명령어(`java 파일명`)로 실행시킨다.

## IV. ByteCode
특정 하드웨어가 아닌 Virtual Machine에서 돌아가는 실행 프로그램을 위한 이진 표현법으로 하드웨어가 아닌 소프트웨어에 의해 처리되기에 기계어보다 추상적이다. Java ByteCode는 JVM이 실행하는 명령어의 형태로 각각의 ByteCode는 1바이트로 구성되지만 몇 개의 파라미터가 사용되는 경우가 있어 총 몇 바이트로 구성되는 경우가 있다.

## V. JIT 컴파일러 정의와 동작과정
JIT(Just-In-Time) 컴파일러는 인터프리터의 단점을 보완하기 위해 도입되었다. 인터프리터 방식으로 실행하다 적절한 시점에 바이트코드 전체를 네이티브 코드로 바꾼다. 그 다음부터 인터프리터는 네이티브 코드로 컴파일된 코드를 바로 사용한다. 네이티브 코드를 실행하는 것이 하나씩 인터프리팅 하는 것보다 빠르고, 네이티브 코드는 캐시에 보관하기 때문에 한 번 컴파일된 코드는 계속 빠르게 수행된다.

다만 JIT 컴파일러가 컴파일하는 과정은 바이트코드를 하나씩 인터프리팅하는 것보다 훨씬 오래걸리기에 만약 한 번만 실행되는 코드라면 컴파일 하지 않고 인터프리팅하는게 유리하다. 따라서 컴파일러를 사용하는 JVM들은 내부에서 해당 메소드의 수행빈도를 확인 후 일정 정도를 넘을 때 컴파일을 수행한다.

JIT 컴파일러는 바이트코드를 우선 중간 단계의 표현인 IR(Intermediate Representation)로 변환하여 최적화를 수행하고 그 다음 네이티브 코드를 생성한다.

```
IR: 소스 코드를 표현하기 위해 컴파일러 또는 가상 시스템에서 내부적으로 사용하는 데이터 구조 또는 코드로
최적화와 번역 등 추가처리를 위해 도움이 되도록 설계되었다.
```

## VI. JVM 구성 요소
JVM은 크게 클래스 로더 시스템, 메모리, 실행 엔진, 네이티브 메소드 인터페이스(JNI), 네이티브 메소드 라이브러리로 나뉜다. 클래스 로더가 컴파일된 Java ByteCode를 메모리영역에 로드하고, 실행 엔진이 Java ByteCode를 실행한다.

### **클래스 로더 시스템**
자바는 런타임에 클래스를 처음으로 참조할 때 해당 클래스를 로드하고 링크하는 특징이 있다. 이 동적 로드를 담당하는 부분이 JVM의 클래스 로더다. <br>
클래스 로더의 특징은 다음과 같다.
- 계층 구조: 클래스 로더끼리 부모-자식 관계를 이뤄 계층 구조로 생성된다. 여기서 최상위 클래스 로더는 부트스트랩 클래스 로더(Bootstrap Class Loader)다.
- 위임 모델: 계층 구조를 바탕으로 클래스 로더끼리 로드를 위임하는 구조로 동작한다. 클래스를 로드할 때 먼저 상위 클래스 로더를 확인하여 상위 클래스 로더에 있다면 해당 클래스를 사용하고 없다면 로드를 요청받은 클래스로더가 클래스를 로드한다.
- 가시성(visibility) 제한: 하위 클래스 로더는 상위 클래스 로더를 찾을 수 있지만 상위 클래스 로더는 하위 클래스로더의 클래스를 찾을 수 없다.
- 언로드 불가: 클래스 로더는 클래스를 로드할 수는 있지만 언로드할 수는 없다. 언로드 대신, 현재 클래스 로더를 삭제하고 아예 새로운 클래스 로더를 생성하는 방법을 사용할 수 있다.
<br> <br>

**클래스 로더 위임 모델**(상위 클래스 로더부터 순서대로)
- Bootstrap Class Loader: `JAVA_HOME\lib`에 있는 코어 Java API를 제공한다. 최상위 우선순위를 가진 클래스 로더
- Extension Class Loader: `JAVA_HOME\lib\ext` 폴더 또는 java.ext.dirs 시스템 변수에 해당하는 위치에 있는 클래스를 읽는다.
- System Class Loader: 애플리케이션 클래스 path(애플리케이션 실행할 때 주는 `-classpath` 옵션 또는 `java.class.path` 환경 변수 값에 해당하는 위치)에서 클래스를 읽는다.
- User-Defined Class Loader: 애플리케이션 사용자가 직접 코드 상에서 생성해서 사용하는 클래스 로더
<br> <br>

컴파일러에 의해 생성된 클래스 파일은 시스템 또는 OS와 독립적이기에 모든 시스템에서 실행이 가능하다. 실행을 위해 기본 클래스 파일(main 메소드를 포함하는 클래스)이 JVM으로 전달된 다음 최종 코드가 실행되기 전 주요 3가지의 단계를 거친다.
- 로딩
  - 클래스 로더가 `.class` 파일을 읽고 그 내용에 따라 적절한 바이너리 데이터를 만들어 `메소드 영역`에 저장
  - 이 때 메소드 영역에 저장하는 데이터는 다음과 같다.
    - FQCN(Fully Qualified Class Name)
    - Class, Interface, Enum
    - 메소드와 변수
  - 로딩이 끝나면 해당 클래스 타입의 Class 객체를 생성하여 heap 영역에 저장

- 링크
  - `Verify`, `Prepare`, `Resolve(Option)` 세 단계로 나뉜다.
  - 검증(Verify): `.class` 파일 형식이 유효한지 검사
  - 준비(Preparation): 클래스 변수(static 변수)와 기본값이 필요한 메모리 준비
  - Resolve: 심볼릭 메모리 레퍼런스를 메모리 영역에 있는 실제 레퍼런스로 교체

- 초기화: `static` 변수의 값을 할당(`static block`이 있다면 이 때 실행된다.)

### **메모리**
JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역
- 스택 영역: 지역 변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값들이 생성되는 영역으로 클래스 수준의 정보를 저장하고 공유자원이다. `int a=10;`이라는 코드를 작성했다면, `a`라는 메모리 영역을 잡고 해당 영역에 10을 할당한다. 즉, 스택에 a라는 이름의 값이 10인 메모리 공간을 만든다.

- PC Register: Thread가 실행될 때마다 생성되는 영역으로 현재 Thread가 실행되는 부분의 주소와 명령을 저장하고 있는 영역이다. 이것을 이용해 Thread를 돌아가며 수행할 수 있게 한다.

- 네이티브 메소드 스택: Java 외 언어로 작성된 네이티브 코드를 위한 메모리영역으로 보통 C/C++등의 코드를 수행하기 위한 스택이다.(JNI)

- 힙(heap): `new` 키워드로 생성된 객체와 배열이 생성되는 영역이다. 메소드 영역에 로드된 클래스만 생성이 가능하고 GC가 참조되지 않는 메모리를 확인/제거하는 영역이다. 위에서 설명했듯이 클래스 수준의 정보는 스택에 저장되지만 객체의 인스턴스는 힙영역에 생성된다. 그리고 스택영역에는 힙영역의 주소값을 저장한다.

- 메소드 영역: 모든 Thread가 공유하는 영역으로 JVM이 시작될 때 생성된다. 저장하는 내용은 JVM이 읽어들인
  - 각각의 클래스
  - 인터페이스에 대한 런타임 상수 풀
  - 필드와 메소드 정보
  - static 변수
  - 메소드의 ByteCode 등을 저장한다.

### **실행 엔진** (Execution Engine)
클래스 로더에 의해 메모리에 적재된 클래스들을 기계어로 변경해 명령어 단위로 실행하는 역할을 한다. 명령어를 하나 하나 실행하는 방식은 `인터프리터`와 `JIT 컴파일러`가 있다. 그리고 더 이상 참조되지 않는 객체를 모아서 정리하는 GC(Garbage Collector)가 있다. GC는 상황에 맞는 적절한 GC를 사용한다.

### **JNI(Java Native Interface) 네이티브 메소드 인터페이스 & 라이브러리**
- Java 언어 자체로 해결이 안 되는 경우, 대처할 수 있는 방법 중 하나
  - Ex: DB 조회의 속도 성능 향상을 위해서 C/C++ 라이브러리 사용이 필요한 경우
- 자바 애플리케이션에서 C, C++, 어셈블리로 작성된 함수를 사용할 수 있는 방법을 제시한다.
- Native Keyword를 사용한 메소드 호출

```
Native Keyword: 자바가 아닌 언어로 구현한 후 
자바에서 사용하려고 할 때 이용하는 키워드
```

## VII. JRE와 JDK의 차이
- JRE(Java Runtime Environment): JVM + 라이브러리
  - 자바 애플리케이션을 실행할 수 있도록 구성된 배포판
  - JVM과 핵심 라이브러리 및 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있다.
  - 개발 관련 도구는 포함하지 않는다. (JDK에서 제공)

```
JDK 11 이후(Java Module 추가)로는 Jlink가 추가되어 JRE가 따로 배포되지 않는다.
```

- JDK(Java Development Kit): JRE + 개발 툴
  - JRE + 개발에 필요한 툴
  - 소스 코드를 작성할 때 사용하는 자바 언어는 플랫폼에 독립적
  - Write Once Run Anywhere

## VIII. HotSpot JVM
Hot한 Spot을 찾아 그 부분에서 JIT Compiler를 사용하는 JVM <br>
내부적으로 프로파일링을 통해 핫스팟을 찾아 해당 부분에 대한 네이티브를 생성하는데, 네이티브 코드를 만드는 방법으로 Client와 Server 두 방법이 존재한다. 이는 Java SE안에 포함되어있어 자바 시작시 모드를 선택할 수 있다.

1. Client: 스타트업 시간과 메모리공간 최적화 중점 모드 <br>
Client 모드에서 동작하는 컴파일러는 주로 프로그램의 시작 시간을 최소하는데 집중한다. Client 모드에서는 바이트코드로부터 최대한 많은 정보를 뽑아 실제 동작하는 코드 블럭에 대한 최적화에 집중하며 전체적인 최적화는 관심없다.
- 바이트 코드를 해석한 뒤 최적화를 쉽게 하기 위해 HIR이라는 정적 바이트 코드 표현을 만든다.
- HIR로부터 플랫폼에 종속적인 중간표현식(LIR)을 만든다.
- LIR을 사용해 기계어를 생성한다.
<br> <br>

2. Server: 다수의 request를 빠르게 처리하는데 중점을 둔 모드 <br>
Server 모드에서 JIT는 부분적인 코드 실행보다는 전체적인 성능 최적화에 관점을 둔다.
- 일반적인 컴파일러 최적화 기술들을 이용해 코드들을 최적화한다. <br> -> 죽은 코드 삭제(Dead Code Elimination) 
<br> -> Loop 변수 끌어올리기(Loop invariants hosting)
<br> -> 공통 부분식 제거(Common Subexpression Elimination)
<br> -> 상수 지연(Constant propagation)
<br> -> 전역 코드 이동(Global Code Motion)
<br> ...

- 자바에 최적화된 최적화를 진행한다.
<br> -> Null Check 삭제
<br> -> 배열의 Range Check 삭제
<br> -> 예외처리 경로 최적화
<br> 대단위 RICS 레지스터들을 최대한 활용하기 위한, Graph 연산을 통한 Register 할당
<br> <br>

참고: https://imdev.tistory.com/23 <br>
https://jjjayyy.tistory.com/20 <br>
https://fors.tistory.com/80 <br>
https://catsbi.oopy.io/df0df290-9188-45c1-b056-b8fe032d88ca <br>
https://asfirstalways.tistory.com/m/158 <br>
https://www.korecmblog.com/jvm-stack-and-register/ <br>
https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=kbh3983&logNo=221292870568
