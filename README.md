<h1 align="center">Xyloria</h1>

<p align="center">
A lightweight, animated, theme-able UI library for Roblox.<br>
<sub>Yes, I used AI for the documentation, ya bums.</sub>
</p>

<p align="center">
<a href="#getting-started">Getting Started</a> ·
<a href="#themes">Themes</a> ·
<a href="#window-api">Window API</a> ·
<a href="#tab-api">Tab API</a> ·
<a href="#flags-and-configs">Flags</a> ·
<a href="#example-script">Example</a>
</p>

---

This documentation covers every public function exposed by **Xyloria v1.1.0**, whether you are a script developer building your first menu or an experienced scripter who just needs a quick API reference.

> [!NOTE]
> Functions, fields and methods that begin with an underscore (for example `Window:_bind`) are internal. They are not part of the public API and may change without notice.

## Table of Contents

- [What is Xyloria?](#what-is-xyloria)
- [Getting Started](#getting-started)
- [Themes](#themes)
- [Window API](#window-api)
  - [Xyloria:CreateWindow](#xyloriacreatewindow)
  - [Window:CreateTab](#windowcreatetab)
  - [Window:SelectTab](#windowselecttab)
  - [Window:Notify](#windownotify)
  - [Window:SetTheme](#windowsettheme)
  - [Window:SetAccent](#windowsetaccent)
  - [Window:SetTitle](#windowsettitle)
  - [Window:Minimize](#windowminimize)
  - [Window:Restore](#windowrestore)
  - [Window:Toggle](#windowtoggle)
  - [Window:Close](#windowclose)
  - [Window:Destroy](#windowdestroy)
  - [Window:SaveConfig](#windowsaveconfig)
  - [Window:LoadConfig](#windowloadconfig)
- [Tab API](#tab-api)
  - [Tab:CreateSection](#tabcreatesection)
  - [Tab:CreateLabel](#tabcreatelabel)
  - [Tab:CreateButton](#tabcreatebutton)
  - [Tab:CreateToggle](#tabcreatetoggle)
  - [Tab:CreateSlider](#tabcreateslider)
  - [Tab:CreateDropdown](#tabcreatedropdown)
  - [Tab:CreateTextbox](#tabcreatetextbox)
  - [Tab:CreateKeybind](#tabcreatekeybind)
- [Flags and Configs](#flags-and-configs)
- [Example Script](#example-script)

---

## What is Xyloria?

Xyloria is a single-file UI library. It gives you a draggable window with a sidebar of tabs, scrollable pages, and a set of ready-made elements: sections, labels, buttons, toggles, sliders, dropdowns, textboxes and keybinds. It also includes toast notifications, a minimize pill, five built-in themes, and optional config saving.

**Key features**

- Animated intro, tab switching, minimize and close.
- Automatic scaling on small screens (the window scales down to fit, never below 50%).
- Mouse and touch support.
- Live theme switching, with every element repainting itself.
- Every element accepts either positional arguments or a configuration table.
- Optional `Flag` system for config saving and loading.
- Uses `gethui()` when available, and falls back to `PlayerGui` otherwise.

---

## Getting Started

Load the library with `loadstring` and create a window:

```lua
local Xyloria = loadstring(game:HttpGet("https://raw.githubusercontent.com/YOURUSER/YOURREPO/main/Xyloria.lua"))()

local Window = Xyloria:CreateWindow({
	Title = "xyloria",
	Theme = "Teal",
})

local Main = Window:CreateTab("Main")
Main:CreateSection("Combat")
Main:CreateToggle("Feature 1")
```

Replace `YOURUSER/YOURREPO` with the GitHub user and repository where you host `Xyloria.lua`.

> [!IMPORTANT]
> `loadstring` and `game:HttpGet` are provided by your executor. They are not part of Xyloria.

The library table also exposes `Xyloria.Version` (a string, currently `"1.1.0"`) and `Xyloria.Themes` (the theme table described [below](#themes)).

### Positional arguments or configuration tables

Almost every function in Xyloria accepts its arguments in one of two forms. Both of these are identical:

```lua
Visuals:CreateSlider("Field Of View", 30, 120, 70)

Visuals:CreateSlider({
	Name = "Field Of View",
	Min = 30,
	Max = 120,
	Default = 70,
})
```

Use positional arguments for quick, simple elements. Use a configuration table when you need extra options such as `Flag`, `Increment` or `Suffix`, which have no positional slot.

---

## Themes

A theme is a table of `Color3` values. Xyloria ships with five themes in `Xyloria.Themes`.

| Theme | Description |
| :--- | :--- |
| `Teal` | The default. Defines every color key below. |
| `Ocean` | Teal with a blue accent `(58, 134, 255)`. |
| `Mint` | Teal with a green accent `(52, 211, 153)`. |
| `Violet` | Teal with a purple accent `(139, 112, 255)`. |
| `Rose` | Teal with a pink accent `(244, 94, 134)`. |

Only `Teal` defines the full set of keys. The other themes only override `Accent`, and every missing key is filled in from `Teal`.

### Theme keys

| Key | Used for | Teal default |
| :--- | :--- | :--- |
| `Background` | Window body, notification cards, minimized pill | `15, 15, 15` |
| `Panel` | Sidebar, content area, tracks, inputs | `22, 22, 22` |
| `Element` | Element backgrounds, selected tab | `33, 33, 33` |
| `ElementHover` | Element background while hovered | `41, 41, 41` |
| `Stroke` | Borders and dividers | `46, 46, 46` |
| `Text` | Normal text | `168, 168, 168` |
| `TextBright` | Highlighted text (titles, active elements) | `232, 232, 232` |
| `Muted` | Labels, placeholders, inactive toggle knobs | `105, 105, 105` |
| `Accent` | Selected tab, sections, filled bars, active states | `20, 184, 200` |
| `Danger` | Close button hover | `226, 84, 84` |

### Custom themes

Anywhere a theme is accepted you may pass a theme name, or a table with just the keys you want to change:

```lua
local Window = Xyloria:CreateWindow({
	Title = "xyloria",
	Theme = {
		Accent = Color3.fromRGB(255, 170, 0),
		Background = Color3.fromRGB(10, 10, 14),
	},
})
```

Missing keys are filled in from `Teal`.

---

## Window API

The `Window` is the object returned by `Xyloria:CreateWindow`. It owns the tabs, the notifications, the theme and the config flags.

### Xyloria:CreateWindow

Creates the interface and plays the intro animation.

```lua
Xyloria:CreateWindow(config: table?) -> Window
```

**Parameters**

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"xyloria"` | Text shown in the top bar. It is typed out letter by letter on load. |
| `Theme` | `string` or `table` | `"Teal"` | A theme name or a custom theme table. See [Themes](#themes). |
| `Name` | `string` | `"Xyloria"` | The name of the ScreenGui. If a GUI with this name already exists it is destroyed first. |
| `Size` | `Vector2` | `Vector2.new(460, 276)` | Window size in pixels, before automatic scaling. |
| `FontFace` | `Font` | Roboto Mono, Bold | Font used by every element. |
| `ToggleKey` | `Enum.KeyCode` or `false` | `Enum.KeyCode.RightShift` | Key that minimizes and restores the window. Pass `false` to disable it. |
| `MinimizeText` | `string` | The window `Title` | Text shown on the minimized pill. |
| `MinimizePosition` | `UDim2` | `UDim2.new(0.5, 0, 0, 32)` | Where the minimized pill appears. |
| `ConfigFolder` | `string` | `"Xyloria"` | Folder used by `SaveConfig` and `LoadConfig`. |
| `OnClose` | `function` | `nil` | Called after the window has been destroyed. |

**Returns:** `Window`, the window object.

**Window fields**

| Field | Type | Description |
| :--- | :--- | :--- |
| `Title` | `string` | The current title. |
| `Theme` | `table` | The current resolved theme. |
| `Tabs` | `{Tab}` | All tabs, in creation order. |
| `Minimized` | `boolean` | Whether the window is currently minimized. |
| `Flags` | `table` | Current values of every element created with a `Flag`. |
| `Gui` | `ScreenGui` | The root ScreenGui. |
| `ToggleKey` | `Enum.KeyCode` | The current minimize/restore key. |

**Example**

```lua
local Window = Xyloria:CreateWindow({
	Title = "My Hub",
	Theme = "Ocean",
	Size = Vector2.new(500, 300),
	ToggleKey = Enum.KeyCode.RightControl,
	OnClose = function()
		print("Window closed")
	end,
})
```

> [!NOTE]
> The window is clamped on screen while you drag it, so it can never be dragged completely out of view. On screens smaller than the window, it automatically scales down (to a minimum of 50%).

---

### Window:CreateTab

Adds a tab to the sidebar and creates its scrollable page. The first tab created is selected automatically.

```lua
Window:CreateTab(name: string) -> Tab
Window:CreateTab(config: { Name: string }) -> Tab
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `Name` | `string` | The text on the tab button. |

**Returns:** `Tab`, the tab object. Use it to add elements. See [Tab API](#tab-api).

```lua
local Main = Window:CreateTab("Main")
local Visuals = Window:CreateTab({ Name = "Visuals" })
```

---

### Window:SelectTab

Switches to a tab, with a fade and slide animation.

```lua
Window:SelectTab(tab: Tab | string, instant: boolean?)
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `tab` | `Tab` or `string` | The tab object, or the name of a tab. Unknown names are ignored. |
| `instant` | `boolean?` | When `true`, skips the animation. |

```lua
Window:SelectTab("Settings")
Window:SelectTab(Main, true)
```

---

### Window:Notify

Shows a toast notification in the bottom-right corner. Notifications slide in, show a countdown bar, and can be dismissed by clicking them.

```lua
Window:Notify(title: string, content: string?, duration: number?) -> () -> ()
Window:Notify(config: { Title: string, Content: string?, Duration: number? }) -> () -> ()
```

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"xyloria"` | The heading of the notification. |
| `Content` | `string` | `""` | The body text. It wraps and the card resizes to fit. |
| `Duration` | `number` | `4` | Seconds before it disappears. |

**Returns:** `function`. Calling it dismisses the notification early.

```lua
Window:Notify("xyloria", "Interface loaded.", 3)

local dismiss = Window:Notify({
	Title = "Heads up",
	Content = "This one can be dismissed from code.",
	Duration = 10,
})
task.wait(2)
dismiss()
```

---

### Window:SetTheme

Changes the theme at runtime. Every element is repainted with a smooth transition.

```lua
Window:SetTheme(theme: string | table)
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `theme` | `string` or `table` | A theme name, or a table of keys to override. Only the keys you give are changed. |

Unknown theme names are ignored.

```lua
Window:SetTheme("Violet")
Window:SetTheme({ Background = Color3.fromRGB(8, 8, 12) })
```

---

### Window:SetAccent

A shortcut for changing only the accent color.

```lua
Window:SetAccent(color: Color3)
```

```lua
Window:SetAccent(Color3.fromRGB(255, 120, 0))
```

---

### Window:SetTitle

Changes the title text in the top bar.

```lua
Window:SetTitle(text: string)
```

```lua
Window:SetTitle("My Hub v2")
```

---

### Window:Minimize

Collapses the window into a small pill. The pill can be dragged around, and clicking it restores the window.

```lua
Window:Minimize()
```

Does nothing if the window is already minimized or closing.

---

### Window:Restore

Expands the window from the minimized pill.

```lua
Window:Restore()
```

Does nothing if the window is not minimized.

---

### Window:Toggle

Minimizes the window if it is open, and restores it if it is minimized. This is what the `ToggleKey` calls.

```lua
Window:Toggle()
```

---

### Window:Close

Plays the closing animation and then destroys the window.

```lua
Window:Close()
```

When the window has been destroyed, `OnClose` is called if you provided one.

```lua
Settings:CreateButton("Unload UI", function()
	Window:Close()
end)
```

---

### Window:Destroy

Destroys the window immediately, without an animation. All connections are disconnected and the ScreenGui is removed.

```lua
Window:Destroy()
```

Use [`Close`](#windowclose) instead if you want the animated exit.

---

### Window:SaveConfig

Saves every element that has a `Flag` to a JSON file.

```lua
Window:SaveConfig(name: string) -> boolean
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The config name. Saved to `<ConfigFolder>/<name>.json`. |

**Returns:** `boolean`. `true` if the file was written, `false` otherwise.

> [!WARNING]
> `SaveConfig` needs executor filesystem functions. It requires `writefile` (and uses `makefolder` and `isfolder` when available). If `writefile` does not exist, the function returns `false`.

```lua
Main:CreateToggle({ Name = "Auto Farm", Flag = "autofarm" })

Window:SaveConfig("default")
```

---

### Window:LoadConfig

Loads a config saved by `SaveConfig` and applies every value to the matching element.

```lua
Window:LoadConfig(name: string) -> boolean
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The config name to load. |

**Returns:** `boolean`. `true` if the file was read and parsed, `false` if `readfile` is missing, the file does not exist, or the file is not valid JSON.

Flags found in the file that do not match any element are ignored. Loading triggers each element's callback, just as if the value had been changed by the user.

```lua
if not Window:LoadConfig("default") then
	Window:Notify("Config", "No saved config found.", 3)
end
```

---

## Tab API

A `Tab` is returned by `Window:CreateTab`. Elements are added to the page in the order they are created.

### Tab:CreateSection

Adds a heading with an underline. Sections are used to group elements. The text is displayed in uppercase, in the accent color.

```lua
Tab:CreateSection(name: string) -> { Instance: Frame }
Tab:CreateSection(config: { Name: string }) -> { Instance: Frame }
```

```lua
Main:CreateSection("Combat")
```

---

### Tab:CreateLabel

Adds a block of muted, wrapping text.

```lua
Tab:CreateLabel(text: string) -> Label
Tab:CreateLabel(config: { Text: string }) -> Label
```

| Member | Description |
| :--- | :--- |
| `Instance` | The label `TextLabel`. |
| `Label:Set(text)` | Changes the displayed text. |

```lua
local info = Settings:CreateLabel("Press RightShift to minimize or restore the window.")
info:Set("Press RightControl to minimize or restore the window.")
```

---

### Tab:CreateButton

Adds a clickable button.

```lua
Tab:CreateButton(name: string, callback: function?) -> Button
Tab:CreateButton(config: { Name: string, Callback: function? }) -> Button
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `Name` | `string` | The button text. |
| `Callback` | `function` | Called when the button is clicked. Receives no arguments. |

**Returns**

| Member | Description |
| :--- | :--- |
| `Name` | The button text. |
| `Callback` | The current callback. It can be replaced. |
| `Instance` | The root `Frame`. |
| `Button:Fire()` | Plays the click flash and runs the callback, as if the user clicked it. |

```lua
local button = Settings:CreateButton("Test Notification", function()
	Window:Notify("xyloria", "Notifications are working.", 4)
end)

button:Fire()
```

---

### Tab:CreateToggle

Adds an on/off switch.

```lua
Tab:CreateToggle(name: string, default: boolean?, callback: function?) -> Toggle
Tab:CreateToggle(config: table) -> Toggle
```

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Name` | `string` | `"Toggle"` | The label text. |
| `Default` | `boolean` | `false` | The starting state. Only `true` turns it on. |
| `Callback` | `function` | `nil` | Called with the new state `(value: boolean)`. |
| `Flag` | `string` | `nil` | Registers the toggle for config saving. See [Flags](#flags-and-configs). |

**Returns**

| Member | Description |
| :--- | :--- |
| `Value` | The current state. |
| `Toggle:Set(value, silent?)` | Sets the state. If `silent` is `true`, the callback is not called. |
| `Toggle:Get()` | Returns the current state. |

```lua
local esp = Visuals:CreateToggle({
	Name = "ESP",
	Default = false,
	Flag = "esp",
	Callback = function(enabled)
		print("ESP:", enabled)
	end,
})

esp:Set(true)         -- runs the callback
esp:Set(false, true)  -- does not run the callback
print(esp:Get())
```

> [!NOTE]
> Setting a toggle to the value it already has does nothing, and does not run the callback.

---

### Tab:CreateSlider

Adds a draggable slider with a value readout.

```lua
Tab:CreateSlider(name: string, min: number, max: number, default: number?, callback: function?) -> Slider
Tab:CreateSlider(config: table) -> Slider
```

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Name` | `string` | `"Slider"` | The label text. |
| `Min` | `number` | `0` | The minimum value. |
| `Max` | `number` | `100` | The maximum value. If it is not greater than `Min`, it becomes `Min + 1`. |
| `Default` | `number` | `Min` | The starting value. |
| `Increment` | `number` | `1` | The step size. Values snap to it. Must be greater than 0. *Config table only.* |
| `Suffix` | `string` | `""` | Text appended to the readout, for example `"%"`. *Config table only.* |
| `Callback` | `function` | `nil` | Called with the new value `(value: number)`. |
| `Flag` | `string` | `nil` | Registers the slider for config saving. |

The number of decimals shown in the readout is worked out automatically from `Increment`, up to 4 places.

**Returns**

| Member | Description |
| :--- | :--- |
| `Value` | The current value. |
| `Min`, `Max` | The configured range. |
| `Slider:Set(value, silent?)` | Clamps and snaps the value, updates the slider, runs the callback. |
| `Slider:Get()` | Returns the current value. |

```lua
Visuals:CreateSlider("Field Of View", 30, 120, 70)

local opacity = Visuals:CreateSlider({
	Name = "Opacity",
	Min = 0,
	Max = 1,
	Default = 0.5,
	Increment = 0.05,
	Flag = "opacity",
	Callback = function(value)
		print("Opacity:", value)
	end,
})

opacity:Set(0.8)
```

> [!NOTE]
> While you drag a slider, scrolling of its page is temporarily disabled so the page does not move under your finger.

---

### Tab:CreateDropdown

Adds a dropdown that expands to show a list of options. Up to five options are visible at once; longer lists scroll.

```lua
Tab:CreateDropdown(name: string, options: {any}, default: any?, callback: function?) -> Dropdown
Tab:CreateDropdown(config: table) -> Dropdown
```

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Name` | `string` | `"Dropdown"` | The label text. |
| `Options` | `{any}` | `{}` | The list of options. Each is shown with `tostring`. |
| `Default` | `any` | The first option | The starting selection. |
| `Callback` | `function` | `nil` | Called with the selected option `(value)`. |
| `Flag` | `string` | `nil` | Registers the dropdown for config saving. |

**Returns**

| Member | Description |
| :--- | :--- |
| `Value` | The current selection. |
| `Options` | The current options list. |
| `Open` | Whether the list is expanded. |
| `Dropdown:Set(value, silent?)` | Selects a value. If `silent` is `true`, the callback is not called. |
| `Dropdown:Get()` | Returns the current selection. |
| `Dropdown:Toggle(state?)` | Opens or closes the list. With no argument it flips the current state. |
| `Dropdown:Refresh(options)` | Replaces the list of options. If the current value is no longer in it, the first option is selected. |

```lua
local mode = Misc:CreateDropdown("Mode", { "Normal", "Fast", "Safe", "Custom" }, "Normal")

Settings:CreateDropdown({
	Name = "Theme",
	Options = { "Teal", "Ocean", "Mint", "Violet", "Rose" },
	Default = "Teal",
	Callback = function(name)
		Window:SetTheme(name)
	end,
})

mode:Refresh({ "Normal", "Fast" })
mode:Set("Fast")
```

> [!NOTE]
> `Refresh` does not run the callback, even if it changes the selected value.

---

### Tab:CreateTextbox

Adds a single-line text input.

```lua
Tab:CreateTextbox(name: string, default: string?, placeholder: string?, callback: function?) -> Textbox
Tab:CreateTextbox(config: table) -> Textbox
```

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Name` | `string` | `"Textbox"` | The label text. |
| `Default` | `string` | `""` | The starting text. |
| `Placeholder` | `string` | `"type here"` | Text shown when the box is empty. |
| `ClearTextOnFocus` | `boolean` | `false` | Clears the text when the box is focused. *Config table only.* |
| `Callback` | `function` | `nil` | Called when the box loses focus: `(value: string, enterPressed: boolean)`. |
| `Flag` | `string` | `nil` | Registers the textbox for config saving. |

The value is only committed, and the callback only runs, when the box loses focus.

**Returns**

| Member | Description |
| :--- | :--- |
| `Value` | The last committed text. |
| `Textbox:Set(value, silent?)` | Sets the text. The callback receives `enterPressed = false`. |
| `Textbox:Get()` | Returns the last committed text. |

```lua
local name = Misc:CreateTextbox("Name", "", "type here")

Misc:CreateTextbox({
	Name = "Webhook",
	Placeholder = "paste url",
	Flag = "webhook",
	Callback = function(text, enterPressed)
		print(text, enterPressed)
	end,
})
```

---

### Tab:CreateKeybind

Adds a button that lets the user pick a keyboard key.

```lua
Tab:CreateKeybind(name: string, default: Enum.KeyCode | string?, callback: function?, onChanged: function?) -> Keybind
Tab:CreateKeybind(config: table) -> Keybind
```

| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Name` | `string` | `"Keybind"` | The label text. |
| `Default` | `Enum.KeyCode` or `string` | `nil` | The starting key. A string is looked up in `Enum.KeyCode`, e.g. `"E"`. Unknown names result in no key. |
| `Callback` | `function` | `nil` | Called with the key `(key: Enum.KeyCode)` when it is pressed. |
| `OnChanged` | `function` | `nil` | Called with the new key `(key: Enum.KeyCode?)` when the binding changes. |
| `Flag` | `string` | `nil` | Registers the keybind for config saving. The key is stored by name. |

**How rebinding works**

Click the key button to start listening. It shows `...`, then:

- Press any key to bind it.
- Press `Escape` to cancel and keep the current key.
- Press `Backspace` to clear the binding (it shows `None`).

While a keybind is listening, the window's `ToggleKey` is suspended, so you can bind `RightShift` without the window minimizing.

The `Callback` does not fire for key presses that Roblox has already processed, such as while typing in a text box or chat.

**Returns**

| Member | Description |
| :--- | :--- |
| `Value` | The current `Enum.KeyCode`, or `nil`. |
| `Listening` | Whether the keybind is waiting for a key. |
| `Keybind:Set(value, silent?)` | Sets the key. Accepts an `Enum.KeyCode`, a name string, or `nil`. `silent` skips `OnChanged`. |
| `Keybind:Get()` | Returns the current `Enum.KeyCode`, or `nil`. |

```lua
Misc:CreateKeybind("Keybind", "E")

Misc:CreateKeybind({
	Name = "Fly",
	Default = Enum.KeyCode.F,
	Flag = "flykey",
	Callback = function(key)
		print(key.Name, "pressed")
	end,
	OnChanged = function(key)
		print("Rebound to", key and key.Name or "None")
	end,
})
```

---

## Flags and Configs

Any element created from a configuration table may be given a `Flag`: a unique string name. Flagged elements:

- Have their current value stored in `Window.Flags[flag]`, which is kept up to date automatically.
- Are saved by `Window:SaveConfig` and restored by `Window:LoadConfig`.

```lua
Main:CreateToggle({ Name = "Auto Farm", Flag = "autofarm" })
Main:CreateSlider({ Name = "Speed", Min = 1, Max = 10, Default = 3, Flag = "speed" })

print(Window.Flags.autofarm, Window.Flags.speed)
```

| Element | Value stored in `Flags` |
| :--- | :--- |
| Toggle | `boolean` |
| Slider | `number` |
| Dropdown | The selected option |
| Textbox | `string` |
| Keybind | The key name as a string, or `"None"` |

> [!NOTE]
> Buttons, labels and sections have no value, so they do not take a `Flag`. Flags are saved as JSON, so dropdown options should be strings or numbers if you plan to use configs.

### Common element methods

Toggles, sliders, dropdowns, textboxes and keybinds share the following members.

| Member | Description |
| :--- | :--- |
| `Set(value, silent?)` | Changes the value. Runs the callback unless `silent` is `true`. |
| `Get()` | Returns the current value. |
| `Instance` | The root `Frame`, for custom tweaks. |
| `Name` | The label text the element was created with. |
| `Callback` | The current callback. Assign a new function to replace it. |

Callbacks are always run in their own thread with `task.spawn`, so an error or a yield in a callback will not break the UI.

---

## Example Script

The following script creates a window with four tabs and demonstrates every element type.

```lua
local Xyloria = loadstring(game:HttpGet("https://raw.githubusercontent.com/YOURUSER/YOURREPO/main/Xyloria.lua"))()

local Window = Xyloria:CreateWindow({
	Title = "xyloria",
	Theme = "Teal",
})

local Main = Window:CreateTab("Main")
Main:CreateSection("Combat")
Main:CreateToggle("Feature 1")
Main:CreateToggle("Feature 2")
Main:CreateToggle("Feature 3")
Main:CreateSection("Movement")
Main:CreateToggle("Feature 4")
Main:CreateToggle("Feature 5")

local Visuals = Window:CreateTab("Visuals")
Visuals:CreateSection("Overlay")
Visuals:CreateToggle("Feature 6")
Visuals:CreateToggle("Feature 7")
Visuals:CreateSlider("Field Of View", 30, 120, 70)
Visuals:CreateSlider({ Name = "Opacity", Min = 0, Max = 1, Default = 0.5, Increment = 0.05 })

local Misc = Window:CreateTab("Misc")
Misc:CreateSection("Extras")
Misc:CreateToggle("Feature 8")
Misc:CreateToggle("Feature 9")
Misc:CreateDropdown("Mode", { "Normal", "Fast", "Safe", "Custom" }, "Normal")
Misc:CreateTextbox("Name", "", "type here")
Misc:CreateKeybind("Keybind", "E")

local Settings = Window:CreateTab("Settings")
Settings:CreateSection("Interface")
Settings:CreateDropdown({
	Name = "Theme",
	Options = { "Teal", "Ocean", "Mint", "Violet", "Rose" },
	Default = "Teal",
	Callback = function(name)
		Window:SetTheme(name)
	end,
})
Settings:CreateButton("Test Notification", function()
	Window:Notify("xyloria", "Notifications are working.", 4)
end)
Settings:CreateButton("Unload UI", function()
	Window:Close()
end)
Settings:CreateLabel("Press RightShift to minimize or restore the window.")

Window:Notify("xyloria", "Interface loaded.", 3)
```

### What each part does

| Part | Result |
| :--- | :--- |
| `CreateWindow({ Title, Theme })` | Opens the window with the Teal theme and types out the title. |
| `CreateTab("Main")` | Adds a tab. The first tab is selected automatically. |
| `CreateSection("Combat")` | Adds a heading to group the toggles below it. |
| `CreateToggle("Feature 1")` | Adds a toggle with no callback (a placeholder). |
| `CreateSlider("Field Of View", 30, 120, 70)` | Positional slider from 30 to 120, starting at 70. |
| `CreateSlider({ ... Increment = 0.05 })` | Table form, stepping in 0.05 increments. |
| `CreateDropdown("Mode", {...}, "Normal")` | Dropdown with four options, starting on `"Normal"`. |
| `CreateTextbox("Name", "", "type here")` | Empty textbox with a placeholder. |
| `CreateKeybind("Keybind", "E")` | Keybind starting on the `E` key. |
| Theme dropdown `Callback` | Calls `Window:SetTheme(name)` so the whole UI changes color live. |
| `Window:Close()` | Animated close, which unloads the UI. |
| `Window:Notify(...)` | Shows a three-second toast when the interface has loaded. |

---

<p align="center">Thank you for being here.</p>

<p align="right"><a href="#xyloria">Back to top</a></p>
