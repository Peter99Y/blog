---
title: Eslint
---

# 项目配置流程

本地 vsCode 安装 eslint 和 prettier 扩展；

创建 vue 项目
`npm create vite my-vue-project --template vue-ts`;

安装 eslint 和 prettier，以及相关插件:

2. `npm install eslint prettier eslint-plugin-vue eslint-config-prettier eslint-plugin-prettier -D`

3. `npx eslint --init` 生成配置文件；

4. 安装 typescript 相关插件和声明文件；

`npm install typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin @types/eslint @types/node -D`

<!-- -   3. eslint - package.json 配置脚本；
-   4. eslint - 配置 eslint rules 规则；
-   5. eslint - 添加 eslint 忽略文件；
-   6. prettier - 创建 prettierrc 格式化文件
-   7. prettier - 配置忽略文件 -->

---

# Eslint

检测 js 代码语法和潜在 bug，但无法检测 css、html、vue 文件等；

插件安装: `npm i eslint -D`;

选择并生成 eslint.config.js 配置文件: `npx eslint --init` or `npm init @eslint/config@latest`;

```
How would you like to use eslint?
  To check syntax only
> To check syntax and find problems
  To check syntax, find problems and enforce code style (eslint自己的校验规则，如自己配置规则或第三方规则选第二)
```

## .mjs & .js 区别

-   根据安装 eslint 版本：eslint 9.0 版本前生成 eslint.config.mjs，9.0 版本后生成 eslint.config.js

-   根据安装时选择配置的 module 类型

```
What type of modules does your project use?
> JavaScript modules (import/export)
  CommonJS (require/exports)
```

-   选择 CommonJS 模块，将生成 eslint.config.js

```
// eslint.config.js

module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
  ],
  parserOptions: {
    ecmaVersion: 12,
    sourceType: 'module',
  },
  rules: {
    'no-unused-vars': 'warn',
    'no-console': 'off',
  },
};
```

-   选择 JavaScript modules 模块，将生成 eslint.config.mjs

```
// eslint.config.mjs

export default {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
  ],
  parserOptions: {
    ecmaVersion: 12,
    sourceType: 'module',
  },
  rules: {
    'no-unused-vars': 'warn',
    'no-console': 'off',
  },
};
```

```
// eslint.config.js

import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint"; // 安装ts检测插件
import pluginVue from "eslint-plugin-vue"; // 安装vue检测插件

export default [
  {languageOptions: { globals: globals.browser }},

  pluginJs.configs.recommended, // 使用 JavaScript 的推荐规则配置

  ...tseslint.configs.recommended, // 使用 TypeScript 的推荐规则配置

  ...pluginVue.configs["flat/essential"], // 使用 vue 规则配置插件
];
```

## 命令

`eslint demo.js` - eslint 全局命令， 执行检测某个文件；
`eslint demo.js --fix` - eslint 全局命令，执行检测某文件并强制修复（将配置的双引号变引号好等）；

每次都需要手动操作指令检测 js 语法，此时 vsCode 中安装 eslint 插件即可在编码过程中检测错误；

## script 脚本

添加执行 eslint 脚本命令

```
"scripts": {
  "lint": "eslint src", // 校验 src 目录下的文件
  "fix": "eslint src --fix" // 修复 src 目录下的文件
},
```

## 忽略

```

// eslint.config.js

export default [
  {
    // 忽略与 eslint.config.js 文件同级的文件
    ignores: [
        ".config/", // config 目录
        ".config/*", // config 目录下所有文件
        "**/node_modules/",
        ".git/" // 后缀.git 文件
    ]
  }
];

```

## rules 规则

关闭规则："off" / 0
打开规则作为警告："warn" / 1
打开规则作为错误： "error" / 2

-   `npm install eslint-config-airbnb-base eslint-plugin-import -D`

    > eslint-config-airbnb-base 代码配置插件，当前业界最火的 ESlint 规范就是 Airbnb;（需依赖 eslint-plugin-import）

-   自己配置规则

```
// eslint.config.js

import js from "@eslint/js";

export default [
  js.configs.recommended,
  {
    rules: {
      "no-unused-vars": "warn", // 未使用的变量
    }
  }
]
```

## eslint 与 typescript

```
npm install
  typescript
  @typescript-eslint/parser   // ESLint用来解析typescript,从而检测ts代码；
  @typescript-eslint/eslint-plugin // ESLint插件，包含各类定义好的检测ts的规范；
  @types/eslint // ts eslint声明插件；
  @types/node // ts node声明插件；
-D
```

# Prettier

[prettier](https://prettier.io/) 是代码格式化的编码风格工具，比如单行的代码长度、tab 长度、空格等；

#### 安装

-   `npm install prettier -D`

#### 创建 prettierrc 文件

在根文件中创建.prettierrc.json 或 .prettierrc.js 文件，并添加格式规则；

> .prettierrc.json 使用 JSON 格式 且 只能包含静态配置，不支持动态逻辑；
> .prettierrc.js 允许使用动态逻辑，可以包含条件语句、函数调用等，更加灵活，适合复杂配置的情况；

-   .prettierrc.json

```
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "arrowParens": "always"
}
```

-   .prettierrc.js

```
module.exports = {
  printWidth: 80,
  tabWidth: 2,
  useTabs: false,
  semi: true,
  singleQuote: true,
  trailingComma: 'all',
  bracketSpacing: true,
  arrowParens: 'always',
  overrides: [
    {
      files: '*.test.js',
      options: {
        printWidth: 100,
      },
    },
  ],
};
```

#### 命令

根据配置的格式的 prettierrc.json 文件，格式化代码；
`npx prettier --write 文件名.类型` prettier 全局命令，根据 eslint 配置规则格式化某个文件；

#### prettier & eslint 规则冲突

解决方案：1. 自定义规则时，eslint.config.js 中的 rules 规则 和 .prettierrc.json 规则保持统一，否则会冲突；2. 工程化时可安装插件避免冲突；

-   `npm install eslint-plugin-prettier -D`

> eslint-plugin-prettier 是一个 ESLint 插件，它将 Prettier 作为一个 ESLint 规则运行，并报告代码格式问题。这使得在使用 ESLint 进行代码检查时，自动应用和报告 Prettier 的格式化规则。

-   `npm install eslint-config-prettier -D`

> eslint-config-prettier 是一个 ESLint 配置扩展库，用于禁用所有不必要或可能与 Prettier 冲突的 ESLint 规则；例如，Prettier 会自动处理代码的行宽、缩进、括号和分号等格式，而 ESLint 中的一些规则也可能会尝试处理这些格式。因此，为避免冲突和重复劳动，eslint-config-prettier 会禁用这些 ESLint 规则。

#### 忽略

在根文件中创建.prettierignore 文件，忽略需要执行格式的文件/目录；

```
// .prettierignore

/dist/_
/html/_
.local
/node*modules/\**
\*\*/_.svg \*_/\_.sh
/public/\*
```

# Husky

[husky](https://typicode.github.io/husky/#/) 是为 git 客户端增加 hook 的插件，在一些 git 操作前自动触发的函数；

## 安装

`npm install husky -D`
在根目录下生成.husky 目录，其中会有 pre-commit 文件，此文件就是在执行推送命令时执行的命令
`npx husky-init`

```

// .husky/pre-commit

#/usr/bin/en sh
. "$(dirname -- "$0")/\_/husky.sh"
npm run fix // 此处的 fix 就是 script 中的脚本命令

```

```

```
