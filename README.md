# orb
*I'll have one Lua with sugar, please.*

Orb is an alternative syntax for Lua. It aims to let you to create more functionality with less typing. It accomplishes this by shortening normal Lua syntax and adding new keywords and operators.

## Table of contents
<!-- TOC depthFrom:2 depthTo:3 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Table of contents](#table-of-contents)
- [Modified syntax](#modified-syntax)
    - [Local](#local)
    - [Blocks](#blocks)
    - [Comments](#comments)
    - [Functions](#functions)
    - [And- and or-operator](#and-and-or-operator)
    - [Not-equal operator](#not-equal-operator)
    - [Negation operator](#negation-operator)
- [New syntax](#new-syntax)
    - [Assignment expressions](#assignment-expressions)
    - [Inline increment/decrement](#inline-incrementdecrement)
    - [Return expressions](#return-expressions)
    - [Lambda expressions](#lambda-expressions)
    - [Compare statements](#compare-statements)
    - [Generators](#generators)

<!-- /TOC -->

## Modified syntax
Orb starts from the default Lua syntax. From here, we can read this section to see what is different, and the next section to see what syntax has been added.

### Local
The `local` keyword is replaced with the `my` keyword. This applies in all cases where the `local` keyword would be used.

##### Orb
```
my x = 4
```

##### Lua
```lua
local x = 4
```

### Blocks
Code blocks such as those after an `if`, `for`, `repeat` or `while` loop have their begin operators (`then`, `do`) and end operators (`end`) replaced with `{` and `}`. They are also required for blocks that did not previously have those begin or end operators, such as the `repeat` statement.

##### Orb
```
if x == 4 {}
for x=1,10,1 {}
repeat {} until x == 4
while x == 4 {}
```

##### Lua
```lua
if x == 4 then end
for x=1,10,1 do end
repeat until x == 4
while x == 4 do end
```

### Comments
Since [Inline increment/decrement](#inline-incrementdecrement) operators may introduce syntactical ambiguity with Lua's `-- Single line comment` syntax, comments are now C-style.

##### Orb
```
// Wow, a single line comment!
/* Could it be?
   A multi-line comment? */
```

##### Lua
```lua
-- Wow, a single line comment!
--[[ Could it be?
   A multi-line comment? ]]
```

### Functions
The `function` keyword is shortened to `func`, along with the change to blocks laid out above.

##### Orb
```
func x() {}
my func x() {}
```

##### Lua
```lua
function x() end
local function x() end
```

### And- and or-operator
The `and` and `or` operators are replaced with `&&` and `||`.

##### Orb
```
x && y
x || y
```

##### Lua
```lua
x and y
x or y
```

### Not-equal operator
The not-equal operator `~=` is replaced with `!=`.

##### Orb
```
x != 5
```

##### Lua
```lua
x ~= 5
```

### Negation operator
The negation operator `not` is replaced with `!`.

##### Orb
```
x == !y
```

##### Lua
```lua
x == not y
```

## New syntax
Orb adds quite a bit of new syntax, most of which is regular syntax sugar. The remainder are better described as macros.

### Assignment expressions
This introduces syntax such as `a += b` as a shorter version of `a = a + b`. Note that this does not allow you to change multiple variables at once.

#### Example 1
Regular mathematical operators.

##### Orb
```
x += 4
x -= 6
x *= 8
x /= 10
x %= 12
```

##### Lua
```lua
x = x + 4
x = x - 6
x = x * 8
x = x / 10
x = x % 12
```

#### Example 2
Inline variable declarations.

##### Orb
```
if x = next() {
    return x
}
```

##### Lua
```lua
x = next()
if x then
    return x
end
```

### Inline increment/decrement
Adds support for `++` and `--` operators, before and after a variable. This is actually the same as [Assignment expressions](#assignment-expressions), but an even shorter version.

##### Orb
```
x = y++

x = --z

y, z = x++, x++
```

##### Lua
```lua
x = y
y = y + 1

z = z - 1
x = z

y, z = x, x + 1
x = x + 2
```

### Return expressions
Checking for null using `if not x then return end` statements is unnecessarily long. The `return` operator in Orb can be placed at positions where expressions are normally expected.

##### Orb
```
x = y or return

// parens may be necessary to disambiguate between 'c()' returning
// multiple results or the error message being assigned to 'b'.
a, b = c() or return (nil, 'error: c() returned nil'), d
```

##### Lua
```lua
x = y
if not x then
    return
end

a, b = c(), d
if not a then
    return nil, 'error: c() returned nil'
end
```

### Lambda expressions
Sometimes we want to pass an inline function as an argument. Since typing `func(x) {}` is still quite bloated, Orb introduces lambda functions.

##### Orb
```
x = -> 4
x = y -> y
x = (y, z) -> y, z
```

##### Lua
```lua
x = function() return 4 end
x = function(y) return y end
x = function(y, z) return y, z end
```

### Compare statements
In order to help with easily disambiguating an input value, the `compare` statement has been added. This closely resembles the typical `switch` statement, but with either single or multiple matches in one line, and without fallthrough. See below for an example.

#### Example 1
Using `compare` as a `switch` statement (still without fallthrough though!).

##### Orb
```
my x = 5
compare x {
    0 -> print('x is zero'), // if x is 0
    _ -> print('x is nonzero') // otherwise (default)
}
```

##### Lua
```
local x = 5
local __compare0 = {
    [0] = function()
        print('x is zero')
    end,
}
local __compare0_result = __compare0[x]
if __compare0_result then
    __compare0_result()
else
    print('x is nonzero')
end
```

#### Example 2
Using `compare` with it's full capabilities.

##### Orb
```
my x = 5
compare x {
    0..3 -> print('x shrunk:', x),
    5 -> print('x did not change since declaration'),
    7, 8, 9 -> print('x has grown taller:', x)
    _ -> print('x does not match any of the patterns')
}
```

##### Lua
```lua
local x = 5
local __compare0_1 = function()
    print('x has grown taller:', x)
end
local __compare0 = {
    [5] = function()
        print('x did not change since declaration')
    end,
    [7] = __compare0_1,
    [8] = __compare0_1,
    [9] = __compare0_1,
}
local __compare0_result = __compare0[x]
if __compare0_result then
    __compare0[x]()
elseif x >= 0 and x <= 3 then
    print('x shrunk:', x)
else
    print('x does not match any of the patterns')
end
```

### Import statements
It shouldn't be necessary to `require(...)` a file only to then pull the imported variables from a table into the local scope, e.g.:
```lua

```
This is there the Orb import statement comes in.

##### Orb
```
import table

from parsel import grammar, literal, pattern
```

##### Lua
```lua
local table = require('table')

local parsel = require('parsel')
local grammar, literal, pattern = parsel.grammar, parsel.literal, parsel.pattern
```

### Export statements
It's much easier to declare inline whether something should be exported or not. With Orb, you can simply prefix any variable with `export` and it will be exported.

##### Orb
```
export my x = 5

export my func = -> 4
```

##### Lua
```lua
local x = 5
local function func()
    return 4
end

return {
    x = x,
    func = func
}
```

### Generators
Orb adds generator functions. A function is a generator when the `func` keyword is suffixed with an asterisk, like so: `func*`. This enables the `yield` keyword. Refer to the examples below for the correct usage.

An interesting detail of generators is that they are functionality wise equivalent to using Lua's built-in `coroutine` library. The difference however is that Orb's generators will still work even if the `coroutine` library is not enabled.

#### Example 1
Without any looping constructs.

##### Orb
```
my func* x() {
    yield 1
    yield 2
    yield 3
}
my generator = x()
print(generator()) // true, {1}
print(generator()) // true, {2}
print(generator()) // true, {3}
print(generator()) // false, nil
```

##### Lua
```lua
-- Using generators
local function x()
    local __state = 1
    return function()
        if __state == 1 then
            __state = __state + 1
            return true, {1}
        elseif state == 2 then
            __state = __state + 1
            return true, {2}
        elseif state == 3 then
            __state = __state + 1
            return true, {3}
        end
        return false
    end
end
local generator = x()
print(generator()) -- true, {1}
print(generator()) -- true, {2}
print(generator()) -- true, {3}
print(generator()) -- false, nil

-- Using coroutines
local function x()
    return coroutine.wrap(function()
        coroutine.yield(1)
        coroutine.yield(2)
        coroutine.yield(3)
    end)
end
-- ...etc
```

#### Example 2
Using a for-loop.

##### Orb
```
my func* y(a, b) {
    for i=a,b,2 do
        yield i
    end
}
```

##### Lua
```lua
local function y(a, b)
    local __state = 1
    local __i = a
    return function()
        if __state == 1 then
            __i = __i + 2
            if __i >= b then
                __state = __state + 1
            end
            return true, {__i}
        end
        return false
    end
end
```

#### Example 3
Using a while-loop.

##### Orb
```
my func* z() {
    my i = 1
    while true {
        yield i++
    }
}
```

##### Lua
Suboptimal:
```lua
local function z()
    local i = 1
    return function()
        if __state == 1 then
            i = i + 1
            return true, {i - 1}
        end
        return false
    end
end
```

Optimal:
```lua
local function z()
    local i = 1
    return function()
        i = i + 1
        return true, {i - 1}
    end
end
```
