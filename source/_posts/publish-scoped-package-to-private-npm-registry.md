---
title: 如何在 npm 私有源发布 scope 包
date: 2024-05-20 16:15:31
tags:
  - Frontend
categories: 
  - Frontend
---
## 背景

当开发公司内部基建组件时，需要发布到公司的 npm 私有源，如 `@myorg/mypackage`。

---

## 什么是 scope 包

作用域包（Scoped packages）是 npm 包管理系统中的一种特殊类型的包，它们具有类似命名空间的功能，以防止包名称冲突，并使得管理相关的包更加方便。

Scoped packages 的名称以 `@scope/` 开头，其中 `scope` 是包的作用域名称。例如，一个名为 `@myorg/mypackage` 的包具有 `@myorg` 作用域。

Scoped packages 通常用于组织包，例如组织内部的私有包、团队开发的共享包等。它们还可以被用于创建公开发布的包，但是它们的名称始终与其所属的作用域相关联。

使用作用域包可以让开发者更好地组织和管理自己的包，避免了不同包之间的命名冲突，并且使得包的归属更加清晰。

---

## 安装 nrm

NRM 是一个 Node.js 包管理工具（npm 的源管理器），可以帮助开发人员在不同的 npm 源之间快速切换。

```bash
npm install -g nrm
```

安装完成后，可以使用以下命令列出当前可用的 npm 源：

```bash
nrm ls        # 列出当前可用的 npm 源
nrm current   # 查看当前使用的源
```

切换到其他源，例如淘宝源：

```bash
nrm use taobao
```

添加自定义源：

```bash
nrm add example https://example.com/npm/
```

---

## 发布 scope 包

### 1. 切换到私有源

```bash
nrm use example
```

### 2. 注册账号

```bash
npm adduser
```

### 3. 登录

```bash
npm login
```

### 4. 检查登录状态

```bash
npm whoami
```

### 5. 发布包

```bash
npm publish
```

如果出现错误：

```
npm ERR! 403 In most cases, you or one of your dependencies are requesting a package version that is forbidden by your security policy.
```

可以参考：  
[https://stackoverflow.com/questions/62830477/how-to-debug-npm-err-403-in-most-cases-you-or-one-of-your-dependencies-are-re](https://stackoverflow.com/questions/62830477/how-to-debug-npm-err-403-in-most-cases-you-or-one-of-your-dependencies-are-re)

可能原因：
- 存在同名包  
- 没有发布该包的权限（非该 npm 包作者 / 无私有源发布权限）  
- 包版本号错误  
- 邮箱未验证  

---

## 使用 scope 包

`.npmrc` 文件是 npm 的配置文件，用于配置 npm 的行为，例如源地址、代理、权限等。

在 `.npmrc` 文件中添加如下配置：

```bash
@example:registry=https://your-private-registry-url
```

其中 `@example` 是你的作用域包名，`https://your-private-registry-url` 是你的私有 npm 源地址。

这样，当你安装或发布 `@example` 作用域的包时，npm 将自动使用该私有源。
