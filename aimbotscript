local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- Check if the user is on mobile
local isMobile = UserInputService.TouchEnabled or (UserInputService.GamepadEnabled and not UserInputService.KeyboardEnabled)

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CombatUI"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- UI Container (Glassmorphism panel)
local mainFrame = Instance.new("Frame")
mainFrame.Size = isMobile and UDim2.new(0, 300, 0, 400) or UDim2.new(0, 200, 0, 200)  -- Larger size for mobile
mainFrame.Position = UDim2.new(0, 20, 0, 20)
mainFrame.BackgroundTransparency = 0.2
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- UI Corner (Rounded edges)
local corner = Instance.new("UICorner", mainFrame)
corner.CornerRadius = UDim.new(0, 12)

-- UI Stroke (Outline glow)
local stroke = Instance.new("UIStroke", mainFrame)
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(70, 70, 255)
stroke.Transparency = 0.3

-- UI Blur
local blurEffect = Instance.new("BlurEffect")
blurEffect.Size = 10
blurEffect.Parent = game:GetService("Lighting")

-- UI List Layout
local layout = Instance.new("UIListLayout", mainFrame)
layout.Padding = UDim.new(0, 10)
layout.FillDirection = Enum.FillDirection.Vertical
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.VerticalAlignment = Enum.VerticalAlignment.Top

-- Dragging Logic for Mobile and PC
local dragging, dragInput, dragStart, startPos

mainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

mainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Create a modern button (adjusted for mobile)
local function createModernButton(name, defaultText, callback)
    local button = Instance.new("TextButton")
    button.Name = name
    button.Size = isMobile and UDim2.new(0, 260, 0, 60) or UDim2.new(1, -20, 0, 40)  -- Larger button on mobile
    button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = isMobile and 18 or 16  -- Larger text size for mobile
    button.Text = defaultText
    button.BorderSizePixel = 0
    button.AutoButtonColor = false
    button.Parent = mainFrame

    -- Corners
    local btnCorner = Instance.new("UICorner", button)
    btnCorner.CornerRadius = UDim.new(0, 8)

    -- Shadow
    local shadow = Instance.new("UIStroke", button)
    shadow.Thickness = 1.5
    shadow.Color = Color3.fromRGB(0, 0, 0)
    shadow.Transparency = 0.5

    -- Hover Animation (only for PC)
    if not isMobile then
        local function tweenColor(toColor)
            TweenService:Create(button, TweenInfo.new(0.25), {BackgroundColor3 = toColor}):Play()
        end

        button.MouseEnter:Connect(function()
            tweenColor(Color3.fromRGB(50, 50, 50))
        end)

        button.MouseLeave:Connect(function()
            tweenColor(Color3.fromRGB(30, 30, 30))
        end)
    end

    -- Click Logic
    button.MouseButton1Click:Connect(callback)

    return button
end

-- Buttons + State Variables
local lockOnEnabled = false
local autoClickEnabled = false
local espEnabled = false

local lockButton = createModernButton("LockButton", "🔒 Lock-On: OFF", function()
    lockOnEnabled = not lockOnEnabled
    lockButton.Text = lockOnEnabled and "🔓 Lock-On: ON" or "🔒 Lock-On: OFF"
end)

local clickButton = createModernButton("ClickButton", "🖱️ Auto Click: OFF", function()
    autoClickEnabled = not autoClickEnabled
    clickButton.Text = autoClickEnabled and "✅ Auto Click: ON" or "🖱️ Auto Click: OFF"
end)

local espButton = createModernButton("ESPButton", "🧿 ESP: OFF", function()
    espEnabled = not espEnabled
    espButton.Text = espEnabled and "🧿 ESP: ON" or "🧿 ESP: OFF"
    if not espEnabled then
        for _, h in pairs(highlights) do
            h:Destroy()
        end
        highlights = {}
    end
end)
