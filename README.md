# 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
## spring initializr
> 스프링 부트 기반으로 스프링 프로젝트를 만들어 주는 사이트  
> start.spring.io

- Project
  - Maven Project: 예전에 많이 씀
  - Gradle Project: 버전설정하고 라이브러리 땡겨 옴 (요즘엔 Gradle을 거의 씀)
- Spring Boot
  - (SNAPSHOT)은 만들고 있는 것
  - (M1)은 아직 정식 release된 버전이 아님
  - 정식 release된 버전 중 최신을 선택한다
- Project Metadata
  - Group
  - Artifact: 빌드되어 나온 결과물(프로젝트 명)
- Dependencies
: 어떤 라이브러리를 땡겨 쓸건지
  - Spring Web
  - Thymeleaf

GENERATE 클릭

## Library
**build.gradle** 파일에서 관리  
- dependencies
  - 라이브러리 간의 의존관계
  - 예를 들어 spring-boot-starter-web이 필요하다면 그 라이브러리에 사용되는 다른 라이브러리도 같이 땡겨와야 한다.
  - 
### 스프링 부트 라이브러리
- **spring-boot-starter-web**
  - spring-boot-starter-tomcat: 톰캣 (웹서버)
  - spring-webmvc: 스프링 웹 MVC
- **spring-boot-starter-thymeleaf**: 타임리프 템플릿 엔진(View)
- **spring-boot-starter**(공통): 스프링 부트 + 스프링 코어 + 로깅
  - spirng-boot
    - spring-core
  - spring-boot-starter-logging
    - logback, slf4j
### 테스트 라이브러리
- **spring-boot-starter-test**
  - junit: 테스트 프레임워크
  - mockito: 목 라이브러리
  - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
  - spring-test: 스프링 통합 테스트 지원

## View 환경설정
### Welcome Page
`/src/main/resources/static/index.html`
> Spring Boot supports both static and templated welcome pages. It first looks for an index.html file in the configured static content locations. If one is not found, it then looks for an index template. If either is found, it is automatically used as the welcome page of the application.

### thymeleaf 템플릿 엔진
> thymeleaf.org

## 빌드하고 실행
  ```
  chmod +x gradlew    // 권한에러 발생시에만 실행
  ```
  ```
  ./gradlew build
  ```
  ```
  cd build/libs
  ```
  ```
  java -jar hello-apring-0.0.1-SNAPSHOT.jar
  ```
> 서버에서 실행할 때 .jar파일만 옮겨놓고 실행할 수 있다.

## 스프링 웹 개발 기초
- **정적 컨텐츠**
  - Welcome Page
  - client에게 HTML 파일만 전달해 웹 브라우저로 보여준다.
- **MVC와 템플릿 엔진**
  - 템플릿 엔진 (JSP/PHP)
  - HTML을 프로그래밍해서 동적으로 바꿔서 내린다.
  - Spring에서는 MVC (`Model`, `View`, `Controller`)를 사용해 동적페이지를 구성한다.
- **API**
  - JSON 데이터 형식으로 client에게 전달하는 방식을 API 방식이라 한다.
  - 서버끼리 통신할 때에는 HTML을 전달할 필요가 없다. 따라서 API 방식을 많이 사용한다.

### 정적 컨텐츠
- 페이지 주소
  - Spring에서는 `/src/main/resources/static/`에 들어있는 HTML 파일명이 주소가 된다.  
    - localhost:8080/example.html

- **실행 과정**
  1. 웹 브라우저가 내장 톰켓 서버에게 localhost:8080/example.html 요청을 보낸다.
  2. 스프링은 우선적으로 example과 매핑된(`@GetMapping("example")`) 컨트롤러를 우선적으로 찾는다.
  3. 없다면 static 폴더로 가서 hello-static.html을 찾는다.

### MVC와 템플릿 엔진
> MVC: Model + Controller + View
- Model
  ```JAVA
  model.addAttribute("key", "value");
  ```
  - HTML에서는 아래와 같이 `${key}`를 통해 `value`를 가져온다.
  ```HTML
  <p th:text="'추가 내용 ' + ${key}"> 예비로 쓰여질 내용(없어도 무방)</p>
  ```
- Controller
  ```JAVA
  @GetMapping("mvc-example")
      public String mvcExample(@RequestParam("key") String value, Model model) {
        ...
        return "mvc-example"
      }
  ```
  - 컨트롤러의 인자에 `Model model` 외에 `@RequestParam("key") String value`를 받으면 client로 부터 key-value를 받을 수 있다.  
    - localhost:8080/mvc-example?key=value
- View
  - 컨트롤러가 return값으로 문자열을 반환하면 `viewReslover`가 화면을 찾아서 처리한다.
    - `src/resources/templates/` + {ReturnString} + `.html`
    - 이 HTML 파일은 `viewReslover`에 의해서 동적으로 변환되어 나타내어진다.
- **실행 과정**
  1. 웹 브라우저가 내장 톰켓 서버에게 localhost:8080/mvc-example?key=value 요청을 보낸다.
  2. 스프링은 우선적으로 컨트롤러를 찾기 때문에 컨트롤러에서 `mvc-example`과 매핑된 컨트롤러를 찾는다.
  3. 해당 컨트롤러는 `@RequestParam`을 인자로 하기 때문에 `?key=value`를 통해 key-value를 함께 전달 받는다.
  4. 이후 문자열을 리턴하고 `viewReslover`가 `/src/main/resources/templates/`에서 `{리턴한 문자열}.html` 파일을 찾아 HTML에 있는 ${key}를 value로 변환하여 웹 브라우저로 보낸다.

### API
> `@ResponseBody`: HTTP의 BODY에 내용을 직접 반환
- XML 방식
  > 문자열을 return (거의 안 씀)
  ```JAVA
  @GetMapping("xml-api")
  @ResponseBody
  public String xmlExample(@RequestParam("mode") String value) {
      return "This is " + value;
  }
  ```    
  > localhost:8080/xml-example?mode=xml
  ```
  This is xml
  ```

- JSON 방식
  > 객체를 return
  ```java
  @GetMapping("json-api")
  @ResponseBody
  public MyObject jsonApi(@RequestParam("key") String value) {
      MyObject obj = new MyObject();
      obj.setMode(value);
      return obj;
  }

  static class MyObject {
    private String mode;
    public String getMode() {return mode}
    public void setMode(String value) {this.mode=value}
  }
  ```
  > localhost:8080/json-api?key=JSON
  ```
  {"mode":"JSON"}
  ```
  - return 된 객체가 화면에 출력하는 것은 json 형식이다.
  - 이 때 json의 key는 해당 객체에 정의된 getOOO/setOOO 메소드의 OOO이다.
  - json의 value는 getOOO이 반환하는 값이다.
- **실행 과정**
  > localhost:8080/json-api?key=JSON
  1. `@ResponseBody`가 존재한다면 `viewReslover`한테 보내는 것이 아니라 바로 웹 브라우저에 던질 준비를 한다.  
  -> `HTTPMessageConverter`
  2. return 값이 문자열이라면 바로 화면에 출력한다.  
  -> `StringHttpMessageConverter`
  3. return 값이 문자열이 아닌 객체라면 json 방식으로 데이터를 만들어서 출력한다.  
  -> `MappingJackson2HttpMessageConverter`
  - byte 처리 등등 기타 여러 "HttpMessageConverter"가 기본으로 등록되어 있다.

# 회원관리 예제 - 백엔드 개발
## 비즈니스 요구사항 정리
- 데이터: 회원ID, 이름
- 기능: 회원 등록, 조회
- 시나리오
  - 아직 데이터 저장소가 선정되지 않았다.  
    > 관계형DB를 쓸지, NoSql을 쓸지 등등이 아직 정해지지 않음
  - 구현체로 가벼운 메모리 기반의 데이터 저장소를 사용한다.
  - 인터페이스로 구현 클래스를 변경할 수 있도록 설계한다.  
    > 추후 데이터 저장소 선정시 인터페이스를 갈아끼움

## Test-Case 작성
> `src/test/~` 폴더에 작성한다.
- `org.junit.jupiter.api.Test`
  ```java
  @Test
  public void testMethod() {
    ...
  }
  ```
- `org.assertj.core.api.Assertions`
  ```java
  // 예외처리를 통해 확인하기
  Assertions.assertThat(member).isEqualTo(result);
  ```
- `org.junit.jupiter.api.AfterEach`
  ```java
  // Class 단위로 Test 실행시 메소드 별로 순서를 보장하지 않음.
  // 따라서 각 메소드가 끝나면 변경내용을 초기화 해주어야 함.
  // AfterEach는 각 메소드가 실행을 마칠때마다 자동으로 한 번씩 실행됨.
  @AfterEach
  public void afterEach() {
      repository.clearStore();
  }
    ```

## 편한 단축키 정리
- 자동 import: (사용하려 하는 함수뒤에 커서 위치해놓고) Ctrl + Space
- 괄호 중간에 내려가기: Ctrl + Enter

## Java 문법 정리
### 람다 표현식
> 람다 표현식이란 간단히 말해 메소드를 하나의 식으로 표현한 것이다.  
> 람다식은 메소드의 매개변수로 전달될 수 있고, 메소드의 결과로 반환될 수 있다.  
> 즉, 메소드를 변수처럼 다루는 것이 가능하다.  

**작성 순서**
1. 메소드에서 이름과 반환타입 제거
2. 매개변수 선언부와 몸통`{}` 사이에 `->` 추가
3. 반환 값이 있는 메서드는 return 대신 식으로 대신할 수 있다.
4. 매개변수가 하나뿐이면 괄호 `()` 생략 가능
    ```java
    // 기본 메소드
    int min(int x, int y) {
      return x < y ? x : y;
    }
    
    // 람다 표현식
   (x,y) -> x < y ? x : y;
   ```

  - 메소드 참조  
    > 람다식이 하나의 메소드만 호출하는 경우, 메소드 참조를 통해 람다식을 간략히 할 수 있다.  
    > 클래스명::메소드명 or 참조변수::메소드명

    ```java
    // 기존
    Function<String, Integer> f = (String s) -> Integer.parseInt(s);
    
    // 메소드 참조
    Funcation<String, Integer> f = Integer::parseInt;
    ```
source: [Integerous DevLog](https://ryan-han.com/post/java/java-lambda/)

### Stream
  > 컬렉션이나 배열의 요소를 하나씩 참조하여 람다식으로 처리할 수 있도록 해주는 반복자이다.
  - `collect()`
    > Stream의 아이템들을 원하는 자료형으로 변환할 수 있다.  
    
    ex) 
    ```java
    [Stream 객체].collect(Collectors.toList());   
    // toList(), toSet(), toCollection(ArrayList::new), ... 
    ```

### Optional
  - Map.get(key) 등과 같이 Null 값을 반환할 가능성이 있는 경우 Optional.ofNullable(...)로 감싸서 return 한다.
