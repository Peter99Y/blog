---
title: Eslint
---

# 项目配置流程

1. vsCode 中 先安装 eslint 和 prettier 扩展；

2. 创建 vue 项目
   `npm create vite@latest` or `npm create vite my-vue-project --template vue-ts`

3. 安装 eslint
   `npm install eslint -D`

4. 生成 eslint.config.js 配置文件
   `npx eslint --init`

5. 安装 prettier 和相关插件(非必选)

6. 安装 husky

---

# Eslint

检测 js 代码语法和潜在 bug，但无法检测 css、html、vue 文件等；

插件安装: `npm i eslint -D`;

互动式命令，选择需要的配置 并 生成 eslint.config.js 配置文件:
`npx eslint --init` or `npm init @eslint/config@latest`

```
// 互动式命令

How would you like to use ESLint? problems - eslint检测语法，检测语法并警告，检测语法并警告并修复

What type of modules does your project use? - 选择 ECMA or CommonJs 模块

Which framework does your project use? - vue，最后会提示安装 eslint-plugin-vue

Does your project use Typescript? - 会自动安装typescript插件， 最后会提示安装typescript-eslint

Where does your code run? - 运行在browser 或 node环境
```

```
// eslint-config.js; 根据全部选择的结果，生成对应的配置；

import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint";
import pluginVue from "eslint-plugin-vue";

export default [
  {files: ["**/*.{js,mjs,cjs,ts,vue}"]},
  {languageOptions: { globals: globals.browser }},
   pluginJs.configs.recommended,            // 使用 JavaScript 的推荐规则配置
  ...tseslint.configs.recommended,          // 使用 TypeScript 的推荐规则配置
  ...pluginVue.configs["flat/essential"],   // 使用 vue 规则配置插件
];
```

## .mjs 文件 & .js 文件 区别

- 根据安装 eslint 版本：eslint 9.0 版本前生成 eslint.config.mjs，9.0 版本后生成 eslint.config.js

- 根据安装时选择配置的 module 类型

```
What type of modules does your project use?
> JavaScript modules (import/export)
  CommonJS (require/exports)
```

- 选择 CommonJS 模块，将生成 eslint.config.js 文件

```
// eslint.config.js

module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  rules: {
    'no-unused-vars': 'warn',
  },
  ...
};
```

- 选择 JavaScript modules 模块，将生成 eslint.config.mjs 文件

```
// eslint.config.mjs

export default {
  env: {
    browser: true,
    es2021: true,
  },

  // extends是扩展eslint检测规范范围，且省略了前缀 'eslint-config-prettier'
  extends: [
    'eslint:recommended',  // eslint默认推荐的规则
    'plugin:vue/vue3-strongly-recommended', // 使用 vue3强烈推荐规则
    'prettier'  // 使用prettier规则
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
// eslint.config.js （ESlint 9.0版本后文件结构）

import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint";
import pluginVue from "eslint-plugin-vue";

export default [
	{
		// 指定eslint检测哪些文件
		files: ["src/**/*.{js,ts,vue}"],  // 例如：检测src下以及嵌套的文件夹下，所有的js、ts、和vue文件；
                files: ['src/**/*.ts'],           // 例如：检测src下以及嵌套的文件夹下，所有的ts文件；

		// 指定eslint忽略检测哪些文件
		ignores: [
			".git/",              // 忽略 .git 目录及其所有内容
			".config/",           // 忽略 .config 目录及其所有内容
			"**/node_modules/",   // 忽略项目中任意位置的 node_modules 目录及其所有内容
		],

		// 插件省略了前缀 'eslint-plugin-' > 'eslint-plugin-vue'
		plugins: ["vue"],

                // 自定义规则，会覆盖extends继承的第三方库规则，权限比第三方库高
		rules: {
			"example/dollar-sign": "error",
		},

		// 可配置解析器
		languageOptions: {
                  ecmaVersion: 'latest',
                  sourceType: 'module',
                  globals: globals.browser,
                  parser: '@typescript-eslint/parser',
			// parserOptions: {
			// 	requireConfigFile: false,
			// 	babelOptions: {
			// 		babelrc: false,
			// 		configFile: false,
			// 		presets: ["@babel/preset-env"],
			// 	},
			// },
		},
	},

	pluginJs.configs.recommended,
	...tseslint.configs.recommended,
	...pluginVue.configs["flat/essential"],
];
```

> ps: ignores 涉及到的符号
> . 这个点通常表示一个隐藏目录或文件
> 例如 .git .vscode 文件；

> / 匹配目录。
> 例如 .config/ 就是忽略目录，和它目录下其所有内容；

> \* 匹配任意数量的字符（除了斜杠 /）。
> 1 例如 \*.js: 匹配所有以 .js 结尾的文件,
> 2 例如 .config/\* 忽略.config 目录下的所有文件，但不包括子目录下的内容。如 .config 目录中有子目录不会被忽略。

> \*\* 匹配任意数量的目录和子目录。
> 1 例如 \*\*/node_modules/ 匹配项目中任意位置（如根目录或嵌套在其他目录中）的 node_modules 都会忽略。
> 2 例如 files: ["src/**/*.{js,ts,vue}"]，src/表示匹配 src 目录，\*\*/ 表示匹配 src 目录下的任意层级的子目录，\*.{js,ts,vue} 表示匹配文件名可以是任何字符，只要扩展名是 .js、.ts 或 .vue 之一；

---

## 命令

全局命令
`eslint demo.js` - 执行检测某个文件；
`eslint demo.js --fix` - 执行检测某文件并强制修复 (如根据规则把双引号 变 单引号等)；

弊端：每次手动操作命令检测文件中的 js 语法；可在 vsCode 中安装 eslint 插件，即可在编码过程中检测错误；

## script

添加脚本命令

```
"scripts": {
  // eslint校验 src 目录下的文件
  "lint": "eslint src",
  "lint": "eslint \"src/**/*.{js,ts,vue}\"",

  // eslint修复 src 目录下的文件
  "fix": "eslint src --fix"
  "fix": "eslint \"src/**/*.{js,ts,vue}\" --fix"
},
```

## ignore

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

## rules

关闭规则："off" / 0
打开规则作为警告："warn" / 1
打开规则作为错误： "error" / 2

- `npm install eslint-config-airbnb-base eslint-plugin-import -D`

  > eslint-config-airbnb-base 代码配置插件，当前业界最火的 ESlint 规范就是 Airbnb;（需依赖 eslint-plugin-import）

- 自己配置规则

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

## eslint 与 typescript 相关的插件

- @typescript-eslint/parser: ESLint 用来解析 typescript,从而检测 ts 代码；
- @typescript-eslint/eslint-plugin: ESLint 插件，包含各类定义好的检测 ts 的规范；
- @types/eslint: ts eslint 声明插件；
- @types/node: ts node 声明插件；

<!-- npm install typescript -D 是否已选安装过了 -->
<!-- npm install @types/eslint @types/node - D-->
<!-- --legacy-peer-deps 安装 @typescript-eslint/parser时会报错，此时加上 -->

`npm install @typescript-eslint/parser  @typescript-eslint/eslint-plugin --legacy-peer-deps -D`

# Prettier

[prettier](https://prettier.io/) 是代码格式化的编码风格工具，比如单行的代码长度、tab 长度、空格等；
主要为了团队代码风格统一，非必须安装；

#### 安装

- `npm install prettier -D`

#### 创建 prettierrc 文件

在根文件中创建.prettierrc.json 或 .prettierrc.js 文件，并添加格式规则；

> PS
> .prettierrc.json 使用 JSON 格式 且 只能包含静态配置，不支持动态逻辑；
> .prettierrc.js 允许使用动态逻辑，可以包含条件语句、函数调用等，更加灵活，适合复杂配置的情况；

- .prettierrc.json

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

- .prettierrc.js

```
module.exports = {
  printWidth: 80, // 一行最多100个字符
  tabWidth: 2, // 空格缩进
  useTabs: false, // 不适用缩进符，而使用空格
  semi: true, // 分号
  singleQuote: true, // 单引号
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

prettier 全局命令

`npx prettier --write 某文件`
根据配置的 prettierrc.json 文件，格式化代码；

#### prettier & eslint 规则冲突

1. 自定义规则时，eslint.config.js 中的 rules 规则 和 .prettierrc.json 规则保持统一，否则会冲突；
2. 工程化时可安装插件避免冲突；

`npm install eslint-plugin-prettier -D`
`npm install eslint-config-prettier -D`

> PS：eslint-plugin-prettier 是一个 ESLint 插件，它将 Prettier 作为一个 ESLint 规则运行，并报告代码格式问题。这使得在使用 ESLint 进行代码检查时，自动应用和报告 Prettier 的格式化规则。

> PS： eslint-config-prettier 是一个 ESLint 配置扩展库，用于禁用所有不必要或可能与 Prettier 冲突的 ESLint 规则；例如，Prettier 会自动处理代码的行宽、缩进、括号和分号等格式，而 ESLint 中的一些规则也可能会尝试处理这些格式。因此，为避免冲突和重复劳动，eslint-config-prettier 会禁用这些 ESLint 规则。

#### ignore

在根文件中创建.prettierignore 文件，prettier 格式化时会忽略需要执行的文件/目录；

```
// .prettierignore

/dist/*
/html
.local
/node*modules/**
**/*.svg
/public/*
```

# Husky

[husky](https://typicode.github.io/husky/#/) 是为 git 客户端增加 hook 的插件，在一些 git 操作前自动触发的函数；

## 安装

`npm install husky -D`

### 命令

- 1. 在根目录下生成.husky 目录，其中会有 pre-commit 文件，此文件就是在执行推送命令时执行的命令

`npx husky-init`

or

添加脚本命令，再执行脚本命令也能自动生成 .husky/pre-commit 文件；
`husky install`

### 添加执行命令

- 2. 给 .husky/pre-commit 文件添加执行命令

`npx husky add .husky/pre-commit "npm run fix"`

or

直接在 .husky/pre-commit 文件中添加命令

```
#/usr/bin/en sh
. "$(dirname -- "$0")/\_/husky.sh"

npm run fix         // 此处的 fix 就是 script 中的脚本命令"fix": "eslint src --fix";
```

- 3. git commit -m 'feat: 新增'，git 每次提交时执行脚本命令，没问题的文件直接提交到暂存区，有问题文件将报错；

---

## 安装 commit 限制

commitlint 对 git commit 提交注释进行校验；

`npm install @commitlint/config-conventional @commitlint/cli -D`

### 命令

生成 .husky/commit-msg 文件
`npx husky add .husky/commit-msg "npx --no -- commitlint --edit ${1}"`

```
#!/usr/bin/env sh
. "$(dirname "$0")/h"

npx --no -- commitlint --edit
```

### 配置文件

在根目录中添加 commit 配置文件 commitlint.config.cjs

```
module.exports = {
	extends: ['@commitlint/config-conventional'],
	rules: {
		'type-enum': [
			2,
			'always',
			[
				// 编译相关的修改，例如发布版本，对项目构建或者依赖的改动
				'build',
				// 新功能(feature)
				'feat',
				// 修复bug
				'fix',
				// 更新某功能
				'update',
				// 重构
				'refactor',
				// 文档
				'docs',
				// 构建过程或者辅助工具的变动,如增加依赖库等
				'chore',
				// 不影响代码运行的变动
				'style',
				// 撤销commit,回滚到上一个版本
				'revert',
				// 性能优化
				'perf',
				// 测试(单元,集成测试)
				'test',
			],
		],
		'type-case': [0],
		'type-empty': [0],
		'scope-empty': [0],
		'scope-case': [0],
		'subject-full-stop': [0, 'never'],
		'subject-case': [0, 'never'],
		'header-max-length': [0, 'always', 74],
	},
};
```

- git commit -m 'feat: 新增'，git 每次提交时执行脚本命令；
