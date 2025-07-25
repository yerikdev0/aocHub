-- Load Fluent Library and Addons
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

-- Create the Main Window
local Window = Fluent:CreateWindow({
    Title = "AOC Hub",
    SubTitle = "Muscle Legends Script",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Theme = "Dark",
    Acrylic = false,
    MinimizeKey = Enum.KeyCode.LeftControl
})

-- Tabs
local Tabs = {
    User = Window:AddTab({ Title = "User", Icon = "user" }),
    AutoFarm = Window:AddTab({ Title = "Auto Farm", Icon = "flame" }),
    Rebirth = Window:AddTab({ Title = "Rebirth", Icon = "refresh-ccw" })
}

local Options = Fluent.Options

-- Show Player Name in User Tab
Tabs.User:AddParagraph({
    Title = "Welcome",
    Content = "Hello, " .. game.Players.LocalPlayer.Name .. "!"
})

-- === AUTO FARM TAB ===
Tabs.AutoFarm:AddButton({
    Title = "Auto Strength",
    Description = "Automatically trains strength",
    Callback = function()
        while true do
            game:GetService("ReplicatedStorage").Events["GainMuscle"]:FireServer()
            task.wait()
        end
    end
})

Tabs.AutoFarm:AddButton({
    Title = "Auto Durability",
    Description = "Automatically trains durability",
    Callback = function()
        while true do
            game:GetService("ReplicatedStorage").Events["GainDurability"]:FireServer()
            task.wait()
        end
    end
})

Tabs.AutoFarm:AddButton({
    Title = "Auto Collect Coins",
    Description = "Collects coins automatically",
    Callback = function()
        for _, coin in pairs(workspace:GetDescendants()) do
            if coin.Name == "Coin" and coin:IsA("Part") then
                coin.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
            end
        end
    end
})

-- === REBIRTH TAB ===
Tabs.Rebirth:AddToggle("AutoRebirth", {
    Title = "Auto Rebirth",
    Default = false
}):OnChanged(function(state)
    while Options.AutoRebirth.Value do
        game:GetService("ReplicatedStorage").RebirthEvent:FireServer()
        task.wait(5)
        if not Options.AutoRebirth.Value then break end
    end
end)

-- === Save/Config Tabs ===
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({})

InterfaceManager:SetFolder("AOC_Hub")
SaveManager:SetFolder("AOC_Hub/MuscleLegends")

InterfaceManager:BuildInterfaceSection(Tabs.User)
SaveManager:BuildConfigSection(Tabs.User)

-- Select initial tab and notify
Window:SelectTab(1)

Fluent:Notify({
    Title = "AOC Hub",
    Content = "Loaded successfully!",
    Duration = 6
})

-- Load saved config if any
SaveManager:LoadAutoloadConfig()
