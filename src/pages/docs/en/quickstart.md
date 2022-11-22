---
title: Quick Start
description: Zephyr Quick Start
layout: ../../../layouts/DocLayout.astro
---

## Bootstrapping Project

We recommend using `create-zephyr-app` to start a new Zephyr app because it does all the setup work for you.

**npm**

```bash
npm create zephyr-app <app-name>
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

Any file contained in the `src/routes` directory is mapped to `/*` and will be considered as an API route.

By default, we have `src/routes/index.ts`, which maps to `/`

You may export **HTTP verbs** , i.e. `GET`, `POST`, `PUT`, `DELETE`, `PATCH` functions from each file. They should return the route specification declared with [`defineRoute()`](/docs/core/define-route) function.

The following API route returns a **JSON** response with default **200** status code

```ts title="src/routes/index.ts"
import { defineRoute } from '@zephyr-js/core';

export function GET() {
  return defineRoute({
    handler() {
      return { foo: 'bar' };
    },
  });
}
```

The above example represents `GET /`

### Building a TODO API

Let's build a TODO API together.
The API routes that we going to create are:

- List todos: `GET /todos`
- Get todo: `GET /todos/[todoId]`
- Create todo: `POST /todos`

#### `GET /todos`

Create a `src/routes/todos/index.ts` file and export a `GET` function.

```ts title="src/routes/todos/index.ts"
import { defineRoute } from '@zephyr-js/core';
import { getTodos } from '@services/todo';

export function GET() {
  return defineRoute({
    handler() {
      const todos = getTodos();
      return { todos };
    },
  });
}
```

#### `GET /todos/[todoId]`

Create a `src/routes/todos/[todoId].ts` file and export a `GET` function.

We also declare a `schema` for the route to have **type checking** and **request validation**. Learn more about [route schema](/docs/core/define-route.md#declaring-schema).

Note that this is also how we declare a dynamic route, which allows us to get the `[todoId]` from `req.params`.

```ts title="src/routes/todos/[todoId].ts"
import { defineRoute } from '@zephyr-js/core';
import { z } from 'zod';
import { TodoSchema } from '@models/todo';
import { getTodoById } from '@services/todo';

export function GET() {
  return defineRoute({
    schema: z.object({
      params: z.object({
        todoId: z.string(),
      }),
      response: z.object({
        todo: TodoSchema,
      }),
    }),
    handler({ params }) {
      const { todoId } = params;
      const todo = getTodoById(todoId);
      return { todo };
    },
  });
}
```

#### `POST /todos`

Export a `POST` function from the `src/routes/todos/index.ts` file that we created earlier.

```ts title="src/routes/todos/index.ts"
import { defineRoute } from '@zephyr-js/core';
import { z } from 'zod';
import { TodoSchema } from '@models/todo';
import { getTodos, createTodos } from '@services/todo';

export function GET() {
  return defineRoute({
    handler() {
      const todos = getTodos();
      return { todos };
    },
  });
}

export function POST() {
  return defineRoute({
    schema: z.object({
      body: z.object({
        name: z.string(),
        status: z.enum(['todo', 'doing', 'done']),
      }),
      response: z.object({
        todo: TodoSchema,
      }),
    }),
    handler({ body }) {
      const todo = createTodo(req.body);
      return { todo };
    },
  });
}
```

Viola! We just built a set of API routes in a couple of minutes 🎉

## Next Steps

- [Lifecycle hooks](/docs/core/lifecycle-hooks.md)
