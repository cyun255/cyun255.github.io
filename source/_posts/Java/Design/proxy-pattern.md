---
title: 设计模式 | 代理模式
date: 2025-2-8
comments: false
categories: 设计模式
tags: 
    - 静态代理
    - 动态代理
    - 反射
description: |
---

## 简介

代理模式旨在**不修改原始代码**的前提下，为其**拓展附加功能**。

通过引入一个**代理对象**来控制对原始对象的访问，代理对象充当**中介**的角色，可以在访问原始对象的前后进行额外的处理。

举个例子：

- 我们点外卖时，只需要在App上下单，等待骑手把外卖送到我们手上。
- 商家收到订单后，只需要按照要求制作，给到骑手配送。
- 骑手接到单子，前往商家取餐，配送给客户，这中间一系列的流程由App代理完成，客户与商家都不需要关心。

## 静态代理

### 实现

代理模式主要涉及以下几个角色：

- 抽象主题（Subject）：定义真实主题和代理的公共接口。
- 真实主题（Real Subject）：实现了抽象主题接口，是代理对象所代表的真实对象，执行核心的业务逻辑。
- 代理（Proxy）：同样实现了抽象主题接口，并引用真实主题，通常在核心业务逻辑的基础上提供额外的功能。
- 客户端（Client）：使用抽象主题的接口操作真实主题或代理，不需要关心内部细节。

还是以上面点外卖的例子来讲解，首先要有点单服务接口：

```java
public interface OrderService {
    public boolean order(String name);
}
```

商家收到订单后，要处理订单（实现服务接口，核心业务逻辑）：

```java
public class OrderServiceImpl implements OrderService {
    @Override
    public boolean order(String name) {
        System.out.println(name + " 下单成功");
        return true;
    }
}
```

代理需要在用户与商家之间完成预备工作与收尾工作：

```java
public class OrderServiceProxy implements OrderService{
    @Override
    public boolean order(String name) {
        // 预备工作
        System.out.println("商品：" + name);
        System.out.println("生成订单中.....");
        System.out.println("订单生成成功。");

        // 引用核心业务逻辑
        OrderService orderService = new OrderServiceImpl();
        boolean status = orderService.order(name);

        // 收尾工作
        if (status) {
            System.out.println("等待商家制作....");
        }else {
            System.out.println("下单失败。");
        }
        return status;
    }
}
```

客户可以直接通过代理点餐，不关心内部细节：

```java
public class Consumer {
    public static void main(String[] args) {
        OrderService orderService = new OrderServiceProxy();
        boolean status = orderService.order("123");
        System.out.println(status);
    }
}
```

### 缺点

静态代理实现了抽象主题接口，**只能为给定接口的实现类做代理**，针对不同的接口需要手动实现不同的代理类。

由于在 JVM 运行前接口、实现类、代理类都会被编译为一个个实际的 `.class` 文件，因此称为静态代理。

随着业务逐渐复杂，需要不断的增加代理类，难以维护真实主题与代理之间的关系。

## 动态代理

相比静态代理，动态代理更加灵活，不需要针对某个类或某个接口来实现。

在 JVM 的角度，代码在运行前并不存在代理类的 `.class` 文件，而是在**运行时通过反射机制动态生成并加载到 JVM 中**。

JDK 中提供了创建动态代理的类 `Proxy` ，其中使用频率最高的方法是 `newProxyInstance()` 

```java
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
```

该方法有3个参数

- **loader**：用于指定用哪个类加载器，去加载生成的代理类
- **interfaces**：指定接口，这些接口用于指定生成的代理类长什么样，也就是有哪些方法
- **h**：用来指定生成的代理对象要干什么事情

要实现动态代理，必须传递实现了 `InvocationHandler` 接口的对象。当动态代理对象调用任意方法时，会转发到实现了 `InvocationHandler` 接口的对象下的 `invoke` 方法，在该方法中自定义代理的业务逻辑。

```java
public interface InvocationHandler{
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

`invoke` 方法也有3个参数

- **proxy**：动态代理生成的代理类对象
- **method**：代理类正在调用的方法
- **args**：调用当前 method 时，携带的参数数组

### 实现

现在，将上面已经实现的静态代理改成动态代理。首先，动态代理类需要实现 `InvocationHandler` 接口，并重写 `invoke` 方法。

为了能够在 `invoke` 方法中调用真实主题所对应的方法，代理类中还需要记录被代理的实现类对象。

```java
public class ServiceProxy implements InvocationHandler {

    private Object target;

    ServiceProxy(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 预备工作
        System.out.print("商品：");
        for (var a : args) {
            System.out.println(" " + a);
        }
        System.out.println("生成订单中.....");
        System.out.println("订单生成成功。");

        // 引用核心业务逻辑
        boolean status = (boolean) method.invoke(target, args);

        // 收尾工作
        if (status) {
            System.out.println("等待商家制作....");
            return status;
        }else {
            System.out.println("下单失败。");
        }
        return null;
    }
}
```

使用一个动态代理工厂来根据实现类创建实际的代理对象

```java
public class ProxyFactory {
    public static <T> T getProxy(Object implClass) {
        return (T) Proxy.newProxyInstance(
                implClass.getClass().getClassLoader(),
                implClass.getClass().getInterfaces(),
                new ServiceProxy(implClass)
        );
    }
}
```

用户通过动态代理工厂获取代理对象，不关心代理细节

```java
public class Consumer {
    public static void main(String[] args) {
        OrderService orderService = ProxyFactory.getProxy(new OrderServiceImpl());
        boolean status = orderService.order("123");
        System.out.println(status);
    }
}
```

## 结语

静态代理的代码非常简单，就是在实现类前面增加一个代理类，在代理类中实现拓展业务同时引用实现类中的核心业务。

静态代理的缺点也很明显：

- 随着业务的增加，每一个接口都需要单独的创建代理类和实现类。
- 若其中任何一个接口发生变化，对应的代理类和实现类都需要进行调整。

动态代理通过反射在运行时动态创建代理对象，不必针对每个接口都单独创建代理类。

动态代理写产品业务时几乎用不到，但是在应用框架中被广泛使用，例如 Spring AOP 的核心思想就是动态代理。


