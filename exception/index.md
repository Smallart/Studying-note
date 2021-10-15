# 异常模块

异常的处理虽然在业务逻辑中并不算的上重点，但是在整个系统中异常的设计却十分重要。它不仅能增强系统的鲁棒性，并且最重要的是为用户提供良好的交互，也使得开发人员能够快速的定位问题。

虽然[-[Java]-]或是各种框架语言提供了很多的异常，但是对于实际的业务来说，这肯定大部分是不适用的，所有就需要我们定义自己的异常。在整个[-[RuoYi]-]系统中，其自定义的异常如下：

![异常体系](/img/ruoyi-exception-design.png)

由于`BaseException`异常之后实现大量的自定义异常，其中按照功能可以划分为**负责文件上传下载的文件类异常**、**定时任务有关的异常**以及**验证用户相关信息异常**。

`BaseException`记录了所需的异常信息：`module`(异常发生的模块)、`code`(错误码)、`args`(错误码对应的参数)、`defaultMessage`(错误信息)。
<details>
<summary>BaseException代码</summary>

```java
public class BaseException extends RuntimeException
{
    private static final long serialVersionUID = 1L;

    /**
     * 所属模块
     */
    private String module;

    /**
     * 错误码
     */
    private String code;

    /**
     * 错误码对应的参数
     */
    private Object[] args;

    /**
     * 错误消息
     */
    private String defaultMessage;

    public BaseException(String module, String code, Object[] args, String defaultMessage)
    {
        this.module = module;
        this.code = code;
        this.args = args;
        this.defaultMessage = defaultMessage;
    }

    public BaseException(String module, String code, Object[] args)
    {
        this(module, code, args, null);
    }

    public BaseException(String module, String defaultMessage)
    {
        this(module, null, null, defaultMessage);
    }

    public BaseException(String code, Object[] args)
    {
        this(null, code, args, null);
    }

    public BaseException(String defaultMessage)
    {
        this(null, null, null, defaultMessage);
    }

    @Override
    public String getMessage()
    {
        String message = null;
        if (!StringUtils.isEmpty(code))
        {
            message = MessageUtils.message(code, args);
        }
        if (message == null)
        {
            message = defaultMessage;
        }
        return message;
    }

    public String getModule()
    {
        return module;
    }

    public String getCode()
    {
        return code;
    }

    public Object[] getArgs()
    {
        return args;
    }

    public String getDefaultMessage()
    {
        return defaultMessage;
    }
}
```

</details>

## 异常的解析
制定好了异常体系之后，我们还需要将抛出的异常进行捕获处理。[-[Spring MVC]-]提供了全局的异常处理方法，就不需要我们在每个`Controller`中一个个处理异常了。

![全局异常处理](/img/globalExceptionHandler.png)

当`Controller`层有未被处理的异常被抛出时，你可以认为这个类相当于是一个拦截器将对应抛出的异常捕获给相关的处理器处理，将其变为状态返回。具体的实现可以去看[-[RuoYi]-]的`GlobalExceptionHandler`这个类。