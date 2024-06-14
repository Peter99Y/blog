---
title: Eslint
---

# Eslint

eslint 是检测语法的工具，确保的是代码正常运行；

## 初始化

###### 普通安装

`npm init @eslint/config@latest`;

###### 工程化安装

`npm i eslint -D`;
`npx eslint --init` 生成配置文件;

eslint 9.0 版本前生成 eslint.config.mjs，9.0 版本后生成 eslint.config.js

```
// eslint.config.js

import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint";
import pluginVue from "eslint-plugin-vue";


export default [
  {languageOptions: { globals: globals.browser }},
  pluginJs.configs.recommended, // 使用 JavaScript 的推荐规则配置
  ...tseslint.configs.recommended, // 使用 TypeScript 的推荐规则配置
  ...pluginVue.configs["flat/essential"], // 使用 vue 规则配置插件
];
```

## 忽略 文件/目录

```
// eslint.config.js
export default [
    {
        // 忽略与eslint.config.js文件同级的
        ignores: [
            ".config/",         // config目录
            ".config/*",        // config目录下所有文件
            "**/node_modules/",
            ".git/"             // 后缀.git文件
        ]
    }
];
```

## rules 规则

关闭规则："off" / 0
打开规则作为警告："warn" / 1
打开规则作为错误： "error" / 2

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
];
```

script 脚本

添加执行 eslint 脚本命令

```
  "scripts": {
    "lint": "eslint src", // 校验src目录下的文件
    "fix": "eslint src --fix" // 修复src目录下的文件
  },
```

# prettier

prettier 是格式化代码美观的工具；

## 安装

`npm install prettier eslint-plugin-prettier eslint-config-prettier -D`

在根文件中创建.prettierrc.json 文件，并添加格式规则

```
// .prettierrc.json

{
    "singleQuote": true,
    "semi": true,
    "tabWidth": 4,
}
```

在根文件中创建.prettierignore 文件，忽略需要执行格式的文件

```
// .prettierignore

/dist/*
/html/*
.local
/node_modules/**
**/*.svg
**/*.sh
/public/*
```

# husky

推送远程仓库时，提前格式化代码并推送

## 安装

`npm install husky -D`
在根目录下生成.husky 目录，其中会有 pre-commit 文件，此文件就是在执行推送命令时执行的命令
`npx husky-init`


```
// .husky/pre-commit

#/usr/bin/en sh
. "$(dirname -- "$0")/_/husky.sh"
npm run fix // 此处的fix就是script中的脚本命令
```