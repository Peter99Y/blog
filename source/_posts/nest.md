---
title: NestJS
---

Nest.js 是一个基于 Node.js 的框架，并且完全支持 TypeScript, Nest.js 底层代码运行了 express 和 fastify；

## 编程思想

[面向过程&面向对象](/blog/2026/01/12/class)

AOP 面向切面编程，AOP 允许用户定义在代码执行过程中，在特定的代码段前后进行拦截，对代码进行扩展。

## 设计模式

IOC (Inversion of Control) 控制反转，高层模块不应该依赖低层模块，两者都应该依赖抽象。

DI (Dependency Injection) 依赖注入，通过构造函数或者属性注入依赖。

```typescript
// 当A类修改为需要传入参数时，B类和C类都需要修改，以及都会依赖A类；

class A {
  name: string;
  //   constructor() {
  constructor(name: string) {
    this.name = name;
  }
}

class B {
  a: any;
  constructor() {
    this.a = new A().name;
  }
}

class C {
  a: any;
  constructor() {
    this.a = new A().name;
  }
}
```

```typescript
class A {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class C {
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
  // 注入
  provide(key: string, md: any) {
    this.module[key] = md;
  }
  get(key: string) {
    return this.module[key];
  }
}

const module = new Container();
module.provide("a", new A("aaa"));
module.provide("c", new C("ccc"));

// 引入IOC container 后，B与A的代码逻辑解耦，降低组件之间的耦合度；
class B {
  a: any;
  c: any;
  constructor(module: Container) {
    this.a = module.get("a");
    this.c = module.get("c");
  }
}
```

## 初始化

- 创建项目脚手架： npm i @nestjs/cli -g
- 创建项目：nest new demo

## 常用命令

nest g controller [name] 创建控制器
nest g service [name] 创建服务
nest g module [name] 创建模块
nest g resource [name] 创建包含以上三个资源统一命令
nest g middleware [name] 创建中间件

## CRUD & RESTful

CRUD 是软件开发和数据库管理中最基础、最核心的四个操作。它是 Create、Read、Update 和 Delete 的首字母缩写.
RESTful API 是一种架构风格，它利用 HTTP 协议的特性（如方法 method、状态码 status、URI）来描述资源及其操作，也就是只需一个接口，就可以完成对资源的增删改查操作。

## 项目结构

- app.service.ts (服务/提供者)

定义类，处理业务逻辑与数据层的交互（如数据库等），和其他额外的一些三方请求；

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

- app.module.ts (模块)

项目的根模块；用来处理其他类的引用与共享；

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

- app.controller.ts (控制器)

处理 http 请求并返回响应；
路由分发：使用 @Get()、@Post() 等装饰器定义资源的 URI 路径；
参数解析：负责从请求中提取 @Body()、@Query() 或 @Param() 等参数；

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

## controller 控制器

```typescript app.controller.ts
import { Controller, Get } from "@nestjs/common";

// 访问路径 localhost:3000
@Controller()
export class AppController {
  @Get()
  getHello(): any {
    return "Hello World!";

    // return {
    //   message: 'success',
    //   status: 200,
    //   data: 'Hello World!',
    // };
  }
}
```

```typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): any {
    return this.appService.getHello();
  }
}

---

import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
```

## service 服务

### 自定义名称

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

--------------------------------------

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

### 自定义值

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
    {
      provide: 'CCC',
      useValue: [1, 2, 3], // 可定义字符串、数组、对象的值
    },
  ],
})
export class UserModule {}

----------------------------------

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

### 工厂模式

### 异步模式

## module 模块

### 共享模块

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

### 全局模块

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

### 动态模块

给某个模块传参

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

## middleware

是在路由处理函数执行之前执行的函数，可对请求对象和响应对象进行修改、调用下一个中间件函数、next 函数若没有调用，则请求将不会继续往下执行。

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

### 全局中间件

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

rxjs 模块提供了 Observable 类，用于处理异步数据流；
Observable 可观察的物件；
Subscriber 监听 Observable；
Operators 操作符, 可以处理管道的数据 map filter concat reduce 等；

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

---

const subs = interval(1000)
  .pipe(map((v) => ({ num: v })))
  .subscribe((e) => {
    console.log(e); // {num: 0}... {num: 4}
    if (e.num === 4) {
      subs.unsubscribe();
    }
  });

---

const subs = interval(1000)
  .pipe(
    map((v) => ({ num: v })),
    filter((e) => e.num % 2 === 0),
  )
  .subscribe((e) => {
    console.log(e); // {num: 0} {num: 2} {num: 4}

    if (e.num === 4) {
      subs.unsubscribe();
    }
  });
```

## intercepter

- 在函数前后执行额外的逻辑
- 转换从函数返回的结果
- 转换从函数抛出的错误
- 扩展函数行为
- 根据所选条件完全重写函数

```typescript src/common/response.ts
// 响应拦截器

import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from "@nestjs/common";
import { map } from "rxjs/operators";
import { Observable } from "rxjs";

// 使用响应拦截器的 NestInterceptor 类型来约束 Response 类
// NestInterceptor 是 interface，它要求需要实现 intercept 方法，这方法接收两个参数；
@Injectable()
export class Response<T> implements NestInterceptor<T, Response<T>> {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      map((data) => ({
        code: 200,
        message: "success",
        data,
      })),
    );
  }
}
```

```typescript src/common/filter.ts
// 异常拦截器

import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
} from "@nestjs/common";
import { Request, Response } from "express";

@Catch()
export class HttpFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response.status(status).json({
      status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

```typescript main.ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import { NestExpressApplication } from "@nestjs/platform-express";
import { join } from "path";
import { Response } from "./common/reponse";
import { HttpFilter } from "./common/filter";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useStaticAssets(join(__dirname, "public"));

  // 注册拦截器；
  app.useGlobalInterceptors(new Response());
  app.useGlobalFilters(new HttpFilter());

  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

## pipe

- 将前端传递的参数进行**类型转换**

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

  // localhost:3000/user/1  如参数非UUID则自动抛出400错误
  @Get(":id")
  // findOne(@Param('id') id: string) {

  // findOne(@Param('id', ParseIntPipe) id: number) {
  findOne(@Param("id", ParseUUIDPipe) id: number) {
    return this.userService.findOne(+id);
  }
}
```

- 校验参数
  需安装第三方校验库：npm i class-validator class-transformer -S

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

  passage: string;
}
```

```typescript main.ts
import { NestExpressApplication } from "@nestjs/platform-express";
import { ValidationPipe } from "@nestjs/common";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

## guard

根据条件（例如权限、角色、访问控制列表、token 等）来确定是否允许请求继续执行。
守卫在每个中间件之后执行，在任何拦截器或管道之前执行。

先创建 nest g res [guard]，再切换到创建的目录中，创建 nest g gu [role];

```typescript role.guard.ts
import { CanActivate, ExecutionContext, Injectable } from "@nestjs/common";
import { Observable } from "rxjs";

@Injectable()
export class RoleGuard implements CanActivate {
  canActivate(
    context: ExecutionContext,
  ): boolean | Promise<boolean> | Observable<boolean> {
    console.log("经过了守卫");
    return true;
  }
}
```

```typescript guard.controller.ts
import { Controller, Get, Post, Body, Param, UseGuards } from "@nestjs/common";
import { GuardService } from "./guard.service";
import { CreateGuardDto } from "./dto/create-guard.dto";
import { RoleGuard } from "../guard/role/role.guard";

// localhost:3000/guard 下任意路由都会触发 RoleGuard 中的canActivate方法
@Controller("guard")
@UseGuards(RoleGuard) // 添加装饰器
export class GuardController {
  constructor(private readonly guardService: GuardService) {}

  @Post()
  create(@Body() createGuardDto: CreateGuardDto) {
    return this.guardService.create(createGuardDto);
  }

  @Get()
  findAll() {
    return this.guardService.findAll();
  }
}
```

- 全局守卫

通过所有路由请求触发守卫；

```typescript main.ts
import { NestExpressApplication } from "@nestjs/platform-express";
import { RoleGuard } from "./guard/role/role.guard";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useGlobalGuards(new RoleGuard());
  await app.listen(process.env.PORT ?? 3000);
}

bootstrap();
```

- 自定义守卫

```typescript guard.controller.ts
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

## database

typeorm 是 Typescript 中最成熟的对象关系映射器（ORM: Object Relational Mapping 数据库的对象映射） 进行数据库操作；
实体就是映射到数据库的一个类（当创建实体时，会自动创建数据库这张表）；

`npm install @nestjs/typeorm typeorm mysql2 -S`

```typescript app.module.ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { UserModule } from "./user/user.module";
import { TypeOrmModule } from "@nestjs/typeorm";

@Module({
  imports: [
    UserModule,
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

      // 自动匹配每个目录下的 entity, 如 user.entity.ts 加载实体文件 (不推荐)
      // entities: [__dirname + '/**/*.entity{.ts,.js}'],

      // 自动加载实体 forFeature() 注册的每个实体都将自动添加到配置对象的实体数组中（推荐）
      autoLoadEntities: true,
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

```typescript user.module.ts
import { Module } from "@nestjs/common";
import { UserService } from "./user.service";
import { UserController } from "./user.controller";
import { TypeOrmModule } from "@nestjs/typeorm";
import { User } from "./entities/user.entity";

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UserController],
  providers: [UserService],
  exports: [UserService],
})
export class UserModule {}
```

```typescript user.entity.ts
import {
  Entity,
  Column,
  Generated,
  CreateDateColumn,
  PrimaryGeneratedColumn,
} from "typeorm";

@Entity()

// user表示数据库中的表名
export class User {
  // 里面的属性对应数据库中的字段

  // 自动递增的主键
  //   @PrimaryGeneratedColumn()

  @PrimaryGeneratedColumn("uuid")
  id: number;

  // type与数据库类型一致；
  @Column({ type: "varchar", length: 255 })
  name: string;

  @Column({
    // primary: true, // 是否是主键，与 PrimaryColumn() 功能一致, 互斥关系，一个表只能存在一个主键
    name: "age", // 表列名
    nullable: true, // 允许字段是否为空, 默认false
    comment: "年龄",
    select: true, // 查询时是否返回该字段
    update: true, // save操作时是否更新该字段值
    unique: false, // 是否唯一
    collation: "", // 定义列排序规则
  })
  age: number;

  // 自动生成时间
  @CreateDateColumn("timestamp")
  createdTime: Date;

  // 自动生成uuid
  @Generated("uuid")
  uuid: string;

  @Column({ type: "enum", enum: ["male", "female"], default: "male" })
  gender: string;
}
```
