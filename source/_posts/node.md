---
title: nodeJs
---

## init

`npm init`: 初始化新的 npm 项目，创建 package.json 文件；
`npm install`: 安装一个包或一组包，并且会在当前目录存放一个 node_modules 目录，里面存放安装的包；
`npm installl <packageName>@<version>`: 安装指定版本包；
`npm installl <packageName> --save`: 安装指定包，并将其添加到 package.json 文件中 dependencies 中 (npm 5.4 之后可不加--save)；
`npm install <packageName> --save-dev or -D`: 安装指定包，并将其添加到 package.json 文件中 devDependencies 中；

`npm config list`: 查看 node 与 npm 的配置信息

### package.json

```
{
    "name": 'demo',   // 包名称
    "version": "1.0.0", // 包版本
    "description": "demo", // 包描述

    "main": "index.js", // 入口文件
    "type":  "module",
    "module": ""
    "brower": "",   // 以上4个都与模块化相关

    "scripts": {    // 脚本，通过npm run 名称 来执行命令

    },

    "repository":  { // 仓库地址
        "type": "git",
        "url": "",
    },
    "homepage": "", // 项目主页

    "devDependencies": {    // 开发依赖包，只是本地开发运行时或打包时，所依赖的包
        "webpack", "webpack-cli", "vite"
    },
    "dependencies": { // 生产环境依赖包
        "md5", "vue", "vue-router"
    },
    "peerDependencies": { // 对等依赖，1.给编写插件人员或npm包开发人员使用，如安装了一个插件"vue-plugin-test",而这个依赖是基于vue宿主环境，因此vue需要安装在这里。2.防止重复安装依赖
        "vue"
    },
"
}
```

### npm install 执行时发生了什么

- 安装依赖存放到 node_modules 目录下, 采用不**扁平化**的方式安装；
- 扁平化采用**广度优先算法**，遍历依赖树，npm 首先处理**项目级根目录下的依赖**，然后逐层处理**每个依赖包的依赖**；
- 处理每个依赖时，npm 会检查该依赖的版本号是否符合依赖树中其他依赖的版本要求，符合则安装，否则尝试安装适合的版本；

  - 如 A 和 B 依赖包，它们各自依赖都有 C 依赖包**相同版本** ，则 C 依赖包会被安装到 A、B 同级依赖目录中，而不是安装到 A、B 的根目录下， A、B 都可复用 C；
  - 如 A 和 B 依赖包，它们各自依赖的 依赖包 C **不同版本**，则 C 会被安装到 A、B 各自的 node_modules 下；

- node_modules 文件排序规则: .bin 文件、接着@符号开头文件、最后剩余文件按照首字母排序；

### npm install 命令执行流程

1. 先检查 npm config 配置文件，优先从项目中根目录下读取.npmrc 文件. 若没有，则依次检查用户目录下.npmrc 文件、 全局目录下.npmrc 文件、 npm 安装目录下.npmrc 文件；
2. 再检查 项目根目录下是否有 package-lock.json 文件,
   - a.若有 lock 文件，会比较 packge.json 和 package-lock.json 文件，然后根据 package-lock.json 文件安装依赖包；
     - 不一致：会根据 package.json 的版本号去下载包，并且更新 lock 文件；
     - 一致：检查是否有缓存，有缓存则使用缓存；没有缓存则下载包，并缓存，并更新 lock 文件；
   - b.若没有 lock 文件，npm 会构建依赖树，再检查是否有缓存...；

**.npmrc** 文件可配置源，代理，配置 http 的代理，配置 https 的证书；

### npm run 命令 执行时发生了什么

- package.json 中，scripts 中所有的可执行命令都来自于 node_modules/.bin 目录；

- node_modules/.bin 中，如安装了 sass 包，这里会有三个文件， sass (给 Unix,Linux,MacOs 平台使用)，sass.cmd (windows 的 cmd 使用)，sass.ps1(windows 的 powershell 使用)；

- node_modules/.bin 中的命令，又是从依赖包自己的 package.json 中的 "bin" 字段配置的，当安装包时 node 会自动往 node_modules/.bin 目录下创建对应的命令文件；

<div style="display:flex; justify-content: center; gap: 20px;">

![](/images/node/bin.png)

![](/images/node/bin2.png)

</div>

### npm run 命令运行流程

- 运行命令，会在当前项目寻找 node_modules/.bin 目录下的命令文件；
- 如果找不到，会往全局的 **npm_modules/.bin** 目录下寻找 (环境变量中 path 配置了 npm_modules 文件路径，所以全局能够执行命令)；
- 最后去环境变量寻找，还没有就会报错；

## npx

- npx 命令是一个命令行工具，npm5.2.0 版本后新增功能；
- 避免全局污染：用户它允许用户在不安装全局包的情况下，运行已安装在本地项目中的包或者远程仓库中的包；
- 使用包的最新版本：本地没有安装包，npx 会从 npm 仓库中下载最新版本并运行；

比如通过 react 创建项目，需要先全局安装 npm install -g create-react-app，再通过 create-react-app my-app 全局命令创建项目；
而通过 npx create-react-app my-app 直接创建项目，无需全局安装脚手架，npx 会从 npm 仓库中下载最新版本并运行；

### npx 命令执行流程

先从本地项目的 node_modules/.bin 中查找可执行命令，找不到则从全局中查找，找不到则从 npm 仓库中下载安装，执行完成后删除下载的包；

### npx & npm 区别

- npx 侧重于执行某个模块命令；
- npm 侧重于管理模块，而不具备执行某个模块命令的能力；

## 模块化

node 环境支持 CommonJs 规范 (Cjs 规范) 和 ESM 规范；
package.json/type 默认就是 commonjs 模块化，ESM 规范将 type 值改成 module 即可；

<!-- node 需要 14.15.1 以上版本同时支持
Es6 模块化方式，通过 babel 第三方插件在 node 中使用 ES6 的模块化； -->

### Cjs 规范

require 引入模块支持 4 种格式；

1. 引入 node 内置模块 (fs, path, http, os, child_process 等);
2. 引入第三方模块 (npm install md5, express, koa 等)；
3. 引入自定义模块 (./index.js, ../test.js)；
4. 引入 json 文件

```javascript inde.js
// 引入 node 内置模块
const fs = require('fs');


// 引入第三方模块
const md5 = require("md5");
const value = md5("hello");
console.log(value);


// 引入自定义模块
require("./test.js");       // 这是test


// 引入json文件
const data = require('./test.json');
console.log(data);          // { data: 123 }

---
test.js

console.log("这是test");

---
test.json

{
  "data": 123
}
```

```javascript
// test.js
module.exports = 123;

// index.js
const num = require("./test.js");
console.log(num); // 123

---

// test.js
module.exports = {
  success: 1,
  error: 0,
  sum: (a, b) => a + b,
};

// index.js
const { success, sum } = require("./test.js");
console.log(success); // 1
console.log(sum(1, 2)); // 3
```

### ESM 规范

```javascript
// test.js
export default {
  success: 1,
  error: 0,
  sum: (a, b) => a + b,
};

export const name = "test";

export function sum(a, b) {
  return a + b;
}
---
index.js

import obj, { name, sum as sumFn } from "./test.js";
console.log(obj);             // { success: 1, error: 0, sum: [Function: sum] }
console.log(name);            // test
console.log(sumFn(1, 2));     // 3
---
index.js

import * as all from "./test.js";
console.log(all.sum(1, 2));   // 3
---
index.js

import json from './test.json' assert  { type: 'json' };
console.log(json);
```

### Cjs 模块化与 ESM 模块化的区别

- Cjs 中顶层 this 指向的是当前模块对象，ESM 中顶层 this 指向 undefined；
- Cjs 是基于运行时的同步加载，ESM 基于编译时的异步加载；
- Cjs 不支持 tree shaking，ESM 支持；
- Cjs 引入的值是可以修改值，esm 不能修改引入的值 (只读)；

```javascript
if (true) {
  // cjs 优点是运行时才同步加载文件，弊端是如果文件过大，会阻塞后续代码执行；
  require("./test.js");

  // esm 不支持在逻辑中去加载数据，在编译代码阶段就会报错；
  import("./test.js"); // error
  import("./test.js").then((res) => {}); // 支持异步加载文件，不会阻塞后续代码执行;
}
```

## 全局变量

JS 由三部分组成 ECMAScript(ES6)，DOM 和 BOM (在 node 环境不可用)；
在浏览器端，全局变量是挂载在 window 对象上的；而在 node 里面是没有 window，通过关键字 global 和 globalThis 来设置全局变量；
globalThis 会根据运行环境自动判断，如果是浏览器环境，则是 window 对象，如果是 node 环境，则是 global 对象；

```javascript
// test.js
console.log(global.name);
console.log(globalThis.name);

---

// index.js
require("./test.js");       // undefined；注意：定义全局变量和引入的顺序，否则读取不到;
global.name = "hello";

// index.js
global.name = "hello";
require("./test.js");       // hello;

// index.js
globalThis.name = "hello";  // hello;
require("./test.js");
```

node 内置全局变量
dirname 获取当前文件所在目录的绝对路径
fileName 获取当前文件所在目录和文件名的绝对路径；
Buffer 用于处理二进制数据和媒体数据；
Procee 进程对象，用于获取当前进程的信息和控制进程；

```javascript
console.log(__dirname); // D:\Projects\node
console.log(__filename); // D:\Projects\node\index.js
```

## SSR

服务端渲染

`npm i jsdom` 在 node 环境不可用 DOM 和 BOM，可通过插件 jsdom 来模拟 DOM 和 BOM；

```javascript
const { JSDOM } = require("jsdom");
const fs = require("fs");

const root = new JSDOM(`<!DOCTYPE html>
<html>
    <body>
        <div id="app"></div>
    </body>
</html>`);

const window = root.window;
const document = window.document;
const app = document.getElementById("app");

// node18之后才有fetch
fetch("https://jsonplaceholder.typicode.com/photos/1")
  .then((res) => res.json())
  .then((data) => {
    const img = document.createElement("img");
    img.src = data.url;
    img.style.width = "100px";
    img.style.height = "200px";
    app.appendChild(img);

    // 创建index.html文件，serialize方法就是序列化后字符串格式的html；
    fs.writeFileSync("./index.html", root.serialize());
  });
```

## 内置模块

path 在不同操作系统有差异，windows 下是'\' 和 '/'，其他 unix, macOs, linix 下是'/';

## path

```javascript D:/Projects/node/index.js
const path = require("path");

console.log(path.sep)                             // \; 根据操作系统差异返回'/'  or '\'

---

console.log(path.dirname("/abc/demo.html"));      // abc; 返回除了最后一个文件名的路径
console.log(path.basename("/abc/demo.html"));     // demo.html; 返回给的路径的最后一个文件名
console.log(path.basename("\\abc\\demo.html"));   // demo.html

---

console.log(path.extname("/abc/demo.html"));      // .html; 返回给的路径的扩展名

---

console.log(path.join("/abc", "/test", "/demo.html"));          // /abc/test/demo.html
console.log(path.join("/abc", "/test", "/demo.html", "../"));   // /abc/test; 支持操作符

---
// 给的路径是绝对路径时，返回结果

console.log(path.resolve("/demo.html"));              // D:/demo.html
console.log(path.resolve("/a", "/b", "/demo.html"));  // D:/demo.html

// 给的路径是相对路径时，返回当前文件所在目录绝对路径
console.log(path.resolve("./demo.html"));             // D:/Projects/node/demo.html

// 给的路径是绝对路径和相对路径时，返回绝对路径
console.log(path.resolve(__dirname, "./demo.html"));  // D:/Projects/node/demo.html
```

## os

os 模块可以跟操作系统进行交互

```javascript
const os = require("os");
const { exec } = require("child_process");

console.log(os.platform()); // win32/windows/darwin/linux等

console.log(os.release()); // 10.0.26100

console.log(os.version()); // Windows 11 Home China

console.log(os.homedir()); // C:/Users/104956; 读取用户目录

console.log(os.cpus().length); // 32; 读取cpu线程数量

---

const platform = os.platform();
const open = (url) => {
  // mac
  if (platform === "darwin") {
    exec(`open ${url}`);
  }
  // windows; PS：start http路径可直接在命令行打开网页
  else if (platform === "win32") {
    exec(`start ${url}`);
  }
  // linux
  else if (platform === "linux") {
    exec(`xdg-open ${url}`);
  }
};

open("https://baidu.com");
```

## process

process 是 node 操作进程的模块，并且是挂载到 globalThis 下的全局 api；

```javascript
const pid = process.pid;
console.log(pid); // 获取进程id

const platform = process.platform;
console.log(platform); // win32

const cwd = process.cwd();
console.log(cwd); // D:\Projects\node; 等价于__dirname, dirname在esm规范下是不可用，可用cwd替代；

const env = process.env; // 获取操作系统的所有环境变量
console.log(env.NODE_ENV);

const argv = process.argv; // 获取自定义的命令行参数
console.log(argv);
/**
 * argv
  [
    'D:\\Applications\\nodejs\\node.exe',   // 运行环境
    'D:\\Projects\\node\\index.js'  // 运行文件
    '...'   // 命令行参数
  ]
*/

process.on("exit", () => {
  console.log("监听到进程已退出");
});

setTimeout(() => {
  console.log("退出进程");
}, 5000);
// or
setTimeout(() => {
  process.exit();
}, 2000);

setTimeout(() => {
  process.kill(process.pid);
}, 2000);
```

## child_process

### execSync & spawn

- exec & execSync(常用) 可执行 shell 命令或与软件交互；参数的字节上限 200kb；执行完命令才会返回结果；
- spawn(常用) & spawnSync 可执行 shell 命令或与软件交互；没有字节上限；实时返回结果流；

```javascript
const {exec, execSync,  spawn,  spawnSync} = require("child_process");

exec("node -v", (err, stdout, stderr) => {
//   返回错误信息，标准输出流buffer，错误输出流
  if (err) return;
  console.log(stdout.toString());
});

// 打开软件
execSync("D:\\LenovoSoftstore\\Install\\wangyiweidaofanyi\\YoudaoDict.exe");
execSync("start https://www.baidu.com");

---

// spawn & spawnSync 没有字节上限，实时返回结果流；
const { stdout } = spawn("netstat");  // 查询网络状态

stdout.on("data", (data) => {
  console.log(data.toString());
});
stdout.on("close", (data) => {
  console.log("结束了");
});
```

### execFile & execFileSync

执行可执行的文件

```javascript
const { execFile, execFileSync, fork } = require("child_process");
const path = require("path");

// windows系统可执行后缀.cmd问价，mac或Linix可执行.sh文件；
execFile(
  path.resolve(__dirname, "./bat.cmd"),
  null,
  (error, stdout, stderr) => {
    console.log(stdout.toString());
  }
);
```

```javascript bat.cmd

echo 'start'      // 输出start

mkdir test        // 创建test文件夹

cd ./test         // 进入test文件夹

echo console.log('hello world') > test.js   // 创建test.js文件并写入内容

echo 'end'        // 输出end

node test.js      // 运行test.js文件
```

### fork

主进程与子进程通信，只能接收 js 模块；
node 的弱点不适合做 cpu 密集型应用，因为是单线程会造成堵塞，此时可把耗时任务丢给子进程处理；

```javascript index.js
const { fork } = require("child_process");
const childProcess = fork("./test.js");

process.on("message", (msg) => {
  console.log("来自子进程的消息：", msg);
});

childProcess.send("父进程发来的信息");
```

```javascript test.js
process.on("message", (msg) => {
  console.log("子进程收到消息：", msg);
});

process.send("子进程发送的消息");
```

## events

事件触发器，采用的是订阅发布模式，vue2 的 bus，mitt 都是采用发布订阅模式；

```javascript
const eventEmitter = require("events");
const bus = new eventEmitter();

// 订阅
const fn = (message) => {
  console.log(message);
};

bus.setMaxListeners(20); // bus.on 默认只能监听10个事件，超出会报错，setMaxListeners可提高；
bus.on("randomKey", fn);

// bus.off("randomKey", fn);    // 需与on传递同一个函数才能回收；

bus.once("once", (message) => {
  console.log(message);
});

// 发布
bus.emit("randomKey", "hello");
bus.emit("randomKey", "world");
bus.emit("once", "不管发布多少次，只会执行一次");
```

## util

util 是 nodejs 提供的一个工具模块，提供了一些常用的方法方便快速开发；

#### util.promisify

```javascript
import util from "node:util";
import { exec } from "node:child_process";

// const execPromise = util.promisify(exec);

const promisify = (fn) => {
  return (...args) => {
    return new Promise((resolve, reject) => {
      fn(...args, (err, ...values) => {
        if (err) reject(err);

        if (values && values.length > 1) {
          let obj = {};
          for (let key in values) {
            obj[key] = values[key];
          }
          resolve(obj);
        } else {
          resolve(values[0]);
        }
      });
    });
  };
};

// const execPromise = util.promisify(exec);
const execPromise = promisify(exec);

execPromise("node -v")
  .then((res) => {
    console.log(res);
  })
  .catch(console.error);
```

#### util.callbackify

```javascript
import util from "node:util";

const fn = (type) => {
  if (type === 1) {
    return Promise.resolve("success");
  } else {
    return Promise.reject("error");
  }
};

const callbackify = (fn) => {
  return (...args) => {
    let callback = args.pop();
    fn(...args)
      .then((res) => {
        callback(null, res);
      })
      .catch((err) => {
        callback(err);
      });
  };
};

// 把Promise函数转成回调函数
// const callback = util.callbackify(fn);
const callback = callbackify(fn);

callback(1, (err, value) => {
  console.log(err, value);
});
```

#### util.format

```javascript
import util from "node:util";

let res1 = util.format("%s-%s", "mm", "dd");
let res2 = util.format("%d-%s", "9m", "dd");
let res3 = util.format("%d-%s", "99", "dd");

console.log(res1); // mm-dd
console.log(res2); // NaN-dd
console.log(res3); // 99-dd
```

## fs

fs 文件模块，可以进行文件系统的读写、更改文件权限、创建目录等；

```javascript
const fs = require("fs");
const fsPromise = require("fs/promises");

// 异步方式
fs.readFile(
  "./text.txt",
  {
    encoding: "utf-8",
    flags: "r",
  },
  function (err, data) {
    if (err) throw err;
    console.log(data);
  }
);

// 同步方式会阻塞代码
const txt = fs.readFileSync("./text.txt", "utf-8").toString();
console.log(txt);

// Promise方式
fsPromise.readFile("./text.txt", "utf-8").then((data) => {
  console.log(data);
});

// 可读流；处理大文件常用；
const readStream = fs.createReadStream("./text.txt", "utf-8");
readStream.on("data", (chunk) => {
  console.log(chunk);
});
readStream.on("end", () => {
  console.log("读取完成");
});
```

```javascript
const fs = require("fs");

// 创建文件夹；创建和删除若是多层级需添加recursive；
fs.mkdirSync("./text", { recursive: true });

// fs.rmSync("./text", { recursive: true });

fs.renameSync("./text", "./newText");

fs.watch("./text.txt", (event, filename) => {
  console.log(filename);
  console.log(event);
});
```

```javascript
const fs = require("fs");

// 写入文本，每次写入都是覆盖
fs.writeFileSync("./text.txt", "hello world");

// 追加内容
fs.writeFileSync("./text.txt", "\n追加内容", {
  flag: "a", // append缩写
});

fs.appendFileSync("./text.txt", "\n另外一种追加方法");

// 创建可写流；大量数据分批插入
let writeStream = fs.createWriteStream("./text.txt");

let poem = [
  "春江花月夜",
  "春江潮水连海平，海上明月共潮生。",
  "滟滟随波千万里，何处春江无月明！",
  "江流宛转绕芳甸，月照花林皆似霰。",
  "空里流霜不觉飞，汀上白沙看不见。",
  "江天一色无纤尘，皎皎空中孤月轮。",
  "江畔何人初见月？江月何年初照人？",
  "人生代代无穷已，江月年年望相似。",
  "不知江月待何人，但见长江送流水。",
  "白云一片去悠悠，青枫浦上不胜愁。",
  "谁家今夜扁舟子？何处相思明月楼？",
  "可怜楼上月徘徊，应照离人妆镜台。",
  "玉户帘中卷不去，捣衣砧上拂还来。",
  "此时相望不相闻，愿逐月华流照君。",
  "鸿雁长飞光不度，鱼龙潜跃水成文。",
  "昨夜闲潭梦落花，可怜春半不还家。",
  "江水流春去欲尽，江潭落月复西斜。",
  "斜月沉沉藏海雾，碣石潇湘无限路。",
  "不知乘月几人归，落月摇情满江树。",
];

poem.forEach((it) => {
  writeStream.write(it + "\n");
});

writeStream.end();

writeStream.on("finish", () => {
  console.log("写入完成");
});
```

```javascript
const fs = require("fs");

// 硬链接；
// 类似于引用类型，不管哪个文件内容修改，两个文件都是同一个内存地址；
// 但是任何一个文件删除，都不影响另外一个文件；
fs.linkSync("./text.txt", "./text2.txt");

// 软链接；
// 需要使用管理员权限才能执行脚本；
// text.txt原始文件删除，text2无法使用，类似于text2是一个快捷键；
fs.symlinkSync("./text.txt", "./text2.txt");
```

## crypto

crypto 模块提供了对称加密、非对称加密、哈希函数和数字签名技术;

```javascript
const crypto = require("crypto");

// 对称加密算法；双方协定一个密钥以及iv
// 1参接收一个算法常用的是aes-256-cbc；
// 2参是密钥32位；
// 3参数是iv初始化向量16位；确保每次生成的密钥串列不同；
const key = crypto.randomBytes(32);
const iv = Buffer.from(crypto.randomBytes(16));
const cipher = crypto.createCipheriv("aes-256-cbc", key, iv);
cipher.update("这是密钥", "utf-8", "hex"); // 生成密文
const result = cipher.final("hex"); // 按16进制输出密文

// 解密；需要相同key和相同iv
const decipher = crypto.createDecipheriv("aes-256-cbc", key, iv);
decipher.update(result, "hex", "utf-8");
const result2 = decipher.final("utf-8");
```

```javascript
const crypto = require("crypto");

// 非对称加密；生成公钥和私钥；私钥只能是管理员拥有不能对外公布；公钥可以公布；
const { publicKey, privateKey } = crypto.generateKeyPairSync("rsa", {
  modulusLength: 2048, // 密钥长度,越长越安全但越慢
});

// 公钥加密
const encrypted = crypto.publicEncrypt(publicKey, Buffer.from("这是密钥"));
const result = encrypted.toString("hex");
console.log(result);

// 私钥解密
const decrypted = crypto.privateDecrypt(privateKey, encrypted);
console.log(decrypted.toString());
```

```javascript
const crypto = require("crypto");

// 哈希函数；单向不可逆无法被解密
// 生成密文具有唯一性，但不是很安全，可能通过撞库测试出密码；
const hash = crypto.createHash("sha256"); // 常用'sha256' 或 'md5';
hash.update("这是密钥");
let result = hash.digest("hex");
console.log(result);
```

## 编写脚手架

- 自定义命令，通过终端创建什么命令 (如 -v, --help, 交互方式(如输入或选择等));
- 通过交互反馈下载对应的模板；

commander：用于构建命令行工具的 npm 库，可以创建命令行接口，处理命令行参数和选项；
inquirer：命令行交互工具，提供丰富的交互，可与用户进行交互和收集用户输入，如输入框、选择列表、确认框等；
download-git-repo：从远程 github 仓库下载指定分支或标签的代码；
ora：命令行界面加载动画库，提供如进度条、加载动画、文字转场效果等；

## markdownToHtml

## zlib

zlib 模块可以对数据压缩和解压，在程序中减少数据的输入大小和提高性能；
zlib 支持流 stream 的方式进行数据的压缩和解压，支持 Gzip 和 Deflate；
Gzip 和 Deflate 区别：

- Gzip 常用于文件压缩、网络压缩、和 http 响应的内容编码；
- Gzip 压缩效率比 deflate 高，但 Gzip 压缩速度比 deflate 低；

```javascript Gzip
const zlib = require("zlib");
const fs = require("fs");

// 压缩
// const readStream = fs.createReadStream("text.txt"); // 创建可读流
// const writeStream = fs.createWriteStream("text.txt.gz"); // 创建可写流
// readStream.pipe(zlib.createGzip()).pipe(writeStream);

// 解压
const readStream = fs.createReadStream("text.txt.gz");
const writeStream = fs.createWriteStream("text2.txt");
readStream.pipe(zlib.createGunzip()).pipe(writeStream);

---

// 压缩
// const readStream = fs.createReadStream("text.txt"); // 创建可读流
// const writeStream = fs.createWriteStream("text.txt.deflate"); // 创建可写流
// readStream.pipe(zlib.createDeflate()).pipe(writeStream);

// 解压
const readStream = fs.createReadStream("text.txt.deflate");
const writeStream = fs.createWriteStream("text3.txt");
readStream.pipe(zlib.createInflate()).pipe(writeStream);
```

```javascript 案例
const zlib = require("zlib");
const http = require("http");

const server = http.createServer((req, res) => {
  const txt = "hello world".repeat(1000);

  res.setHeader("Content-Encoding", "gzip"); // 设置压缩头，浏览器可自动解析；
  res.setHeader("Content-type", "text/plain;charset=utf-8");
  const result = zlib.gzipSync(txt);
  res.end(result);
});

server.listen(3000, () => {
  console.log("服务启动");
});
```

## http

- http 模块可创建 http 服务器，提供 Web 应用程序或网站；通过监听特定端口，服务器可以接收客户端的请求并响应；
- 构建 RESTful API，使请求方法和路径来定义 API 的不同端点；
- 创建代理服务器，用于转发客户端的请求到其他服务器。代理服务器可用语负载均衡、缓存、安全过滤、跨域等请求场景；
- 创建一个简单的文件服务器，提供静态文件（如 HTML、CSS、JavaScript、图片等）给客户端访问；

### http 调试

vscode 插件：REST Client；
根目录下新建 anyword.http 文件，内容如下：

```javascript anyword.http
# POST http://localhost:3000/user/registry

# Content-Type: application/json

# {
#     "username": "Tom"
# }

send request
GET http://localhost:3000/user/login?username=Tom&password=123456 HTTP/1.1
```

### http 服务器

```javascript
const http = require("http");
const url = require("url");

http
  .createServer((req, res) => {
    // true 表示GET参数将字符串转为对象
    const { pathname, query } = url.parse(req.url, true);

    if (req.method === "POST") {
      if (pathname === "/login") {
        let data = "";
        req.on("data", (chunk) => {
          data += chunk;
        });

        req.on("end", () => {
          res.setHeader("Content-Type", "application/json");
          res.statusCode = 200;
          res.end("接收到的请求参数是：" + data);
        });
      } else {
        res.statusCode = 404;
        res.end("404");
      }
    }

    if (req.method === "GET") {
      if (pathname === "/list") {
        res.statusCode = 200;
        res.end("接收到的请求参数username=" + query.username);
      } else {
        res.statusCode = 404;
        res.end("404");
      }
    }
  })
  .listen(3000, () => {
    console.log("服务启动");
  });
```

### 反向代理

反向代理充当服务器和客户端之间的中间层，将客户端的请求转发给后端服务器，并返回结果给客户端；
需要借助第三方库实现反向代理：http-proxy-middleware；

- 负载均衡根据预先定义的请求分发到多个后端服务器，实现负载均衡，避免单个后端服务器过载；
- 如果某个服务器出现故障，代理服务器会自动将请求转发给其他服务器，实现故障转移；
- 缓存静态资源或经常访问的动态内容，减轻服务器负载压力，提高响应速度；
- 反向代理可作为防火墙，保护服务器免受恶意请求和攻击，过滤、检测、阻止恶意请求；
- 可重写请求的域名和路径，实现 URL 路由和重定向；

---

创建配置文件，根据 commonJs 规范；

```javascript customized.config.js
module.exports = {
  serve: {
    proxy: {
      "/api": {
        target: "http://localhost:3000",
        changeOrigin: true,
      },
    },
  },
};
```

访问 http://localhost:80 服务，index 服务会将 html 文件返回给客户端；
html 文件中，用户通过交互 script 中会发送如 /api 接口请求服务；

```javascript index.js
const http = require("http");
const url = require("url");
const fs = require("fs");
const { createProxyMiddleware } = require("http-proxy-middleware");
const html = fs.readFileSync("index.html", "utf8");
const config = require("./customized.config.js");

http
  .createServer(function (req, res) {
    const { pathname } = url.parse(req.url);
    const proxyList = Object.keys(config.serve.proxy); // 获取所有代理路径；

    if (proxyList.includes(pathname)) {
      const proxy = createProxyMiddleware(config.serve.proxy[pathname]);
      proxy(req, res);
      return;
    }

    res.writeHead(200, { "Content-Type": "text/html" });
    res.end(html);
  })
  .listen(80);
```

启动 test.js 服务，检测是否从 80 端口的服务代理到 3000 端口的服务

```javascript test.js
const http = require("http");
const url = require("url");

http
  .createServer(function (req, res) {
    const { pathname } = url.parse(req.url);
    if (pathname === "/api") {
      res.end("proxy success");
    }
  })
  .listen(3000);
```

### 动静分离

将动态生成的内容 (如动态网页、API 请求) 与 静态资源 (如 HTML、CSS、JS、图像) 分开处理和分发；
通过将动态请求分发到不同的服务器或服务商，可以平衡服务器负载；使用缓存机制将静态资源存储在浏览器缓存中，减少网络请求；

```javascript
import http from "http";
import fs from "fs";
import path from "path";
import mime from "mime";

http
  .createServer(function (req, res) {
    const { method, url } = req;
    const isStatic = url.startsWith("/static");

    // 判断是否是访问静态资源 (访问静态资源必须是get)
    if (method === "GET" && isStatic) {
      const staticPath = path.join(process.cwd(), url);

      fs.readFile(staticPath, (err, data) => {
        if (err) {
          res.writeHead(404, { "Content-Type": "text/plain" });
          res.end("404 Not Found");
        } else {
          console.log("访问静态资源");

          const type = mime.getType(staticPath); // 根据文件路径获取媒体类型
          res.writeHead(200, {
            "Content-Type": type, // 设置响应头媒体类型
            "cache-control": "public, max-age=3600", // 设置强缓存，浏览器缓存后不会再从服务器获取资源
          });
          res.end(data);
        }
      });
    }

    // 动态资源
    if (method === "GET" || method === "POST") {
    }
  })
  .listen(80, () => {
    console.log("浏览器访问静态资源：http://127.0.0.1/static/index.css");
    console.log("服务器启动成功");
  });
```

## express 框架

express 框架是一个基于 Node.js 平台的快速、开放、极简的 Web 框架，简化了 HTTP 请求、响应和中间件的过程；

- 强大的中间件生态系统，可使用各种中间件扩展和增强应用程序的功能，中间件允许在请求前后执行逻辑，例如身份验证、会话管理、日志记录、静态文件服务、模板引擎等等；
- 路由模块化，使得应用程序可以根据不同的功能或模块进行分组，将特定的 URL 路径映射到响应的处理函数， 提高代码的组织性和可维护性；

```javascript app.js
import express from "express";
import LoggerMiddleware from "./middleware/logger.js";
import User from "./src/user.js";
import List from "./src/list.js";

const app = express();
app.use(express.json()); // 支持post解析json
app.use(LoggerMiddleware); // 使用日志中间件

app.use("/user", User);
app.use("/list", List);

app.listen(3000, () => console.log("Server started on port 3000"));
```

```javascript user.js
import express from "express";

const router = express.Router();

router.get("/login", (req, res) => {
  res.json({ code: 200, message: "登录成功" });
});

router.post("/registry", (req, res) => {
  res.json({ code: 200, message: "注册成功" });
});

export default router;
```

```javascript logger.js
import log4js from "log4js";

// 配置日志
log4js.configure({
  appenders: {
    out: { type: "stdout", layout: { type: "colored" } },
    file: { type: "file", filename: "/logs/server.log" },
  },
  categories: {
    default: { appenders: ["out", "file"], level: "debug" },
  },
});

// 初始化日志
const logger = log4js.getLogger("default");

// 每个请求都会经过中间件；req, res, next不执行的话会一直卡着
const LoggerMiddleware = (req, res, next) => {
  logger.debug(`[${req.method} ${req.url}]`); // 将请求方法和访求路径记录到日志中
  next();
};

export default LoggerMiddleware;
```

### 跨域

浏览器 **同源策略** (协议、域名、 端口号) ，其中任何一个不同都会造成的跨域问题；

- 同源策略跨域
- 请求方式跨域
- 预检请求跨域

```javascript
import express from "express";

const app = express();

// 设置中间件
app.use("*", (req, res, next) => {
  // 同源策略跨域：允许所有域名访问，或 指定域名
  res.setHeader("Access-Control-Allow-Origin", "*");

  // 请求方式跨域：Access-Control-Allow-Methods 默认支持GET、POST、HEAD 请求方式，其他方式请求也会报跨域，添加允许的请求方式；
  res.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");

  /**
   * 预检请求跨域
     CORS 的 Content-Type 默认支持 application/x-www-form-urlencoded | multipart/form-data | text/plain 3种格式
   
   * 浏览器的预检请求 OPTIONS 会根据3种情况自动触发
      1.客户端发起的请求头中设置了 Content-Type: application/json 格式；
      2.请求方式不是GET、POST、HEAD；
      3.请求头中包含自定义的请求头；
   */
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");

  next();
});

app.get("/list", (req, res) => {
  res.json({ code: 200, message: "获取成功", data: [] });
});

app.listen(3000, () => console.log("Server started on port 3000"));
```

## mysql

mysql2 库链接 mysql 数据库和编写 sql 语句；

```javascript app.js
import sql from "./config/db.config.js";
import express from "express";

const app = express();
app.use(express.json());

// 查询列表
app.get("/users", async (req, res) => {
  const [data] = await sql.query("SELECT * FROM tb_user");
  console.log(data);
  res.send(data);
});

// 查询单个
app.get("/user/:id", async (req, res) => {
  const [data] = await sql.query(
    `SELECT * FROM tb_user where id=${req.params.id}`
  );
  res.send(data);
});

// 新增用户
app.post("/createUser", async (req, res) => {
  const { username, nickname, age, gender, departmentId } = req.body;
  await sql.query(
    `INSERT INTO tb_user (id, username, nickname, age, gender, department_id) VALUES (7, '${username}', '${nickname}', ${age}, ${gender}, ${departmentId})`
  );
  res.send({ code: 200, message: "新增成功" });
});

// 修改用户
app.post("/updateUser", async (req, res) => {
  const { nickname, age, id } = req.body;
  await sql.query(
    `UPDATE tb_user SET nickname ='${nickname}', age = ${age} where id=${id}`
  );
  res.send({ code: 200, message: "修改成功" });
});

// 删除用户
app.post("/deleteUser", async (req, res) => {
  await sql.query(`DELETE from tb_user where id=${req.body.id}`);
  res.send({ code: 200, message: "删除成功" });
});

app.listen(3000, () => console.log("server started"));
```

```javascript db.config.js
// import mysql2 from "mysql2";
import mysql2 from "mysql2/promise"; // 提供promise支持

const sql = await mysql2.createConnection({
  host: "localhost",
  user: "root",
  password: "123456",
  database: "study",
});

export default sql;
```

## knex

knex 是一个 SQL 查询构建器，它允许使用 js 代码来生成和执行 SQL 语句，无需直接编写 SQL 语句与数据库交互，并且支持多种数据库，如 MySQL、PostgreSQL、SQLite 等；

```javascript db.config.js
import knex from "knex";

const database = knex({
  client: "mysql2", // 链接数据库类型
  connection: {
    host: "127.0.0.1",
    user: "root",
    password: "123456",
    database: "study",
  },
});

export default database;
```

```javascript app.js
import database from "./config/db.config.js";
import express from "express";

const app = express();
app.use(express.json());

// 创建表
app.get("/createTable", async (req, res) => {
  try {
    const exists = await database.schema.hasTable("tb_user");
    if (exists) throw new Error("已经创建过表");

    await database.schema.createTable("tb_userinfo", (table) => {
      table.increments("id").primary();
      table.string("work_no");
      table.string("username");
      table.string("nickname");
      table.string("gender");
      table.string("phone");
    });

    res.send({ code: 200, message: "创建成功" });
  } catch (error) {
    res.send({ code: 500, message: error.message });
  }
});

// 查询列表
app.get("/users", async (req, res) => {
  // const rawSql = database.raw('select * from tb_user'); // 支持原生SQL语句查询
  // const sql = database("tb_user").select().toSQL().sql; // 支持反向查询SQL语法

  const list = await database("tb_user").select().orderBy("age", "desc");
  const list2 = await database("tb_user")
    .select()
    .leftJoin("tb_department", "tb_user.department_id", "tb_department.id");

  const total = await database("tb_user").count("* as total");
  res.json({
    list,
    list2,
    total: total[0].total,
  });
});

// 查询单个
app.get("/user/:id", async (req, res) => {
  const data = await database("tb_user").select().where({ id: req.params.id });
  res.send({
    message: "查询成功",
    data: data[0],
  });
});

// 新增用户
app.post("/createUser", async (req, res) => {
  const { username, nickname, age, gender, departmentId } = req.body;
  const data = await database("tb_user").insert({
    id: 10,
    username,
    nickname,
    age,
    gender,
    department_id: departmentId,
  });

  res.send({ code: 200, message: "新增成功", data });
});

// 修改用户
app.post("/updateUser", async (req, res) => {
  try {
    const { nickname, age, id } = req.body;
    const data = await database("tb_user")
      .update({ nickname, age })
      .where({ id });

    if (data === 0) throw new Error("未查询到此用户");

    res.send({ code: 200, message: "修改成功", data });
  } catch (error) {
    res.send({ code: 500, message: error.message });
  }
});

// 删除用户
app.post("/deleteUser", async (req, res) => {
  try {
    const data = await database
      .delete()
      .from("tb_user")
      .where({ id: req.body.id });

    if (data === 0) throw new Error("未查询到此用户");

    res.send({ code: 200, message: "删除成功" });
  } catch (error) {
    res.send({ code: 500, message: error.message });
  }
});

app.post("/exchangeSalary", async (req, res) => {
  try {
    const { changedId, changedSalary, exchangedId, exchangedSalary } = req.body;

    // 事务，要么一起成功，要么都回滚
    const trx = await database.transaction();
    const a = await trx("tb_user")
      .update({ salary: changedSalary })
      .where({ id: changedId });

    const b = await trx("tb_user")
      .update({ salary: exchangedSalary })
      .where({ id: exchangedId });

    if (a === 1 && b === 1) {
      await trx.commit();
      res.send({ code: 200, message: "地下交易成功" });
    } else {
      await trx.rollback();
      throw new Error("有内鬼，终止交易");
    }
  } catch (error) {
    res.send({ code: 500, message: error.message });
  }
});

app.listen(3000, () => console.log("server started"));
```

## MVC

MVC（Model-View-Controller）是一种软件架构模式，它将一个应用程序分解为三个主要部分：模型、视图和控制器。
MVC 主要目标是提供了一种清晰的结构将应用程序的逻辑、数据和界面分离，提高代码的可维护性、可扩展性和可复用性。

- Model：模型是应用程序的数据和业务逻辑，负责数据的存储、检索、验证和更新等操作，通常包含与数据库、文件系统或外部服务进行交互的代码；
- View：视图是应用程序的数据以可视化的形式呈现给用户，负责用户界面的展示，包含各种图形元素、页面布局和用户交互组件等。通常是根据数据的状态来动态生成和更新视图；
- Controller：控制器是应用程序的中间层，充当模型和视图之间的中间人，负责协调两者之间的交互。接收用户的输入输入跟新模型的状态，并且根据模型变化更新视图；
