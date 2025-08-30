local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local lockOnEnabled = false
local autoClickEnabled = false
local espEnabled = false
local currentTarget = nil
local highlights = {}

-- Create GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CombatUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local function createButton(name, position, defaultText)
    local button = Instance.new("TextButton")
    button.Name = name
    button.Size = UDim2.new(0, 160, 0, 40)
    button.Position = position
    button.Text = defaultText
    button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 18
    button.BorderSizePixel = 0
    button.AutoButtonColor = true
    button.Parent = screenGui
    return button
end

-- Create Buttons
local lockButton = createButton("LockButton", UDim2.new(0, 20, 0, 20), "Lock-On: OFF")
local clickButton = createButton("ClickButton", UDim2.new(0, 20, 0, 70), "Auto Click: OFF")
local espButton = createButton("ESPButton", UDim2.new(0, 20, 0, 120), "ESP: OFF")

-- Button Logic
lockButton.MouseButton1Click:Connect(function()
    lockOnEnabled = not lockOnEnabled
    lockButton.Text = lockOnEnabled and "Lock-On: ON" or "Lock-On: OFF"
end)

clickButton.MouseButton1Click:Connect(function()
    autoClickEnabled = not autoClickEnabled
    clickButton.Text = autoClickEnabled and "Auto Click: ON" or "Auto Click: OFF"
end)

espButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espButton.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    if not espEnabled then
        for _, h in pairs(highlights) do
            h:Destroy()
        end
        highlights = {}
    end
end)

-- Visibility Check
local function isVisible(targetPart)
    local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not myHRP or not targetPart then return false end

    local direction = (targetPart.Position - myHRP.Position)
    local rayParams = RaycastParams.new()
    rayParams.FilterDescendantsInstances = {LocalPlayer.Character, targetPart.Parent}
    rayParams.FilterType = Enum.RaycastFilterType.Blacklist

    local result = workspace:Raycast(myHRP.Position, direction, rayParams)
    return result == nil
end

-- Find Closest Visible Enemy (Head)
local function getClosestEnemyPlayer()
    local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not myHRP then return nil end

    local closest = nil
    local shortestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and player.Character and player.Character:FindFirstChild("Head") and player.Character:FindFirstChild("Humanoid") then
            local head = player.Character.Head
            local distance = (head.Position - myHRP.Position).Magnitude
            if distance < shortestDistance and player.Character.Humanoid.Health > 0 and isVisible(head) then
                shortestDistance = distance
                closest = player
            end
        end
    end

    return closest
end

-- Auto Click
local function autoClick()
    if not currentTarget or not currentTarget.Character or not currentTarget.Character:FindFirstChild("Head") then return end
    local character = LocalPlayer.Character
    if not character then return end
    local tool = character:FindFirstChildOfClass("Tool")
    if tool then
        tool:Activate()
    end
end

-- ESP Highlights
local function updateESP()
    if not espEnabled then return end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and player.Character then
            if not highlights[player] then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = player.Character
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = screenGui
                highlights[player] = highlight
            end

            local highlight = highlights[player]
            if player == currentTarget then
                highlight.FillColor = Color3.fromRGB(0, 255, 0) -- Green
            else
                highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Red
            end
        end
    end
end

-- Main Loop
RunService.RenderStepped:Connect(function()
    if lockOnEnabled then
        local closest = getClosestEnemyPlayer()
        if closest ~= currentTarget then
            currentTarget = closest
        end

        if currentTarget and currentTarget.Character and currentTarget.Character:FindFirstChild("Head") then
            local targetPos = currentTarget.Character.Head.Position
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPos)

            if autoClickEnabled then
                autoClick()
            end
        end
    end

    updateESP()
end)
