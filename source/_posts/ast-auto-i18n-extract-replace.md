---
title: 基于 AST 实现一键自动提取&替换国际化文案
date: 2024-02-05 15:41:53
tags:
  - Frontend
---
> **背景**：在调研 @formatjs/cli 使用（[使用 @formatjs/cli 进行国际化文案自动提取](https://juejin.cn/post/7324750282859462693) ）过程中，发现有以下需求@formatjs/cli 无法满足：
>
> 1.  id 需要一定的语义化；
> 1.  `defaultMessage`和Id不能直接hash转换；
> 1.  需要直接从中文转换为`formatMessage`；
> 1.  需要显式注入ID（个人觉得编译时注入还是反直觉了一点）；
>
> 另外也是希望借助这个机会好好学一下AST相关知识，所以决定自己写一个AST转换工具。
>
> ***注意：工具无法满足脱离中文文案和文件名的语义化ID需求。**

# 实现效果

![ast-i18n-preview](ast-i18n-preview.webp)

# 如何使用

https://www.npmjs.com/package/core-i18n-cli?activeTab=readme

## 安装

```
npm i -g core-i18n-cli
```

## CLI 参数

### corei18n `-i, --init`

初始化项目，生成配置文件 `corei18n.config.json`，方便根据你的项目需求进行配置。

默认配置包括以下参数：

```
export type ProjectConfig = {
  /** corei18n文件根目录，用于放置提取的langs文件 */
  corei18nDir: string;
  /** 导出的新增文案目录 */
  tempLangFile: string;
  /** 需要做国际化的文件目录 */
  path: string;
  /** 已有文案入口，用于过滤已经存在id的文案，支持js、ts、json */
  localLangFile?: string;
  /** 忽略的文件 string | string[]，参考GlobOptions.ignore */
  ignoreFile?: GlobOptions["ignore"];

  /** 生成id的方式，默认为translate，需要提供baiduApiKey */
  idType: "translate" | "hash";
  /** 百度翻译开放平台配置，参考 https://fanyi-api.baidu.com/product/113 */
  baiduApiKey?: {
    appId: string;
    appKey: string;
  };
  /** 生成id前缀，会以.拼接在id前面 */
  idSuffix?: string;
  /** 替换后是否保留DefaultMessage，默认为false */
  keepDefaultMessage?: boolean;
  /** 格式化代码的选项，参考prettier.options */
  prettierOptions?: Options;
};
```

例子：

```
{
  "corei18nDir": "./.corei18n",
  "tempLangFile": "./.corei18n/tempLang.json",
  "path": "src/pages/**/*.{ts,js,jsx,tsx}",
  "localLangFile": "src/locales/zh-CN.ts",
  "ignoreFile": "src/pages/**/*.d.ts",
  "baiduApiKey": {
    "appId": "",
    "appKey": ""
  },
  "keepDefaultMessage": false,
  "idType": "hash",
  "idSuffix": "tools",
  "prettierOptions": {
    "parser": "typescript",
    "printWidth": 80,
    "singleQuote": true,
    "trailingComma": "all",
    "proseWrap": "never"
  }
}
```

### corei18n `-s, --scan`

一键扫描指定文件夹下的所有中文文案，新增文案会存放至`tempLangFile`

### corei18n `-r, --replace`

一键替换指定文件夹下的所有中文文案


* * *

# 实现过程

## 关于AST

> AST explorer：https://astexplorer.net/

AST（抽象语法树）是源代码的抽象表示形式，它捕捉了代码的结构，而不关心具体的字符格式。AST是在编译器设计和解析源代码时常见的一种数据结构。

在编程语言的编译过程中，源代码首先被解析器解析成一种称为AST的中间表示。AST反映了代码的语法结构，每个节点代表代码中的一个结构元素，如表达式、语句、函数、变量等。这种树状结构使得程序的结构和语法可以被更容易地分析和处理。

## 操作流程


![ast-process.webp](ast-process.webp)
**scan 阶段**

1.  根据`path`和`ignoreFile`得到所有目标文件
1.  对于每个文件，读取文件内容，将代码转换为AST
1.  遍历AST节点，若是`StringLiteral`或者`JSXText`，判断是否符合要求（包含中文且不属于default Message），如果是则记录下来
1.  过滤得到所有新增文案并生成id
1.  将新增文案导出到目标文件

**replace 阶段**

1.  根据`path`和`ignoreFile`得到所有目标文件
1.  获取所有文案对；
1.  对于每个文件，读取文件内容，将代码转换为AST
1.  遍历AST节点，若是`StringLiteral`或者`JSXText`，判断是否符合要求（包含中文且不属于default Message），如果是则替换当前AST节点；
1.  使用`prettier`进行格式化；
1.  根据AST生成代码写入文件路径；

## 依赖的npm包

**babel**

1.  @babel/core：负责整个编译过程的调度和控制；
1.  @babel/parser：用于将 JavaScript 源代码解析成抽象语法树（AST）；
1.  @babel/traverse：用于遍历和修改 AST 的工具；
1.  @babel/types：用于创建、检查和修改 AST 节点

**cli相关**

1.  commander：解析命令行参数和生成帮助信息；
1.  inquirer：交互式命令行工具，用于收集用户输入；
1.  glob：匹配文件路径
1.  lodash：工具库
1.  prettier：代码格式化

## 遇到的问题

### 解决babel/generater生成中文等特殊字符被转义为Unicode编码

```
 const newCode = generator.default(
    ast,
    { retainLines: true, jsescOption: { minimal: true } }, // add this
    code
  ).code;
```

### Error [ERR_REQUIRE_ESM]: require() of ES Module

```
// tsconfig
{
  "compilerOptions": {
    "module": "esnext",
    "target": "esnext",
    "moduleResolution": "node",
  }
}
```

```
// package.json
{
    "type": "module"
}
```

### Error [ERR_MODULE_NOT_FOUND]: Cannot find module

https://github.com/microsoft/TypeScript/issues/16577

https://stackoverflow.com/questions/62619058/appending-js-extension-on-relative-import-statements-during-typescript-compilat

原因：tsc输出时不会添加文件拓展名，nodejs运行时不会自动匹配文件拓展名

尝试在文件首行添加 --experimental-specifier-resolution=node 无效

使用`tsc-alias`为导出文件添加js后缀后解决：

```
npm install --save-dev tsc-alias
```

```
// tsconfig.json
{
  "compilerOptions": {
    ...
  },
  "tsc-alias": {
    "resolveFullPaths": true,
    "verbose": false
  }
}
```

```
"scripts": {
    "compile": "tsc && tsc-alias"
}
```

# 参考

-   [小玩具:利用AST实现代码文案的自动翻译与替换 - 掘金](https://juejin.cn/post/7176161125044584507)
-   https://github.com/alibaba/kiwi/tree/master/kiwi-cli