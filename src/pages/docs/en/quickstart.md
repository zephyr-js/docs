---
title: Quick Start
description: Zephyr Quick Start
layout: ../../../layouts/DocLayout.astro
---

## Bootstrapping Project

We recommend using `create-zephyr-app` to start a new Zephyr app because it does all the setup work for you.

**npm**

```bash
npm create zephyr-app@latest <app-name>
```

**yarn**

```bash
yarn create zephyr-app <app-name>
```

**pnpm**

```bash
pnpm create zephyr-app <app-name>
```

Starting the dev server:

**npm**

```bash
cd <app-name>
npm run dev
```

**yarn**

```bash
cd <app-name>
yarn dev
```

**pnpm**

```bash
cd <app-name>
pnpm dev
```

You should now have your first Zephyr app running!
Visit [http://localhost:3000](http://localhost:3000) to see the "Hello world" message

## Defining API Routes

Let's walk through the steps of building typesafe API with Zephyr.

Any file contained in the `src/api` directory is mapped to `/*` and will be considered as an API route.

By default, we have `src/api/index.ts`, which maps to `/`

You may export **HTTP methods** (`get`, `post`, `put`, `delete`, `patch`) as functions from each file. They are created with the [`defineRoute()`](/docs/core/define-route) function.

The following API route returns a **JSON** response with **200** status code

```ts title="src/api/index.ts"
import { defineRoute } from '@zephyr-js/core';

export const get = defineRoute({
  handler(req, res) {
    return res.status(200).json({ foo: 'bar' });
  },
});
```

The above example represents `GET /`

### Building a TODO API

Let's build a TODO API together.
The API routes that we going to create are:

- List todos: `GET /todos`
- Get todo: `GET /todos/[todoId]`
- Create todo: `POST /todos`

#### `GET /todos`

Create a `src/api/todos/index.ts` file and export a `get` route.

```ts title="src/api/todos/index.ts"
import { defineRoute } from '@zephyr-js/core';
import { getTodos } from '@services/todo';

export const get = defineRoute({
  handler(req, res) {
    const todos = getTodos();
    return res.json({ todos });
  },
});
```

#### `GET /todos/[todoId]`

Create a `src/api/todos/[todoId].ts` file and export a `get` route.

We also declare a `schema` for the route to have **type checking** and **request validation**. Learn more about [route schema](/docs/core/define-route.md#declaring-schema).

Note that this is also how we declare a dynamic route, which allows us to get the `[todoId]` from `req.params`.

```ts title="src/api/todos/[todoId].ts"
import { defineRoute } from '@zephyr-js/core';
import { z } from 'zod';
import { TodoSchema } from '@models/todo';
import { getTodoById } from '@services/todo';

export const get = defineRoute({
  schema: z.object({
    params: z.object({
      todoId: z.string(),
    }),
    response: z.object({
      todo: TodoSchema,
    }),
  }),
  handler(req, res) {
    const { todoId } = req.params;
    const todo = getTodoById(todoId);
    return res.json({ todo });
  },
});
```

#### `POST /todos`

Export a `post` route from the `src/api/todos/index.ts` file that we created earlier.

```ts title="src/api/todos/index.ts"
import { defineRoute } from '@zephyr-js/core';
import { z } from 'zod';
import { TodoSchema } from '@models/todo';
import { getTodos, createTodos } from '@services/todo';

export const get = defineRoute({
  handler(req, res) {
    const todos = getTodos();
    return res.json({ todos });
  },
});

export const post = defineRoute({
  schema: z.object({
    body: z.object({
      name: z.string(),
      status: z.enum(['todo', 'doing', 'done']),
    }),
    response: z.object({
      todo: TodoSchema,
    }),
  }),
  handler(req, res) {
    const todo = createTodo(req.body);
    return res.json({ todo });
  },
});
```

Viola! We just built a set of API routes in a couple of minutes 🎉

## Next Steps

- [Lifecycle hooks](/docs/core/lifecycle-hooks.md)
