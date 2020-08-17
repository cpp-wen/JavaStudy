# AOP

## 什么是AOP

Aop（Aspect -oriented programming） 面向切面的程序设计，旨在将横切关注点与业务进行分离。在业务设计中未考虑到实现细节如日志打印等操作，在实现中是必不可少但是与业务相关性不高的一系列操作，对于这些重复的操作 进行提取封装 既体现了抽象原则也实现了单一职责原则，将业务无关代码进行抽离统一处理。Oop（Object-oriented programming）的三大特性是从纵向的角度进行思考，而Aop是横向进行思考

## aop使用场景

- 日志打印
- 权限验证
- 性能监测
- 事务管理 **Transactional**注解

## 如何使用

实现方式有三种分别为

- 纯xml实现
- xml配合注解实现
- 单纯注解实现（广泛使用）



在主要介绍第三种使用方式之前 需要了解aop切面的一些术语：

通知(advice)：通知定义了一个切面在什么时候需要完成什么样的功能，通知和切点组成切面。

切点(pointCut)：切点定义了切面需要作用在什么地方。

切面(Aspect)：是通知和切点的组合，表示在指定的时间点什对指点的地方进行一些额外的操作。

链接点(join points)：连接点表示可以被选择用来增强的位置，连接点是一组集合，在程序运行中的整个周期中都存在。

织入(Weaving)：在不改变原类代码的前提下，对功能进行增强。



通知advice的时机：

Before——在方法调用之前调用通知

After——在方法完成之后调用通知，无论方法执行成功与否

After-returning——在方法执行成功之后调用通知

After-throwing——在方法抛出异常后进行通知

Around——通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为



## 如何实现

静态代理：在类初始化之前使用代理模式，创建一个代理类 将执行主类作为target作为参数传入

动态代理：使用反射在运行的时候动态生成类，覆盖其中的一些方法

cglib：需要导入包，通过创建子类重写父类方法来实现动态代理

jdk：不需要导入包，通过创建自己实现的接口来创建动态代理 

使用过程中需要注意出现的问题：

如果实现的接口中没有定义代理的方法这个时候，可以使用cglib实现动态代理@EnableAspectJAutoProxy(proxyTargetClass = true)，或者在接口中重新声明代理方法否则会报错NoSuchBeanDefinitionException。

同时定义为final的类无法被继承，在使用cglib代理的时候需要考虑 默认一般使用jdk方式来实现动态代理

实现动态代理主要的两个对象（一个接口 一个类）

1. InvocationHandler接口  执行invoke方法
2. proxy类  使用newProxyInstance 方法 创建一个代理对象



## spring 实现aop 使用案例

使用aop实现登录验证 实现效果为在类的请求方法上添加@CheckLog 实现登录状态验证

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface CheckLogin {

}
```

```java
@Order(1)
@Aspect
@Component
public class SessionService extends RestAop {

    /**
     * token 请求头
     */
    private static final String AUTHORIZATION = "Authorization";

    @Autowired
    private UserBasicService userBasicService;

    @Pointcut("execution(* us.betahouse.haetae.controller..*(..)) && @annotation(us.betahouse.haetae.common.session.CheckLogin)")
    public void checkLogin() {
    }

    /**
     * 校验登陆态
     *
     * @param proceedingJoinPoint
     */
    @Around("checkLogin()")
    public Object verify(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        RestRequest request = parseRestRequest(proceedingJoinPoint);
        HttpServletRequest httpServletRequest = parseHttpServletRequest(proceedingJoinPoint);
        AssertUtil.assertNotNull(request, CommonResultCode.SYSTEM_ERROR.getCode(), "登陆检测失败, 没有登陆请求");
        AssertUtil.assertNotNull(httpServletRequest, CommonResultCode.SYSTEM_ERROR.getCode(), "登陆检测失败, 没有登陆凭证");

        String token = httpServletRequest.getHeader(AUTHORIZATION);
        if (StringUtils.isBlank(token)) {
            return RestResultUtil.buildResult(RestResultCode.UNAUTHORIZED, "用户未登录");
        }
        // 检测登录态
        UserBO userBO = userBasicService.checkLogin(token, IPUtil.getIpAddr(httpServletRequest));
        if (userBO == null) {
            return RestResultUtil.buildResult(RestResultCode.UNAUTHORIZED, "用户未登录");
        }
        request.setUserId(userBO.getUserId());

        return proceedingJoinPoint.proceed();
    }
}
```