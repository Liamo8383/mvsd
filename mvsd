local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Rayfield Example Window",
   Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "Rayfield Interface Suite",
   LoadingSubtitle = "by Sirius",
   Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil, -- Create a custom folder for your hub/game
      FileName = "Big Hub"
   },

   Discord = {
      Enabled = false, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "noinvitelink", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ABCD would be ABCD
      RememberJoins = true -- Set this to false to make them join the discord every time they load it up
   },

   KeySystem = false, -- Set this to true to use our key system
   KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided", -- Use this to tell the user how to get a key
      FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
      Key = {"Hello"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})

local Aim = Window:CreateTab("Aim", 4483362458) -- Title, Image
local Section = Aim:CreateSection("Main")

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local SilentAimEnabled = false

local HighlightFolder = Instance.new("Folder", game.CoreGui)
HighlightFolder.Name = "SilentAimHighlights"

-- Function to check if a player is alive
local function isPlayerAlive(player)
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        return player.Character.Humanoid.Health > 0
    end
    return false
end

-- Function to check if a player is visible
local function isPlayerVisible(targetPart)
    local origin = Camera.CFrame.Position
    local direction = (targetPart.Position - origin).Unit * (targetPart.Position - origin).Magnitude
    local rayParams = RaycastParams.new()
    rayParams.FilterType = Enum.RaycastFilterType.Blacklist
    rayParams.FilterDescendantsInstances = {LocalPlayer.Character}

    local result = workspace:Raycast(origin, direction, rayParams)
    return result and result.Instance:IsDescendantOf(targetPart.Parent)
end

-- Function to equip the second tool in the local player's backpack
local function equipSecondTool()
    local backpack = LocalPlayer:FindFirstChild("Backpack")
    if backpack then
        local tools = backpack:GetChildren()
        if #tools >= 2 then
            local secondTool = tools[2]
            LocalPlayer.Character.Humanoid:EquipTool(secondTool)
        end
    end
end

-- Custom function to simulate Mouse1Click
local function mouse1click()
    UserInputService.InputBegan:Fire({
        UserInputType = Enum.UserInputType.MouseButton1,
        UserInputState = Enum.UserInputState.Begin
    })
    wait(0.1)
    UserInputService.InputEnded:Fire({
        UserInputType = Enum.UserInputType.MouseButton1,
        UserInputState = Enum.UserInputState.End
    })
end

-- Function to aim at the target's head
local function aimAtTarget(targetPart)
    local targetPosition = targetPart.Position
    Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPosition)
end

-- Function to create a highlight for the target
local function createHighlight(player)
    local highlight = Instance.new("Highlight")
    highlight.Name = player.Name .. "_Highlight"
    highlight.Adornee = player.Character
    highlight.FillColor = Color3.fromRGB(0, 255, 255) -- Cyan
    highlight.OutlineColor = Color3.fromRGB(0, 255, 255)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Parent = HighlightFolder
end

-- Function to remove all highlights
local function removeAllHighlights()
    for _, highlight in pairs(HighlightFolder:GetChildren()) do
        highlight:Destroy()
    end
end

-- Main Silent Aim function
local function silentAim()
    removeAllHighlights()

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and isPlayerAlive(player) and player.Character then
            local head = player.Character:FindFirstChild("Head")

            if head and isPlayerVisible(head) then
                if not HighlightFolder:FindFirstChild(player.Name .. "_Highlight") then
                    createHighlight(player)
                end

                -- Equip second tool and aim
                equipSecondTool()
                aimAtTarget(head)

                -- Simulate Mouse1Click
                mouse1click()
                return
            end
        end
    end
end

-- Silent Aim Toggle
local SilentAim = Aim:CreateToggle({
    Name = "SilentAim Toggle",
    CurrentValue = false,
    Flag = "SilentAim",
    Callback = function(Value)
        SilentAimEnabled = Value

        if SilentAimEnabled then
            local connection
            connection = RunService.RenderStepped:Connect(function()
                if SilentAimEnabled then
                    silentAim()
                else
                    connection:Disconnect()
                    removeAllHighlights()
                end
            end)
        else
            removeAllHighlights()
        end
    end,
})

local Visuals = Window:CreateTab("Visuals", 4483362458) -- Title, Image
local Section = Visuals:CreateSection("Main")

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Esp2dEnabled = false

local EspFolder = Instance.new("Folder", game.CoreGui)
EspFolder.Name = "2DEspFolder"

-- Function to check if the local player is on a team
local function isOnTeam()
    return LocalPlayer.Team ~= nil
end

-- Function to create a 2D box using BillboardGui
local function create2DBox(player)
    if not player.Character then return end

    local head = player.Character:FindFirstChild("Head")
    if not head then return end

    -- Check if already exists
    if EspFolder:FindFirstChild(player.Name .. "_ESP") then return end

    -- Create BillboardGui
    local billboard = Instance.new("BillboardGui")
    billboard.Name = player.Name .. "_ESP"
    billboard.Adornee = head
    billboard.Size = UDim2.new(4, 0, 6, 0) -- Adjust the size of the box
    billboard.AlwaysOnTop = true
    billboard.LightInfluence = 0
    billboard.ResetOnSpawn = false
    billboard.Parent = EspFolder

    -- Add the Frame inside the BillboardGui (box)
    local boxFrame = Instance.new("Frame")
    boxFrame.Size = UDim2.new(1, 0, 1, 0)
    boxFrame.BackgroundTransparency = 0.5
    boxFrame.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Red for enemy players
    boxFrame.BorderSizePixel = 2
    boxFrame.BorderColor3 = Color3.fromRGB(0, 0, 0) -- Black outline
    boxFrame.Parent = billboard
end

-- Function to remove all 2D boxes
local function removeAllESP()
    for _, v in pairs(EspFolder:GetChildren()) do
        v:Destroy()
    end
end

-- Function to update the ESP for enemies
local function updateESP()
    removeAllESP()

    if not isOnTeam() then return end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team then
            create2DBox(player)
        end
    end
end

-- ESP Toggle
local Visual = {} -- Replace this with your library
local Esp2d = Visuals:CreateToggle({
    Name = "2D Esp",
    CurrentValue = false,
    Flag = "2DEsp",
    Callback = function(Value)
        Esp2dEnabled = Value

        if Esp2dEnabled then
            -- Continuously update ESP every second
            while Esp2dEnabled do
                updateESP()
                wait(1) -- Update every second
            end
        else
            removeAllESP()
        end
    end,
})
