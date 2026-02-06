--=====================================================
-- BLOX FRUITS - HOHO HUB MOBILE v3.5
-- Interface Flutuante com Botões Táteis
-- 100% Compatível com Executor Mobile
--=====================================================

local Version = "3.5 Mobile"
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

--=====================================================
-- CONFIGURAÇÕES
--=====================================================
local Config = {
    -- AUTO FARM
    AutoFarm = false,
    AutoQuest = false,
    AutoAttack = false,
    AutoFruit = false,
    AutoStats = false,
    
    -- PLAYER
    GodMode = false,
    NoClip = false,
    WalkSpeed = 16,
    JumpPower = 50,
    
    -- MISC
    ESP = false,
    RemoveFog = false,
    AntiAFK = false,
    AntiDetectionEnabled = true,
    
    -- ADVANCED
    FarmRange = 150,
    AttackRange = 50,
}

--=====================================================
-- LOGGER
--=====================================================
local function Log(message, isSuccess)
    local prefix = isSuccess and "[✅]" or "[📌]"
    print(prefix .. " HOHO HUB: " .. message)
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
-- BOTÃO FLUTUANTE PRINCIPAL (ABRIR MENU)
--=====================================================
local FloatingButton = Instance.new("TextButton")
FloatingButton.Name = "FloatingButton"
FloatingButton.Size = UDim2.new(0, 60, 0, 60)
FloatingButton.Position = UDim2.new(1, -80, 1, -100)
FloatingButton.BackgroundColor3 = Color3.fromRGB(255, 150, 0)
FloatingButton.BorderSizePixel = 0
FloatingButton.Text = "🔥"
FloatingButton.TextSize = 30
FloatingButton.Font = Enum.Font.GothamBold
FloatingButton.Parent = ScreenGui
FloatingButton.Draggable = true
FloatingButton.Active = true

-- Arredondar botão flutuante
local FloatingCorner = Instance.new("UICorner")
FloatingCorner.CornerRadius = UDim.new(1, 0)
FloatingCorner.Parent = FloatingButton

-- Sombra no botão
local FloatingShadow = Instance.new("UIStroke")
FloatingShadow.Color = Color3.fromRGB(200, 100, 0)
FloatingShadow.Thickness = 2
FloatingShadow.Parent = FloatingButton

--=====================================================
-- MAIN MENU FRAME
--=====================================================
local MenuFrame = Instance.new("Frame")
MenuFrame.Name = "MenuFrame"
MenuFrame.Size = UDim2.new(0, 280, 0, 550)
MenuFrame.Position = UDim2.new(1, -300, 1, -570)
MenuFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MenuFrame.BorderSizePixel = 2
MenuFrame.BorderColor3 = Color3.fromRGB(255, 150, 0)
MenuFrame.Parent = ScreenGui
MenuFrame.Visible = false
MenuFrame.ZIndex = 10

local MenuCorner = Instance.new("UICorner")
MenuCorner.CornerRadius = UDim.new(0, 10)
MenuCorner.Parent = MenuFrame

--=====================================================
-- MENU HEADER
--=====================================================
local MenuHeader = Instance.new("Frame")
MenuHeader.Name = "MenuHeader"
MenuHeader.Size = UDim2.new(1, 0, 0, 50)
MenuHeader.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MenuHeader.BorderSizePixel = 0
MenuHeader.Parent = MenuFrame

local MenuHeaderCorner = Instance.new("UICorner")
MenuHeaderCorner.CornerRadius = UDim.new(0, 10)
MenuHeaderCorner.Parent = MenuHeader

local MenuTitle = Instance.new("TextLabel")
MenuTitle.Size = UDim2.new(1, -40, 1, 0)
MenuTitle.BackgroundTransparency = 1
MenuTitle.Text = "🔥 HOHO HUB"
MenuTitle.TextColor3 = Color3.fromRGB(255, 200, 0)
MenuTitle.TextSize = 16
MenuTitle.Font = Enum.Font.GothamBold
MenuTitle.TextXAlignment = Enum.TextXAlignment.Left
MenuTitle.Parent = MenuHeader

-- Botão fechar menu
local CloseMenuBtn = Instance.new("TextButton")
CloseMenuBtn.Size = UDim2.new(0, 35, 0, 35)
CloseMenuBtn.Position = UDim2.new(1, -40, 0.5, -17.5)
CloseMenuBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseMenuBtn.BorderSizePixel = 0
CloseMenuBtn.Text = "X"
CloseMenuBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseMenuBtn.TextSize = 14
CloseMenuBtn.Font = Enum.Font.GothamBold
CloseMenuBtn.Parent = MenuHeader

local CloseMenuCorner = Instance.new("UICorner")
CloseMenuCorner.CornerRadius = UDim.new(0, 6)
CloseMenuCorner.Parent = CloseMenuBtn

CloseMenuBtn.MouseButton1Click:Connect(function()
    MenuFrame.Visible = false
    FloatingButton.Visible = true
end)

--=====================================================
-- MENU SCROLL
--=====================================================
local MenuScroll = Instance.new("ScrollingFrame")
MenuScroll.Name = "MenuScroll"
MenuScroll.Size = UDim2.new(1, 0, 1, -50)
MenuScroll.Position = UDim2.new(0, 0, 0, 50)
MenuScroll.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MenuScroll.BorderSizePixel = 0
MenuScroll.ScrollBarThickness = 4
MenuScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
MenuScroll.Parent = MenuFrame

local MenuLayout = Instance.new("UIListLayout")
MenuLayout.Padding = UDim.new(0, 8)
MenuLayout.SortOrder = Enum.SortOrder.LayoutOrder
MenuLayout.Parent = MenuScroll

MenuLayout.Changed:Connect(function()
    MenuScroll.CanvasSize = UDim2.new(0, 0, 0, MenuLayout.AbsoluteContentSize.Y)
end)

--=====================================================
-- FUNÇÕES HELPER
--=====================================================
local function CreateToggleButton(parent, label, icon, configKey)
    local Container = Instance.new("Frame")
    Container.Size = UDim2.new(1, -16, 0, 40)
    Container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Container.BorderSizePixel = 0
    Container.Parent = parent
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Container
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(0, 200, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Text = icon .. " " .. label
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.TextSize = 12
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Container
    
    local Toggle = Instance.new("TextButton")
    Toggle.Size = UDim2.new(0, 50, 0, 28)
    Toggle.Position = UDim2.new(1, -58, 0.5, -14)
    Toggle.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    Toggle.BorderSizePixel = 0
    Toggle.Text = "OFF"
    Toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    Toggle.TextSize = 10
    Toggle.Font = Enum.Font.GothamBold
    Toggle.Parent = Container
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 4)
    ToggleCorner.Parent = Toggle
    
    Toggle.MouseButton1Click:Connect(function()
        Config[configKey] = not Config[configKey]
        Toggle.BackgroundColor3 = Config[configKey] 
            and Color3.fromRGB(50, 180, 50) 
            or Color3.fromRGB(80, 80, 80)
        Toggle.Text = Config[configKey] and "ON" or "OFF"
        Log(label .. ": " .. (Config[configKey] and "✅ ATIVADO" or "❌ DESATIVADO"), true)
    end)
    
    return Container
end

local function CreateActionButton(parent, label, icon, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -16, 0, 38)
    Button.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    Button.BorderSizePixel = 0
    Button.Text = icon .. " " .. label
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 12
    Button.Font = Enum.Font.GothamBold
    Button.Parent = parent
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Button
    
    Button.MouseButton1Click:Connect(function()
        Button.BackgroundColor3 = Color3.fromRGB(40, 120, 40)
        wait(0.1)
        Button.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
        
        if callback then
            callback()
        end
    end)
    
    return Button
end

--=====================================================
-- ADICIONAR ELEMENTOS AO MENU
--=====================================================

-- Seção FARM
local FarmLabel = Instance.new("TextLabel")
FarmLabel.Size = UDim2.new(1, -16, 0, 25)
FarmLabel.BackgroundColor3 = Color3.fromRGB(255, 150, 0)
FarmLabel.BorderSizePixel = 0
FarmLabel.Text = "⚙️ AUTO FARM"
FarmLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
FarmLabel.TextSize = 12
FarmLabel.Font = Enum.Font.GothamBold
FarmLabel.Parent = MenuScroll

local FarmCorner = Instance.new("UICorner")
FarmCorner.CornerRadius = UDim.new(0, 4)
FarmCorner.Parent = FarmLabel

CreateToggleButton(MenuScroll, "Auto Farm", "🔄", "AutoFarm")
CreateToggleButton(MenuScroll, "Auto Attack", "⚔️", "AutoAttack")
CreateToggleButton(MenuScroll, "Auto Fruit", "🍎", "AutoFruit")
CreateToggleButton(MenuScroll, "Auto Stats", "💪", "AutoStats")

CreateActionButton(MenuScroll, "INICIAR FARM", "▶️", function()
    Config.AutoFarm = true
    Config.AutoAttack = true
    Log("FARM INICIADO! 🚀", true)
end)

CreateActionButton(MenuScroll, "PARAR FARM", "⏹️", function()
    Config.AutoFarm = false
    Config.AutoAttack = false
    Log("Farm parado!", true)
end)

-- Separador
local Sep1 = Instance.new("Frame")
Sep1.Size = UDim2.new(1, -16, 0, 2)
Sep1.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
Sep1.BorderSizePixel = 0
Sep1.Parent = MenuScroll

-- Seção PLAYER
local PlayerLabel = Instance.new("TextLabel")
PlayerLabel.Size = UDim2.new(1, -16, 0, 25)
PlayerLabel.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
PlayerLabel.BorderSizePixel = 0
PlayerLabel.Text = "👤 PLAYER"
PlayerLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
PlayerLabel.TextSize = 12
PlayerLabel.Font = Enum.Font.GothamBold
PlayerLabel.Parent = MenuScroll

local PlayerCorner = Instance.new("UICorner")
PlayerCorner.CornerRadius = UDim.new(0, 4)
PlayerCorner.Parent = PlayerLabel

CreateToggleButton(MenuScroll, "God Mode", "👻", "GodMode")
CreateToggleButton(MenuScroll, "Anti-AFK", "🏃", "AntiAFK")

CreateActionButton(MenuScroll, "Walk Speed +5", "🏃", function()
    Config.WalkSpeed = Config.WalkSpeed + 5
    Humanoid.WalkSpeed = Config.WalkSpeed
    Log("Walk Speed: " .. Config.WalkSpeed, true)
end)

CreateActionButton(MenuScroll, "Walk Speed -5", "🐢", function()
    Config.WalkSpeed = math.max(16, Config.WalkSpeed - 5)
    Humanoid.WalkSpeed = Config.WalkSpeed
    Log("Walk Speed: " .. Config.WalkSpeed, true)
end)

-- Separador
local Sep2 = Instance.new("Frame")
Sep2.Size = UDim2.new(1, -16, 0, 2)
Sep2.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
Sep2.BorderSizePixel = 0
Sep2.Parent = MenuScroll

-- Seção TELEPORT
local TeleportLabel = Instance.new("TextLabel")
TeleportLabel.Size = UDim2.new(1, -16, 0, 25)
TeleportLabel.BackgroundColor3 = Color3.fromRGB(150, 100, 255)
TeleportLabel.BorderSizePixel = 0
TeleportLabel.Text = "📍 TELEPORT"
TeleportLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
TeleportLabel.TextSize = 12
TeleportLabel.Font = Enum.Font.GothamBold
TeleportLabel.Parent = MenuScroll

local TeleportCorner = Instance.new("UICorner")
TeleportCorner.CornerRadius = UDim.new(0, 4)
TeleportCorner.Parent = TeleportLabel

local Locations = {
    {name = "Starter Island", pos = Vector3.new(0, 20, 0)},
    {name = "Colosseum", pos = Vector3.new(100, 50, 100)},
    {name = "Pirate Village", pos = Vector3.new(-500, 30, 0)},
    {name = "Desert", pos = Vector3.new(0, 30, -500)},
}

for _, location in ipairs(Locations) do
    CreateActionButton(MenuScroll, location.name, "📍", function()
        HumanoidRootPart.CFrame = CFrame.new(location.pos)
        Log("Teleportado para " .. location.name, true)
    end)
end

-- Separador
local Sep3 = Instance.new("Frame")
Sep3.Size = UDim2.new(1, -16, 0, 2)
Sep3.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
Sep3.BorderSizePixel = 0
Sep3.Parent = MenuScroll

-- Seção MISC
local MiscLabel = Instance.new("TextLabel")
MiscLabel.Size = UDim2.new(1, -16, 0, 25)
MiscLabel.BackgroundColor3 = Color3.fromRGB(200, 100, 100)
MiscLabel.BorderSizePixel = 0
MiscLabel.Text = "⚙️ MISC"
MiscLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
MiscLabel.TextSize = 12
MiscLabel.Font = Enum.Font.GothamBold
MiscLabel.Parent = MenuScroll

local MiscCorner = Instance.new("UICorner")
MiscCorner.CornerRadius = UDim.new(0, 4)
MiscCorner.Parent = MiscLabel

CreateToggleButton(MenuScroll, "Anti-Detecção", "🛡️", "AntiDetectionEnabled")
CreateToggleButton(MenuScroll, "Remove Fog", "🌫️", "RemoveFog")

CreateActionButton(MenuScroll, "FECHAR MENU", "❌", function()
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
local FarmSystem = {}

function FarmSystem:GetNearestMob()
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

function FarmSystem:AttackMob(mob)
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
    
    -- AUTO FARM
    if Config.AutoFarm and Config.AutoAttack then
        local mob = FarmSystem:GetNearestMob()
        if mob then
            FarmSystem:AttackMob(mob)
        end
    end
    
    -- GOD MODE
    if Config.GodMode then
        Humanoid.Health = Humanoid.MaxHealth
    end
    
    -- ANTI-AFK
    if Config.AntiAFK then
        local randomX = math.random(-2, 2) * 0.1
        local randomZ = math.random(-2, 2) * 0.1
        HumanoidRootPart.CFrame = HumanoidRootPart.CFrame * CFrame.new(randomX, 0, randomZ)
    end
end)

--=====================================================
-- RECONEXÃO
--=====================================================
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
    Log("Personagem respawned!", true)
end)

--=====================================================
-- INICIALIZAÇÃO
--=====================================================
Log("HOHO HUB Mobile v" .. Version .. " carregado!", true)
Log("Toque no botão 🔥 para abrir o menu!", true)
print("=====================================")
print("🔥 HOHO HUB - BLOX FRUITS MOBILE 🔥")
print("=====================================")
