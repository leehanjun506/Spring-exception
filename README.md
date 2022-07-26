# Summary
## 서블릿 예외 처리
### 서블릿은 2가지 방식으로 예외 처리를 지원한다.
* Exception(예외)
* response.sendError(HTTP 상태 코드, 오류 메시지)

#### 웹 애플리케이션은 사용자 요청별로 별도의 쓰레드가 할당되고, 서블릿 컨테이너 안에서 실행된다. 애플리케이션에서 예외가 발생했는데, 어디선가 try ~ catch로 예외를 잡아서 처리하면 아무런 문제가 없다. 그런데 만약에 애플리케이션에서 예외를 잡지 못하고, 서블릿 밖으로 까지 예외가 전달되면 어떻게 동작할까?
```
WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
#### WAS에 예외가 올라오면 tomcat같은 경우는 상태코드 500 오류화면을 기본으로 제공해준다.
</br>

### response.sendError(HTTP 상태 코드,오류 메시지)
#### 오류가 발생 했을 때 HttpServletResponse가 제공하는 sendError메서드를 사용하면 서블릿 컨테이너에게 오류가 발생했다는 것을 전달 할 수 있다. 
#### sendError 흐름
```
WAS(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러
(response.sendError())
```
---
## 스프링 부트 -오류 페이지
### 스프링 부트는 /error라는 경로로 기본 오류 페이지를 설정한다. 만약 서블릿 밖으로 예외가 발생하거나, response.sendError()가 호출되면 모든 오류는 /error를 호출하게 된다.
</br>

---
## API 예외 처리
</br>

### 스프링이 제공하는 ExceptionResolver

1. ExceptionHandlerExceptionResolver
2. ResponseStatusExceptionResolver : HTTP 상태코드를 지정해준다
3. DefaultHandlerExceptionResolver : 스프링 내부 기본 예외를 처리한다.

## Code Feature

```
@ExceptionHandler
 public ResponseEntity<ErrorResult> userExHandle(UserException e) {
 log.error("[exceptionHandle] ex", e);
 ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
 return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
 }
 ```
 ### @ExceptionHandler 선언을 하고, 해당 컨트롤러에서 처리하고 싶은 예외를 지정해주면, 해당 컨트롤러에서 예외가 발생하면 이 메서드가 호출이 된다.
