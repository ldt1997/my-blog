---
title: JSON 格式化高亮展示
date: 2024-04-25 15:57:48
categories: 
  - Frontend
tags:
  - Frontend
---
## 背景

项目需要展示 JSON，想找找看有没有用户体验好且较轻量的 npm 库。

---

## 原生方案

如果不需要高亮展示，其实可以直接用原生的 `JSON.stringify` 实现：

```tsx
const JSONResult = ({ result }: { result?: any }) => {
  return (
    <code style={{ whiteSpace: 'pre-line' }}>
      {result ? JSON.stringify(result, null, 2) : ''}
    </code>
  );
};

export default JSONResult;
```

## 使用 react-json-view
📦 npm 地址：
https://www.npmjs.com/package/react-json-view