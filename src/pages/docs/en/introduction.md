---
title: Introduction
description: Docs intro
layout: ../../../layouts/DocLayout.astro
---

Zephyr is a [Typescript](https://www.typescriptlang.org/) **server-side** meta framework that is inspired by [Next.js](https://nextjs.org/). It is built on top of [Express.js](https://expressjs.com/) and uses [Zod](https://zod.dev/) in request / response validation as well as providing **typesafe** API.

Zephyr provides a structure and starting point for building your application, letting you concentrate on developing something wonderful while we handle the rest of it.

## Why Zephyr?

The established server-side web frameworks for Node.js at the moment are [Nest.js](https://nestjs.com/) and [Adonis.js](https://adonisjs.com/), both of which are fantastic and rely on controllers and decorators in OOP. However, we believe that Zephyr has its own strength to play.

**Functional by Default**

Some developers prefer functional programming to object-oriented programming (OOP). As a result, Zephyr seeks to let programmers write and export functions as API routes and incorporates file-based routing from Next.js.

**Declarative API**

Zephyr provides simple and intuitive interfaces for developers in declaring API routes and hooks, making the process of writing API incredibly convenient.

## Features

- 🧭 **Intuitive routing** - File-based routing similar to Next.js.
- ✅ **Built-in validation** - Define schema for API routes, validation is done out of the box.
- 🪝 **Lifecycle hooks** - Register callbacks on certain points of the request lifecycle.
- ⚙️ **Functional by default** - Write functions instead of bloated controllers with mass decorators.
- ✍️ **Typesafe** - Request and response types are typed checked and validated.
- ✨ **Productivity boost** - Bootstrap your project with [`create-zephyr-app`](quickstart#bootstrapping-project) with zero configuration.

## Next Steps

- [Quick Start](quickstart)
