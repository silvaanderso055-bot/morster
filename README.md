--=====================================================
-- BLOX FRUITS - HOHO HUB MOBILE v3.6
-- Interface Flutuante - CORRIGIDO PARA MOBILE
-- 100% Compatível com Executor Mobile
--=====================================================

local Version = "3.6 Mobile Fixed"
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

--=====================================================
-- CONFIGURAÇÕES
--=====================================================
local Config = {
    AutoFarm = false,
    AutoAttack = false,
    AutoFruit = false,
    GodMode = false,
    AntiAFK = false,
    AntiDetectionEnabled = true,
    WalkSpeed = 16,
    FarmRange = 150,
    AttackRange = 50,
}

--=====================================================
-- LOGGER
--=====================================================
local function Log(msg)
    print("[HOHO HUB] " .. msg)
end

--=====================================================
-- CRIAR GUI PRINCIPAL
--=====================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HohoHubMobile"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = false
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

--=====================================================
-- BOTÃO FLUTUANTE PRINCIPAL
--=====================================================
local FloatingButton = Instance.new("TextButton")
FloatingButton.Name = "FloatingButton"
FloatingButton.Size = UDim2.new(0, 70, 0, 70)
FloatingButton.Position = UDim2.new(1, -90, 1, -120)
FloatingButton.BackgroundColor3 = Color3.fromRGB(255, 150, 0)
FloatingButton.BorderSizePixel = 2
FloatingButton.BorderColor3 = Color3.fromRGB(200, 100, 0)
FloatingButton.Text = "🔥"
FloatingButton.TextSize = 40
FloatingButton.Font = Enum.Font.GothamBold
FloatingButton.Parent = ScreenGui
FloatingButton.Draggable = true
FloatingButton.Active = true
FloatingButton.ZIndex = 5

local FloatingCorner = Instance.new("UICorner")
FloatingCorner.CornerRadius = UDim.new(1, 0)
FloatingCorner.Parent = FloatingButton

--=====================================================
-- MENU FRAME - GRANDE E VISÍVEL
--=====================================================
local MenuFrame = Instance.new("Frame")
MenuFrame.Name = "MenuFrame"
MenuFrame.Size = UDim2.new(0.9, 0, 0.85, 0) -- 90% da largura, 85% da altura
MenuFrame.Position = UDim2.new(0.05, 0, 0.08, 0) -- Centralizado
MenuFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MenuFrame.BorderSizePixel = 3
MenuFrame.BorderColor3 = Color3.fromRGB(255, 150, 0)
MenuFrame.Parent = ScreenGui
MenuFrame.Visible = false
MenuFrame.ZIndex = 10
MenuFrame.Draggable = true
MenuFrame.Active = true

local MenuCorner = Instance.new("UICorner")
MenuCorner.CornerRadius = UDim.new(0, 15)
MenuCorner.Parent = MenuFrame

--=====================================================
-- MENU HEADER
--=====================================================
local MenuHeader = Instance.new("Frame")
MenuHeader.Name = "MenuHeader"
MenuHeader.Size = UDim2.new(1, 0, 0, 60)
MenuHeader.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MenuHeader.BorderSizePixel = 0
MenuHeader.Parent = MenuFrame
MenuHeader.ZIndex = 11

local HeaderPadding = Instance.new("UIPadding")
HeaderPadding.PaddingLeft = UDim.new(0, 15)
HeaderPadding.PaddingRight = UDim.new(0, 15)
HeaderPadding.Parent = MenuHeader

local MenuTitle = Instance.new("TextLabel")
MenuTitle.Size = UDim2.new(1, -80, 1, 0)
MenuTitle.BackgroundTransparency = 1
MenuTitle.Text = "🔥 HOHO HUB - BLOX FRUITS"
MenuTitle.TextColor3 = Color3.fromRGB(255, 200, 0)
MenuTitle.TextSize = 20
MenuTitle.Font = Enum.Font.GothamBold
MenuTitle.TextXAlignment = Enum.TextXAlignment.Left
MenuTitle.Parent = MenuHeader
MenuTitle.ZIndex = 11

-- Botão fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Name = "CloseBtn"
CloseBtn.Size = UDim2.new(0, 50, 0, 50)
CloseBtn.Position = UDim2.new(1, -60, 0.5, -25)
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 24
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = MenuHeader
CloseBtn.ZIndex = 11

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 8)
CloseCorner.Parent = CloseBtn

CloseBtn.MouseButton1Click:Connect(function()
    MenuFrame.Visible = false
    FloatingButton.Visible = true
end)

--=====================================================
-- MENU SCROLL (CONTEÚDO PRINCIPAL)
--=====================================================
local MenuScroll = Instance.new("ScrollingFrame")
MenuScroll.Name = "MenuScroll"
MenuScroll.Size = UDim2.new(1, 0, 1, -60)
MenuScroll.Position = UDim2.new(0, 0, 0, 60)
MenuScroll.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MenuScroll.BorderSizePixel = 0
MenuScroll.ScrollBarThickness = 8
MenuScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
MenuScroll.Parent = MenuFrame
MenuScroll.ZIndex = 10

local ScrollPadding = Instance.new("UIPadding")
ScrollPadding.PaddingLeft = UDim.new(0, 15)
ScrollPadding.PaddingRight = UDim.new(0, 15)
ScrollPadding.PaddingTop = UDim.new(0, 15)
ScrollPadding.PaddingBottom = UDim.new(0, 15)
ScrollPadding.Parent = MenuScroll

local MenuLayout = Instance.new("UIListLayout")
MenuLayout.Padding = UDim.new(0, 12)
MenuLayout.SortOrder = Enum.SortOrder.LayoutOrder
MenuLayout.Parent = MenuScroll

MenuLayout.Changed:Connect(function()
    MenuScroll.CanvasSize = UDim2.new(0, 0, 0, MenuLayout.AbsoluteContentSize.Y + 30)
end)

--=====================================================
-- FUNÇÕES HELPER - TOGGLE
--=====================================================
local function CreateToggle(parent, label, icon, configKey)
    local Container = Instance.new("Frame")
    Container.Size = UDim2.new(1, 0, 0, 50)
    Container.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Container.BorderSizePixel = 0
    Container.Parent = parent
    Container.ZIndex = 10
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = Container
    
    local Padding = Instance.new("UIPadding")
    Padding.PaddingLeft = UDim.new(0, 12)
    Padding.PaddingRight = UDim.new(0, 12)
    Padding.Parent = Container
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -70, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Text = icon .. " " .. label
    Label.TextColor3 = Color3.fromRGB(220, 220, 220)
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Container
    Label.ZIndex = 10
    
    local Toggle = Instance.new("TextButton")
    Toggle.Name = configKey
    Toggle.Size = UDim2.new(0, 60, 0, 32)
    Toggle.Position = UDim2.new(1, -65, 0.5, -16)
    Toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    Toggle.BorderSizePixel = 0
    Toggle.Text = "OFF"
    Toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    Toggle.TextSize = 12
    Toggle.Font = Enum.Font.GothamBold
    Toggle.Parent = Container
    Toggle.ZIndex = 10
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 6)
    ToggleCorner.Parent = Toggle
    
    Toggle.MouseButton1Click:Connect(function()
        Config[configKey] = not Config[configKey]
        Toggle.BackgroundColor3 = Config[configKey] 
            and Color3.fromRGB(50, 200, 50) 
            or Color3.fromRGB(100, 100, 100)
        Toggle.Text = Config[configKey] and "ON" or "OFF"
        Log(label .. ": " .. (Config[configKey] and "✅" or "❌"))
    end)
    
    return Container
end

--=====================================================
-- FUNÇÕES HELPER - BOTÃO
--=====================================================
local function CreateButton(parent, label, icon, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, 0, 0, 50)
    Button.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    Button.BorderSizePixel = 0
    Button.Text = icon .. " " .. label
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 14
    Button.Font = Enum.Font.GothamBold
    Button.Parent = parent
    Button.ZIndex = 10
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = Button
    
    local Padding = Instance.new("UIPadding")
    Padding.PaddingLeft = UDim.new(0, 12)
    Padding.PaddingRight = UDim.new(0, 12)
    Padding.Parent = Button
    
    Button.MouseButton1Click:Connect(function()
        Button.BackgroundColor3 = Color3.fromRGB(40, 120, 40)
        wait(0.15)
        Button.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
        
        if callback then
            callback()
        end
    end)
    
    return Button
end

--=====================================================
-- FUNÇÕES HELPER - HEADER SEÇÃO
--=====================================================
local function CreateSectionHeader(parent, label, color)
    local Header = Instance.new("TextLabel")
    Header.Size = UDim2.new(1, 0, 0, 40)
    Header.BackgroundColor3 = color
    Header.BorderSizePixel = 0
    Header.Text = label
    Header.TextColor3 = Color3.fromRGB(0, 0, 0)
    Header.TextSize = 14
    Header.Font = Enum.Font.GothamBold
    Header.Parent = parent
    Header.ZIndex = 10
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = Header
    
    return Header
end

--=====================================================
-- CONTEÚDO DO MENU
--=====================================================

-- SEÇÃO 1: AUTO FARM
CreateSectionHeader(MenuScroll, "⚙️ AUTO FARM", Color3.fromRGB(255, 150, 0))
CreateToggle(MenuScroll, "Auto Farm", "🔄", "AutoFarm")
CreateToggle(MenuScroll, "Auto Attack", "⚔️", "AutoAttack")
CreateToggle(MenuScroll, "Auto Fruit", "🍎", "AutoFruit")
CreateButton(MenuScroll, "INICIAR FARM", "▶️", function()
    Config.AutoFarm = true
    Config.AutoAttack = true
    Log("🚀 FARM INICIADO!")
end)
CreateButton(MenuScroll, "PARAR FARM", "⏹️", function()
    Config.AutoFarm = false
    Config.AutoAttack = false
    Log("⛔ Farm parado!")
end)

-- SEÇÃO 2: PLAYER
CreateSectionHeader(MenuScroll, "👤 PLAYER", Color3.fromRGB(100, 200, 255))
CreateToggle(MenuScroll, "God Mode", "👻", "GodMode")
CreateToggle(MenuScroll, "Anti-AFK", "🏃", "AntiAFK")
CreateButton(MenuScroll, "Walk Speed +5", "🏃", function()
    Config.WalkSpeed = Config.WalkSpeed + 5
    Humanoid.WalkSpeed = Config.WalkSpeed
    Log("Walk Speed: " .. Config.WalkSpeed)
end)
CreateButton(MenuScroll, "Walk Speed -5", "🐢", function()
    Config.WalkSpeed = math.max(16, Config.WalkSpeed - 5)
    Humanoid.WalkSpeed = Config.WalkSpeed
    Log("Walk Speed: " .. Config.WalkSpeed)
end)
CreateButton(MenuScroll, "Reset Walk Speed", "🔄", function()
    Config.WalkSpeed = 16
    Humanoid.WalkSpeed = 16
    Log("Walk Speed resetado!")
end)

-- SEÇÃO 3: TELEPORT
CreateSectionHeader(MenuScroll, "📍 TELEPORT", Color3.fromRGB(150, 100, 255))
CreateButton(MenuScroll, "Starter Island", "🏝️", function()
    HumanoidRootPart.CFrame = CFrame.new(0, 20, 0)
    Log("Teleportado!")
end)
CreateButton(MenuScroll, "Colosseum", "⚔️", function()
    HumanoidRootPart.CFrame = CFrame.new(100, 50, 100)
    Log("Teleportado!")
end)
CreateButton(MenuScroll, "Pirate Village", "🏴‍☠️", function()
    HumanoidRootPart.CFrame = CFrame.new(-500, 30, 0)
    Log("Teleportado!")
end)
CreateButton(MenuScroll, "Desert", "🏜️", function()
    HumanoidRootPart.CFrame = CFrame.new(0, 30, -500)
    Log("Teleportado!")
end)

-- SEÇÃO 4: MISC
CreateSectionHeader(MenuScroll, "⚙️ MISC", Color3.fromRGB(200, 100, 100))
CreateToggle(MenuScroll, "Anti-Detecção", "🛡️", "AntiDetectionEnabled")
CreateButton(MenuScroll, "FECHAR MENU", "❌", function()
    MenuFrame.Visible = false
    FloatingButton.Visible = true
end)

--=====================================================
-- EVENTO DO BOTÃO FLUTUANTE
--=====================================================
FloatingButton.MouseButton1Click:Connect(function()
    MenuFrame.Visible = true
    FloatingButton.Visible = false
end)

--=====================================================
-- SISTEMA DE AUTO FARM
--=====================================================
local function GetNearestMob()
    local nearest = nil
    local minDistance = Config.FarmRange
    
    local mobFolder = workspace:FindFirstChild("Enemies")
    if not mobFolder then return nil end
    
    for _, mob in pairs(mobFolder:GetChildren()) do
        if mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
            local distance = (mob.HumanoidRootPart.Position - HumanoidRootPart.Position).Magnitude
            
            if distance < minDistance and mob.Humanoid.Health > 0 then
                minDistance = distance
                nearest = mob
            end
        end
    end
    
    return nearest
end

local function AttackMob(mob)
    if not mob or not mob:FindFirstChild("HumanoidRootPart") then return end
    
    local distance = (mob.HumanoidRootPart.Position - HumanoidRootPart.Position).Magnitude
    
    if distance > Config.AttackRange then
        HumanoidRootPart.CFrame = mob.HumanoidRootPart.CFrame + Vector3.new(0, 3, 5)
        wait(0.3)
    end
    
    local args = {[1] = mob, [2] = true}
    local attackEvent = Character:FindFirstChild("Combat") or Character:FindFirstChildOfClass("RemoteEvent")
    if attackEvent then
        attackEvent:FireServer(unpack(args))
    end
    
    wait(0.5)
end

--=====================================================
-- LOOP PRINCIPAL
--=====================================================
RunService.Heartbeat:Connect(function()
    if not Character or Humanoid.Health <= 0 then return end
    
    if Config.AutoFarm and Config.AutoAttack then
        local mob = GetNearestMob()
        if mob then
            AttackMob(mob)
        end
    end
    
    if Config.GodMode then
        Humanoid.Health = Humanoid.MaxHealth
    end
    
    if Config.WalkSpeed ~= 16 then
        Humanoid.WalkSpeed = Config.WalkSpeed
    end
    
    if Config.AntiAFK then
        HumanoidRootPart.CFrame = HumanoidRootPart.CFrame * CFrame.new(math.random(-1, 1) * 0.05, 0, math.random(-1, 1) * 0.05)
    end
end)

--=====================================================
-- RECONEXÃO
--=====================================================
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
end)

--=====================================================
-- INICIALIZAÇÃO
--=====================================================
Log("✅ HOHO HUB v" .. Version .. " CARREGADO!")
Log("Toque no botão 🔥 para abrir o menu!")
print("=".."=".."=".."=".."=".."=".."=".."=".."=".."=".."=")
print("🔥 HOHO HUB MOBILE - BLOX FRUITS 🔥")
print("=".."=".."=".."=".."=".."=".."=".."=".."=".."=".."=")
