# Spring MVC로 웹 콘텐츠 제공

### 개요

- 정적 홈 페이지가 있고 `<http://localhost:8080/greeting>`에서 HTTP GET 요청도 수락하는 애플리케이션을 빌드합니다.
- HTML을 표시하는 웹 페이지로 응답합니다. HTML 본문에는 `Hello, World!` 문자열 인사말이 포함됩니다.
- 쿼리 문자열에서 선택적 `name` 매개변수를 사용하여 인사말을 사용자 지정할 수 있습니다. 그러면 URL은 `http://localhost:8080/greeting?name=User`가 될 수 있습니다.
- `name` 매개변수 값은 기본값인 `World`를 재정의하고 "Hello, User!"로 변경되는 내용으로 응답에 반영됩니다.

### 종속성

- Spring Web
- Thymeleaf
- Spring Boot DevTools

### Web Controller 생성

웹 사이트 구축에 대한 Spring의 접근 방식에서 HTTP 요청은 컨트롤러에 의해 처리됩니다. `@Controller` 어노테이션으로 컨트롤러를 쉽게 식별할 수 있습니다. 다음 예에서 GreetingController는 View의 이름( 이경우 Greeting)을 반환하여 `/greeting`에 대한 GET 요청을 처리합니다. View는 HTML 컨텐츠를 렌더링하는 역할을 합니다.

`src/main/java/com/example/servingwebcontent`:

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class GreetingController {

	@GetMapping("/greeting")
	public String greeting(@RequestParam(name="name", required=false, defaultValue="World") String name, Model model) {
		model.addAttribute("name", name);
		return "greeting";
	}

}
```

- `@GetMapping` 어노테이션은 `/greeting`에 대한 HTTP GET 요청이 `greeting()` 메서드에 매핑되도록 합니다.
- `@RequestParam`은 쿼리 문자열 매개 변수 `name`의 값을 `gretting()` 메서드의 `name` 매개 변수에 바인딩합니다. 이 쿼리 문자열 매개변수는 필요하지 않습니다. 요청에 없으면 `World`의 `defaultValue`가 사용됩니다. `name` 매개변수의 값이 Model 객체에 추가되어 궁극적으로 View 템플릿에 액세스할 수 있습니다.

메서드 본문의 구현은 HTML의 서버 측 렌더링을 수행하기 위해 view 기술(이 프로젝트는 Thymeleaf)에 의존합니다. Thymeleaf는 `greeting.html` 템플릿을 구문 분석하고 `th:text` 표현식을 평가하여 컨트롤러에 설정된 `${name}` 매개변수의 값을 렌더링합니다.

`src/main/resources/templates/greeting.html`:

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head> 
    <title>Getting Started: Serving Web Content</title> 
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
    <p th:text="'Hello, ' + ${name} + '!'" />
</body>
</html>
```

### Spring Boot DevTools

웹 응용 프로그램 개발의 일반적인 기능은 변경 사항을 코딩하고, 응용 프로그램을 다시 시작하고, 변경 사항을 보기 위해 브라우저를 새로 고치는 것입니다. 이 전체 프로세스는 많은 시간을 소모할 수 밖에 없습니다. 이 새로 고침 주기의 속도를 높이기 위해 Spring Boot는 spring-boot-devtoolds로 알려진 편리한 모듈을 제공합니다.

지원 기능:

- hot swapping 활성화
- 캐싱을 비활성화하도록 템플릿 엔진을 전환
- LiveReload를 할성화하여 브라우저를 자동으로 새로 고침
- 프로덕션 대신 개발을 기반으로 한 기타 합리적인 기본값

### 애플리케이션 실행

Gradle:

```shell
./gradlew bootRun
```

### JAR 빌드 및 실행

Gradle 또는 Maven을 사용하여 명령줄에서 애플리케이션을 실행할 수 있습니다. 필요한 모든 종속성, 클래스 및 리소스가 포함된 단일 실행이 가능한 JAR 파일을 빌드하고 실행할 수도 있습니다. 실행 가능한 jar를 빌드하면 개발 수명 주기 전반에 걸쳐 다양한 환경 등에서 서비스를 애플리케이션으로 쉽게 제공, 버전 지정 및 배포할 수 있습니다.

다음 명령어를 사용하면 jar 파일을 빌드할 수 있습니다.

```shell
./gradlew build
```

다음과 같이 JAR 파일을 실행할 수 있습니다.

```shell
java -jar build/libs/serving-web-content-0.0.1-SNAPSHOT.jar
```

### 애플리케이션 테스트

<http:://localhost:8080/greeting>에 방문하면 `Hello, World!` 문자열을 볼 수 있습니다.

<http://localhost:8080/greeting?name=User>에 방문해 `World` 문자열이 어떻게 바뀌었는지 확인해보세요.  쿼리 문자열 매개변수를 통해 `Hello, User!` 문자열로 바뀐 것을 볼 수 있습니다.

이 변경은 `GreetingController`의 `@RequestParam` 배열이 예상대로 작동하고 있음을 보여줍니다. `name` 매개변수에는 기본값(`defaultValue`) `World`가 지정되었지만 쿼리 문자열을 통해 명시적으로 재정의할 수 있습니다.

### 홈 페이지 추가

HTML, JavaScript 및 CSS를 포함한 정적 리소스는 소스 코드의 올바른 위치에 드롭하여 Spring Boot 애플리케이션에서 제공할 수 있습니다. 기본적으로 Spring Boot는 `/static`에 있는 클래스 경로의 리소스에서 정적 콘텐츠를 제공합니다.

`index.html` 리소스는 존재하는 경우 환영하는 페이지로 사용되기 때문에 특별합니다(<http://localhost:8080/>).

다음 파일을 작성해 홈 페이지를 만듭니다.

`src/main/resources/static/index.html`:

```html
<!DOCTYPE HTML>
<html>
<head> 
    <title>Getting Started: Serving Web Content</title> 
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
    <p>Get your greeting <a href="/greeting">here</a></p>
</body>
</html>
```

애플리케이션을 다시 시작하면 <http://localhost:8080/>에 만들어둔 HTML이 표시됩니다.