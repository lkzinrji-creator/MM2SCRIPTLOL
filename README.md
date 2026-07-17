-- MM2 Script Local V23 (NO KEY EDITION)
-- ESP, AIMBOT, AUTO SHOOT, GUNDROP TELEPORT, KILL ALL

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local StarterGui = game:GetService("StarterGui")
local LocalPlayer = Players.LocalPlayer

-- [ANTI-DUPLICATION]
if CoreGui:FindFirstChild("MM2_Menu_Manus_V23") then
    CoreGui:FindFirstChild("MM2_Menu_Manus_V23"):Destroy()
end

-- Configurações
_G.ESPEnabled = true
_G.AimbotEnabled = true
_G.AutoShootEnabled = true
_G.TeleportGunEnabled = true
_G.KillAllEnabled = false
_G.NotificationsEnabled = true
local isTeleporting = false
local isMinimized = false

-- [NOTIFICAÇÃO DE INICIALIZAÇÃO]
pcall(function()
    local thumb = Players:GetUserThumbnailAsync(Players:GetUserIdFromNameAsync("System32wind"), Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
    StarterGui:SetCore("SendNotification", {
        Title = "SYSTEM32WIND HUB",
        Text = "Carregado com Sucesso!",
        Icon = thumb,
        Duration = 5
    })
end)

-- Criar Interface Principal (GUI)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MM2_Menu_Manus_V23"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 99999

local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.Position = UDim2.new(0.1, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 250, 0, 220)
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame)

local TitleBar = Instance.new("Frame")
TitleBar.Parent = MainFrame
TitleBar.Size = UDim2.new(1, 0, 0, 35)
TitleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Instance.new("UICorner", TitleBar)

local Title = Instance.new("TextLabel")
Title.Parent = TitleBar
Title.Text = "SYSTEM32WIND HUB"
Title.Size = UDim2.new(0.8, 0, 1, 0)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Parent = TitleBar
MinimizeBtn.Size = UDim2.new(0, 25, 0, 25)
MinimizeBtn.Position = UDim2.new(1, -30, 0, 5)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.new(1, 1, 1)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Instance.new("UICorner", MinimizeBtn)

local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainFrame
ContentFrame.Size = UDim2.new(1, 0, 1, -35)
ContentFrame.Position = UDim2.new(0, 0, 0, 35)
ContentFrame.BackgroundTransparency = 1

MinimizeBtn.MouseButton1Down:Connect(function()
    isMinimized = not isMinimized
    MainFrame.Size = isMinimized and UDim2.new(0, 250, 0, 35) or UDim2.new(0, 250, 0, 220)
    ContentFrame.Visible = not isMinimized
    MinimizeBtn.Text = isMinimized and "+" or "-"
end)

local function CreateGridButton(name, text, pos, size, globalVar)
    local btn = Instance.new("TextButton")
    btn.Parent = ContentFrame
    btn.Position = pos
    btn.Size = size
    btn.BackgroundColor3 = _G[globalVar] and Color3.fromRGB(0, 160, 0) or Color3.fromRGB(160, 0, 0)
    btn.Text = text
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 11
    Instance.new("UICorner", btn)
    btn.MouseButton1Down:Connect(function()
        _G[globalVar] = not _G[globalVar]
        btn.BackgroundColor3 = _G[globalVar] and Color3.fromRGB(0, 160, 0) or Color3.fromRGB(160, 0, 0)
    end)
    return btn
end

CreateGridButton("ESP", "ESP", UDim2.new(0.05, 0, 0.1, 0), UDim2.new(0.42, 0, 0, 35), "ESPEnabled")
CreateGridButton("Aimbot", "Aimbot", UDim2.new(0.53, 0, 0.1, 0), UDim2.new(0.42, 0, 0, 35), "AimbotEnabled")
CreateGridButton("AutoShoot", "AutoShoot", UDim2.new(0.05, 0, 0.35, 0), UDim2.new(0.42, 0, 0, 35), "AutoShootEnabled")
CreateGridButton("TeleportGun", "TeleportGun", UDim2.new(0.53, 0, 0.35, 0), UDim2.new(0.42, 0, 0, 35), "TeleportGunEnabled")
CreateGridButton("KillAll", "Kill All", UDim2.new(0.05, 0, 0.6, 0), UDim2.new(0.42, 0, 0, 35), "KillAllEnabled")
CreateGridButton("Notif", "Notif", UDim2.new(0.53, 0, 0.6, 0), UDim2.new(0.42, 0, 0, 35), "NotificationsEnabled")

-- Lógica de Roles
local function GetPlayerRole(player)
    local backpack = player:FindFirstChild("Backpack")
    local character = player.Character
    if (backpack and backpack:FindFirstChild("Knife")) or (character and character:FindFirstChild("Knife")) then return "Murderer"
    elseif (backpack and backpack:FindFirstChild("Gun")) or (character and character:FindFirstChild("Gun")) then return "Sheriff"
    else return "Innocent" end
end

-- [GUNDROP TELEPORT]
RunService.Heartbeat:Connect(function()
    if not _G.TeleportGunEnabled or isTeleporting then return end
    if GetPlayerRole(LocalPlayer) == "Murderer" then return end
    local gunDrop = workspace:FindFirstChild("GunDrop")
    if gunDrop and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        if LocalPlayer.Character.HumanoidRootPart.Position.Y < 500 then
            isTeleporting = true
            local old = LocalPlayer.Character.HumanoidRootPart.CFrame
            LocalPlayer.Character.HumanoidRootPart.CFrame = gunDrop:IsA("Model") and gunDrop:GetModelCFrame() or gunDrop.CFrame
            task.wait(0.4)
            LocalPlayer.Character.HumanoidRootPart.CFrame = old
            task.wait(3)
            isTeleporting = false
        end
    end
end)

-- Loops de ESP e Aimbot
RunService.RenderStepped:Connect(function()
    if not ScreenGui.Parent then return end
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local role = GetPlayerRole(p)
            if _G.ESPEnabled then
                local h = p.Character:FindFirstChild("MM2_ESP_V23") or Instance.new("Highlight")
                h.Name = "MM2_ESP_V23"; h.Parent = p.Character
                h.FillColor = (role == "Murderer" and Color3.new(1,0,0)) or (role == "Sheriff" and Color3.new(0,0,1)) or Color3.new(0,1,0)
                h.FillTransparency = 0.5
            elseif p.Character:FindFirstChild("MM2_ESP_V23") then
                p.Character.MM2_ESP_V23:Destroy()
            end
        end
    end
    
    local gunDrop = workspace:FindFirstChild("GunDrop")
    if gunDrop then
        if _G.ESPEnabled then
            local h = gunDrop:FindFirstChild("GunHighlight") or Instance.new("Highlight")
            h.Name = "GunHighlight"; h.Parent = gunDrop; h.FillColor = Color3.new(1,1,0); h.FillTransparency = 0.3
        elseif gunDrop:FindFirstChild("GunHighlight") then
            gunDrop.GunHighlight:Destroy()
        end
    end
    
    if _G.AimbotEnabled then
        local gun = LocalPlayer.Character:FindFirstChild("Gun")
        if gun then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character and GetPlayerRole(p) == "Murderer" then
                    local root = p.Character:FindFirstChild("HumanoidRootPart")
                    if root then
                        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, root.Position)
                        if _G.AutoShootEnabled then gun:Activate() end
                    end
                end
            end
        end
    end
end)

-- Kill All (Partida Apenas)
task.spawn(function()
    while task.wait(0.1) do
        if _G.KillAllEnabled and GetPlayerRole(LocalPlayer) == "Murderer" then
            local knife = LocalPlayer.Character:FindFirstChild("Knife") or LocalPlayer.Backpack:FindFirstChild("Knife")
            if knife and LocalPlayer.Character.HumanoidRootPart.Position.Y < 500 then
                LocalPlayer.Character.Humanoid:EquipTool(knife)
                for _, p in pairs(Players:GetPlayers()) do
                    if p ~= LocalPlayer and p.Character and p.Character.Humanoid.Health > 0 and p.Character.HumanoidRootPart.Position.Y < 500 then
                        if GetPlayerRole(p) ~= "Murderer" then
                            LocalPlayer.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 1)
                            knife:Activate(); task.wait(0.2)
                        end
                    end
                end
            end
        end
    end
end)
