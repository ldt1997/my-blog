---
title: 2 分钟为你的网站加上暗黑模式（基于Ant Design V5）
date: 2023-11-17 16:00:00
tags:
  - Frontend
---
## 前言
> **夜间模式**（英语：night mode）有时亦叫做**黑暗模式**（black mode）或者深色模式（Light-on-dark color scheme），是一种高对比度，或者反色模式的屏幕显示模式。

效果展示（基于github上的开源后台项目）：

![Dark Mode Preview](darkmode-preview.webp)

## Antd 暗黑主题
很简单，两行代码搞定

```js
// main.tsx
import { ConfigProvider, theme } from "antd"

const App = () => {
  const { theme: themeState } = useAppSelector((state) => state.system)
  return (
    <ConfigProvider
      theme={{
        algorithm: themeState === "dark" ? theme.darkAlgorithm : undefined,
      }}
    >
      <AppRoute />
    </ConfigProvider>
  )
}

root.render(
  <Provider store={store}>
    <App />
  </Provider>
)
```

## 调整其他页面风格
之前写死的颜色样式,现需要根据亮暗主题切换，这里使用css变量

```js
// src/assets/styles/style.scss

body {
  --bg-color: #fff;
  --color-text-1: rgba(0,0,0, 0.88)
  --color-text-2: rgba(0,0,0, 0.45)
}

body[tomato-theme="dark"] {
  --bg-color: #141414;
  --color-text-1: rgba(255, 255, 255, 0.85);
  --color-text-2: rgba(255, 255, 255, 0.45)
}

```


```js
// src/componments/header/style.scss

.my-header {
-  background: #fff !important;
+  background: var(--bg-color);
   }
```

切换主题

```ts
// src/componments/header/index.tsx

const HomeHeader: React.FC<Props> = function () {
 const { theme } = useAppSelector((state) => state.system)
 
 // ...
 
 function handleTheme() {
    const newTheme: string = theme === "light" ? "dark" : "light"
    dispatch(SET_THEME(newTheme))
    if (newTheme === "dark") {
      document.body.setAttribute("tomato-theme", newTheme)
    } else {
      document.body.removeAttribute("tomato-theme")
    }
  }
}

// ...

return (
//...
    <li onClick={handleTheme}>
          {theme === "dark" ? <BulbFilled /> : <BulbOutlined />}
    </li>
// ...
)
```

## 跟随系统主题自动切换

```js
const darkThemeMq = window.matchMedia("(prefers-color-scheme: dark)");

darkThemeMq.addListener(e => {
 if (e.matches) {
   document.body.setAttribute('tomato-theme', 'dark');
 } else {
    document.body.removeAttribute('tomato-theme');
  }
});
```

## 参考
- https://juejin.cn/post/7242284648021164091
- [Arco Design](https://arco.design/react/docs/dark#%E5%A6%82%E4%BD%95%E5%88%87%E6%8D%A2%E6%9A%97%E9%BB%91%E6%A8%A1%E5%BC%8F)