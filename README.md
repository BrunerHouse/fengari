[![Build Status](https://travis-ci.org/fengari-lua/fengari.svg?branch=master)](https://travis-ci.org/fengari-lua/fengari)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![#fengari on Freenode](https://img.shields.io/Freenode/%23fengari.png)](https://webchat.freenode.net/?channels=fengari)

<p align="center">
    <img src="https://github.com/fengari-lua/fengari/raw/master/logo.png" alt="Fengari" width="304" height="304">
</p>


# Fengari

🐺 φεγγάρι - The Lua VM written in JS ES6 for Node and the browser

## Semantics

Fengari implements Lua 5.3 semantics and will hopefully follow future Lua releases. If you find any noticeable difference between Fengari and Lua's behaviours, please [report it](https://github.com/fengari-lua/fengari/issues).

### Strings

Lua strings are 8-bits clean and can embed `\0`. Which means that invalid UTF-8/16 strings are valid Lua strings. Lua functions like `string.dump` even use strings as a way of storing binary data.

To address that issue, Lua strings are represented by an array of bytes in Fengari. To push a JS string on the stack you can use `lua_pushliteral` which will convert it to an array of bytes before pushing it. To get a Lua string on the stack as a JS string you can use `lua_tojsstring` which will attempt to convert it to a UTF-16 JS string. The latter won't give you what you expect if the Lua string is not a valid UTF-16 sequence.


### _Missing_ features

- `lua_gc`/`collectgarbage`: Fengari relies on the JS garbage collector and does not implement its own.
- The following functions are only available in Node:
    - `luaL_dofile`
    - `luaL_loadfilex`
    - `luaL_loadfile`
    - `loadfile`
    - `dofile`
    - The entire `io` lib
    - `os.remove`
    - `os.rename`
    - `os.tmpname`
    - `os.execute`
- [Weak tables](http://www.lua.org/manual/5.3/manual.html#2.5.2)

## Extensions

### `dv = lua_todataview(L, idx)`

Equivalent to `lua_tolstring` but returns a [`DataView`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DataView) instead of a string.


### `lua_pushjsfunction(L, func)`

Alias for `lua_pushcfunction`.


### `lua_pushjsclosure(L, func, n)`

Alias for `lua_pushcclosure`.


### `lua_atnativeerror(L, func)`

Sets a function to be called if a native JavaScript error is thrown across a lua pcall.
The function will be run as if it were a message handler (see https://www.lua.org/manual/5.3/manual.html#2.3).
The current message handler will be run after the native error handler returns.


### `b = lua_isproxy(p, L)`

Returns a boolean `b` indicating whether `p` is a proxy (See `lua_toproxy`).
If `L` is non-null, only returns `true` if `p` belongs to the same global state.


### `p = lua_toproxy(L, idx)`

Returns a JavaScript object `p` that holds a reference to the lua value at the stack index `idx`.
This object can be called with a lua_State to push the value onto that state's stack.

This example would be an inefficient way to write `lua_pushvalue(L, 1)`:

```js
var p = lua_toproxy(L, 1);
p(L);
````

## NYI

- `io.input()`: partially implemented
- `io.lines()`
- `io.open()`
- `io.output()`: partially implemented
- `io.popen()`
- `io.read()`
- `io.tmpfile()`
- `file:lines()`
- `file:read()`
- `file:setvbuf()`
- `file:__gc()`

## References

- [Source code for Lua 5.3](lua.org/source/5.3/)
- [Lua 5.2 Bytecode and Virtual Machine](http://files.catwell.info/misc/mirror/lua-5.2-bytecode-vm-dirk-laurie/lua52vm.html)
- [Lua 5.3 Bytecode Reference](http://the-ravi-programming-language.readthedocs.io/en/latest/lua_bytecode_reference.html)
- [A No-Frills Introduction to Lua 5.1 VM Instructions](http://luaforge.net/docman/83/98/ANoFrillsIntroToLua51VMInstructions.pdf)
