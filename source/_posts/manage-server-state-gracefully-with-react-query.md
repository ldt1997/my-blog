---
title: 使用 React Query 更优雅地管理你的服务端状态
date: 2025-1-28 16:19:29
categories: 
  - Frontend
tags:
  - Frontend
---
在使用 React Hooks 编写组件时，我们常需要手动维护来自服务器的处理状态（server side status)。 处理异步数据时，我们需要考虑很多事情，例如更新，缓存或重新获取。 使用 React-Query 能够更高效的帮你管理服务端的状态。

官方文档：https://tanstack.com/query/v3/docs/react/overview

中文文档：https://cangsdarm.github.io/react-query-web-i18n/react/

GitHub：https://github.com/tanstack/query

下面是一个简单的从服务端获取数据的案例：

```tsx
function App() {

  const [data, updateData] = useState(null);
  const [isError, setError] = useState(false);
  const [isLoading, setLoading] = useState(false);
  
  useEffect(async () => {
    setError(false);
    setLoading(true);
    try {
      const data = await axios.get('/api/user');
      updateData(data);
    } catch(e) {
      setError(true);
    }
    setLoading(false);
  }, [])

}
```

## 什么是 React Query （What）

React Query 通常被描述为 React 缺少的数据获取(data-fetching)库，但是从更广泛的角度来看，它使 React 程序中的获取，缓存，同步和更新服务器状态变得轻而易举。

### 核心概念

#### 查询（Queries）

React Query 引入了 "查询" 这一概念，它代表一个数据获取操作。通过使用 useQuery 钩子函数，可以轻松地发起数据请求并处理返回结果。React Query 将数据自动缓存，减少不必要的网络请求，同时提供了强大的缓存控制选项。

#### 变更（Mutations）

当需要进行数据变更操作时，比如创建、更新、删除等，React Query 提供了 useMutation 钩子函数。它简化了数据变更的处理，并且自动更新相关的查询缓存，确保应用程序状态的一致性。

#### 查询客户端（Query Client）

QueryClient 是 React Query 的核心管理器，负责维护全局的查询状态和缓存。通过创建一个全局的 Query Client，可以确保在整个应用程序中共享相同的查询状态，从而实现数据的一致性管理。

### 常用参数配置

重要的默认配置 Important Defaults | TanStack Query 中文文档

- **queryKey**：查询键值，这个唯一键值将在内部用于重新获取数据、缓存和在整个程序中共享该查询信息  
- **staleTime**：设置数据保持新鲜时间，在该时间内，我们认为数据是新鲜的，不会重新发起请求，默认为0  
- **cacheTime**：设置数据缓存时间，超过该时间，我们会清空该条缓存数据，默认为5分钟

```tsx
// 使用 useQuery 钩子，配置 staleTime 和 cacheTime
const { data } = useQuery('exampleQueryKey', fetchData, {
  staleTime: 5000,  // 数据被视为过时的时间，单位是毫秒，这里设置为5秒
  cacheTime: 60000, // 数据在缓存中的保留时间，单位是毫秒，这里设置为1分钟
});
```

enabled：如果为“false”的话，“useQuery”不会触发，需要使用其返回的“refetch”来触发操作  
retry：失败重试次数，默认 3次  
refetchOnWindowFocus：当用户聚焦到浏览器窗口时，是否重新获取数据，默认为true  
refetchOnReconnect：当浏览器重新连接到网络时，是否重新获取数据，默认为true  
refetchOnMount：当组件首次挂载时，是否在组件挂载时重新获取数据，默认为true

## 为什么是...? （Why）

React Query 的设计理念和功能使其在前端开发中具有诸多优势，使开发人员能够更轻松地处理数据，提高应用程序性能。

### 自动缓存和数据预取

React Query 提供了自动的缓存机制，它会自动处理数据的缓存和过期。通过合理使用 staleTime 和 cacheTime 等配置选项，可以精确地控制数据缓存的时间和更新策略。

### 实时更新和无缝状态管理

React Query 通过使用 QueryClient 来管理全局状态，确保了整个应用程序的状态一致性。当一个查询的数据发生变化时，React Query 自动更新相应的组件，实现了实时的数据更新。

### 高度灵活的查询配置

React Query 提供了丰富的查询配置选项，可以根据实际需求进行灵活定制。通过配置 queries 和 mutations，可以控制查询的行为，例如缓存、重试、轮询等。

### 轻松处理并发请求

React Query 能够轻松处理并发请求，并自动优化网络请求，避免不必要的重复请求。

### 强大的开发者工具支持

React Query 提供了 React Query Devtools，可以在浏览器中直观地监视和调试应用程序中的查询和变更。

https://npmtrends.com/@tanstack/react-query-vs-mobx-vs-redux-vs-swr

![trends.png](trends.png)

以及 @umijs/max 已内置 react-query，这是 umi 团队推荐的请求状态管理方式。  
https://github.com/umijs/umi/discussions/10410

![umi.png](umi.png)

## 为什么不是...?

### Redux（mobx、dva）

TanStack Query 是一个服务器状态库，负责管理服务器和客户端之间的异步操作。  
Redux、MobX、Zustand 等是客户端状态库，可用于存储异步数据，但与 TanStack Query 相比效率较低。

### ahooks useRequest

React Query 更注重在大型应用中提供强大的数据管理和缓存解决方案，而 useRequest 更注重于提供一个简单、轻量级的方式来处理数据请求。

| 功能 | react-query | useRequest |
|------|--------------|-------------|
| 自动请求/手动请求 | √ | √ |
| 依赖请求 | √ | √ |
| 轮询 | √ | √ |
| 防抖/节流 | √ | √ |
| 屏幕聚焦重新请求 | √ | √ |
| 错误重试 | √ | √ |
| Loading Delay | √ | √ |
| 缓存 | √ | √ |
| 开发者工具 | √ | √ |
| 网络模式 | √ | *但不支持动态cacheKey* |
| 分页/无限查询 | √ | ❌ |
| 预取数据 | √ | ❌ |

## 如何使用 （How）

### 安装

Umi：https://umijs.org/docs/max/react-query

```tsx
export default {
  reactQuery: {},
}
```

其他框架：

```bash
npm i @tanstack/react-query
# or
yarn add @tanstack/react-query
```

### 快速入门

```tsx
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
import { getTodos, postTodo } from "../my-api";

// 创建一个 client
const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  );
}

function Todos() {
  const queryClient = useQueryClient();
  const query = useQuery({ queryKey: ['todos'], queryFn: getTodos });
  const mutation = useMutation({
    mutationFn: postTodo,
    onSuccess: () => queryClient.invalidateQueries(["todos"]),
  });

  return (
    <div>
      <ul>
        {query.data.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>
      <button
        onClick={() => mutation.mutate({ id: Date.now(), title: "Do Laundry" })}
      >
        Add Todo
      </button>
    </div>
  );
}
```

Demo with umi@4  
https://gitlab.bj.sensetime.com/luodantong/my-react-query-demo

## 原理 （Implement）
> 以 `useQuery` 为例

### QueryClientProvider
`packages/react-query/src/QueryClientProvider.tsx`
利用 `React.Context` 下发 `QueryClient` 实例

```jsx
'use client'
import * as React from 'react'
import type { QueryClient } from '@tanstack/query-core'

export const QueryClientContext = React.createContext<QueryClient | undefined>(
  undefined,
)

export const useQueryClient = (queryClient?: QueryClient) => {
  const client = React.useContext(QueryClientContext)

  if (queryClient) {
    return queryClient
  }

  if (!client) {
    throw new Error('No QueryClient set, use QueryClientProvider to set one')
  }

  return client
}

// 提供一个react context上下文，value为QueryClient实例
export const QueryClientProvider = ({
  client,
  children,
}: QueryClientProviderProps): JSX.Element => {
  React.useEffect(() => {
    client.mount()
    return () => {
      client.unmount()
    }
  }, [client])

  return (
    <QueryClientContext.Provider value={client}>
      {children}
    </QueryClientContext.Provider>
  )
}

```

### QueryClient
`packages/query-core/src/queryClient.ts`
QueryClient类，维护queryCache，并向外暴露 mount、unmount、invalidateQueries等方法

```jsx
export class QueryClient {
  #queryCache: QueryCache

  constructor(config: QueryClientConfig = {}) {
    // 在构造器中初始化queryCache
    this.#queryCache = config.queryCache || new QueryCache() // new Map<string, Query>()
  }
  
  mount(): void {}
  unmount(): void {}
  
  // ...
  
  invalidateQueries(
    filters: InvalidateQueryFilters = {},
    options: InvalidateOptions = {},
  ): Promise<void> {
    return notifyManager.batch(() => {
      this.#queryCache.findAll(filters).forEach((query) => {
        query.invalidate() // 将该query的state.isInvalidated设置为false
      })

      if (filters.refetchType === 'none') {
        return Promise.resolve()
      }
      const refetchFilters: RefetchQueryFilters = {
        ...filters,
        type: filters.refetchType ?? filters.type ?? 'active',
      }
      // 重新请求
      return this.refetchQueries(refetchFilters, options)
    })
  }
  
 }
```
---

### useBaseQuery
`packages/react-query/src/useBaseQuery.ts`
useBaseQuery会创建一个observer，并添加到与之对应的Query。observer 会订阅 Query 的状态，当这些状态变化时，触发 React 强制更新。

```jsx
export function useBaseQuery(
  options,
  Observer,
  queryClient?: QueryClient,
) {
  
  const client = useQueryClient(queryClient)

  // ...

  // 实例一个观察者对象 observer，并根据 queryKey 绑定到唯一的 Query 实例
  // observer 会订阅 query 的状态，当这些状态变化时，触发 React 强制更新
  const [observer] = React.useState(
    () =>
      new Observer<TQueryFnData, TError, TData, TQueryData, TQueryKey>(
        client,
        defaultedOptions,
      ),
  )

  // 构造result { status , data, isLoading , ...}
  const result = observer.getOptimisticResult(defaultedOptions)

  // 使用 useSyncExternalStore 钩子，通过观察者的订阅，在数据发生变化时通知组件重新渲染
  // https://zh-hans.react.dev/reference/react/useSyncExternalStore
  React.useSyncExternalStore(
    React.useCallback(
      (onStoreChange) => {
        const unsubscribe = isRestoring
          ? () => undefined
          : observer.subscribe(notifyManager.batchCalls(onStoreChange))

        // Update result to make sure we did not miss any query updates
        // between creating the observer and subscribing to it.
        observer.updateResult() // 更新

        return unsubscribe
      },
      [observer, isRestoring],
    ),
    () => observer.getCurrentResult(),
    () => observer.getCurrentResult(),
  )

  // 确保选项更改时不会通知更新
  React.useEffect(() => {
    // Do not notify on updates because of changes in the options because
    // these changes should already be reflected in the optimistic result.
    observer.setOptions(defaultedOptions, { listeners: false })
  }, [defaultedOptions, observer])

  // Handle result property usage tracking
  return !defaultedOptions.notifyOnChangeProps
    ? observer.trackResult(result)
    : result
}

```
### 完整流程：
1. 与请求相关的底层逻辑都封装在了 Query 中，直接与服务端交互
2. 同时 Query 又被保管在外部 store 的 queryClient 中
3. queryClient 会在 App 顶层使用 Provider 全局注入到 React
4. 组件使用 hook 与 Query 建立连接，订阅状态触发更新

![process.png](process.png)
## *V5版本*

[Announcing TanStack Query v5 | TanStack Blog （10/17/2023）](https://tanstack.com/blog/announcing-tanstack-query-v5)

v5 延续了 v4 的历程，更小、更直观，并统一了 useQuery API。

---

## 参考

- [为什么我放弃使用 RTK Query - 掘金](https://juejin.cn/post/7240857449044492347)  
- [Why We Replaced Redux by React Query](https://www.qovery.com/blog/why-we-replaced-redux-by-react-query)
- [React Query 原理与设计 - 掘金](https://juejin.cn/post/7169515109172609032)  
- [面试官: 你真的知道 React Query 吗? - 掘金](https://juejin.cn/post/7208741162520739896?searchId=20240105171000CE159A18C9D49F93973A)  
