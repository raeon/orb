# orb
*I'll have one Lua with sugar, please.*

Orb is an alternative syntax for Lua. It aims to let you to create more functionality with less typing. It accomplishes this by shortening normal Lua syntax and adding new keywords and operators.

## Table of contents
<!-- TOC depthFrom:2 depthTo:3 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Table of contents](#table-of-contents)
- [Modified syntax](#modified-syntax)
	- [Local](#local)
	- [Blocks](#blocks)
	- [Functions](#functions)
	- [Not-equal operator](#not-equal-operator)
	- [Negation operator](#negation-operator)
- [New syntax](#new-syntax)
	- [Assignment expressions](#assignment-expressions)
	- [Inline increment/decrement](#inline-incrementdecrement)
	- [Lambda expressions](#lambda-expressions)
	- [Generators](#generators)

<!-- /TOC -->

## Modified syntax
Orb starts from the default Lua syntax. From here, we can read this section to see what is different, and the next section to see what syntax sugar has been added.

### Local
The `local` keyword is replaced with the `let` keyword. This applies in all cases where the `local` keyword would be used.

##### Orb
```
let x = 4
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

### Functions
The `function` keyword is shortened to `func`, along with the change to blocks laid out above.

##### Orb
```
func x() {}
let func x() {}
```

##### Lua
```lua
function x() end
local function x() end
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
This introduces syntax such as `+=` as a shorter version of `= var +`. Note that this does not allow you to change multiple variables at once.

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

### Inline increment/decrement
Adds support for `++` and `--` operators, before and after a variable.

##### Orb
```
x = y++

x = --z
```

##### Lua
```lua
x = y
y = y + 1

z = z - 1
x = z
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

### Generators
Orb adds generator functions. A function is a generator when the `func` keyword is suffixed with an asterisk, like so: `func*`. This enables the `yield` keyword. Refer to the examples below for the correct usage.

#### Example 1
Without any looping constructs.

##### Orb
```
let func* x() {
    yield 1
    yield 2
    yield 3
}
let generator = x()
print(generator()) -- true, {}
print(generator())
print(generator())
```

##### Lua
```lua
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
print(generator())
print(generator())
print(generator())
```

#### Example 2
Using a for-loop.

##### Orb
```
let func* y(a, b) {
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
let func* z() {
    let i = 1
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
