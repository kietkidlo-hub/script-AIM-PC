--NTK HUB--
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local lp = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- TRẠNG THÁI
local isHoldingE = false

-- GUI SETUP
local ScreenGui = Instance.new("ScreenGui", lp:WaitForChild("PlayerGui"))
ScreenGui.Name = "NTK_HUB"
ScreenGui.ResetOnSpawn = false

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 220, 0, 90)
Frame.Position = UDim2.new(0.5, -110, 0.4, -45)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.Active = true
Frame.Draggable = true -- DI CHUYỂN ĐƯỢC
Instance.new("UICorner", Frame)

local UIStroke = Instance.new("UIStroke", Frame)
UIStroke.Color = Color3.fromRGB(255, 0, 0)
UIStroke.Thickness = 2

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "NTK HUB | " .. lp.Name
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.SourceSansBold

local Info = Instance.new("TextLabel", Frame)
Info.Size = UDim2.new(1, 0, 0, 50)
Info.Position = UDim2.new(0, 0, 0.35, 0)
Info.Text = "ĐÈ [E]: LOCK ĐẦU (CỨNG)\nBẤM [Y]: ẨN/HIỆN BẢNG"
Info.TextColor3 = Color3.fromRGB(200, 200, 200)
Info.BackgroundTransparency = 1

-- SỰ KIỆN PHÍM
UserInputService.InputBegan:Connect(function(key, gp)
    if gp then return end
    if key.KeyCode == Enum.KeyCode.Y then Frame.Visible = not Frame.Visible end
    if key.KeyCode == Enum.KeyCode.E then isHoldingE = true end
end)

UserInputService.InputEnded:Connect(function(key, gp)
    if key.KeyCode == Enum.KeyCode.E then isHoldingE = false end
end)

-- HÀM AIM
local function getClosestHead()
    local closestPart = nil
    local shortestDistance = math.huge
    local screenCenter = camera.ViewportSize / 2
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= lp and player.Character then
            local head = player.Character:FindFirstChild("Head")
            local hum = player.Character:FindFirstChildOfClass("Humanoid")
            if head and hum and hum.Health > 0 then
                local pos, onScreen = camera:WorldToViewportPoint(head.Position)
                if onScreen then
                    local dist = (Vector2.new(pos.X, pos.Y) - screenCenter).Magnitude
                    if dist < shortestDistance then
                        shortestDistance = dist
                        closestPart = head
                    end
                end
            end
        end
    end
    return closestPart
end

-- VÒNG LẶP AIM CỨNG
RunService.RenderStepped:Connect(function()
    if isHoldingE then
        local target = getClosestHead()
        if target then
            camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
        end
    end
end)
