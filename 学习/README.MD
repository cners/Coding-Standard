# 知识学习

## 控制反转（IOC）

> 参考地址：<https://www.cnblogs.com/artech/p/inside-asp-net-core-03-01.html>
>
> 控制反转（IoC：Inverse of Control）
>
> 通过流程控制从应用程序向框架的反转实现了针对流程自身的重用
>
> 通过内置的扩展机制使这个被重用的流程能够自由地被定制

依赖的一个设计原则：依赖于抽象，而不是具体的实现.

**依赖注入解决的问题**：

直接依赖：A->B->C

（1）在直接依赖中，如果A类需要更换为其他实现，那么就必须要修改修改依赖的其他类；

（2）如果有多个依赖B类的类，那么将会实例化多个配置，这样的代码会比较分散和冗余；

（3）这种实现方法很难实现单元测试；

解决这些问题的办法：

（1）使用接口抽象依赖关系的实现，改动实现只需要改动注入的地方即可；

（2）注册服务容器中的依赖关系，有多处需要不许多出实例化配置；

## 服务的声明周期（.Net core DI）

在.NET Core中DI的核心分为两个组件：IServiceCollection和 IServiceProvider

> IServiceCollection 负责注册
>
> IServiceProvider 负责提供实例

Transient：每次从服务容器进行请求时创建的。适合轻量级、无状态的服务。暂时生存期在每次请求的时候会创建一个实例

Scoped：每个客户端连接时创建一次实例，然后每次请求的实例都是相同的

Singleton：单例生存期仅会在程序第一次请求时创建一次实例，所有整个程序使用的实例都是同一个实例

## 3. 替换默认服务容器

（1）为什么替换默认服务容器？

内置的服务容器足够实现一些小型的项目或满足大多数的消费者，但是遇到大型的项目就比较麻烦了，依赖较多，内置的服务容器就显得有点短板了。遇到这些问题的时候，就可以考虑替换默认的服务容器。

（2）如何替换默认服务容器？

可以选用：Autofac

安装适当的包：
> Autufac
>
> Autofac.Extensions.DependencyInjection

```c#
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add other framework services
    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<DefaultModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);

}
```

如果使用第三方容器的话，Startup.ConfigureServices方法必须返回IServiceProvider

然后在DefaultModule 中配置 Autofac

```c#
public class DefaultModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
    }
}
```

## c#中的sealed关键字

**修饰class**：任何类不能继承被sealed修饰的类

**修饰方法或属性字段**：基类中的属性或方法必须是virtual,一级派生类中可以通过override重写方法或属性，若一次派生类在特定方法或属性上加上sealed关键字，那么二级派生类不允许从一级派生类中重写被sealed修饰的方法或属性。

## 什么是线程安全和不安全

**线程安全**：指多个线程在执行同一段代码的时候采用加锁机制，使每次的执行结果和单线程执行的结果都是一样的，不存在执行时出现意外结果。

**线程不安全**：是指不提供加锁机制保护，有可能出现多个线程先后更改数据造成所得到的数据是脏数据。

## c#中的virtual与abstract区别

* virtual修饰的方法必须有方法实现（哪怕只有一对大括号），abstract修饰的方法不能有实现
* virtual可以被子类重写，abstract必须被子类重写
* 如果类中的某一函数被abstract修饰，则类名也必须用abstract修饰
* abstract修饰的类，不能被创建实例，只能被继承
* c#中如果准备在子类重写父类的方法，则该方法在父类中必须用virtual修饰，在子类中必须用override修饰，避免了程序员在子类中不小心重写了父类方法

## 什么时候用virtual，什么时候用abstract

* virtual 重点是实现多态，同一个方法能做不同的事情，也就是当一个方法有不同的操作的时候要用virtual。例如：支付方法pay()，可能通过微信支付，也可能通过支付宝支付，这个时候定义pay()方法的时候最好用virtual修饰。
* 作为一个必须存在的方法的时候用abstract

> 参考地址：<https://www.cnblogs.com/stgp/p/6233359.html>

## interlocked与lock的区别

> 参考地址：<https://zhidao.baidu.com/question/652236206795414285.html>
