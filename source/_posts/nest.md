---
title: NestJS
---

Nest.js 是一个基于 Node.js 的框架，并且完全支持 TypeScript, Nest.js 底层代码运行了 express 和 fastify；

## 编程思想

### 面向过程 & 面向对象

[面向过程&面向对象](/blog/2026/01/12/class)

### 面向切面编程 (AOP)

在不破坏封装功能的前提下，额外增加功能；
如增加一个日志功能，不是在原有的所有class类中增加功能；

## 设计模式

IOC (Inversion of Control) 控制反转，高层模块不应该依赖低层模块，两者都应该依赖抽象；

DI (Dependency Injection) 依赖注入，通过构造函数或者属性注入依赖；

- 栗子1

```typescript
// 耦合度非常高
// B类 和 C类 都必须依赖A类；
// 当修改A类，从不需要传入参数为需要时，依赖他们的B类和C类都得跟着改；
class A {
  name: string;
  // constructor() {}
  constructor(name: string) {
    this.name = name;
  }
}

class B {
  a: any;
  constructor() {
    // this.a = new A().name;
    this.a = new A("b");
  }
}

class C {
  a: any;
  constructor() {
    // this.a = new A().name;
    this.a = new A("c");
  }
}
```

```typescript IOC控制反转解耦
class A {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class B {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class Container {
  module: any;
  constructor() {
    this.module = {};
  }
  // DI 依赖注入
  provide(key: string, md: any) {
    this.module[key] = md;
  }
  get(key: string) {
    return this.module[key];
  }
}

const module = new Container();
module.provide("a", new A("aaa"));
module.provide("b", new B("bbb"));

// 引入 IOC container 后，C类 与 A类 逻辑解耦；
class C {
  a: any;
  b: any;
  constructor(module: Container) {
    this.a = module.get("a");
    this.b = module.get("b");
  }
}
```

- 栗子2

```typescript
class Iphone {
  playGame(name: string) {
    console.log(`${name} is playing ios game`);
  }
}

// Student的play方法 与 Iphone类有强依赖关系；
// 1. 如果Ipone类的playGame方法修改了，Student类的play方法代码逻辑就会出错；
// 2. 如果这个学生不是使用的ios类型的手机，因为这种强依赖关系，就需要修改play方法；
// 因此 Ipone类 与 Student类 需要进行解耦，也就是抽离play方法，传入动态参数传进来，也就解耦了；
class Student {
  constructor(private name: string) {}
  getName() {
    return this.name;
  }
  setName(name: string) {
    this.name = name;
  }
  play() {
    const iphone = new Iphone();
    iphone.playGame(this.name);
  }
}

const student = new Student("Tom");
student.play(); // Tom is playing ios game
```

```typescript
interface PhoneType {
  playGame(name: string): void;
}

class DIStudent {
  constructor(private name string, private phone: PhoneType) {
    this.name = name;
    this.phone = phone;
  }
  getName() {
    return this.name;
  }
  setName(name: string) {
    this.name = name;
  }
  play(){
    // 将强依赖的类抽离，而是作为一个参数传进来，也就实现了依赖注入；
    // 这种编程思想也就是控制反转；
    this.phone.playGame(this.name);
  }
}

class Iphone implements PhoneType {
  playGame(name: string) {
    console.log(`${name} is playing ios game`);
  }
}

class Android implements PhoneType  {
  playGame(name: string) {
    console.log(`${name} is playing android game`);
  }
}

class NewPhone implements PhoneType {
  playGame(name: string) {
    console.log(`${name} is playing new game`);
  }
}

const tom = new DIStudent('Tom', new Android());
tom.play(); // Tom is playing android game

const jerry = new DIStudent('Jerry', new Iphone());
jerry.play(); // Jerry is playing ios game
```

## command

`npm i @nestjs/cli -g` 创建项目脚手架
`nest new nest-project` 创建 nest 服务项目

nest g --help 查看所有命令
nest g mo/co/s (-d 查看会创建哪些文件) (\-\-no-spec 不创建测试文件)
nest g controller [name] 创建控制器
nest g service [name] 创建服务
nest g module [name] 创建模块
nest g resource [name] 创建包含以上三个资源统一命令
nest g middleware [name] 创建中间件

## MVC

M(model) V(view) C(controller): MVC 是一种软件架构模式，它将应用程序分为模型、视图和控制器三个部分；
视图层（客户端）发送请求给控制器，控制器根据不同的请求处理不同的模型逻辑，模型将结果返回并影响视图层；

## DTO & DAO

- D(data) T(transfer) O(object) 对请求发送来的参数数据操作，接收部分参数、对参数筛选、约束的属性小于等于实体类（service），通过 pipe 来操作；

- D(data) A(access) O(object) 对接数据库接口，不暴露数据内部信息，对应实体类；

## CURD & RESTful

CURD 是软件开发和数据库管理中最基础、最核心的四个操作。它是 Create、Read、Update 和 Delete 的首字母缩写.
RESTful API 是一种架构风格，它利用 HTTP 协议的特性（如方法 method、状态码 status、URI）来描述资源及其操作，也就是只需一个接口，就可以完成对资源的增删改查操作。

## 项目结构

### controller 控制器

- 处理 http 请求并返回响应；
- 路由分发：使用 @Get()、@Post() 等装饰器定义资源的 URI 路径；
- 参数解析：负责从请求中提取 @Body()、@Query() 或 @Param() 等参数；

```typescript user.controller.ts
import { Controller, Get, Post, Body, Param, Query } from "@nestjs/common";
import { UserService } from "./user.service";

@Controller("user")
export class UserController {
  constructor(private readonly userService: UserService) {}

  // localhost:3000/user  post方式创建
  @Post()
  create(@Body() body) {
    return {
      code: 200,
      message: "success",
      data: body,
    };
  }

  // localhost:3000/user  查询全部
  @Get()
  findAll(@Query() query) {
    return {
      code: 200,
      message: "success",
      data: query,
    };
  }

  // localhost:3000/user/1  查询单个根据动态参数
  @Get(":id")
  findOne(@Param("id") id: string) {
    return {
      code: 200,
      message: "success",
      data: id,
    };
  }

  // ...
}
```

### module 模块

> app.module.ts 项目根模块，用来处理其他模块的引用与共享
> 其他模块若设置了Global装饰器，只需在根模块引入，其他模块无需再导入就可直接使用；

- 功能模块：与共享模块（模块之间共用的一些功能）书写是一模一样的，只是叫法不同，功能模块如 order.module.ts、good.module.ts 模块，
- 共享模块：如 user.module.ts 模块；
- 全局模块：如 config.module.ts、database.module.ts、auth.module.ts、logger.module.ts 模块；
- 动态模块：使用时，才初始化模块；

```Typescript user.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UserModule } from './user/user.module';

@Module({
  imports: [UserModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

##### 共享模块

app.controller.ts 引用 user.service.ts 模块

```typescript app.controller.ts
import { Controller, Get } from "@nestjs/common";
import { AppService } from "./app.service";
import { UserService } from "./user/user.service";

@Controller()
export class AppController {
  constructor(
    private readonly appService: AppService,
    private readonly userService: UserService
  ) {}

  @Get()
  getHello(): string {
    return this.userService.findAll(); // 这是 user.service.ts 中的方法
  }
}

--------------------------------------

 // user.module.ts
import { Module } from '@nestjs/common';
import { UserService } from './user.service';
import { UserController } from './user.controller';

@Module({
  controllers: [UserController],
  providers: [UserService],
  exports: [UserService], // 暴露服务
})

export class UserModule {}
```

##### 全局模块

```typescript config.module.ts
import { Module, Global } from "@nestjs/common";

@Global() // 通过装饰器注册为全局模块
@Module({
  providers: [
    {
      provide: "Config",
      useValue: { baseUrl: "/api" },
    },
  ],
  // 导出模块，其他模块就可以注入该模块的配置项
  exports: [
    {
      provide: "Config",
      useValue: { baseUrl: "/api" },
    },
  ],
})
export class ConfigModule {}
```

```typescript app.module.ts
// app.module.ts 引用全局模块

@Module({
  imports: [UserModule, InfoModule, ConfigModule],
  controllers: [AppController],
  providers: [AppService],
})
```

```typescript user.controller.ts
export class UserController {
  constructor(
    private readonly userService: UserService,
    @Inject("Config") private readonly base: any,
  ) {}

  // localhost:3000/user  { baseUrl: '/api' }
  @Get()
  findAll() {
    return this.base;
  }
}
```

##### 动态模块

```typescript config.module.ts
import { Module, Global, DynamicModule } from "@nestjs/common";

interface Options {
  path: string;
}

@Global() // 通过装饰器注册为全局模块
@Module({})
export class ConfigModule {
  // 定义一个静态方法
  static forRoot(options: Options): DynamicModule {
    // 必须返回对象，且必须有module属性
    return {
      module: ConfigModule,
      providers: [
        {
          provide: "Config",
          useValue: { baseUrl: "/api" + options.path },
        },
      ],
      // 导出模块，其他模块就可以注入该模块的配置项
      exports: [
        {
          provide: "Config",
          useValue: { baseUrl: "/api" + options.path },
        },
      ],
    };
  }
}
```

```typescript app.module.ts
@Module({
  imports: [UserModule, InfoModule, ConfigModule.forRoot({ path: "/test" })],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

```typescript user.controller.ts
@Controller("user")
export class UserController {
  constructor(
    private readonly userService: UserService,
    @Inject("Config") private readonly base: any,
  ) {}

  // { baseUrl: '/api/test' }
  @Get()
  findAll() {
    return this.base;
  }
}
```

### service 服务

> 定义类，处理业务逻辑与数据层的交互（如数据库等），和其他额外的一些三方请求；

- create 方法：相当于创建了一个实体对象；
- save 方法：相当于具体的sql语句，把数据写入到数据库中，如果实体ID存在会更新数据，可触发钩子方法；
- insert 方法：插入不了数据会报错；

- remove 方法：根据实体对象删除，可触发钩子方法；
- delette 方法：根据ID、ID数组、查询条件删除；

```typescript user.service.ts
import { Injectable } from "@nestjs/common";
import { CreateUserDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";

// 装饰器，表示该类可以被 Nest.js 的 IoC 容器管理，并注入到 Controller 中
@Injectable()

// 定义类
export class UserService {
  create(createUserDto: CreateUserDto) {
    return "This action adds a new user";
  }

  findAll() {
    return `This action returns all user`;
  }

  findOne(id: number) {
    return `This action returns a #${id} user`;
  }

  update(id: number, updateUserDto: UpdateUserDto) {
    return `This action updates a #${id} user`;
  }

  remove(id: number) {
    return `This action removes a #${id} user`;
  }
}
```

##### 自定义名称

```TYPESCRIPT user.module.ts
import { Module } from '@nestjs/common';
import { UserService } from './user.service';
import { UserController } from './user.controller';

@Module({
  controllers: [UserController],

  // 简写
  // providers: [UserService],

  // 完整写法
  providers: [
    UserService,
    {
      provide: 'AAA', // 自定义类名称
      useClass: UserService, // 使用类
    },
  ],
})
export class UserModule {}

--------------------------------------------------------------------------

import { Controller, Get, Inject } from '@nestjs/common';
import { UserService } from './user.service';

@Controller('user')
export class UserController {

  // 注入自定义类名
  constructor(@Inject('AAA') private readonly userService: UserService) {}

  @Get()
  findAll() {
    return this.userService.findAll();
  }
}

```

##### 自定义值

```TYPESCRIPT user.module.ts
import { Module } from '@nestjs/common';
import { UserService } from './user.service';
import { UserController } from './user.controller';

@Module({
  controllers: [UserController],
  // providers: [UserService],   // 简写

  // 完整写法
  providers: [
    UserService,
    {
      provide: 'AAA', // 自定义类名称
      useClass: UserService, // 使用类
    },
    {
      provide: 'CCC',
      useValue: [1, 2, 3], // 可定义字符串、数组、对象的值
    },
  ],
})
export class UserModule {}

-------------------------------------------------------------

import { Controller, Get, Inject } from '@nestjs/common';
import { UserService } from './user.service';

@Controller('user')
export class UserController {
  constructor(
    @Inject('AAA') private readonly userService: UserService,
    @Inject('CCC') private readonly shop: number[],
  ) {}

  // http://localhost:3000/user  [1,2,3]
  @Get()
  findAll(): number[] {
    return this.shop;
  }
}
```

##### 工厂模式

##### 异步模式

## 生命周期

### processes

| 生命周期       | 描述                                               |                |
| -------------- | -------------------------------------------------- | -------------- |
| ⬇️ request     | 客户端发起请求                                     |                |
| ⬇️ middleware  | 全局中间件 ➡️ 模块中间件                           |                |
| ⬇️ guard       | 全局守卫 ➡️ 控制器守卫 ➡️ 路由守卫                 | 权限           |
| ⬇️ interceptor | 全局拦截器pre ➡️ 控制拦截器pre ➡️ 路由拦截器pre    |                |
| ⬇️ pipe        | 全局管道 ➡️ 控制器管道 ➡️ 路由管道 ➡️ 路由参数管道 | 过滤多余字段   |
| ⬇️ controller  | -                                                  |                |
| ⬇️ service     | -                                                  |                |
| ⬇️ interceptor | 路由拦截器post ➡️ 控制拦截器post ➡️ 全局拦截器post | 过滤响应的字段 |
| ⬇️ filter      | 路由过滤器 ➡️ 控制器过滤器 ➡️ 全局过滤器           | 处理应用异常   |
| ⬇️ response    | 响应客户端                                         |                |

### middleware

是在路由处理函数执行之前执行的函数，可对请求对象和响应对象进行修改、调用下一个中间件函数、next 函数若没有调用，则请求将不会继续往下执行。

##### 模块中间件

先创建一个名为 logger 的中间件： nest g middleware logger

```typescript logger.middleware.ts
import { Injectable, NestMiddleware } from "@nestjs/common";

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: any, res: any, next: () => void) {
    res.send("若不执行next, 执行send, 中间件拦截了路由的 response");
    // next();
  }
}
```

```typescript user.module.ts
import {
  Module,
  NestModule,
  MiddlewareConsumer,
  RequestMethod,
} from "@nestjs/common";
import { UserService } from "./user.service";
import { UserController } from "./user.controller";
import { LoggerMiddleware } from "../logger/logger.middleware";

@Module({
  controllers: [UserController],
  providers: [UserService],
  exports: [UserService],
})
export class UserModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    // 注入中间件并指定 user 的路由
    // consumer.apply(LoggerMiddleware).forRoutes('user');

    // 只针对get方法
    consumer.apply(LoggerMiddleware).forRoutes({
      path: "user",
      method: RequestMethod.GET,
    });

    // 拦截user所有路由
    // consumer.apply(LoggerMiddleware).forRoutes(UserController);
  }
}
```

##### 全局中间件

```typescript main.ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import * as cors from "cors";
import { Request, Response, NextFunction } from "express";

const whiteList = ["/user"];

function GlobalMiddleware(req: Request, res: Response, next: NextFunction) {
  if (whiteList.includes(req.originalUrl)) {
    next();
  } else {
    res.send("全局中间件拦截了 response");
  }
}

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.use(cors); // 注册第三方中间件
  app.use(GlobalMiddleware); // 注册全局中间件
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

### guard

根据条件（例如权限、角色、访问控制列表、token 等）来确定是否允许请求继续执行。
守卫在每个中间件之后执行，在任何拦截器或管道之前执行。

##### 路由守卫

##### 控制器守卫

如是否是管理员角色的路由守卫 `nest g gu role/role --no-spec`

```ts role.guard.ts
import { CanActivate, ExecutionContext, Injectable } from "@nestjs/common";
import { Observable } from "rxjs";
import { UserService } from "src/user/user.service";

@Injectable()
export class RoleGuard implements CanActivate {
  async canActivate(
    context: ExecutionContext,
    userService: UserService,
  ): boolean | Promise<boolean> | Observable<boolean> {
    // 获取请求对象
    const request = context.switchToHttp().getRequest();
    const user = await this.userService.findOneByUsername(
      request.user.username,
    );

    if (user && user.role === "isAdmin") {
      return true;
    } else {
      return false;
    }
  }
}
```

```ts user.controller.ts
import { Controller, Get, Post, Body, Param, UseGuards } from "@nestjs/common";
import { GuardService } from "./guard.service";
import { CreateGuardDto } from "./dto/create-guard.dto";
import { RoleGuard } from "src/guard/role/role.guard";

@Controller("user")
// 控制器守卫，当前控制器下的所有路由都会触发守卫；
@UseGuards(RoleGuard)
export class UserController {
  constructor(private readonly guardService: GuardService) {}

  @Post()
  create(@Body() createGuardDto: CreateGuardDto) {
    return this.guardService.create(createGuardDto);
  }

  @Get()
  // 路由守卫
  @UseGuards(RoleGuard)
  findAll() {
    return this.guardService.findAll();
  }
}
```

##### 全局守卫

全局守卫：所有路由请求都会触发此守卫；

```ts
main.ts;
// import { NestExpressApplication } from "@nestjs/platform-express";
// import { RoleGuard } from "src/guard/role/role.guard";

// async function bootstrap() {
//   const app = await NestFactory.create<NestExpressApplication>(AppModule);

//   注意：若 RoleGuard 守卫中有引入模块如UserService，会无法访问，需在app.module.ts中引入方式；
//   app.useGlobalGuards(new RoleGuard());
//   await app.listen(process.env.PORT ?? 3000);
// }
// bootstrap();

app.module.ts
import { RoleGuard } from "src/guard/role/role.guard";

@Module({
  providers: [AppService, {
    provide: 'APP_GUARD',
    useClass: RoleGuard,
  }],
})
```

##### 自定义守卫

```ts guard.controller.ts
import {
  Controller,
  Get,
  Post,
  Body,
  Param,
  UseGuards,
  SetMetadata,
} from "@nestjs/common";
import { GuardService } from "./guard.service";
import { CreateGuardDto } from "./dto/create-guard.dto";
import { RoleGuard } from "./role/role.guard";

// localhost:3000/guard 下任意路由都会触发 RoleGuard 中的canActivate方法
@Controller("guard")
@UseGuards(RoleGuard) // 添加装饰器
export class GuardController {
  constructor(private readonly guardService: GuardService) {}

  @Post()
  create(@Body() createGuardDto: CreateGuardDto) {
    return this.guardService.create(createGuardDto);
  }

  // 访问 localhost:3000/guard/1?roles=admin 触发路由守卫
  // 两个参数都可是任意字符，通过第二个参数判断是否能访问此路由；
  @Get(":id")
  @SetMetadata("roles", ["admin"])
  findOne(@Param("id") id: string) {
    return this.guardService.findOne(+id);
  }
}
```

```typescript role.guard.ts
import { CanActivate, ExecutionContext, Injectable } from "@nestjs/common";
import { Observable } from "rxjs";
import { Reflector } from "@nestjs/core";
import type { Request } from "express";

@Injectable()
export class RoleGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(
    context: ExecutionContext,
  ): boolean | Promise<boolean> | Observable<boolean> {
    // 与controller中定义的key: roles 一致
    const roles = this.reflector.get<string[]>("roles", context.getHandler());
    console.log(roles); // [ 'admin' ]

    const req = context.switchToHttp().getRequest<Request>();

    // 读取GET请求中的query中携带的参数
    if (roles.includes(req.query.roles as string)) {
      console.log("通过");
      return true;
    } else {
      return false;
    }
  }
}
```

### pipe

`npm i class-validator class-transformer -S`

##### 类型转换

```typescript user.controller.ts
import {
  ValidationPipe,
  ParseIntPipe,
  ParseFloatPipe,
  ParseBoolPipe,
  ParseArrayPipe,
  ParseUUIDPipe,
  ParseEnumPipe,
  DefaultValuePipe,
} from "@nestjs/common";

@Controller("user")
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Get(":id")
  // findOne(@Param('id') id: string) {}

  // findOne(@Param('id', ParseIntPipe) id: number) {}

  // 参数非 UUID 格式会抛出 400 错误
  findOne(@Param("id", ParseUUIDPipe) id: number) {
    return this.userService.findOne(+id);
  }
}
```

##### 校验参数

接收参数过滤没有装饰器修饰的字段；
参数将根据 DTO 类的验证规则进行校验；

```typescript create-user.dto.ts
import { IsNotEmpty, IsString, Length } from "class-validator";

export class CreateUserDto {
  @IsString()
  @IsNotEmpty({
    message: "用户名不能为空",
  })
  @Length(3, 20, {
    message: "用户名长度在3 - 20之间",
  })
  username: string;

  // 没有装饰器修饰的属性，请求对象中的参数将被过滤掉；
  passage: string;
}
```

```typescript main.ts
import { NestExpressApplication } from "@nestjs/platform-express";
import { ValidationPipe } from "@nestjs/common";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);

  // 需全局配置后，参数报错信息才会根据 dto.ts 中定义的规则返回
  app.useGlobalPipes(
    new ValidationPipe({
      // 参数过滤，只接受 DTO 中定义的有装饰器修饰的参数 (更新操作有风险)，常打开；
      whitelist: true,
    }),
  );
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

##### 自定义校验

### intercepter

> 拦截器会触发两次，若 service 这层执行错误，则不会触发第二次；
> 拦截器用于**成功响应**时，对响应数据进行操作；filter对异常时，对**异常处理**；
> request > middleware > guard > **interceptor** > pipe > controller > service > **interceptor** > filter > response;

- 统一响应格式 (如 {code: 200, message: 'success'})
- 缓存 (对响应数据进行缓存)
- 日志记录 (记录请求日志)
- 异常映射 (转换异常类型)
- 数据转换 (对响应数据进行序列化/转换)

#### 案例 - 数据转换

service 完成后，处理成功的响应数据返回给前端；

```typescript
// src/common/response.interceptor.ts
import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor,
} from '@nestjs/common';
import { Observable, map } from 'rxjs';
import { Request } from 'express';
//同步 异步 then catch ->数据流->pipe -> map filter -> 返回

// 当返回大数据时会报错，转换 bigint 成字符串
const transformBigInt = (data: any) => {
  if (typeof data === 'bigint') {
    return data.toString();
  }
  if (Array.isArray(data)) {
    return data.map(transformBigInt);
  }
  if (typeof data === 'object' && data !== null) {
    if (data instanceof Date) {
      return data;
    }
    return Object.fromEntries(
      Object.entries(data).map(([key, value]) => [key, transformBigInt(value)]),
    );
  }
  return data;
};

@Injectable()
export class ResponseInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const ctx = context.switchToHttp();
    const request = ctx.getRequest<Request>();
    return next.handle().pipe(
      // 获取到从server 返回的数据；
      map((data) => {
        return {
          success: true,
          path: request.url,
          timestmap: new Date().toISOString(),
          code: data.code ?? 200, //业务逻辑自定义
          message: data.message ?? 'success', //业务逻辑自定义
          data: transformBigInt(data.data) ?? null, // data响应数据
        };
      }),
    );
  }
}



---------------------------------------------------------------------

// main.ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import { NestExpressApplication } from "@nestjs/platform-express";
import { join } from "path";
import { ResponseInterceptor } from "./common/response.interceptor.ts";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useStaticAssets(join(__dirname, "public"));
  app.useGlobalInterceptors(new ResponseInterceptor());   // 注册全局拦截器；
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

#### 案例 - 过滤响应字段

使用内置的 ClassSerializerInterceptor 与 @Exclude/@Expose 配合使用，过滤/暴露响应时的字段；
ps: 另一方案是通过自定义拦截器过滤过滤字段；

```ts User.entity.ts
export class User {
  @PrimaryGeneratedColumn()
  // 返回时显示 id 字段
  @Expose()
  id: number;

  @Column()
  // 返回时排除 password 字段
  @Exclude()
  password: string;
}
```

```ts auth.controller.ts
import { ClassSerializerInterceptor } from "@nestjs/common";

@Controller("auth")
export class AuthController {
  constructor(private readonly authService: AuthService) {}

  @Get("/profile/:userId")
  @UseInterceptors(ClassSerializerInterceptor)
  getProfile(@Param("userId") id: string, @Req() req) {
    return this.authService.getProfile(id);
  }
}
```

### filter

路由过滤器 ➡️ 控制器过滤器 ➡️ 全局过滤器
HttpException是nestjs内置的异常，负责处理整个应用的异常并发送响应对象。

##### 路由过滤器

```ts user.controller.ts
import {
  Get,
  HttpException,
  HttpStatus,
  NotFoundException,
  ForbiddenException,
} from "@nestjs/common";

export class UserController {
  @Get()
  findAll(@Query() query: { keyword: string; page: number; size: number }) {
    const mockData = { isAdmin: false };
    if (mockData.isAdmin === false) {
      // throw new HttpException('用户没有授权', HttpStatus.FORBIDDEN);
      throw new ForbiddenException("用户没有授权");
    }
    return this.userService.findAll(query);
  }
}
```

##### 全局过滤器

通过**自定义异常过滤器**与结合**logger**设置全局的错误处理逻辑；

```typescript src/common/http-exception-filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  Logger,
} from "@nestjs/common";
import { Request, Response } from "express";

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  constructor(private readonly logger: Logger) {}

  catch(exception: HttpException, host: ArgumentsHost) {
    // 控制台打印异常信息；
    this.logger.error(`${exception.message} ${exception.stack}`);

    // 获取上下文，host代表进程；
    const ctx = host.switchToHttp();
    // 获取请求和响应对象和状态码；
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    // 自定义响应数据
    response.status(status).json({
      // 将 statusCode 转为 status 字段；
      status,
      path: request.url,
      method: request.method,
      timestamp: new Date().toISOString(),
      message: exception.message || HttpException.name,
    });
  }
}
```

```typescript main.ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import { NestExpressApplication } from "@nestjs/platform-express";
import { Logger, ValidationPipe } from "@nestjs/common";
import { HttpExceptionFilter } from "./common/http-exception-filter";

async function bootstrap() {
  const logger = new Logger();
  const app = await NestFactory.create<NestExpressApplication>(AppModule, {
    logger: ["error", "warn"],
  });
  app.enableCors();
  app.useGlobalPipes(new ValidationPipe());
  app.useGlobalFilters(new HttpExceptionFilter(logger));
  await app.listen(process.env.PORT ?? 3000);
  logger.warn(`"Server is running on 127.0.0.1:${process.env.PORT ?? 3000}"`);
}
bootstrap();
```

## static

```typescript main.ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import { NestExpressApplication } from "@nestjs/platform-express";
import { join } from "path";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);

  // 配置静态资源访问目录为根目录 public；

  // 访问路径：http://localhost:3000/images/1766740375065-585060088.png ，
  // 若配置了 prefix，则访问 http://localhost:3000/public/images/1766740375065-585060088.png
  app.useStaticAssets(join(__dirname, "public"), { prefix: "/public" });
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

## log

- nest 内置 Logger

```ts user.controller.ts 单文件使用内置 logger
import {
  Controller,
  Get,
  Post,
  Body,
  Patch,
  Param,
  Delete,
  Query,
  Logger,
} from "@nestjs/common";
export class UserController {
  // 初始化一个logger
  private logger = new Logger(UserController.name);

  constructor(private readonly userService: UserService) {
    this.logger.warn("nestjs-logger init");
  }

  @Get(":id")
  findOne(@Param("id") id: string) {
    // 调用Logger打印一下日志
    this.logger.log("User Controller findOne");
    return this.userService.findOne(+id);
  }
}
```

- 第三方库 pino 及 配置 pino 格式和回滚
  `npm install nestjs-pino pino-pretty --save`

```ts user.controller.ts 单文件调用 pino
import { UserService } from "./user.service";
import { Logger } from "nestjs-pino";

export class UserController {
  constructor(
    private readonly userService: UserService,
    private readonly logger: Logger,
  ) {
    this.logger.warn("nestjs-pino init");
  }
}
```

```ts app.module.ts 全局配置 pino
@Module({
  imports: [
    // 不传就是默认，可传配置对象定义样式和格式化
    LoggerModule.forRoot({
      pinoHttp: {
        transport:
          process.env.NODE_ENV === "development"
            ? {
                // 开发环境
                target: "pino-pretty",
                options: {
                  colorize: true,
                },
              }
            : {
                // 生产环境
                target: "pino-roll",
                options: {
                  mkdir: true, // 根目录创建logs目录
                  dateFormat: "yyyy-MM-dd", // 日志文件名
                  file: join("logs", "log"), // 日志文件后缀名
                  frequency: "daily", // 日志文件生成频率
                  size: "20m", // 日志文件大小
                  maxFiles: 10, // 保留最近10个日志文件
                },
              },
      },
    }),
  ],
})
export class UserModule {}
```

## upload

需安装 multer、@types/multer、 @nestjs/platform-express (nest 已经自带) 模块；
再生成 upload 目录: nest g res upload；

```typescript upload.module.ts
import { Module } from "@nestjs/common";
import { UploadService } from "./upload.service";
import { UploadController } from "./upload.controller";
import { MulterModule } from "@nestjs/platform-express";
import { diskStorage } from "multer";
import { extname, join } from "path";

@Module({
  controllers: [UploadController],
  providers: [UploadService],
  imports: [
    MulterModule.register({
      storage: diskStorage({
        // 配置存储路径为根目录下的 public/images 文件夹；
        // 可以从dist/public/images 看到上传的文件；
        destination: join(__dirname, "../public/images"),
        filename: (req, file, cb) => {
          // 获取文件名称后缀 如 .png
          const fileExtName = extname(file.originalname);

          // 重新定义文件名
          const fileName =
            Date.now() + "-" + Math.round(Math.random() * 1e9) + fileExtName;

          cb(null, fileName);
        },
      }),
    }),
  ],
})
export class UploadModule {}
```

```typescript upload.controller.ts
import {
  Controller,
  Post,
  UseInterceptors,
  UploadedFile,
} from "@nestjs/common";
import { UploadService } from "./upload.service";
import { FileInterceptor } from "@nestjs/platform-express";

@Controller("upload")
export class UploadController {
  constructor(private readonly uploadService: UploadService) {}

  // localhost:3000/upload 接口路径
  // 设置接收单个文件并设置接收文件名
  @Post()
  @UseInterceptors(FileInterceptor("file"))
  upload(@UploadedFile() file) {
    return "文件上传成功";
  }
}
```

## download

```typescript upload.controller.ts
import { Controller, Res, Get } from "@nestjs/common";
import { UploadService } from "./upload.service";
import type { Response } from "express";
import { join } from "path";
import { zip } from "compressing"; // 需先安装 compressing 引入zip模块

@Controller("upload")
export class UploadController {
  constructor(private readonly uploadService: UploadService) {}

  // localhost:3000/upload/export 下载接口路径
  @Get("export")
  download(@Res() res: Response) {
    const url = join(__dirname, "../public/images/1766990076897-424988024.png");
    res.download(url);
  }

  /**
    localhost:3000/upload/byStream 前端接收二进制流并下载;

    const useFetch =async （url:string)=>{
      const res = await fetch（url).then（res =>res.arrayBuffer())
      const blob =new Blob（[res])
      const Url=URL.createObjectURL（blob)
      const a=document.createElement('a')
      a.href=Url;
      a.download='xiaoman.zip
      a.click()
    }
   */
  @Get("byStream")
  async downloadByStream(@Res() res: Response) {
    const url = join(__dirname, "../public/images/1766990076897-424988024.png");
    const stream = new zip.Stream();
    await stream.addEntry(url);

    res.setHeader("Content-Type", "application/octet-stream");
    res.setHeader("Content-Disposition", `attachment; filename=${"file.zip"}`);

    stream.pipe(res);
  }
}
```

## rxjs

NextJs 自带 rxjs 模块，用于将同步和异步都处理成数据流，并且可同时处理多个异步 (.then .catch 只能处理一个异步)；(较少使用)

- Observable 可观察的物件；
- Subscriber 监听 Observable；
- Operators 操作符, 可以处理管道的数据 map filter concat reduce 等；

```typescript
import { of, Observable, interval, take } from "rxjs";
import { map, filter } from "rxjs/operators";

// 创建一个实例对象，接收一个回调函数
const observable = new Observable((observer) => {
  observer.next(1);
  observer.next(2);
  observer.next(3);

  setTimeout(() => {
    observer.next(4);
    observer.complete();
  }, 2000);
});

observable.subscribe({
  next(x) {
    console.log("got value " + x);
  },
  error(err) {
    console.error("something wrong occurred: " + err);
  },
  complete() {
    console.log("done");
  },
});
```

```typescript
import { of, Observable, interval, take } from "rxjs";
import { map, filter } from "rxjs/operators";

// 以1秒的速度从0-4输出，直到5暂停
interval(1000)
  .pipe(take(5))
  .subscribe((e) => {
    console.log(e); // 0 1 2 3 4
  });

-----------------------------------------

const subs = interval(1000)
  .pipe(map((v) => ({ num: v })))
  .subscribe((e) => {
    console.log(e); // {num: 0}... {num: 4}
    if (e.num === 4) subs.unsubscribe();
  });

-----------------------------------------

const subs = interval(1000)
  .pipe(
    map((v) => ({ num: v })),
    filter((e) => e.num % 2 === 0),
  )
  .subscribe((e) => {
    console.log(e); // {num: 0} {num: 2} {num: 4}
    if (e.num === 4) subs.unsubscribe();
  });
```

## 自定义装饰器

创建 nest g decorator [decorator]

## swagger

npm i @nestjs/swagger swagger-ui-express

```typescript main.ts
import { SwaggerModule, DocumentBuilder } from "@nestjs/swagger";
import { NestExpressApplication } from "@nestjs/platform-express";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  const options = new DocumentBuilder()
    .setTitle("Api docs")
    .setDescription("Nest.js API description")
    .setVersion("1.0")
    .addTag("Nest.js")
    .build();
  const document = SwaggerModule.createDocument(app, options);

  // 访问 localhost:3000/api-docs 访问swagger文档地址
  SwaggerModule.setup("/api-docs", app, document);
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

```typescript user.controller.ts
import { Controller, Get, Post, Body, Param } from "@nestjs/common";
import { UserService } from "./user.service";
import { CreateUserDto } from "./dto/create-user.dto";
import {
  ApiTags,
  ApiOperation,
  ApiParam,
  ApiQuery,
  ApiResponse,
} from "@nestjs/swagger";

@Controller("user")
// user 相关接口的分组名称
@ApiTags("用户接口")
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Post()
  create(@Body() createUserDto: CreateUserDto) {
    return this.userService.create(createUserDto);
  }

  @Get()
  // 单个接口添加描述
  @ApiOperation({ summary: "获取所有用户", description: "更详细的描述信息" })
  // 参数描述
  @ApiQuery({
    name: "name",
    description: "用户名",
    required: false,
    type: String,
  })
  // 返回参数描述
  @ApiResponse({
    status: 403,
    description: "你是小黑子，拒绝访问",
    type: CreateUserDto,
  })
  findAll() {
    return this.userService.findAll();
  }

  @Get(":id")
  // 动态参数的描述
  @ApiParam({ name: "id", description: "用户id", required: true, type: String })
  findOne(@Param("id") id: string) {
    return this.userService.findOne(+id);
  }
}
```

```typescript create-user.dto.ts
import { IsNotEmpty, IsString, Length } from "class-validator";
import { ApiProperty } from "@nestjs/swagger";

export class CreateUserDto {
  @IsString()
  @IsNotEmpty({
    message: "用户名不能为空",
  })
  @Length(3, 20, {
    message: "用户名长度在3 - 20之间",
  })
  @ApiProperty({
    description: "用户名",
    example: "admin",
    required: true,
    enum: ["admin", "user"],
  })
  username: string;

  @ApiProperty({ description: "密码", example: "123456" })
  passage: string;
}
```

![](/images/nest/swagger.png)

## config

需全局配置，就能读取环境变量；

`npm i @nestjs/config -S`

```ts app.module.ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { UserModule } from "./user/user.module";
import { ConfigModule } from "@nestjs/config";

@Module({
  imports: [
    UserModule,

    // 方式1：只能在 app.controller.ts 和 app.service.ts 两个文件中才能使用；
    // ConfigModule,

    // 方式2：在 app.module.ts 这里import，可以在任何地方使用；
    // 其他模块如 user.module.ts 不用imports再引入一遍，只需在 user.controller.ts 中引入 ConfigModule 使用即可；
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: [".env", ".env.development", ".env.production"], // 读取环境文件
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

```ts user.controller.ts
import { Controller, Get } from "@nestjs/common";
import { UserService } from "./user.service";
import { ConfigService } from "@nestjs/config"; // 引入config服务
import { ConfigEnum } from "src/enum/config.enum";

@Controller("user")
export class UserController {
  constructor(
    private readonly userService: UserService,
    private readonly configService: ConfigService,
  ) {}

  @Get()
  findAll() {
    const db = this.configService.get(ConfigEnum.DB_DATABASE);
    const dbHost = this.configService.get(ConfigEnum.DB_HOST);
    console.log(db, dbHost); // study 127.0.0.1
    return this.userService.findAll();
  }
}
```

- 通过环境变量配置数据库连接信息

```ts app.module.ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { UserModule } from "./user/user.module";
import { ConfigModule } from "@nestjs/config";

@Module({
  imports: [
    UserModule,
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: [`.env.${process.env.NODE_ENV}`, ".env"],
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

需安装 'npm i cross-env -S'

```json package.json
{
  "scripts": {
    // "build": "nest build",
    "build": "cross-env NODE_ENV=production nest build",

    // "start:dev": "nest start --watch",
    "start:dev": "cross-env NODE_ENV=development nest start --watch",

    // "start:debug": "nest start --debug --watch",
    "start:debug": "cross-env NODE_ENV=development nest start --debug --watch",

    // npm run build 打包后文件路径有点问题，需加上src，再 npm run start:prod 可运行生产环境；
    // "start:prod": "node dist/main",
    "start:prod": "cross-env NODE_ENV=production node dist/src/main"
  }
}
```

运行了start:dev 或 start:prod，.env.development 或 .env.production 文件会合并且覆盖 .env 文件中同名变量;

```ts
// .env
DB_TYPE=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_SYNCHRONIZE=false

// .env.development
DB_DATABASE=study
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USERNAME=root
DB_PASSWORD=123456
DB_SYNCHRONIZE=true

// .env.production
DB_DATABASE=study
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USERNAME=root
DB_PASSWORD=123456
DB_SYNCHRONIZE=false
```

## Typeorm

typeorm 是 Typescript 中最成熟的对象关系映射器（ORM: Object Relational Mapping 数据库的对象映射），把面向对象的概念跟数据库中的概念对应起；
举例：定义一个对象就对应着一张表，这个对象的实例就对应着表中的以表中的一条数据，当创建实体时，会自动创建数据库这张表；

`npm install @nestjs/typeorm typeorm mysql2 -S`

```ts app.module.ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { UserModule } from "./user/user.module";
import { TypeOrmModule } from "@nestjs/typeorm";

@Module({
  imports: [
    UserModule,

    // 方式A
    TypeOrmModule.forRoot({
      type: "mysql", // 连接的数据库类型
      username: "root", // 数据库的账户名
      password: "123456", // 数据库的密码
      host: "localhost", // 数据库的连接地址
      port: 3306, // 端口
      database: "study", // 连接哪个数据库
      retryAttempts: 5, // 重试次数
      retryDelay: 3000, // 重连间隔时间
      synchronize: true, // 自动将实体类同步到数据库中（开发环境可以，生产环境最好false）
      logging: process.env.NODE_ENV === "development", // 访问接口时，打印原生形式的sql语句

      // 方式1：手动添加实体文件 (不推荐)
      // entities: [User, Profile, Log, Role],

      // 方式2：自动匹配每个目录下的 entity, 如 user.entity.ts 加载实体文件 (不推荐)
      // entities: [__dirname + '/**/*.entity{.ts,.js}'],

      // 方式3：开启后，自动加载实体，注册的每个实体都将自动添加到配置对象的实体数组中（推荐）
      autoLoadEntities: true,
    }),

    // 方式B
    // 通过运行环境动态配置数据库连接信息
    TypeOrmModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (configService: ConfigService) => ({
        type: "mysql", // 连接的数据库类型
        database: configService.get(ConfigEnum.DB_DATABASE),
        username: configService.get(ConfigEnum.DB_USERNAME),
        password: configService.get(ConfigEnum.DB_PASSWORD),
        host: configService.get(ConfigEnum.DB_HOST),
        synchronize: configService.get(ConfigEnum.DB_SYNCHRONIZE),
        autoLoadEntities: true,
      }),
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

#### entity

实体是一个映射到数据库表的类

##### 属性

- entity中定义的属性，默认前端就是必传参数，否则会报500 (非必传设置 nullable:true)，dto 验证是报 400 (非必传设置IsOptional)；
- 没添加任意有关列的修饰符 (如 Column, Exclude 等)，数据库没有这个字段；

##### Column

- @Column({select: false}) 数据库层级隐藏字段 (service 层都查出来的数据都没有这个字段);

##### Exclude

- @Exclude() **响应对象**过滤字段 (需配合 ClassSerializerInterceptor 拦截器使用；默认所有字段都是暴露状态);

##### JoinColumn

- 哪张表是关系的所有者就这一侧设置外键 @JoinColumn，如 user与profile 一对一关系，就在user设置；如 order 与 user 多对一关系，就在order设置 @JoinColumn;
- 定义外键字段，默认外键字段名为 '属性名\_id', 只需在一侧表定义即可，另一侧无需定义，默认就是主键;
- 在 @OneToOne 一对一的关系中，必须有 @JoinColumn，在@ManyToOne 多对一关系可省略 @JoinColumn；

```ts user.entity.ts
import {
  Entity,
  Column,
  OneToMany,
  OneToOne,
  PrimaryGeneratedColumn,
  JoinColumn,
  ManyToMany,
  JoinTable,
  BeforeInsert,
  AfterInsert,
  BeforeUpdate,
  AfterUpdate,
  AfterRemove,
} from "typeorm";
import { Phone } from "./phone.entity";
import { Profile } from "./profile.entity";
import { Role } from "../../role/entities/role.entity";

@Entity()

// user表示数据库中的表名
export class User {
  // 自动递增的主键
  @PrimaryGeneratedColumn()
  @Expose()
  id: number;

  @Column({ unique: true })
  username: string;

  // 响应时排除 password 字段
  @Exclude()
  password: string;

  @Column({
    name: "age", // 表列名
    nullable: true, // 允许字段是否为空
    comment: "年龄",
    select: false, // 查询时false不返回该字段
    update: true, // save操作时是否更新该字段值
    collation: "", // 定义列排序规则
  })
  age: number;

  @Column({ type: "enum", enum: ["male", "female"], default: "male" })
  gender: string;

  // user表与profile表是一对一关系，cascade 级联操作允许两种表双向的插入、更新、删除操作；
  // 如删除某条user数据，会同时删除该条profile数据，反之亦然；
  @OneToOne(() => Profile, (profile) => profile.user, { cascade: true })
  profile: Profile;

  // user表与phone表是一对多关系，1参是回调函数来与谁创建关联关系，2参通过哪个具体字段进行关联查询；
  // 如删除某条user数据，会同时删除关于该user的所有phones数据，反之亦然；
  @OneToMany(() => Phone, (phone) => phone.user, {
    cascade: true,
    eager: true, // 查询主实体时，无需再设置relations就会自动关联查询；
  })
  phones: Phone[];

  // user表与role表是多对多关系；
  // 限制 user 实体 insert 对于 roles 属性的操作; 同样 role.insert 对于 users 属性操作; role 实体 remove 不会删除 user 数据；
  @ManyToMany(() => Role, (role) => role.users, { cascade: ["insert"] })
  // 创建中间表；
  @JoinTable({ name: "user_role" })
  roles: Role[];

  @BeforeInsert()
  beforeInsert() {
    console.log("User inserted");
  }
  @AfterInsert()
  afterInsert() {
    console.log("User inserted");
  }
  @BeforeUpdate()
  beforeUpdate() {
    console.log("User updated");
  }
  @AfterUpdate()
  afterUpdate() {
    console.log("User updated");
  }
  @AfterRemove()
  afterRemove() {
    console.log("User removed");
  }
}
```

```ts profile.entity.ts
import {
  Entity,
  Column,
  PrimaryGeneratedColumn,
  OneToOne,
  JoinColumn,
} from "typeorm";
import { User } from "./user.entity";

@Entity()
export class Profile {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ type: "varchar" })
  username: string;

  @Column({ type: "enum", enum: ["male", "female"], default: "male" })
  gender: string;

  @Column({ type: "varchar", length: 255, nullable: true })
  nickname: string;

  // profile表与user表是一对一的关系，1参是回调函数来与谁创建关联关系；
  // profile不能设置 cascade 会导致死循环，只需user单边设置即可；
  @OneToOne(() => User, { onDelete: "CASCADE" })
  // 创建关联字段：默认是id + 定义的字段user = userId，也可自定义；
  @JoinColumn({
    // name: "fk_user_id", // 创建外键列，可自定义外键列名
    // referencedColumnName: "id", // 创建外键列，可自定义外键列名（默认 'id'）
  })
  // user的值就是User实体定义的数据；
  user: User;
}
```

```ts phone.entity.ts
import {
  Entity,
  Column,
  PrimaryGeneratedColumn,
  ManyToOne,
  JoinColumn,
} from "typeorm";
import { User } from "./user.entity";

@Entity()
export class Phone {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  brand: string;

  @Column()
  model: string;

  @Column()
  price: number;

  // phone表与user表是多对一的关系，1参是回调函数来与谁创建关联关系；
  @ManyToOne(() => User)
  // ManyToOne可以省略JoinColumn
  // @JoinColumn()
  user: User;
}
```

```ts role.entity.ts
import { Entity, Column, PrimaryGeneratedColumn, ManyToMany } from "typeorm";
import { User } from "src/user/entities/user.entity";

@Entity()
export class Role {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  description: string;

  @ManyToMany(() => User, (user) => user.roles)
  users: User[];
}
```

##### cascade

- cascade 是在 '一' 侧，onDelete是在 '多' 侧；

- cascade: [\'insert\']

在父类中直接保存所有新建父类和子类对象；

```ts
const category = new Category();
category.name = "技术文章";

const article1 = new Article();
article1.title = "NestJS 入门教程";

const article2 = new Article();
article2.title = "TypeORM 高级技巧";

category.articles = [article1, article2];

// ✅ 只保存分类，2篇文章也会自动保存
await categoryRepository.save(category);
// 1. 先保存 category（生成 id）
// 2. 自动为每个 article 设置 category.id
// 3. 自动保存所有 article
// 结果：1 条 SQL INSERT 分类 + 2 条 SQL INSERT 文章


-----------------------------------------------------------


// 若没有设置 cascade: ['insert']，则需要手动保存每个实体，需要 3 次 save 操作
const category = new Category();
category.name = '技术文章';
await categoryRepository.save(category);  // 先保存分类

const article1 = new Article();
article1.title = 'NestJS 入门教程';
article1.category = category;
await articleRepository.save(article1);  // 再保存文章

const article2 = new Article();
article2.title = 'TypeORM 高级技巧';
article2.category = category;
await articleRepository.save(article2);  // 再保存另一篇文章
```

- cascade: [\'update\']
  在父类中，对关联的子类对象进行更新时，也会更新父类对象。

```ts
// 查询已存在的分类和文章
const category = await categoryRepository.findOne({
  where: { id: 1 },
  relations: ["articles"],
});

// 修改分类名称
category.name = "前端技术";

// 修改文章标题
category.articles[0].title = "React 18 新特性";
category.articles[1].title = "Vue 3 组合式 API";

// ✅ 一次 save 自动更新所有
await categoryRepository.save(category);
// 自动执行：
// UPDATE categories SET name = '前端技术' WHERE id = 1
// UPDATE articles SET title = 'React 18 新特性' WHERE id = ?
// UPDATE articles SET title = 'Vue 3 组合式 API' WHERE id = ?


------------------------------------------------------


// 若没有设置 cascade:['update']
const foundCategory = await categoryRepository.findOne({
  where: { id: category.id },
  relations: ['articles']
});

// 修改分类名称和文章标题
foundCategory.name = '前端技术';
foundCategory.articles[0].title = 'React 18 新特性';  // 修改标题
foundCategory.articles[1].title = 'Vue 3 组合式 API'; // 修改标题

// 保存父实体
await categoryRepository.save(foundCategory);

// ✅ category.name 更新成功（'前端技术'）
// ❌ article.title 没有更新（仍然是 'NestJS 教程' 和 'TypeORM 指南'）
```

- cascade: [\'remove\']

注意：
情况0：有 cascade + 有 relations，子类有 onDelete: 'RESTRICT'，（父子记录都不会删除）；
情况1：有 cascade + 有 relations，（父+子都删除）；
情况2：有 cascade + 无 relations，只删除父记录，子记录不会删除（可能因外键约束失败）；
情况3：无 cascade + 有/无 relations，只删除父记录，子记录保留；

```ts
// 查询分类及其文章
const category = await categoryRepository.findOne({
  where: { id: 1 },
  relations: ["articles"],
});

// ✅ 删除分类时，自动删除所有关联文章
await categoryRepository.remove(category);
// 自动执行：
// DELETE FROM articles WHERE categoryId = 1
// DELETE FROM categories WHERE id = 1


-------------------------------------------------------


// 另外注意一个点 relations： 如果只删除分类（不加载 articles）
const category = await categoryRepository.findOne({
  where: { id: 1 }
  // 注意：没有加载 relations: ['articles']
});

// ❌ 不会级联删除文章！
await categoryRepository.remove(category);
// 只执行：DELETE FROM categories WHERE id = 1
// 但会因为外键约束失败（如果有文章关联）
```

##### onDelete

- onDelete: 'CASCADE'
  categoryRepository.delete(1)；删除分类1，所有 categoryId = 1 的文章被自动删除；
- onDelete: 'SET NULL'
  categoryRepository.delete(1); 删除分类1，所有文章的 categoryId 变为 NULL，文章本身仍然存在；
- onDelete: 'RESTRICT'
  categoryRepository.delete(1); 删除分类1，执行删除时会报错，因为该分类有关联的文章，无法删除；
  正确的做法是先删除或转移文章，再删除分类；

```ts onDelete: 'RESTRICT'
// 尝试删除有关联文章的分类
try {
  await categoryRepository.delete(1);
} catch (error) {
  // ❌ 抛出错误：有文章引用此分类，无法删除
  console.error("无法删除，该分类下还有文章");
}

// 正确的做法：先删除或转移文章
await articleRepository.delete({ categoryId: 1 });
await categoryRepository.delete(1); // ✅ 现在可以删除
```

##### eager

双边 eager 可能导致循环嵌套的关系；

```ts
// ❌ 不适合：用户有大量文章，在service查询时手动加载关联查询
@Entity("users")
export class User {
  @OneToMany(() => Article, (article) => article.author, {
    eager: true, // 如果用户有1000篇文章，每次查询用户都会加载1000条
  })
  articles: Article[];
}

---------------------------------------------------------

// ✅ 适合：用户和用户配置（一对一，数据量小）
@Entity('users')
export class User {
  @OneToOne(() => UserProfile, profile => profile.user, {
    eager: true  // 总是需要显示用户配置
  })
  profile: UserProfile;
}

// ❌ 不适合：用户的高级配置（很少用到）
@Entity('users')
export class User {
  @OneToOne(() => AdvancedSettings, settings => settings.user, {
    eager: true  // 大部分情况下不需要，浪费查询
  })
  advancedSettings: AdvancedSettings;
}

---------------------------------------------------------

// ✅ 适合：单向 eager，避免循环
@Entity("users")
export class User {
  @OneToMany(() => Post, (post) => post.author, {
    eager: true, // 用户总是需要显示其文章列表
  })
  posts: Post[];
}

// ❌ 循环加载，可能导致性能问题或堆栈溢出
@Entity('posts')
export class Post {
  @ManyToOne(() => User, user => user.posts, {
    eager: true
  })
  author: User;
}

---------------------------------------------------------

// ✅ 适合：文章和分类（多对一，分类数据少且总是需要）
@Entity('articles')
export class Article {
  @ManyToOne(() => Category, category => category.articles, {
    eager: true  // 文章列表总是需要显示分类名
  })
  category: Category;
}

---------------------------------------------------------

// ✅ 适合：一个订单总是需要显示商品信息
@Entity("orders")
export class Order {
  @ManyToOne(() => Product, (product) => product.orders, {
    eager: true, // 99% 的查询都需要商品信息
  })
  product: Product;
}
```

##### JoinTable

只需要在关系的一侧设置，通常设置在需要被查询更多的那一侧;

```ts
// 哪个实体是关系的"主体"？
// 例如：文章和标签

// ✅ 推荐：文章拥有标签
@Entity('articles')
export class Article {
  @ManyToMany(() => Tag, tag => tag.articles)
  @JoinTable()  // 文章是关系的主体
  tags: Tag[];
}

@Entity('tags')
export class Tag {
  @ManyToMany(() => Article, article => article.tags)
  articles: Article[];
}
```

#### service

原生查询、联合查询、聚合查询

![](/images/nest/join.png)

```ts user.service.ts
import { Injectable } from "@nestjs/common";
import { CreateUserDto, transferMoneyDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";
import { User } from "./entities/user.entity";
import { Phone } from "./entities/phone.entity";
import { Role } from "../role/entities/role.entity";
import { Repository, Like, In } from "typeorm";
import { InjectRepository } from "@nestjs/typeorm";
import type { UserType } from "../types/user";
import { conditionUtils } from "../utils/db.helper";

@Injectable()
export class UserService {
  constructor(
    @InjectRepository(User) private readonly user: Repository<User>,
    @InjectRepository(Phone) private readonly phone: Repository<Phone>,
    @InjectRepository(Role) private readonly role: Repository<Role>,
  ) {}

  // 创建用户
  async create(createUserDto: CreateUserDto) {
    // const data = new User();
    // data.username = createUserDto.username;
    // data.age = createUserDto.age;
    // data.gender = createUserDto.gender;
    // return this.user.save(data);

    let roleData: Role[] = [];
    if (createUserDto.roleIds && createUserDto.roleIds.length > 0) {
      // 根据id查询出所有角色数据;
      roleData = await this.role.find({
        where: { id: In(createUserDto.roleIds) },
      });
    }

    const data = this.user.create({
      ...createUserDto,
      profile: createUserDto, // cascade: true 级联操作会自动将关联的profile数据插入到 profile 表中；
      roles: roleData, // cascade: ['insert'] 级联操作会自动将关联的角色数据插入到 user_role 中间表；
    });
    return this.user.save(data);
  }

  // 聚合查询
  findPhonesByGroup(id: number) {
    // 原生sql查询方式
    // return this.user.query(
    //   `SELECT phone.price as price, COUNT(phone.price) as count from phone, user WHERE user.id = phone.userId AND user.id = ${id} GROUP BY phone.price`,
    // );

    return this.phone
      .createQueryBuilder("phone")
      .select("phone.price", "price") // as price
      .addSelect("COUNT(phone.price)", "count") // COUNT(phone.price) as count
      .leftJoinAndSelect("phone.user", "user")
      .where("phone.userId = :id", { id })
      .groupBy("price")
      .orderBy("price", "DESC")
      .addOrderBy("count", "DESC")
      .getMany();
  }

  // 模糊查询所有用户
  async fuzzyFindAll(query: UserType) {
    let { page, size, keyword } = query;
    const take = size || 10;
    const skip = ((page || 1) - 1) * take;

    const data = await this.user.find({
      where: [{ username: Like(`%${keyword}%`) }],
      relations: ["phones"], // 关联查询
      order: { id: "DESC" },
      skip,
      take,
    });

    const total = await this.user.count({
      where: [{ username: Like(`%${keyword}%`) }],
    });

    return { data, total };
  }

  async findAll2(query: UserType) {
    let { page, size, username, gender, roleId } = query;
    const take = size || 10;
    const skip = ((page || 1) - 1) * take;

    return this.user.find({
      skip,
      take,

      select: {
        // user表返回哪些字段
        // 默认返回 (entity中非select:false) 所有字段；
        // 若设置了字段则只返回设置的字段 (比entity中select:true权重大)；
        id: true,
        username: true,

        // profile返回salary字段
        profile: {
          salary: true,
        },
      },

      // 联合查询哪些表；
      relations: {
        profile: true,
        phones: true,
        roles: true,
      },

      where: {
        // 默认就是查的user表，匹配所有名称是username数据
        username: username,

        // 查询profile表，匹配gender的数据
        profile: {
          gender: gender,
        },
        // 查询roles表，匹配id的数据
        roles: {
          id: roleId,
        },
      },
    });
  }

  async findAll3(query: UserType) {
    let { page, size, username, gender, roleId } = query;
    const take = size || 10;
    const skip = ((page || 1) - 1) * take;

    const builder = this.user
      // 创建聚合查询
      .createQueryBuilder("user")
      // 左连接并设置别名
      .leftJoinAndSelect("user.profile", "profile")
      .leftJoinAndSelect("user.roles", "roles");

    const obj = {
      "user.username": username,
      "profile.gender": gender,
      "roles.id": roleId,
    };
    const newBuilder = conditionUtils<User>(builder, obj);
    return newBuilder.take(take).skip(skip).getMany();
  }

  // 通过用户名查单个用户
  async findOneByUsername(username: string) {
    return this.user.findOne({
      where: { username: username },
    });
  }

  // 查单个用户
  findOne(id: number) {
    return this.user.findOne({ where: { id }, relations: ["roles"] });
  }

  // 查询用户详情
  async findProfile(id: number) {
    const data = await this.user.findOne({
      where: { id },
      // 关联查询出profile、phones详细数据
      relations: {
        profile: true,
        phones: true,
      },
      select: {
        profile: { id: false, avatar: false, email: false, salary: true },
      },
    });

    return data;
  }

  // 修改用户
  async update(id: number, updateUserDto: UpdateUserDto) {
    // 只适合但模型的更新，不适合有关联关系的模型更新；
    // return this.user.update(id, updateUserDto);

    const data = await this.findProfile(id);
    if (!data) return { message: "用户不存在" };
    // 合并新旧数据，生成一个新的对象；
    const newData = this.user.merge(data, updateUserDto);
    return this.user.save(newData);
  }

  // 删除用户
  remove(id: number) {
    return this.user.delete(id);
  }

  // 新增一对多关系的数据；
  async buyPhones(params: {
    userId: number;
    phones: { brand: string; model: string; price: number }[];
  }) {
    const { userId, phones } = params;

    // 读取用户信息
    const userInfo = await this.user.findOne({ where: { id: userId } });

    if (userInfo === null) {
      return { message: "用户不存在" };
    }

    const phoneList: Phone[] = [];

    // 先循环添加到phone表中，再插入到user_phone中
    for (let i = 0; i < phones.length; i++) {
      // new一个对象并添加信息；
      const P = new Phone();
      P.brand = phones[i].brand;
      P.model = phones[i].model;
      P.price = phones[i].price;

      // 调用类的save方法保存
      await this.phone.save(P);
      phoneList.push(P);
    }

    // 赋值给 user 实体的 phones 属性
    userInfo.phones = phoneList;
    // 自动根据id更新用户数据
    await this.user.save(userInfo);

    return {
      message: "购买成功",
    };
  }

  // 事务
  transferMoney(transferMoneyDto: transferMoneyDto) {
    try {
      return this.user.manager.transaction(async (manager) => {
        let fromUser = await this.user.findOne({
          where: { id: transferMoneyDto.fromUserId },
        });

        let toUser = await this.user.findOne({
          where: { id: transferMoneyDto.toUserId },
        });

        if (fromUser && toUser && fromUser.salary >= transferMoneyDto.amount) {
          manager.save(User, {
            id: fromUser.id,
            salary: fromUser.salary - transferMoneyDto.amount,
          });
          manager.save(User, {
            id: toUser.id,
            salary: Number(toUser.salary) + transferMoneyDto.amount,
          });

          return { message: "转账成功" };
        } else {
          return { message: "转账失败" };
        }
      });
    } catch (e) {
      throw new Error(e);
    }
  }
}
```

```ts db.helper.ts
import { SelectQueryBuilder, ObjectLiteral } from "typeorm";

export const conditionUtils = <T extends ObjectLiteral>(
  builder: SelectQueryBuilder<T>,
  obj: Record<string, unknown>,
) => {
  Object.keys(obj).forEach((key) => {
    if (obj[key]) {
      builder.andWhere(`${key} = :${key}`, { [key]: obj[key] });
    } else {
      builder.andWhere(`${key} IS NOT NULL`);
    }
  });
  return builder;
};
```

#### controller

```ts user.controller.ts
import {
  Controller,
  Get,
  Post,
  Body,
  Patch,
  Param,
  Delete,
  Query,
  Logger,
} from "@nestjs/common";
import { UserService } from "./user.service";
import { CreateUserDto, transferMoneyDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";
import type { UserType } from "../types/user";

@Controller("user")
export class UserController {
  // 初始化一个logger
  private logger = new Logger(UserController.name);

  constructor(private readonly userService: UserService) {
    this.logger.warn("nestjs-pino init");
  }

  // 访问 /user/phonesByGroup
  @Get("/phonesByGroup/:id")
  async findPhones(@Param("id") id: string) {
    return this.userService.findPhonesByGroup(+id);
  }

  // 访问 /user/transferMoney
  @Post("/transferMoney")
  transferMoney(@Body() transferMoneyDto: transferMoneyDto) {
    return this.userService.transferMoney(transferMoneyDto);
  }

  // 访问 /user/buyPhones
  @Post("/buyPhones")
  buyPhones(
    @Body()
    params: {
      userId: number;
      phones: { brand: string; model: string; price: number }[];
    },
  ) {
    return this.userService.buyPhones(params);
  }

  @Post()
  create(@Body() createUserDto: CreateUserDto) {
    return this.userService.create(createUserDto);
  }

  @Get()
  findAll(@Query() query: UserType) {
    return this.userService.fuzzyFindAll(query);
  }

  @Get("/allInfo")
  findAll2(@Query() query: UserType) {
    return this.userService.findAll2(query);
  }

  @Get(":id")
  findOne(@Param("id") id: string) {
    this.logger.log("User Controller findOne");
    return this.userService.findOne(+id);
  }

  // 访问 /user/profile
  @Get("/profile/:id")
  findDetail(@Param("id") id: string) {
    return this.userService.findProfile(+id);
  }

  @Patch(":id")
  update(@Param("id") id: string, @Body() updateUserDto: UpdateUserDto) {
    return this.userService.update(+id, updateUserDto);
  }

  @Delete(":id")
  remove(@Param("id") id: string) {
    return this.userService.remove(+id);
  }
}
```

```ts user.module.ts
import { Module } from "@nestjs/common";
import { UserService } from "./user.service";
import { UserController } from "./user.controller";
import { TypeOrmModule } from "@nestjs/typeorm";
import { User } from "./entities/user.entity";
import { Phone } from "./entities/phone.entity";
import { Profile } from "./entities/profile.entity";
import { Role } from "src/role/entities/role.entity";

@Module({
  // 关联entity
  imports: [TypeOrmModule.forFeature([User, Phone, Profile, Role])],
  controllers: [UserController],
  providers: [UserService],
  exports: [UserService], // 导出服务，供其他模块使用
})
export class UserModule {}
```

#### dto

- 输入参数过滤：pipe.whitelist 打开后，只会接收 DTO 中定义的字段 且 有校验装饰器修饰的字段，然后将校验通过的字段传递到 controller 中；(如 createUserDto 定义并校验 username 和 password，updateUserDto 仅定义并校验并只接收username);

- 输出参数过滤：Entity中，使用@Exclude/@Expose() 修饰的字段 & Interceptor 拦截器搭配使用，过滤掉响应的字段;

```ts create-user.dto.ts
import {
  IsArray,
  IsNotEmpty,
  IsNumber,
  IsString,
  Length,
  IsOptional,
  IsInt,
} from "class-validator";

export class CreateUserDto {
  @IsString()
  @IsNotEmpty({
    message: "用户名不能为空",
  })
  @Length(2, 20, {
    message: "用户名长度在2 - 20之间",
  })
  username: string;

  @IsString()
  @IsNotEmpty()
  @Length(6, 6, {
    message: "密码长度必须为6",
  })
  password: string;

  @IsNumber()
  age: number;

  @IsString()
  @IsOptional()
  gender: string;

  @IsInt({ each: true })
  @IsArray({ message: "roleIds 必须是数组" })
  roleIds?: number[];
}

export class transferMoneyDto {
  @IsNumber()
  @IsNotEmpty({
    message: "金额不能为空",
  })
  amount: number;

  @IsNumber()
  @IsNotEmpty({
    message: "发起用户id不能为空",
  })
  fromUserId: number;

  @IsNumber()
  @IsNotEmpty({
    message: "接收用户id不能为空",
  })
  toUserId: number;
}
```

#### Typeorm Cli

> 定义数据库结构的变化（数据升级、系统版本升级、新增/删除表、添加或修改列字段、修改列字段类型）
> 维护一个有序的数据库版本，方便追踪数据库版本，并回滚；
> 人为的手动导出再导入数据库，或对数据库进行一些操作可能会遗漏、报错等，在生产环境中使用 Cli 工具大大的提升效率和保证安全；
> synchronize 正式环境不允许使用 synchronize，此时需要 migration 每个版本精细化操作 (开发环境可以开启，就能实时将对typeorm修改更新到数据库)；

- 在script 添加命令 或使用 npx typeorm 命令

```json package.json中添加命令
"script" {
    "typeorm": "typeorm-ts-node-commonjs -d ormconfig.ts",

    // 生成新的 migration 版本文件
    "migration:create": "typeorm-ts-node-commonjs migration:create",

    // 运行 migration 文件下的所有文件；
    "migration:run": "npm run typeorm migration:run",

    // 回滚版本；
    "migration:revert": "npm run typeorm migration:revert",

    // 生成差异版本 (新增/修改表结构 生成新的差异版本)；
    "migration:generate": "npm run typeorm -- migration:generate src/migrations/%npm_config_name%",
}
```

- `npm run migration:create src/migrations/init`
  生成初始的迁移目录及文件 migrations/1774854099173-init.ts

- `npm run migration:generate --name=addLevelColumn`
  生成迁移文件 newCategory，里面有生成好的 up 与 down 语句的方法；

- `npm run migration:run`
  运行一下，执行migration下所有迁移文件，将数据库结构更新到最新版本；

- `npm run migration:revert`
  回滚迁移文件，将数据库结构回滚到上一个版本；

```ts 1776844760881-init.ts
import { MigrationInterface, QueryRunner } from "typeorm";

export class Init1776839102541 implements MigrationInterface {
  // 执行迁移的代码
  public async up(queryRunner: QueryRunner): Promise<void> {}

  // 执行回滚的代码
  public async down(queryRunner: QueryRunner): Promise<void> {}
}
```

```ts 1776845959238-addLevelColumn.ts
import { MigrationInterface, QueryRunner } from "typeorm";

export class NewCategory1776845959238 implements MigrationInterface {
  name = "NewCategory1776845959238";

  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(
      `ALTER TABLE \`category\` ADD \`level\` int NOT NULL`,
    );
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`ALTER TABLE \`category\` DROP COLUMN \`level\``);
  }
}
```

## Prisma

Prisma 是一个 Node.js / TypeScript 的 ORM（对象关系映射）工具，用于简化数据库操作

### command

| 命令               | 用途                                                                                                |
| ------------------ | --------------------------------------------------------------------------------------------------- |
| prisma init        | 初始化项目 - 创建一个新的本地 Prisma Postgres 开发项目，生成 schema.prisma 等基础文件               |
| prisma dev         | 启动开发服务器 - 启动一个本地的 Prisma Postgres 服务器用于开发                                      |
| prisma generate    | 生成 Prisma Client - 根据 schema 生成类型安全的数据库客户端代码，每次修改 schema 后都需要运行       |
| prisma studio      | 可视化数据库 - 打开一个 Web 界面，让你可以浏览和编辑数据库中的数据                                  |
| prisma migrate dev | 开发环境迁移 - 根据 schema 变更创建迁移文件，应用到数据库，并重新生成 Prisma Client（开发时最常用） |
| prisma db pull     | 拉取数据库结构 - 从现有数据库反向生成/更新 schema.prisma（适用于已有数据库的项目）                  |
| prisma db push     | 推送 schema 到数据库 - 直接将 schema 变更同步到数据库，不创建迁移文件（适合原型开发阶段）           |
| prisma validate    | 验证 schema - 检查 schema.prisma 文件的语法和配置是否正确                                           |
| prisma format      | 格式化 schema - 自动格式化 schema.prisma 文件，使其更易读                                           |
| prisma version     | 显示版本 - 查看当前安装的 Prisma 版本信息                                                           |
| prisma debug       | 调试信息 - 显示 Prisma 的调试信息，排查问题时有用                                                   |

### init

1. 在 nest 项目中安装 prisma 依赖包；
   `npm i @prisma/client prisma --save`
   `npm i dotenv --save-dev`

2. 执行命令，初始化项目 (只需初始化一次)，生成 .env文件(有连接数据库信息)、prisma.config.ts 文件、Prisma/schema.prisma 文件；
   `npx prisma init`

3. 定义表、字段

```ts schema.prisma
generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql" // 数据库类型
}

// 定义User表
model User {
  // 字段   类型      @id设置为主键 @default(cuid())设置cuid类型的默认id；
  id        String    @id @default(cuid())
  email     String    @unique
  password  String
  createdAt DateTime  @default(now()) // 创建时自动生成时间戳
  updatedAt DateTime  @updatedAt // 修改时自动更新时间戳
  articles  Article[] // 一对多关系

  /**
   * @@map 表的别名
   * @@index([field1, field2]) 增加索引，提升查询速度
   * @unique 唯一值
   * @igmore 忽略字段
   * @map 字段别名
   * @relation 关联表
   */
}

// 定义文章表
model Article {
  id        String   @id @default(cuid())
  title     String
  content   String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // 定义外键字段userId 关联用户表的id
  userId String
  user   User   @relation(fields: [userId], references: [id], onDelete: Cascade, onUpdate: Cascade)
  /**
   * @relation 关联 User 表
   * 外键字段 fields 设为当前定义的 userId
   * 与 Article 表的id关键
   * onDelete: Cascade 级联删除
   * onUpdate Cascade 级联更新 (一般是主表id值改了后与关联表id进行升级)
   */
}
```

4. 执行命令，根据 shema 生成数据库表 (每次修改 shema 后都需要执行此命令)
   `npx prisma migrate dev --name init`
   生成目录及文件 Prsma > migrations目录>2021-05-05-09-05-05_init.sql,
   `npx prisma generate`
   根据 sql文件 生成数据库 User、Article 表

## Others

### JWT

jwtService.sign 生成 token & 自定义 AuthGuard validate 验证 token
`npm install passport passport-jwt @nestjs/jwt @nestjs/passport --S`
`npm install @types/passport-jwt -D`

### Encryption

argon2.hash 加密 与 argon2.verify 验证密码
``npm install argon2`
