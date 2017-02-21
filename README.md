# TypeScript Declaration Generator for Haxe/JS

**STATUS: WORK IN PROGRESS, CONTRIBUTIONS WELCOME**

This is a [TypeScript declaration file](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)
generator for the [Haxe](https://haxe.org/) JavaScript output target.

What it does is generate a `.d.ts` file next to the `.js` file generated by Haxe compiler,
containing TypeScript declarations for classes and functions exposed from Haxe using the [`@:expose` metadata](http://haxe.org/manual/target-javascript-expose.html).

## Usage

Just add `-lib hxtsdgen` to compiler arguments and it'll do the rest.

## Why?

To make using Haxe/JS modules from both JavaScript and TypeScript much easier, of course!
Just compile a haxe library as a JS module and be able to use it in TypeScript in a perfectly
typed way.

You may ask, how is plain JavaScript involved here? Well, the thing is, modern JavaScript editors
such as VS Code and (I think) IDEA, can download and use TypeScript "typings" for providing hints
while editing pure-js code.

## Early screenshot:

![](http://i.imgur.com/W90hCvO.gif)
