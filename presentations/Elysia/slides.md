---
theme: elysia
highlighter: shiki
layout: cover
addons:
  - slidev-component-progress
---

<CoverContent/>

---
layout: default
---
<SlideLogo framework="ExpressJS" title="Плюсы и минусы"/>

<p class="text-green">Плюсы</p>

- Проверен временем
- Большое количество материала
- Считается стандартом (к сожалению)
- Популярен (множество готовых решений)

<p class="text-red">Минусы</p>

- Медленный
- Middleware
- Плохая типизация
- Из коробки почти ничего не имеет
- Написан в эру мамонтов
- Костыльный
- Разработчики забили

---
layout: default
---
<SlideLogo framework="ExpressJS" title="Медленный"/>

// TODO: Сделать свой чарт

<img class="mt-7" src="/benchmark.png"/>

---
layout: default
---
<SlideLogo framework="ExpressJS" title="Медленный"/>

// TODO: Описать медленный регексовый поиск экспресса

---
layout: default
---
<SlideLogo framework="ExpressJS" title="Middleware"/>

<div class="mt-7"/>

```ts twoslash
declare module "express-serve-static-core" {
    interface Request {
        user: { name: "Yandex" }
    }
}
// ---cut---
import Express from "express";

const app = Express();

app.use((req, res, next) => {
	if (!req.headers.authorization) return res.send("No access");
    
	req.user = { name: "Yandex" };
	console.log("before handler");

	next();
});

app.get("/", (req, res) => {
	return res.send(`Hello, ${req.user.name}!`);
});

app.use((req, res) => {
	console.log("after handler never executes...");
});
```

---
layout: default
---

<SlideLogo framework="ExpressJS" title="Написан в эру мамонтов + костыльный"/>

<div class="mt-7"/>

```ts twoslash
/** Какая-то асинхронная операция по поиску юзера */
function findUser() {}
// ---cut---
import Express from "express";
import "express-async-errors";

const app = Express();

app.use(async (req, res) => {
	if (!req.headers.authorization) throw new Error("No access");

    await findUser();
});
// Да-да кривые типы экспресса с примером из доки - https://expressjs.com/en/guide/error-handling.html#error-handling
app.use((err, req, res, next) => {
	return res.status(400).json(err.message);
});
```

---
layout: default
---

<SlideLogo framework="ExpressJS" title="Разработчики забили"/>

<div class="mt-7"/>

- 4.18.2 (latest) - год назад
- 4.18.1 - 2 года назад
- 4.18.0 - 2 года назад
- 4.17.3 - 2 года назад
- 5.0.0-beta.1 - 2 года назад
- 4.17.2 - 2 года назад
- 5.0.0-alpha.8 - 4 года назад
- 4.1.1 - 5 лет назад

// TODO: Придумать как обыграть покрасивее

---
layout: default
---

<SlideLogo framework="KoaJS" title="Плюсы и минусы"/>

<p class="text-green">Плюсы</p>

- Написан командой ExpressJS
- Значительно быстрее ExpressJS

<p class="text-red">Минусы</p>

- Middleware
- Плохая типизация
- Плохая работа с валидацией

---
layout: default
---

<SlideLogo framework="FastifyJS" title="Плюсы и минусы"/>

<p class="text-green">Плюсы</p>

- Современный
- Life-cycle hooks
- Производительный
- fast-json-stringify
- Построен на JSON Schema и AJV
- Отличный DX и swagger одной строчкой
- express-compatibility plugin

<p class="text-red">Минусы</p>

- Не идеал типизации

---
layout: default
---

<SlideLogo framework="FastifyJS" title="Life-cycle hooks"/>

Request => Routing => Logger => onRequest Hook => preParsing Hook => Parsing => preValidation Hook => Validation => preHandler Hook => User Handler => Reply => preSerialization Hook => onSend Hook => Response => onResponse Hook

// TODO: flow chart

---
layout: default
---

<SlideLogo framework="FastifyJS" title="Валидация и сериализация"/>

<div class="mt-7"/>

```ts twoslash
// @noErrors
import { TypeBoxTypeProvider } from "@fastify/type-provider-typebox";
import { Type } from "@sinclair/typebox";
import Fastify from "fastify";

const fastify = Fastify().withTypeProvider<TypeBoxTypeProvider>();

fastify.get(
	"/route",
	{
		schema: {
			querystring: Type.Object({
				foo: Type.Number(),
				fooBar: Type.String(),
			}),
		},
	},
	(request, reply) => {
		request.query.f; // type safe!
        //       ^|
	},
);

```

---
layout: default
---

<SlideLogo framework="FastifyJS" title="Миграция с Express"/>

<div class="mt-7"/>

```ts twoslash
import fastifyExpress from "@fastify/express";
import Express from "express";
import Fastify from "fastify";

const fastify = Fastify();

await fastify.register(fastifyExpress);

const router = Express.Router();
fastify.use(router);

router.use((req, res, next) => {
	res.setHeader("x-custom", "fastify + express");
	next();
});

router.get("/hello", (req, res) => {
	res.status(201).json({ hello: "world" });
});
router.use("*", (req, res) => {
	res.status(404).json({ msg: "not found" });
});

fastify.listen({ port: 3000 }, console.log);
```

---
layout: default
---

<SlideLogo framework="ElysiaJS" title="Фичи"/>

<img src="/feature-sheet.webp"/>

---
layout: default
---

<SlideLogo framework="ElysiaJS" title="Быстрый"/>

// TODO: 

---
layout: default
---

<SlideLogo framework="ElysiaJS" title="Валидация"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia, t } from "elysia";

new Elysia().post(
	"/yandex/question",
	({ body }) => ({ message: "Question created!" }),
	// ^?
	{
		body: t.Object({
			presenter: t.TemplateLiteral("{Доклад 1|Доклад 2}"),
			text: t.String(),
		}),
		response: t.Object({
			message: t.String(),
		}),
	},
);

```

---

<SlideLogo framework="ElysiaJS" title="OpenAPI / Swagger"/>

<div class="mt-7"/>

```ts twoslash
// @filename: routes.ts
import { Elysia } from "elysia";

export const feed = new Elysia();
export const users = new Elysia();
// @filename: index.ts
// ---cut---
import { swagger } from "@elysiajs/swagger";
import { Elysia, t } from "elysia";
import { feed, users } from "./routes";

new Elysia()
  .use(swagger())
  .use(feed)
  .use(users)
  .listen(3000);

```

---
layout: full
---

<img src="/scalar-dark-mode.webp" />

---

<SlideLogo framework="ElysiaJS" title="e2e type-safety | TODO: лучше разместить"/>


```ts twoslash
import { Elysia, t } from "elysia";

const app = new Elysia()
	.post("/yandex/employee", () => {}, {
		body: t.Object({
			name: t.String(),
			stack: t.Array(t.TemplateLiteral("{Elysia|React|Effector}")),
		}),
	})
	.listen(1997);

export type App = typeof app;
```


```ts twoslash
// @filename: server.ts
import { Elysia, t } from "elysia";

const app = new Elysia()
	.post("/yandex/employee", () => {}, {
		body: t.Object({
			name: t.String(),
			stack: t.Array(t.TemplateLiteral("{Elysia|React|Effector}")),
		}),
	}).post("/yandex/another", () => {}, {
		body: t.Object({
			name: t.String(),
			stack: t.Array(t.TemplateLiteral("{Elysia|React|Effector}")),
		}),
	})
	.listen(1997);

export type App = typeof app;
// @filename: index.ts
// ---cut---
import { edenTreaty } from "@elysiajs/eden";
import type { App } from "./server";

const eden = edenTreaty<App>("http://localhost:1997");

await eden.yandex.employee.post({
    //            ^|
	name: "Всеволод",
	stack: ["Elysia", "Svelte"],
});

```

---

<SlideLogo framework="ElysiaJS" title="WinterCG"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia } from "elysia";
import { Hono } from "hono";
// ---cut---
const elysia = new Elysia()
    .get("/", 'Hello from Elysia inside Hono inside Elysia')

const hono = new Hono()
    .get('/', (c) => c.text('Hello from Hono!'))
    .mount('/elysia', elysia.fetch)

const main = new Elysia()
    .get('/', () => 'Hello from Elysia')
    .mount('/hono', hono.fetch)
    .listen(3000)
```

---

<SlideLogo framework="ElysiaJS" title="Context"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia, t } from "elysia";

const app = new Elysia();
// ---cut---
app.get(
	"/:type",
	({ query: { pageSize }, params: { type }, set }) => {
		set.status = "I'm a teapot";
		set.headers["Content-Type"] = "application/x-teapot";
	},
	{
		query: t.Object({
			pageSize: t.Numeric(),
		}),
	},
);
```

---

<SlideLogo framework="ElysiaJS" title="State & Decorate"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia, t } from "elysia";

const app = new Elysia();
// ---cut---
app
	.state("requests", 1)
	.get("/increment", ({ store }) => ++store.requests);
```

<br/>

```ts twoslash
import { Elysia, t } from "elysia";

const app = new Elysia();
class Logger {
    log(text: string) {}
}
// ---cut---
app
  .decorate("logger", new Logger())
  .get("/", ({ logger }) => {
    	logger.log("hi");

    	return "hi";
});
```

---

<SlideLogo framework="ElysiaJS" title="Derive"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia, t } from "elysia";

const app = new Elysia();
// ---cut---
app
	.derive(({ headers }) => {
		const auth = headers.Authorization;

		return {
			bearer: auth?.startsWith("Bearer ") ? auth.slice(7) : null,
		};
	})
	.get("/", ({ bearer }) => bearer);
```

---

<SlideLogo framework="ElysiaJS" title="Affix"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia } from "elysia";

// ---cut---
const setup = new Elysia({ name: 'setup' })
    .decorate({
        argon: 'a',
        boron: 'b',
        carbon: 'c'
    })

const app = new Elysia()
    .use(
        setup
            .prefix('decorator', 'setup')
    )
    .get('/', ({ setupCarbon }) => setupCarbon)
    //                              ^?
```

---

<SlideLogo framework="ElysiaJS" title="Elysia plugin"/>

<div class="mt-7"/>

TODO: переделать кнш

<img width="500" src="/new-elysia-twitter.png" />

---

<SlideLogo framework="ElysiaJS" title="Elysia plugin"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia } from "elysia";

const app = new Elysia();

class YandexController {
    createEvent(name: string) {}
}
// ---cut---
const plugin = new Elysia()
	.decorate("yandex", new YandexController())
	.get("/", () => "Hello, Yandex!");

app
	.use(plugin)
	.post("/event", ({ yandex }) => yandex.createEvent("Я 💛 Фронтенд"))
	.listen(3000);
```

---

<SlideLogo framework="ElysiaJS" title="Elysia plugin - дедупликация"/>

<div class="mt-7"/>

```ts twoslash
import { Elysia } from "elysia";

const pluginOptions = {}
// ---cut---
const somePlugin = new Elysia({
    name: "elysia-some",
    seed: pluginOptions
});

const yandexPlugin = new Elysia().use(somePlugin);
const elytriumPlugin = new Elysia().use(somePlugin);

const app = new Elysia().use(yandexPlugin).use(elytriumPlugin);
```