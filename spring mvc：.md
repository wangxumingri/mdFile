#spring mvc：





`@Controller`



`@RestControllerAdvice`



`@ExceptionHandler`



`@RequestMapping`



`@GetMapping`



`@PutMapping`







##过滤器与拦截器：

参考:<https://blog.csdn.net/zxd1435513775/article/details/80556034> 



`扩展：`

1.web.xml中常用的中文乱码过滤器原理：

​	通过      `HttpServletRequest`  和  `HttpServletResponse`设置请求和响应的字符编码

```java
public class CharacterEncodingFilter extends OncePerRequestFilter {
    @Nullable
    private String encoding;
    private boolean forceRequestEncoding; // 默认false
    private boolean forceResponseEncoding;// 默认false

    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        String encoding = this.getEncoding();
        if (encoding != null) {
            if (this.isForceRequestEncoding() || request.getCharacterEncoding() == null) {
                request.setCharacterEncoding(encoding);
            }

            if (this.isForceResponseEncoding()) {
                response.setCharacterEncoding(encoding);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

##异常统一管理：

<https://www.cnblogs.com/junzi2099/p/7840294.html> 三种方式

**异常处理的原则**

>1、调用方法的时候返回布尔值来代替返回null，这样可以 NullPointerException。由于空指针是java异常里最恶心的异常。
>
>2、 catch块里别不写代码。空catch块是异常处理里的错误事件，因为它只是捕获了异常，却没有任何处理或者提示。通常你起码要打印出异常信息，当然你最好根据需求对异常信息进行处理。
>
>3、能抛受控异常（checked Exception）就尽量不抛受非控异常(unchecked Exception[Error或者RuntimeException的异常])。通过去掉重复的异常处理代码，可以提高代码的可读性。
>
>4、 绝对不要让你的数据库相关异常显示到客户端。由于绝大多数数据库和SQLException异常都是受控异常，在Java中，你应该在DAO层把异常信息处理，然后返回处理过的能让用户看懂并根据异常提示信息改正操作的异常信息。
>
>5、 在Java中，一定要在数据库连接，数据库查询，流处理后，在finally块中调用close()方法。

**思路：**

1.首先，创建一个 `自定义异常类` 继承JDK提供的异常类

2.然后，使用 `@RestControllerAdvice` 和 `    @ExceptionHandler(自定义异常类.class)` 来捕获指定异常，并处理

**自定义异常处理类：**

```java
package com.wxss.unifiedexceptionhandlingdemo.exception;

import com.wxss.unifiedexceptionhandlingdemo.pojo.ResponseResult;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * Author:Created by wx on 2019/4/19
 * Desc:
 */
@RestControllerAdvice
public class MyExceptionHandler {

    /**
     * 所有CustomeException异常都会被这个方法捕获，处理
     * @param e 自定义异常
     * @return 自己封装的结果类
     */
    @ExceptionHandler(CustomeException.class)
    public ResponseResult handlerCustomeException(CustomeException e){
        return new ResponseResult(e.getCode(),e.getMessage());
    }
}

```

`@ControllerAdvice `

<https://blog.csdn.net/zxfryp909012366/article/details/82955259> 

```
作用：包含了@Component注解
应用：
	1.结合方法型注解@ExceptionHandler，用于捕获Controller中抛出的指定类型的异常，从而达到不同类型的
		异常区别处理的目的；
	2.结合方法型注解@InitBinder，用于request中自定义参数解析方式进行注册，从而达到自定义指定格式参数
		的目的；
	3.结合方法型注解@ModelAttribute，表示其标注的方法将会在目标Controller方法执行之前执行。

```



>这三种注解如果应用于 `@ControllerAdvice` 注解所标注的类中，那么它们表示会对@ControllerAdvice所指定的范围内的接口都有效；
>
>如果单纯的将这三种注解应用于`某个Controller`中，那么它们将只会对该Controller中所有的接口有效，并且此时是不需要在该Controller上标注@ControllerAdvice的。




```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface ControllerAdvice {

	/**
	 * Alias for the {@link #basePackages} attribute.
	 * <p>Allows for more concise annotation declarations e.g.:
	 * {@code @ControllerAdvice("org.my.pkg")} is equivalent to
	 * {@code @ControllerAdvice(basePackages="org.my.pkg")}.
	 * @since 4.0
	 * @see #basePackages()
	 */
	@AliasFor("basePackages")
	String[] value() default {};

	/**
	 * Array of base packages.
	 * <p>Controllers that belong to those base packages or sub-packages thereof
	 * will be included, e.g.: {@code @ControllerAdvice(basePackages="org.my.pkg")}
	 * or {@code @ControllerAdvice(basePackages={"org.my.pkg", "org.my.other.pkg"})}.
	 * <p>{@link #value} is an alias for this attribute, simply allowing for
	 * more concise use of the annotation.
	 * <p>Also consider using {@link #basePackageClasses()} as a type-safe
	 * alternative to String-based package names.
	 * @since 4.0
	 */
	@AliasFor("value")
	String[] basePackages() default {};

	/**
	 * Type-safe alternative to {@link #value()} for specifying the packages
	 * to select Controllers to be assisted by the {@code @ControllerAdvice}
	 * annotated class.
	 * <p>Consider creating a special no-op marker class or interface in each package
	 * that serves no purpose other than being referenced by this attribute.
	 * @since 4.0
	 */
	Class<?>[] basePackageClasses() default {};

	/**
	 * Array of classes.
	 * <p>Controllers that are assignable to at least one of the given types
	 * will be assisted by the {@code @ControllerAdvice} annotated class.
	 * @since 4.0
	 */
	Class<?>[] assignableTypes() default {};

	/**
	 * Array of annotations.
	 * <p>Controllers that are annotated with this/one of those annotation(s)
	 * will be assisted by the {@code @ControllerAdvice} annotated class.
	 * <p>Consider creating a special annotation or use a predefined one,
	 * like {@link RestController @RestController}.
	 * @since 4.0
	 */
	Class<? extends Annotation>[] annotations() default {};

}
```



