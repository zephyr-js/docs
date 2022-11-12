---
title: Zephyr Common
description: Zephyr Common
layout: ../../../../layouts/DocLayout.astro
---

## ZephyrRoute

API route interface in Zephyr application.

**Type**

```ts
interface ZephyrRoute<TRequest extends ZephyrBaseRequest = any, TResponse = any>
  extends ZephyrRouteHooks {
  name?: string;
  method: RouteMethod;
  path: string;
  schema?: AnyZodObject;
  handler: ZephyrHandler<TRequest, TResponse>;
}
```

## ZephyrHandler

The main handler of an API route, takes two arguments:

- `req` [`ZephyrRequest`](/docs/common/zephyr-request)
- `res` [`ZephyrResponse`](/docs/common/zephyr-response)

**Type**

```ts
type ZephyrHandler<
  TRequest extends ZephyrBaseRequest = any,
  TResponse = any,
> = (
  req: ZephyrRequest<TRequest>,
  res: ZephyrResponse<TResponse>,
) => any | Promise<any>;
```

## ZephyrRequest

A wrapper around Express [`Request`](https://expressjs.com/en/4x/api.html#req), supports all of its properties.

**Type**

```ts
interface ZephyrBaseRequest {
  params?: object;
  query?: object;
  body?: object;
}

type ZephyrRequest<
  T extends ZephyrBaseRequest = any,
  TResponse = any,
> = ExpressRequest<T['params'], TResponse, T['body'], T['query']>;
```

## ZephyrResponse

A wrapper around Express [`Response`](https://expressjs.com/en/4x/api.html#res), supports all of its properties.

**Type**

```ts
type ZephyrResponse<T = any> = ExpressResponse<T>;
```
