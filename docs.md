# Fluent Interface
## Booting
- ### Interface Library
```lua
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Fluent.Unloaded -- is true if the UI is closed
```
- ### Save Manager Library
```lua
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
```
- ### Interface Manager Library
```lua
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
```

It is recommended to use all of these together.

## Creating a Window
```lua
local Window = Fluent:CreateWindow({
    Title = "Fluent " .. Fluent.Version,
    SubTitle = "by dawid",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true, -- The blur may be detectable, setting this to false disables blur entirely
    Theme = "Dark", -- Dark, Darker, Light, Aqua, Amethyst, Rose
    MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})
```

## Notifying
```lua
Fluent:Notify({
    Title = "Notification",
    Content = "This is a notification",
    SubContent = "SubContent", -- Optional
    Duration = 5 -- Set to nil to make the notification not disappear
})
```

## Dialog
```lua
Window:Dialog({
    Title = "Title",
    Content = "This is a dialog",
    Buttons = {
        {
            Title = "Confirm",
            Callback = function()
                print("Confirmed the dialog.")
            end
        },
        {
            Title = "Cancel",
            Callback = function()
                print("Cancelled the dialog.")
            end
        }
    }
})
```

## Creating Tabs
```lua
local Main = Window:AddTab({ Title = "Main", Icon = "globe" }) -- Title, Icon
local Options = Fluent.Options -- Ensure this is here, it stores data about components
```

A full list of icons can be found [here.](https://github.com/dawid-scripts/Fluent/blob/master/src/Icons.lua)
Use the names of icons without "lucide-" at the start.

## Components
- ### Paragraph
```lua
Main:AddParagraph({
    Title = "Paragraph",
    Content = "This is a paragraph.\nSecond line!"
})
```

- ### Button
```lua
Main:AddButton({
    Title = "Button",
    Description = "Prints 'Hello world!' to the console.",
    Callback = function()
        print("Hello world!")
    end
})
```

- ### Toggle
```lua
local Toggle = Main:AddToggle("ToggleIdentifier", {
    Title = "Toggle Name",
    Default = false
})

-- Toggle identifier is the name of the table in Options that represents the table you have created
-- Toggle name is the text that shows on the UI

Toggle:OnChanged(function()
    print("Toggle state:", Options.ToggleIdentifier.Value)
end)

Options.ToggleIdentifier:SetValue(true) -- true or false
```

- ### Slider
```lua
local Slider = Main:AddSlider("SliderIdentifier", {
    Title = "Slider Name",
    Description = "Walkspeed Changer",
    Default = game.Players.LocalPlayer.Character.Humanoid.WalkSpeed, -- Current slider value
    Min = 0, -- Minimum slider value
    Max = 200, -- Maximum slider value
    Rounding = 10, -- The increment of increase / decrease
    Callback = function(Value) -- Value?: number value
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
    end
})

Slider:SetValue(16)
```

- ### Dropdown
```lua
local Dropdown = Main:AddDropdown("DropdownIdentifier", {
    Title = "Dropdown Name",
    Description = "Number selector",
    Values = {"one", "two", "three"}, -- Values that can be selected in the dropdown (string only)
    Multi = false, -- Set to true to allow users to select multiple values from the dropdown
    Default = 1 -- Index of the value that's selected by default
})

Dropdown:SetValue("one")
Dropdown:OnChanged(function(Value) -- Value ?: string
    print(Value, "was selected")
end)
```

- ### Color Picker
```lua
local Colorpicker = Main:AddColorpicker("ColorpickerIdentifier", {
    Title = "Colorpicker Name",
    Default = Color3.fromRGB(255, 0, 0),
    Description = "Allows to select colors",
    Transparency = 0 -- Optionally allow users to set a transparency on colors
})

Colorpicker:OnChanged(function()
    print("Color:", Colorpicker.Value) -- ?: RBG value, "Optional Transparency:", Colorpicker.Transparency -- ?: number)
end)
```

- ### Keybind
```lua
local Keybind = Main:AddKeybind("KeybindIdentifier", {
    Title = "Keybind Name",
    Mode = "Toggle", -- Always, Toggle, Hold
    Default = "E", -- String as the input of the keybind (MB1, MB2 for mouse buttons)
    Callback = function(Value) -- Value ?: boolean | Called when keybind is held down / released
        print("Keybind clicked!", Value)
    end,
    ChangedCallback = function(New) -- New ?: string | Called when the input keybind is changed
        print("Keybind changed to", New)
    end
})

task.spawn(function()
    while not Fluent.Unloaded and Keybind:GetState() and task.wait(1) do -- :GetState() returns true while the keybind is being held down
        print("Keybind is being held down")
    end
end)

Keybind:SetValue("Q", "Toggle")
```

- ### Textbox
```lua
local Textbox = Main:AddInput("TextboxIdentifier", {
    Title = "Textbox Name",
    Default = "Default Value",
    PlaceHolder = "Placeholder",
    Numeric = false, -- Only allows numbers
    Finished = true, -- Only calls the callback when you've pressed enter
    Callback = function(Value) -- Value ?: string
        print("Value:", Value)
    end
})
```

## Add-ons
- ### Save Manager
Allows your UI to have a configuration system that saves across executions.
```lua
SaveManager:SetLibrary(Fluent) -- Sets the current UI library instance
SaveManager:IgnoreThemeSettings() -- Ignores the current theme selected while saving preferences
SaveManager:SetIgnoreIndexes({'TextboxIdentifier'}) -- Sets an ignore list of UI components you don't want saved
SaveManager:SetFolder("ParentFolder/SpecificFolder")

local Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
SaveManager:BuildConfigSection(Settings) -- Assigns a tab where users can edit settings manually

SaveManager:LoadAutoloadConfig() -- Loads the saved configuration (SaveManager:SetFolder required), recommended to do at the end of your code
```

- ### Interface Manager
Allows your UI to have an interface management system.
```lua
InterfaceManager:SetLibrary(Fluent)
InterfaceManager:SetFolder("ParentFolder")

local Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
InterfaceManager:BuildInterfaceSection(Settings)
```

## Misc
### Auto Selecting Tab
This should be close to the end of your code, first tab is recommended.
```lua
Window:SelectTab(1) -- Selects the first tab
```
