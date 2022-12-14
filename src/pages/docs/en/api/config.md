---
title: Zephyr Config
description: Zephyr Config
layout: ../../../../layouts/DocLayout.astro
---

### load()

Load configuration from `config/<env>` path.

**Type**

```ts
interface LoadOptions {
  path?: string | null;
  variables?: object;
  dotenv?: boolean;
  schema?: AnyZodObject;
}

function load<T extends object>(options: LoadOptions): Promise<T>;
```

**Details**

Config file examples:

- `config/development.yml`
- `config/production.yaml`

Supported file extensions are `.yml`, `.yaml`

The file name should be the same as `process.env.NODE_ENV`. So make sure to set `NODE_ENV` to the correct value before running the application.

### Variables

You may pass custom variables into the `load()` function and Zephyr will parse them accordingly.

Given that you have `config/development.yml`

```yaml
database:
  url: <%= it.DB_URL %>
```

By passing specifying `variables` option on `load()`

```ts
load({
  variables: {
    DB_URL: 'mongodb://localhost:27017',
  },
});
```

The parsed config object will be

```json
{
  "database": {
    "url": "mongodb://localhost:27017"
  }
}
```

### Environment Variables

Zephyr config will automatically take variables in `.env` to fill in the placeholders in config file.

Given that you have `config/development.yml`

```yaml
jwt:
  secret: <%~ process.env.JWT_SECRET %>
```

and a root level `.env`

```
JWT_SECRET=super-secure-secret
```

The parsed config object will be

```json
{
  "jwt": {
    "secret": "super-secure-secret"
  }
}
```

### Consuming Config Object

By default the config object is loaded on the app dependencies initialization, see `src/lib/deps.ts`. It will then be injected into every API route.

Example of accessing the config object in a route:

```ts
import { defineRoute } from '@zephyr-js/core';
import { AppDeps } from '@/lib/deps';

export function GET({ config }: Pick<AppDeps, 'config'>) {
  return defineRoute({
    handler(req, res) {
      console.log(config);
    },
  });
}
```
