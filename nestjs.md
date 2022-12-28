# 介绍

Nestjs 是一个用于构建高效可扩展的一个基于Node js 服务端 应用程序开发框架

并且完全支持typeScript  结合了 AOP 面向切面的编程方式

nestjs 还是一个spring MVC 的风格 其中有依赖注入 IOC 控制反转 都是借鉴了Angualr

nestjs 的底层代码运用了 express 和  Fastify 在他们的基础上提供了一定程度的抽象，同时也将其 API 直接暴露给开发人员。这样可以轻松使用每个平台的无数第三方模块

nest js 英文官网 [NestJS - A progressive Node.js framework](https://nestjs.com/)

nestjs 中文网  [NestJS 简介 | NestJS 中文文档 | NestJS 中文网](https://nestjs.bootcss.com/)

nestjs 中文网2  [Nest.js 中文文档](https://docs.nestjs.cn/)



## express

**nestjs内置框架express 默认express**

能够快速构建服务端应用程序，且学习成本非常低，容易上手

express 文档[Express - 基于 Node.js 平台的 web 应用开发框架 - Express 中文文档 | Express 中文网](https://www.expressjs.com.cn/)



## Fastify

**nestjs唯二内置框架 Fastify**

- 高性能： 据我们所知，Fastify 是这一领域中最快的 web 框架之一，另外，取决于代码的复杂性，Fastify 最多可以处理每秒 3 万次的请求。
- 可扩展： Fastify 通过其提供的钩子（hook）、插件和装饰器（decorator）提供完整的可扩展性。
- 基于 Schema： 即使这不是强制性的，我们仍建议使用 JSON Schema 来做路由（route）验证及输出内容的序列化，Fastify 在内部将 schema 编译为高效的函数并执行。
- 日志： 日志是非常重要且代价高昂的。我们选择了最好的日志记录程序来尽量消除这一成本，这就是 Pino!
- 对开发人员友好： 框架的使用很友好，帮助开发人员处理日常工作，并且不牺牲性能和安全性。
- 支持 TypeScript： 我们努力维护一个 TypeScript 类型声明文件，以便支持不断成长的 TypeScript 社区。

![img](https://markdown-czz.oss-cn-hangzhou.aliyuncs.com/img/c3e25f041333448d90dee6f3cf8ae645.png)



# IOC控制反转 DI依赖注入

在学习nestjs 之前需要先了解其设计模式

## **IOC**

Inversion of Control字面意思是控制反转，具体定义是高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。

## **DI**

依赖注入（Dependency Injection）其实和IoC是同根生，这两个原本就是一个东西，只不过由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的名字：“依赖注入”。 类A依赖类B的常规表现是在A中使用B的instance。

案例未使用控制反转和依赖注入之前的代码

```typescript
class A {
  name: string
  constructor(name: string) {
    this.name = name
  }
}

class B {
  age: number
  entity: A
  constructor(age: number) {
    this.age = age
    this.entity = new A("曹")
  }
}
const c = new B(18)
console.log(c.entity.name)
```

我们可以看到，**B** 中代码的实现是需要依赖 **A** 的，**两者的代码耦合度非常高。当两者之间的业务逻辑复杂程度增加的情况下，维护成本与代码可读性都会随着增加，并且很难再多引入额外的模块进行功能拓展**。

为了解决这个问题可以使用IOC容器

```typescript
export { }
class A {
  name: string
  constructor(name: string) {
    this.name = name
  }
}


class C {
  name: string
  constructor(name: string) {
    this.name = name
  }
}

class Container {
  modeuls: any
  constructor() {
    this.modeuls = {}
  }
  provide(key: string, modeuls: any) {
    this.modeuls[key] = modeuls
  }
  get(key: string) {
    return this.modeuls[key]
  }
}

const mo = new Container()

mo.provide('a', new A('czz1'))
mo.provide('c', new C('czz2'))


class B {
  a: any
  c: any
  constructor(container: Container) {
    this.a = container.get('a')
    this.c = container.get('c')
  }
}

const b = new B(mo)
console.log(b.a);
console.log(b.c);
```

其实就是写了一个中间件，来收集依赖，主要是为了解耦，减少维护成本

输出

```
A { name: 'czz1' }
C { name: 'czz2' }
```



# 装饰器

## 什么是装饰器

装饰器是一种特殊的类型声明，他可以附加在类，方法，属性，参数上面

装饰器写法 **tips（需要开启一项配置）**

![img](https://markdown-czz.oss-cn-hangzhou.aliyuncs.com/img/95a018ec2abe4c20adeea449534d0ded.png)

## **类装饰器 **

**主要是通过@符号添加装饰器**

他会自动把class的构造函数传入到装饰器的第一个参数 target

然后通过prototype可以自定义添加属性和方法

```typescript
const doc: ClassDecorator = (target: any) => {
  target.prototype.name = 'czz'
}


@doc
class A {
  constructor() {

  }
}

const a: any = new A()
console.log(a.name);
```

如果不兼容，上面的方法也等同于

```typescript
const doc: ClassDecorator = (target: any) => {
  target.prototype.name = 'czz'
}

class A {
  constructor() {
  }
}
doc(A)

const a: any = new A()
console.log(a.name);
```

输出

```
czz
```





## 属性装饰器

同样使用@符号给属性添加装饰器

他会返回两个参数

- 原形对象
- 属性的名称

```typescript
const prototypeDecorator: PropertyDecorator = (target: any, key: string | symbol) => {
  console.log(target, key);
}
class B {
  @prototypeDecorator
  public name: string
  constructor() {
    this.name = 'czz'
  }
}
const b: any = new B()
```

输出

```
{} name
```



##  参数装饰器

同样使用@符号给属性添加装饰器

他会返回两个参数

- 原形对象
- 方法的名称
- 参数的位置从0开始

```typescript
const parameterDecorator: ParameterDecorator = (target: any, key: string | symbol, index: number) => {
  console.log(target, key, index)
}


class C {
  public name: string
  constructor() {
    this.name = ''
  }
  getName(@parameterDecorator name: string, @parameterDecorator age: number) {
    return this.name
  }
}
```



## 方法装饰器 

同样使用@符号给属性添加装饰器

他会返回两个参数

- 原形对象
- 方法的名称
- 属性描述符 可写对应writable，可枚举对应enumerable，可配置对应configurable

```typescript
const methodDecorator: MethodDecorator = (target: any, key: string | symbol, descriptor: any): any => {
  console.log(target, key, descriptor)
}

class D {
  public name: string

  constructor() {
    this.name = ''
  }

  @methodDecorator
  getName() {
    return this.name
  }
}
```

输出

```
{ getName: [Function (anonymous)] } getName {
  value: [Function (anonymous)],
  writable: true,
  enumerable: true,
  configurable: true
}
```

注意，如果*MethodDecorator*报红，编译错误，需要在tsconfig中设置

```
"compilerOptions": {
	"target": "ES5"
}
```

