# Home

This style guide is written by [SilentsReplacement](https://www.roblox.com/users/700264840/profile) and only intended for code that belongs to a **game of [PerduxStudios](https://www.roblox.com/groups/8876330/Perdux-Studios)**. It is
mostly similar to [Roblox Luau's Style Guide](https://roblox.github.io/lua-style-guide/) except
with cmdnoticeable changes and style changes and will continue to get changed eventually as time passes on.

## Starting principles

- Consistency is very important, code inconsistencies makes it hard
  for other developers to review your code and in general makes your code harder to maintain and less readable.

- Always declare variables and functions locally, unless you want a variable to fully accessed from any scope. Global variables that reference a table, function, userdata, thread, string will
not be garbage collected unless the global variable is set to `nil` explicitly or when the script ends.

- Never **micro optimize** code. Code should only be optimized if the performance gains are going to be significant and only when needed.

- Avoid magic values and dangerous code. Use constants over magic value that you use more than once. Constants help maintain code and will
also help you not to constantly change your code if you change any crucial value.  Magic values
are nice, unless the code doesn't work as intended and will be extremely hard to debug because most of the times you won't know what went wrong, or you would be required to change your code
because you changed a magic value to something else.

- Variable names should be verbose, even if they get a little to long. Never declare variables like `x`, `plr`, etc.  Who would want to read variables like those?!

- Don't use variables for values you only use once, don't clutter code with unnecessary variables. Again, they make code slightly more harder to maintain and harder for other developers to review your code. Exceptions include if you use a variable to reference a service,
module, or an object created from a prototype class that acts like an service.

- Keep a header for every script you code in, this helps other developers know who is the maintainer of what code and in general improves collaboration. Version control
is also a option but isn't that dynamic. The format for the header 
should be as follows:

    ```lua
    -- AUTHOR_NAME
    -- SCRIPT_NAME
    -- MONTH DATE, YEAR
    ```

- Always write clean and efficient code. Code that has certain edge cases unhandled / silent bugs
is bad code.

## File Structure

Files should consist of these things (if present) in order:

- A header.

- Services used by the file, via [GetService](https://developer.roblox.com/en-us/api-reference/function/ServiceProvider/GetService).

- [ModuleScript](https://developer.roblox.com/en-us/api-reference/class/ModuleScript)s used by the file. Note that objects created by an prototype class that refer to services or a package
are also **considered** an ModuleScript.

- Any constants used by the file.

- Variables (If they hold a reference to an object created by an prototype class, they should be first before any other variable, tables must be at the last, booleans/numbers/strings can be in any order).

- A return statement (if the file is a [ModuleScript](https://developer.roblox.com/en-us/api-reference/class/ModuleScript)).

## Requiring an module

- Use relative paths when importing modules from the same package.

```lua
local Roact = require(script.Parent.Roact)
```

- Use absolute paths when importing modules from a different package.

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Fin = require(ReplicatedStorage.Shared.Module)
```

## Naming

- Spell out words fully! Abbreviations generally make code easier to write, but harder to read.

- Use [PascalCase](https://techterms.com/definition/pascalcase#:~:text=PascalCase%20is%20a%20naming%20convention,in%20PascalCase%20is%20always%20capitalized.) for classes, enum/service-like objects and public
table members.

- Use [camelCase](https://en.wikipedia.org/wiki/Camel_case#:~:text=Camel%20case%20sometimes%20stylized%20as,word%20starting%20with%20either%20case.) for local variables. Also for private members and private functions, except prefixed with an underscore.

- Use [LOUD_SNAKE_CASE](https://en.wikipedia.org/wiki/Snake_case) names for constants.

- Modules should be named whatever they export and return. If you have a module that returns a custom yielding function which uses [RunService.Heartbeat](https://developer.roblox.com/en-us/api-reference/event/RunService/Heartbeat), the module
 should be named `Heartbeat`. 

## Prototype Classes

First of, Luau has no concept of classes, hence a class in Lua is actually a prototype class or
a object where other objects inherit from it, also known as [Prototype Inheritance](https://javascript.info/prototype-inheritance). 

First up, we create a empty table which will be the prototype class:

```lua
local MyClass = {}
```

Next, we assign the `__index` member on the class back to itself. This is a handy trick that lets us use the class's table as the metatable for instances as well.

When we construct an instance, we'll tell Lua to use our __index value to find values that are missing in our instances:

```lua
MyClass.__index = MyClass
```

In most cases, we create a default constructor for our class. By convention, we usually call it new.

Methods that don't operate on instances of our class are usually defined using a dot (.) 
(static methods) instead of a colon (:) (instance methods):

```lua
function MyClass.new()
    return setmetatable({
        phrase = "bark"
    }, MyClass)
end
```

```lua
-- This is functionally identical to `function MyClass.bark(self)`
function MyClass:bark()
    print("My phrase is", self.phrase)
end
```

We can construct instances (objects) and start tinkering with it:

```lua
local instance = MyClass.new()

-- Properties on the instance are visible, since it's just a table:
print(instance.phrase) -- "bark"

-- Methods are pulled from MyClass because of our metatable:
instance:bark() -- "My phrase is bark"

-- We can also invoke methods with a dot by explicitly passing `instance`:
MyClass.bark(instance)
instance.bark(instance)
```

Further additions you can make to your class as needed:

Introduce a `__tostring` metamethod to make debugging easier.

Add a method to check type given an instance, like:

```lua
function MyClass.IsMyClass(instance)
    return getmetatable(instance).__index == MyClass
end
```

## General Whitespace

- It's always best to indent with tabs.

- Keep lines under 140 columns wide, assuming four column wide tabs.

- Wrap comments to 100 columns wide, assuming four column wide tabs.

- This is different than normal code; the hope is that short lines help improve readability of comment prose, but is too restrictive for code.

- Don't leave whitespace at the end of lines.

- Add a newline at the end of the file.

- No vertical alignment, they make code more difficult to edit and it's hard to keep exact
  precision and is a tedious process to do so anyways.
    
    <span style="color:green">**Good**:</span>
    ```lua
    local bo = 132
    local nanako = 17
    ```
    <span style="color:red">**Bad**:</span>
    ```lua
    local bo = 132
    local nanako       =  17
    ```

- Use one statement per line. Put function bodies on new lines:

    <span style="color:green">**Good**:</span>

    ```lua
    table.sort({}, function(a, b)
        local sum = a + b
        return math.abs(sum) > 2
    end)
    ```
    <span style="color:red">**Bad**:</span>

    ```lua
    table.sort({}, function(a, b) local sum = a + b return math.abs(sum) > 2 end
    ```

- This is also true for if blocks, even if their body is just a return statement.
    Consistency is important!

    <span style="color:green">**Good**:</span>

    ```lua
    if valueIsInvalid then
        return
    end
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    if valueIsInvalid then return end   
    ```

- Put function arguments (if they're too long) in new lines and expand the whitespace for the call:

    <span style="color:green">**Good**:</span>
    ```lua
    local function foo(...)
        return ...
    end

    foo(
        1, 
        2, 
        {}, 
        {}, 

        function () 
        
        end, 
        
        function () 
            print("test") 
        end
    )
    ```

    <span style="color:red">**Bad**:</span>
    ```lua
    local function foo(...)
        return ...
    end

    foo(1, 2, {}, {},  function () end, function () print("test") end)
    ```
    
- Put a space before and after operators, except when clarifying precedence:

    <span style="color:green">**Good**:</span>
    ```lua
    print(2+2 - 4/4)
    ```
      <span style="color:red">**Bad**:</span>
    ```lua
    print(2+2-4/4)
    ```

- Avoid putting curly braces for tables on their own line. Doing so harms readability, since it forces the reader to move to another line in an awkward spot in the statement.

    <span style="color:green">**Good**:</span>

    ```lua
    local foo = {
        bar = {
            baz = "baz",
        },
    }

    frob({
        x = 1,
    })
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    local foo =
    {
        bar =

        {
            baz = "baz",
        },
    }

    frob(
    {
        x = 1,
    })
    ```

- Exception: In function calls with large inline tables or functions, sometimes it's
  more clear to put braces and functions on new lines:

    ```lua
    foo(
        {
            type = "foo",
            bo = "e",
            "2"
            {},
            {}
        },
        function(something)
            print("Hello," something)
        end
    )
    ```

- Break dictionary-like tables with more than a couple keys onto multiple lines:

    <span style="color:green">**Good**:</span>

    ```lua
    local profile = {
        Data = {
            Cash = 0,
            Level = 1
        },

        MetaData = {}
    }
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    local profile = {Data = {Cash = 0, Level = 1}, MetaData = {}}
    ```

## Guarding against silent errors

Indexing into a table in Luau gives you `nil` if the key isn't present, which can cause errors that are difficult to trace!

Our other major use case for metatables is to prevent certain forms of this problem. For types that act like enums, we can carefully apply an __index metamethod that throws:

```lua
local MyEnum = {
    A = "A",
    B = "B",
    C = "C",
}

setmetatable(MyEnum, {
    __index = function(self, key)
        error(("%s is not a valid member"):format(key))
    end,
})
```
Since the `__index` metamethod is only called when a key is missing in the table, `MyEnum.A` and `MyEnum.B` will still give you back the expected values. But `MyEnum.Typo` will result in an error. Some nice debugging practices.

## Blocks

- Don't use parentheses around the conditions in if, while, or repeat blocks. They aren't necessary in Luau and usually harm readability.

    <span style="color:green">**Good**:</span>

    ```lua
    if CONDITION then
    end

    while CONDITION do
    end
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    if (CONDITION) then
    end

    while (CONDITION) do
    end
    ```

- Use do blocks if limiting the scope of a variable is useful:

    ```lua
    local getId
    do
        local lastId = 0
        getId = function()
            lastId = lastId + 1
            return lastId
        end
    end
    ```

## String literals

- Use double quotes when declaring string literals.

    <span style="color:green">**Good**:</span>

    ```lua
    local message = "Hi"
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    local message = 'Hi'
    ```
 
- Use single quotes when declaring string literals that represent characters.

    <span style="color:green">**Good**:</span>

    ```lua
    local characterA = 'A'
    ```

    <span style="color:red">**Bad**:</span>

    ```lua
    local characterA = "A"
    ```

## Tables

- Avoid tables with both array-like and dictionary-like keys.

- Use `ipairs` to iterate over arrays, and `pairs` to iterate over dictionaries. This also helps clarify what kind of table we're expecting in a given block of code.

## Events and yielding

- Busy polling is bad practice as it constantly yields the thread back and forth. You should **always** use an event based approach instead. Also make sure to never unnecessary yield a thread!

<span style="color:green">**Good**:</span>

```lua
local Players = game:GetService("Players")

local MIN_PLAYER_COUNT = 3

-- Wait for enough players to start:
local playerCount = #Players:GetPlayers()

while playerCount < MIN_PLAYER_COUNT do
    Players.PlayerAdded:Wait()
    playerCount += 1 -- Don't use GetPlayers() unnecessary
end
```

<span style="color:red">**Bad**:</span>

```lua
local Players = game:GetService("Players")

local MIN_PLAYER_COUNT = 3

-- Wait for enough players to start:
local playerCount = #Players:GetPlayers()

while playerCount < MIN_PLAYER_COUNT do
    playerCount = #Players:GetPlayers()
    wait()
end
```

- Yield through `RunService.Heartbeat:Wait()`. Infact, you can also use a custom yielding function and wrap it in a module as well. Using `wait` is often regarded as a code smell due to
the issues it accumulates.

<span style="color:green">**Good**:</span>

```lua
local RunService = game:GetService("RunService")

local baseplate = workspace.Baseplate

-- Destroy baseplate approximately after a frame:
RunService.Heartbeat:Wait()

baseplate:Destroy()
```

<span style="color:red">**Bad**:</span>

```lua
local baseplate = workspace.Baseplate

-- Destroy baseplate approximately after 2 frames:
wait()

baseplate:Destroy()
```

## Garbage collection

- Always make sure strong references do get cleared in your code. Never have a reference
that is never cleared. An reference type object (table, userdata, string, function, thread) are
garbage collected when they have 0 references. 

- Functions that use an [external local variable](https://en.wikipedia.org/wiki/External_variable#:~:text=In%20the%20C%20programming%20language,defined%20inside%20a%20function%20block.) hold reference to them (assuming they're reference type objects). The function will only no longer hold a reference to them once the function it 
self is garbage collected.

```lua
local obj = {}

local function test()
    print(obj) -- Reference to the up value obj
end 
```

- Always disconnect events when they're no longer in need. Events hold an internal reference
to the connected callback, and that callback will not be garbage collected unless the event it self is disconnected (event's disconnected no longer reference the connected callback). You can
use a [Maid](https://devforum.roblox.com/t/how-to-use-a-maid-class-on-roblox-to-manage-state/340061) to easily track events and clean them up.

## Comments

- You don't need to necessarily use comments if the code is self explanatory. Although comments
should **both** explain why and what the code is doing in simple terms. 

- Don't use multilines for comments. Exceptions include the listing of methods/documentation on top of the code.