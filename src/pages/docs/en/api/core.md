---
title: Zephyr Core
description: Zephyr Core
layout: ../../../../layouts/DocLayout.astro
---

## Application

### createApp()

Create an application instance.

**Type**

```ts
function createApp(
  options: ZephyrApplicationOptions,
): Promise<ZephyrApplication>;
```

**Details**

The first argument accepts options such as application level lifecycle hooks.

**Example**

```ts
import { createApp } from '@zephyr-js/core';

const app = await createApp({
  /* options */
});
```

## Define Route

### Basic Usage

We can declare API route by exporting HTTP verb as function and returning the route specification declared with the `defineRoute()` function.

```ts title="src/routes/index.ts"
import { defineRoute } from '@zephyr-js/core';

export function GET() {
  return defineRoute({
    handler(req, res) {
      return res.json({ foo: 'bar' });
    },
  });
}
```

The `handler` prop is the main handler of the request which receives two arguments. `req` is an instance of `ZephyrRequest` while `res` is an instance of `ZephyrResponse`. They supports all the properties of Express [`Request`](https://expressjs.com/en/4x/api.html#req) and Express [`Response`](https://expressjs.com/en/4x/api.html#res).

The export name represents the request method, in this case is a `GET` request.

Request path will be inferred from the file name. `src/routes/index.ts` will be mapped to `/`.

### Declaring Schema

The `schema` prop in `defineRoute` function accepts a [Zod Schema](https://zod.dev/?id=objects), which will be used to **validate** the request and **provide typing** the route handler.

The supported fields are:

- `params`
- `query`
- `body`
- `response`

```ts title="src/routes/index.ts"
import { defineRoute } from '@zephyr-js/core';
import { z } from 'zod';

export const POST = defineRoute({
  schema: z.object({
    body: z.object({
      foo: z.string(),
      bar: z.string(),
    }),
    response: z.object({
      foo: z.string(),
      bar: z.string(),
    }),
  }),
  handler(req) {
    const { foo, bar } = req.body; // Type checked and validated
    return { foo, bar }; // Type checked
  },
});
```

## Lifecycle Hooks

### onBeforeHandle()

Registers a callback to be called before the route handler is executed.

**Type**

```ts
function onBeforeHandle(req: ZephyrRequest, res: ZephyrResponse): void;
```

**Example**

Logging request before handler:

```ts
import { defineRoute } from '@zephyr-js/core';

defineRoute({
  onBeforeHandle(req) {
    console.log(req);
  },
  handler(req, res) {
    return res.send('OK');
  },
});
```

### onBeforeValidate()

Registers a callback to be called before request validation occurs.

**Type**

```ts
function onBeforeValidate(req: ZephyrRequest, res: ZephyrResponse): void;
```

**Example**

Logging request before validation:

```ts
import { defineRoute } from '@zephyr-js/core';

defineRoute({
  onBeforeValidate(req) {
    console.log(req);
  },
  handler() {
    return 'OK';
  },
});
```

### onRequest()

Registers a callback to be called when receiving a request.

**Type**

```ts
function onRequest(req: ZephyrRequest, res: ZephyrResponse): void;
```

**Example**

Logging request on request:

```ts
import { defineRoute } from '@zephyr-js/core';

defineRoute({
  onRequest(req) {
    console.log(req);
  },
  handler() {
    return 'OK';
  },
});
```

### onResponse()

Registers a callback to be called right after response is sent.

**Type**

```ts
function onResponse(req: ZephyrRequest, res: ZephyrResponse): void;
```

**Example**

Logging request after response is sent:

```ts
import { defineRoute } from '@zephyr-js/core';

defineRoute({
  onResponse(req) {
    console.log(req);
  },
  handler() {
    return 'OK';
  },
});
```

### onErrorCaptured()

Registers a callback to be called when an error is caught.

**Type**

```ts
function onErrorCaptured<T = unknown>(
  req: ZephyrRequest,
  res: ZephyrResponse,
  err: T,
): void;
```

**Example**

Logging error when an error is caught:

```ts
import { defineRoute } from '@zephyr-js/core';

defineRoute({
  onErrorCaptured(req, res, err) {
    console.log(err);
  },
  handler() {
    return 'OK';
  },
});
```

## Dependency Injection

### Initializing Dependencies

We can initialize and return all our app dependencies with `init` function in `src/lib/deps.ts`

```ts
import { MyService } from '@/services';

interface AppDeps {
  myService: MyService;
}

export async function init(): Promise<AppDeps> {
  const myService = new MyService();
  return { myService };
}

export default { init };
```

### Providing Dependencies

The `createApp()` function has a `dependencies` argument that allows you to pass in your app dependencies. They will be injected into every API route.

In `src/index.ts`, we import and call the `initializeDependencies` function and pass the instance to `createApp()`

```ts
import deps from './lib/deps';

async function bootstrap() {
  const dependencies = await deps.init();
  const app = await createApp({ dependencies });
  app.listen(3000);
}

bootstrap().catch((err) => {
  console.error(err);
  process.exit(1);
});
```

### Consuming Dependencies

`src/routes/index.ts`

```ts
import { defineRoute } from '@zephyr-js/core';
import { AppDeps } from '@/lib/deps';

export function GET({ myService }: Pick<AppDeps, 'myService'>) {
  return defineRoute({
    handler() {
      // do stuff with the injected `myService` instance
    },
  });
}
```
