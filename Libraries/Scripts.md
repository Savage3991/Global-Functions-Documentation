# Scripts

The **Script** library provides functions that access to script environments and internal state.

---

## getscriptbytecode

> [!NOTE]
> This function should return `nil` if the script has no bytecode.
> We encourage this behavior, as it's easier for people to check for `nil`, rather than each executor having its own output.

```luau
function getscriptbytecode(script: Script | LocalScript | ModuleScript): string | nil
```

### Parameter

- `script` - The `Script`, `LocalScript` or `ModuleScript` the bytecode would be obtained from.

### Example

```luau
local AnimateScriptBytecode = getscriptbytecode(game.Players.LocalPlayer.Character.Animate)
print(AnimateScriptBytecode) -- Returns a string with the bytecode.

print(getscriptbytecode(Instance.new("LocalScript"))) -- Output: nil
```

---

## getscripthash

> [!NOTE]
> Hash the compressed and encrypted bytecode, don't decrypt and decompress it.
> 
> This function should return `nil` if the script has no bytecode.
> We encourage this behavior, as it's easier for people to check for `nil`, rather than each executor having its own output.

Returns a `SHA384` hash represented in hex of the module/script's bytecode.

```luau
function getscripthash(script: Script | LocalScript | ModuleScript): string | nil
```

### Parameter

- `script` - The script instance to get the hash of.

### Example

```luau
local ScriptHash = getscripthash(game.Players.LocalPlayer.Character.Animate)
print(ScriptHash) -- Should return a non-changing SHA384 hash in hex representation

print(getscripthash(Instance.new("LocalScript"))) -- Output: nil
```

---

## getscriptclosure

Creates a new closure (function) from the module/script's bytecode. The game does not use the function you will get, as it's usually used to retrieve constants.

```luau
function getscriptclosure(script: Script | LocalScript | ModuleScript): (...any) -> (...any) | nil
```

### Parameter

- `script` - The script instance to get its closure.

### Example

```luau
local AnimateScript = game.Players.LocalPlayer.Character.Animate
print(getscriptclosure(AnimateScript)) -- Output: function 0x...

print(getscriptclosure(Instance.new("LocalScript"))) -- Output: nil
```

---

## getsenv

Returns the L->gt table of the script's Lua state. This function should allow getting the environment even if game devs set the `script` global to `nil`.

```luau
function getsenv(script: Script | LocalScript | ModuleScript): { [any]: any }
```

### Parameter

- `script` - The module/script the function gets the globals table of.

### Example

```luau
local ScriptEnv = getsenv(game.Players.LocalPlayer.Character.Animate)
print(ScriptEnv.onSwimming) -- Output: function 0x...

print(getsenv(Instance.new("LocalScript"))) -- Throws an error
```

---

## getscripts

Returns all the scripts in the game, CoreScripts should be filtered by default.

```luau
function getscripts(): { Script | LocalScript | ModuleScript }
```

### Example

```luau
local DummyScript = Instance.new("LocalScript")

for _, ValueScript in pairs(getscripts()) do
    if (ValueScript == DummyScript) then
        print(`Found the script {DummyScript}`)
    end
end
```

---

## getrunningscripts

Returns all the running scripts in the caller's global state, CoreScripts should be filtered by default.

```luau
function getrunningscripts(): { LocalScript | ModuleScript | Script }
```

### Example

```luau
local DummyScript = game.Players.LocalPlayer.Character.Animate
local DummyScript2 = Instance.new("LocalScript")

for _, ValueScript in pairs(getrunningscripts()) do
    if (ValueScript == DummyScript) then
        print(`Found the running script {DummyScript}`)
    elseif (ValueScript == DummyScript2) then
        print("Should never happen")
    end
end
```

---

## getloadedmodules

Returns all the loaded modules in the caller's global state, CoreScripts should be filtered by default.

```luau
function getloadedmodules(): { ModuleScript }
```

### Example

```luau
local DummyModule = Instance.new("ModuleScript")
local DummyModule2 = Instance.new("ModuleScript")

pcall(require, DummyModule)

for _, ValueModule in pairs(getloadedmodules()) do
    if ValueModule == DummyModule then
        print(`Found the loaded {DummyModule}`)
    elseif ValueModule == DummyModule2 then
        print("Should never happen")
    end
end
```

---

## getcallingscript

Returns the script that's running the current Luau code. This shouldn't just look for the `script` global, as game devs can set it to `nil` and break this approach.

```luau
function getcallingscript(): LocalScript | ModuleScript | Script | nil
```

### Example

```luau
local Old; Old = hookmetamethod(game, "__index", function(t, k)
    if not checkcaller() then
        local callingScript = getcallingscript() -- Should return a foreign script

        warn("__index called from script:", callingScript:GetFullName())

        hookmetamethod(game, "__index", old)
        return old(t, k)
    end
end)

print(getcallingscript().Name)
```

---

## getthreadidentity

Returns the caller's thread identity.

```luau
function getthreadidentity(): number
```

### Example

```luau
setthreadidentity(3)
print(getthreadidentity()) -- Output: 3
setthreadidentity(7)
print(getthreadidentity()) -- Output: 7
```

---

## setthreadidentity

Sets the caller's identity, and capabilities matching that identity.

```luau
function setthreadidentity(input: number): ()
```

### Parameter

- `input` - The wanted identity to set.

### Examples

```luau
local Success, Result = pcall(function()
	setthreadidentity(0)
	return game.DataCost
end)

print(Success) -- Output: false
print(Result) -- Output: The current thread cannot read 'DataCost'...
```

```luau
local Success, Result = pcall(function()
	setthreadidentity(8)
	return Instance.new("Player")
end)

print(Success) -- Output: true
print(typeof(Result)) -- Output: Instance
print(Result.AccountAge) -- Output: 0
```


---

## loadstring

Compiles the given string, and returns it runnable in a function.

```luau
function loadstring<A...>(src: string, chunkname: string?): ((A...) -> any | nil, string?)
```

### Parameters

- `src` - The source to compile.
- `chunkname` - Name of the chunk.

### Examples

```luau
loadstring([[
    Placeholder = {"Example"}
]])()

print(Placeholder[1]) -- Output: Example
```

```luau
local Func, Err = loadstring("Example = ", "CustomName")

print(`{Func}, {Err}`) -- Output: nil, [string "CustomName"]:1: Expected identifier when parsing expression, got <eof>
```
