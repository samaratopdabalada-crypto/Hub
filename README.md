--[[
Float Script
Crie uma GUI com botão de velocidade e botão "Float".
Quando ativar, personagem voa na direção que está olhando.
Coloque em StarterGui como LocalScript.
]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local camera = workspace.CurrentCamera

-- GUI Setup
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FloatGui"
screenGui.Parent = player:WaitForChild("PlayerGui")

local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(0, 100, 0, 40)
speedBox.Position = UDim2.new(0, 10, 0, 10)
speedBox.Text = "Velocidade: 50"
speedBox.Parent = screenGui

local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 100, 0, 40)
floatButton.Position = UDim2.new(0, 10, 0, 60)
floatButton.Text = "Float"
floatButton.Parent = screenGui

local stopButton = Instance.new("TextButton")
stopButton.Size = UDim2.new(0, 100, 0, 40)
stopButton.Position = UDim2.new(0, 10, 0, 110)
stopButton.Text = "Parar"
stopButton.Parent = screenGui
stopButton.Visible = false

local flying = false
local speed = 50

speedBox.FocusLost:Connect(function()
    local val = tonumber(speedBox.Text:match("%d+"))
    if val then
        speed = val
        speedBox.Text = "Velocidade: " .. tostring(speed)
    else
        speedBox.Text = "Velocidade: " .. tostring(speed)
    end
end)

local bodyVelocity

floatButton.MouseButton1Click:Connect(function()
    if not flying then
        flying = true
        stopButton.Visible = true
        floatButton.Visible = false

        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bodyVelocity.Parent = humanoidRootPart

        -- Loop para manter o voo
        while flying do
            local direction = camera.CFrame.LookVector
            bodyVelocity.Velocity = direction * speed
            wait(0.03)
        end
    end
end)

stopButton.MouseButton1Click:Connect(function()
    flying = false
    stopButton.Visible = false
    floatButton.Visible = true
    if bodyVelocity then
        bodyVelocity:Destroy()
    end
end)

-- Segurança: para caso o personagem seja resetado
player.CharacterAdded:Connect(function(char)
    character = char
    humanoidRootPart = character:WaitForChild("HumanoidRootPart")
end)
