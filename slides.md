---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shikiji
lineNumbers: false
info: |
  ## io-ts presentation slides for developers.

  Learn more at [io-ts](https://github.com/gcanti/io-ts/blob/master/index.md)
drawings:
  persist: false
transition: slide-left
title: Welcome to io-ts
mdc: true
---

# Welcome to io-ts

Presentation slides for developers

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/arthurgubaidullin/io-ts-slides" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---

# What problem do we want to solve?

We want the types to always be consistent with the value

We have

```ts twoslash
type User = {
  name: string;
  age: number;
}
```

We can get

```ts twoslash
const userFromNetwork: unknown = { name: 'foo', age: 33 };
```

We want to achieve

```ts twoslash
import * as E from 'fp-ts/Either'

declare function validate(user: unknown): E.Either<Error, User>

const userFromNetwork: unknown = { name: 'foo', age: 33 };

type User = {
  name: string;
  age: number;
}
// ---cut---
const user: E.Either<Error, User> = validate(userFromNetwork);
```

or

```ts twoslash
declare function validate(user: unknown): User

const userFromNetwork: unknown = { name: 'foo', age: 33 };

type User = {
  name: string;
  age: number;
}
// ---cut---
const user: User = validate(userFromNetwork);
```

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
transition: fade-out
---

# How can io-ts help us?

What can we do?

We can build a codec

```ts twoslash
import * as t from "io-ts";
// ---cut---
type User = t.TypeOf<typeof User>

const User = t.type({
  name: t.string,
  age: t.number,
});
```

We can validate (decode) the value

```ts twoslash
import * as t from "io-ts";
type User = t.TypeOf<typeof User>

const User = t.type({
  name: t.string,
  age: t.number,
});

const userFromNetwork: unknown = { name: 'foo', age: 33 };
// ---cut---
const user: t.Validation<User> = User.decode(userFromNetwork);
```

or

```ts ts twoslash
import * as t from "io-ts";
import { pipe, identity } from 'fp-ts/function'
import * as E from 'fp-ts/Either'

type User = t.TypeOf<typeof User>

const User = t.type({
  name: t.string,
  age: t.number,
});

const userFromNetwork: unknown = { name: 'foo', age: 33 };
// ---cut---
const user: User = pipe(userFromNetwork, User.decode, E.fold((e) => { throw e; }, identity));
```

This is it.

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
transition: fade-out
---

# Can we encode & decode an instance of the Date class?

Yes

We have

```ts twoslash
import * as t from "io-ts";
import { JsonFromString, DateFromISOString } from "io-ts-types";

// ---cut---
type User = t.TypeOf<typeof User>

const User = t.type({
  name: t.string,
  birthday: DateFromISOString, // npm: io-ts-types
});

const userFromNetwork: unknown = User.encode({ name: 'foo', birthday: new Date() });
```

We can validate (decode) the value

```ts twoslash
import * as t from "io-ts";
import { JsonFromString, DateFromISOString } from "io-ts-types";

type User = t.TypeOf<typeof User>

const User = t.type({
  name: t.string,
  birthday: DateFromISOString, // npm: io-ts-types
});
const userFromNetwork: unknown = User.encode({ name: 'foo', birthday: new Date() });
// ---cut---
const user: t.Validation<User> = User.decode(userFromNetwork);
```

This is it.

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
transition: fade-out
---

# Can we encode & decode an object as a string?

Yes

We have

```ts twoslash
import * as t from "io-ts";
import { JsonFromString, DateFromISOString } from "io-ts-types";

// ---cut---
type User = t.TypeOf<typeof User>

const User = t.string.pipe(JsonFromString).pipe(t.type({
  name: t.string,
  birthday: DateFromISOString, // npm: io-ts-types
}));
```

and

```ts twoslash
import * as t from "io-ts";
import { JsonFromString, DateFromISOString } from "io-ts-types";

type User = t.TypeOf<typeof User>;

const User = t.string.pipe(JsonFromString).pipe(
  t.type({
    name: t.string,
    birthday: DateFromISOString, // npm: io-ts-types
  })
);

// ---cut---
const userFromNetwork: unknown = User.encode({ name: 'foo', birthday: new Date() });
```

We can validate (decode) the value

```ts twoslash
import * as t from "io-ts";
import { JsonFromString, DateFromISOString } from "io-ts-types";

type User = t.TypeOf<typeof User>;

const User = t.string.pipe(JsonFromString).pipe(
  t.type({
    name: t.string,
    birthday: DateFromISOString, // npm: io-ts-types
  })
);

const userFromNetwork: unknown = User.encode({
  name: "foo",
  birthday: new Date(),
});

// ---cut---
const user: t.Validation<User> = User.decode(userFromNetwork);
```

This is it.

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
transition: fade-out
---

# What about an end-to-end experience?

It depends. Most of the time it is easy.

We can do

```ts twoslash
import * as E from "fp-ts/Either";
import { identity, pipe } from "fp-ts/lib/function";
import * as t from "io-ts";

declare const fetcher: { post: (request: unknown) => unknown };

// ---cut---
const Request = t.type({ foo: t.literal("foo") });

const Response = t.type({ bar: t.literal("bar") });

export const doSomethingOnClient = (
  request: t.TypeOf<typeof Request>
): E.Either<Error, t.TypeOf<typeof Response>> =>
  pipe(request, Request.encode, fetcher.post, Response.decode, E.mapLeft(E.toError));

export const doSomethingOnServer = (
  request: t.OutputOf<typeof Request>,
  response: { write: (response: unknown) => void }
): void =>
  pipe(
    request, Request.decode, E.fold((e) => { throw E.toError(e); }, identity), 
    () => ({ bar: "bar" as const }), 
    Response.encode, response.write);
```

This is it.

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
layout: default
---

# Table of contents

```html
<Toc minDepth="1" maxDepth="1"></Toc>
```

<Toc maxDepth="1"></Toc>

---
layout: center
class: text-center
---

# Learn More

[Documentation](https://github.com/gcanti/io-ts/blob/master/index.md) · [GitHub](https://github.com/gcanti/io-ts/blob/master/index.md)

---
layout: center
class: text-center
---

# The End

[Telegram Channel (RU)](https://t.me/arthur_g_writes) · [GitHub](https://github.com/arthurgubaidullin) · [LinkedIn](https://www.linkedin.com/in/arthur-d-g/)
