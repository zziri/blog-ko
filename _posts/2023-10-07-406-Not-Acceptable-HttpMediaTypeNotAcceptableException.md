---
title: "406 Not Acceptable HttpMediaTypeNotAcceptableException"
categories:
  - IT
---

While working on a project based on Spring Boot, I encountered a 406 Not Acceptable HttpMediaTypeNotAcceptableException error.

I didn't do anything special, so why did I get a 406 error in the response?

{% include figure image_path="/assets/images/2023-10-07/img1.png" alt="" caption="" %}
It's a regular request, but it's responding with a 406 error.

To see the contents of the error, I created an ExceptionHandler and called the ex.printStackTrace() method.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(value = HttpMediaTypeNotAcceptableException.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ResponseDto<ErrorDto> handleHttpMediaTypeNotAcceptableException(HttpMediaTypeNotAcceptableException ex) {
        ex.printStackTrace();
        return ResponseDto.<ErrorDto>builder()
                .data(
                        ErrorDto.builder()
                                .code(HttpStatus.INTERNAL_SERVER_ERROR.value())
                                .message("HttpMediaTypeNotAcceptableException").build())
                .success(false).build();
    }
}
```

```shell
2021-08-21 19:36:38.279  INFO 10384 --- [           main] c.zziri.community.CommunityApplication   : Started CommunityApplication in 10.616 seconds (JVM running for 11.627)
2021-08-21 19:36:39.452  INFO 10384 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-08-21 19:36:39.453  INFO 10384 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2021-08-21 19:36:39.455  INFO 10384 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 2 ms
org.springframework.web.HttpMediaTypeNotAcceptableException: Could not find acceptable representation
	at org.springframework.web.servlet.mvc.method.annotation.AbstractMessageConverterMethodProcessor.writeWithMessageConverters(AbstractMessageConverterMethodProcessor.java:315)
	at org.springframework.web.servlet.mvc.method.annotation.RequestResponseBodyMethodProcessor.handleReturnValue(RequestResponseBodyMethodProcessor.java:183)
	at org.springframework.web.method.support.HandlerMethodReturnValueHandlerComposite.handleReturnValue(HandlerMethodReturnValueHandlerComposite.java:78)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:124)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
...
```

From my gut feeling (?), it seems like there was an issue during the message conversion process.

When I looked at the ResponseDto I created, I usually just added Getter and Setter, but this time I didn't.

If there's no Getter method, you can't access the attributes, so maybe there was a problem when converting to JSON. So I tried marking the @Getter annotation.

```java
@Builder
@Getter
public class ResponseDto <T> {
    private boolean success;
    private T data;
}
```

{% include figure image_path="/assets/images/2023-10-07/img2.png" alt="" caption="" %}

Now it's responding properly.

To return the ResponseDto to the client, it needs to be converted to JSON. Without a Getter method, it's obviously impossible to access private properties.

So, you can also solve this problem using the @JsonProperty annotation!

If you want to convert to JSON without these additional steps, you can use gson.