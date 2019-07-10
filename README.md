# TypeScript Declaration Generator for Haxe/JS

[![Build Status](https://travis-ci.org/nadako/hxtsdgen.svg?branch=master)](https://travis-ci.org/nadako/hxtsdgen)

**STATUS: WIP usable with limitations, feedback welcome!**

This is a [TypeScript declaration file](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)
generator for the [Haxe](https://haxe.org/) JavaScript output target.

What it does is generate a `.d.ts` file next to the `.js` file generated by Haxe compiler,
containing TypeScript declarations for classes and functions exposed from Haxe using the
[`@:expose` metadata](http://haxe.org/manual/target-javascript-expose.html), and all their
related types (function arguments and return types).

## Why?

To make using Haxe/JS modules from both JavaScript and TypeScript much easier, of course!
Just compile your Haxe library to a JS module and use it in TypeScript in a perfectly typed way.

## Usage

Just add `-lib hxtsdgen` to compiler arguments and a `.d.ts` file will be produced along the normal JS output.

## Options

### Concrete enums export

Using compiler option `-D hxtsdgen_enums_ts`, the library will produce an extra `output-enums.ts`
with concrete TypeScript enums from Haxe Abstract Enums (see "Limitations").

### Separate types export

Using compiler option `-D hxtsdgen_types_ts`, the library will produce an extra `output-types.d.ts`
with all the interfaces and typedefs separated from the classes & functions declarations.

### Custom output header

By default, generated files include a comment warning that the file is generated.

Using compiler option `-D hxtsdgen_skip_header`, no header will be included.

But it's often necessary to add a custom header, for instance to disable linting.
It can be achieved by adding the following compiler macro:

```
--macro hxtsdgen.Generator.setHeader('/* tslint:disable */')
```


## Supported Haxe features

Certain Haxe features are compiler abstractions and thus may not be possible to translate
to TypeScript.

- [x] Classes
- [x] Interfaces
- [x] [Anonymous Structure](https://haxe.org/manual/types-anonymous-structure.html) Typedefs
- [ ] Option to generate interfaces from Typedefs?
- [ ] Other uses of [typedefs](https://haxe.org/manual/type-system-typedef.html)
- [x] Properties (as `get_prop/set_prop`)
- [ ] Native properties (needs compiler support)
- [ ] Enums (needs compiler support)
- [x] [Abstract enums](https://haxe.org/manual/types-abstract-enum.html) (see limitations)
- [x] Abstract enums as concrete `.ts` enums (`-D hxtsdgen_enums_ts`)
- [ ] Option to generate "fat enums" from Abstract enums?
- [ ] More general [abstracts](https://haxe.org/manual/types-abstract.html) (unlikely, excepted possibly for return values)

### Haxe packages

Exporting packages isn't very idiomatic to JS but supported:

```haxe
// com/foo/Thing.hx -> thing.js
package com.foo;

@:expose
class Thing {...}
```

Can be (awkwardly) imported as:

```typescript
import * as things from './thing';
const thing = new things.com.foo.Thing();
// or
import { com } from './thing';
const thing = new com.foo.Thing();
```

*Warning: currently however it isn't supported to use both
`-D hxtsdgen_enums_ts` and `-D hxtsdgen_types_ts` with both enums and types
sharing the same root package name, as the generator isn't smart enough.*

**To avoid that**, you can use `@:expose('FriendlyName')`, which will export
module-level declarations:

```haxe
// com/foo/Thing.hx -> thing.js
package com.foo;

@:expose('Thing')
class Thing {...}
```

Can be (nicely) imported as:

```typescript
import { Thing } from './thing';
const thing = new Thing();
```

### Limitations

#### No automatic export

Currently all the types have to be explicitly exposed; types used by functions won't be
automatically exported so everything must be currently annotated for export.

#### Abstract enums

`hxtsdgen` can generate TypeScript [const enums](https://www.typescriptlang.org/docs/handbook/enums.html),
which are a pure compiler construction, substituted at compile time with their value.

However `.ts` code can be compiled by 2 compilers: TypeScript and Babel.

- Check Babel's `const enum` support: https://github.com/babel/babel/issues/8741
- TypeScript supports `const enum` in `.d.ts` files only in full compilations.

For optimal compiler compatibility, add `-D hxtsdgen_enums_ts` to hint hxtsdgen to
generate enums in a separate `.ts` file which will be compatible with
`--transpileOnly --preserveConstEnums`.

#### Abstracts and (regular) Enums

Enums and Abstracts are a key feature of the Haxe language, but They don't translate well
to JavaScript APIs as they are a compiler feature.

General Abstracts, offering compile-time types abstractions and operators overloading will
probably never be supported.

Enums could be somehow supported, but currently the compiler doesn't allow exposing them.

#### Native properties

Haxe JS doesn't generate native properties in any language - they are always transformed
into `get_prop/set_prop` functions. It is possible that in the future the Haxe compiler
will allow exporting native properties.


## How does it look?

Check out these 3 awesome panes (left-to-right):

 * `Main.hx` (Haxe source code)
 * `main.d.ts` (generated TypeScript declarations)
 * `test.ts` (TypeScript source code that uses the declarations)

![](http://i.imgur.com/AHmdHtv.gif)
