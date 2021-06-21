# [-[AOP]-]

Aspects can have methods and fields, the same as any other class. They can also contain pointcut, advice, and introduction(inter-type) declarations.

In Spring AOP, aspects themselves cannot be the targets of advice from other aspects. The @Aspect annotation on a class mark it as an aspect and hence, excludes it from auto-proxying.

Spring AOP only supports method execution join points for Spring beans, so you can think of a pointcut as matching the executionof methods on Spring bean.

A pointcut declaration has two parts:a signature comprising a name and any parameters and a pointcut expression that determines exactly which method executions we are interested in.

## Advice
Advice is associated with a pointcut expression and runs before, after,or around method executions matched by the pointcut.

Spring AOP的使用范围是受到Spring beans。
Using AspectJ with Spring Applications.


