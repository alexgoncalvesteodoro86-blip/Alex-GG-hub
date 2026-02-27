```lua
--[[
    █████╗ ██╗     ███████╗██╗  ██╗     ██████╗  ██████╗     ██╗  ██╗██╗   ██╗██████╗ 
    ██╔══██╗██║     ██╔════╝╚██╗██╔╝    ██╔════╝ ██╔═══██╗    ██║  ██║██║   ██║██╔══██╗
    ███████║██║     █████╗   ╚███╔╝     ██║  ███╗██║   ██║    ███████║██║   ██║██████╔╝
    ██╔══██║██║     ██╔══╝   ██╔██╗     ██║   ██║██║   ██║    ██╔══██║██║   ██║██╔══██╗
    ██║  ██║███████╗███████╗██╔╝ ██╗    ╚██████╔╝╚██████╔╝    ██║  ██║╚██████╔╝██████╔╝
    ╚═╝  ╚═╝╚══════╝╚══════╝╚═╝  ╚═╝     ╚═════╝  ╚═════╝     ╚═╝  ╚═╝ ╚═════╝ ╚═════╝ 
    
    ██████╗ ██╗      ██████╗ ██╗  ██╗    ███████╗██████╗ ██╗   ██╗██╗████████╗███████╗
    ██╔══██╗██║     ██╔═══██╗╚██╗██╔╝    ██╔════╝██╔══██╗██║   ██║██║╚══██╔══╝██╔════╝
    ██████╔╝██║     ██║   ██║ ╚███╔╝     █████╗  ██████╔╝██║   ██║██║   ██║   █████╗  
    ██╔══██╗██║     ██║   ██║ ██╔██╗     ██╔══╝  ██╔══██╗██║   ██║██║   ██║   ██╔══╝  
    ██████╔╝███████╗╚██████╔╝██╔╝ ██╗    ██║     ██║  ██║╚██████╔╝██║   ██║   ███████╗
    ╚═════╝ ╚══════╝ ╚═════╝ ╚═╝  ╚═╝    ╚═╝     ╚═╝  ╚═╝ ╚═════╝ ╚═╝   ╚═╝   ╚══════╝
    
    Version: 2.0.0
    Developer: Alex GG
--]]

-- Serviços
local Players = game:GetService("Players")
local VirtualUser = game:GetService("VirtualUser")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local MarketplaceService = game:GetService("MarketplaceService")

-- Variáveis globais
local player = Players.LocalPlayer
local mouse = player:GetMouse()
local camera = workspace.CurrentCamera
local LocalPlayer = player

-- Verificação de segurança
if not player or not player.Character then
    return
end

-- Sistema de Key (simples)
local KeySystem = {
    RequiredKey = "ALEXGG2024",
    IsAuthenticated = false
}

-- Configurações do Hub
local Settings = {
    AutoFarm = false,
    AutoQuest = false,
    AutoMastery = false,
    AutoFruit = false,
    AutoBoss = false,
    AutoHaki = false,
    AutoEquip = false,
    AutoStats = false,
    SelectedStat = "Melee",
    FarmRadius = 200,
    MasteryType = "Sword",
    SafeMode = true,
    Notifications = true,
    AntiLag = true
}

-- Dados das ilhas para teleporte
local Islands = {
    {Name = "Marine Starter", Position = Vector3.new(1044.5, 27.5, 1189.5)},
    {Name = "Jungle", Position = Vector3.new(-1152.5, 12.5, 1562.5)},
    {Name = "Pirate Village", Position = Vector3.new(-1231.5, 14.5, 394.5)},
    {Name = "Desert", Position = Vector3.new(1012.5, 23.5, 1439.5)},
    {Name = "Frozen Village", Position = Vector3.new(621.5, 72.5, -558.5)},
    {Name = "Marine Fortress", Position = Vector3.new(-472.5, 19.5, -2755.5)},
    {Name = "Skyland 1", Position = Vector3.new(-4952.5, 295.5, -2401.5)},
    {Name = "Skyland 2", Position = Vector3.new(-6802.5, 435.5, -2234.5)},
    {Name = "Prison", Position = Vector3.new(4872.5, 23.5, -874.5)},
    {Name = "Colosseum", Position = Vector3.new(-1755.5, 10.5, -1512.5)}
}

-- Interface principal
local AlexGGHub = Instance.new("ScreenGui")
AlexGGHub.Name = "AlexGGHub"
AlexGGHub.Parent = CoreGui
AlexGGHub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
AlexGGHub.Enabled = false -- Começa desativado até key ser inserida

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = AlexGGHub
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -350, 0.5, -250)
MainFrame.Size = UDim2.new(0, 700, 0, 500)
MainFrame.ClipsDescendants = true

-- Sombra
local Shadow = Instance.new("ImageLabel")
Shadow.Name = "Shadow"
Shadow.Parent = MainFrame
Shadow.BackgroundTransparency = 1
Shadow.Position = UDim2.new(0, -15, 0, -15)
Shadow.Size = UDim2.new(1, 30, 1, 30)
Shadow.Image = "rbxassetid://6014261993"
Shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
Shadow.ImageTransparency = 0.3
Shadow.ScaleType = Enum.ScaleType.Slice
Shadow.SliceCenter = Rect.new(10, 10, 118, 118)

-- Gradient superior
local TopGradient = Instance.new("UIGradient")
TopGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 144, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(147, 112, 219))
})
TopGradient.Rotation = 90

-- Título
local TitleFrame = Instance.new("Frame")
TitleFrame.Name = "TitleFrame"
TitleFrame.Parent = MainFrame
TitleFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
TitleFrame.BorderSizePixel = 0
TitleFrame.Size = UDim2.new(1, 0, 0, 60)

local TitleGradient = Instance.new("UIGradient")
TitleGradient.Parent = TitleFrame
TitleGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 144, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(147, 112, 219))
})
TitleGradient.Rotation = 90

local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Parent = TitleFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 20, 0, 0)
Title.Size = UDim2.new(0, 200, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "ALEX GG HUB"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 24
Title.TextXAlignment = Enum.TextXAlignment.Left

local Version = Instance.new("TextLabel")
Version.Name = "Version"
Version.Parent = TitleFrame
Version.BackgroundTransparency = 1
Version.Position = UDim2.new(1, -80, 0, 0)
Version.Size = UDim2.new(0, 60, 1, 0)
Version.Font = Enum.Font.Gotham
Version.Text = "v2.0"
Version.TextColor3 = Color3.fromRGB(200, 200, 200)
Version.TextSize = 14
Version.TextXAlignment = Enum.TextXAlignment.Right

-- Botão fechar
local CloseButton = Instance.new("ImageButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = TitleFrame
CloseButton.BackgroundTransparency = 1
CloseButton.Position = UDim2.new(1, -40, 0, 10)
CloseButton.Size = UDim2.new(0, 40, 0, 40)
CloseButton.Image = "rbxassetid://10709789112"
CloseButton.ImageColor3 = Color3.fromRGB(255, 255, 255)

CloseButton.MouseButton1Click:Connect(function()
    AlexGGHub.Enabled = false
end)

-- Abas
local TabsFrame = Instance.new("Frame")
TabsFrame.Name = "TabsFrame"
TabsFrame.Parent = MainFrame
TabsFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
TabsFrame.BorderSizePixel = 0
TabsFrame.Position = UDim2.new(0, 0, 0, 60)
TabsFrame.Size = UDim2.new(1, 0, 0, 50)

local Tabs = {
    {Name = "FARM", Icon = "rbxassetid://10709742641"},
    {Name = "BOSS", Icon = "rbxassetid://10709801055"},
    {Name = "TELEPORT", Icon = "rbxassetid://10709807785"},
    {Name = "PLAYER", Icon = "rbxassetid://10709813212"},
    {Name = "MISC", Icon = "rbxassetid://10709818717"}
}

local TabButtons = {}
local CurrentTab = "FARM"

-- Container do conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "ContentFrame"
ContentFrame.Parent = MainFrame
ContentFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
ContentFrame.BackgroundTransparency = 0.1
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 0, 0, 110)
ContentFrame.Size = UDim2.new(1, 0, 1, -110)

-- Função para criar abas
local function CreateTabs()
    for i, tabData in ipairs(Tabs) do
        local TabButton = Instance.new("ImageButton")
        TabButton.Name = tabData.Name.."Tab"
        TabButton.Parent = TabsFrame
        TabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
        TabButton.Position = UDim2.new(0, (i-1) * 140, 0, 0)
        TabButton.Size = UDim2.new(0, 140, 1, 0)
        TabButton.AutoButtonColor = false
        
        local TabIcon = Instance.new("ImageLabel")
        TabIcon.Name = "Icon"
        TabIcon.Parent = TabButton
        TabIcon.BackgroundTransparency = 1
        TabIcon.Position = UDim2.new(0, 10, 0, 5)
        TabIcon.Size = UDim2.new(0, 40, 0, 40)
        TabIcon.Image = tabData.Icon
        TabIcon.ImageColor3 = Color3.fromRGB(200, 200, 200)
        
        local TabLabel = Instance.new("TextLabel")
        TabLabel.Name = "Label"
        TabLabel.Parent = TabButton
        TabLabel.BackgroundTransparency = 1
        TabLabel.Position = UDim2.new(0, 55, 0, 0)
        TabLabel.Size = UDim2.new(0, 75, 1, 0)
        TabLabel.Font = Enum.Font.GothamBold
        TabLabel.Text = tabData.Name
        TabLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        TabLabel.TextSize = 14
        
        TabButtons[tabData.Name] = {
            Button = TabButton,
            Icon = TabIcon,
            Label = TabLabel
        }
        
        TabButton.MouseButton1Click:Connect(function()
            for name, data in pairs(TabButtons) do
                data.Button.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
                data.Icon.ImageColor3 = Color3.fromRGB(200, 200, 200)
                data.Label.TextColor3 = Color3.fromRGB(200, 200, 200)
            end
            TabButton.BackgroundColor3 = Color3.fromRGB(30, 144, 255)
            TabIcon.ImageColor3 = Color3.fromRGB(255, 255, 255)
            TabLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            CurrentTab = tabData.Name
            UpdateContent()
        end)
    end
end

-- Função para criar toggle button
local function CreateToggle(parent, name, default, callback)
    local ToggleFrame = Instance.new("Frame")
    ToggleFrame.Name = name.."Toggle"
    ToggleFrame.Parent = parent
    ToggleFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    ToggleFrame.BorderSizePixel = 0
    ToggleFrame.Size = UDim2.new(1, -20, 0, 50)
    
    local ToggleLabel = Instance.new("TextLabel")
    ToggleLabel.Parent = ToggleFrame
    ToggleLabel.BackgroundTransparency = 1
    ToggleLabel.Position = UDim2.new(0, 10, 0, 0)
    ToggleLabel.Size = UDim2.new(0, 150, 1, 0)
    ToggleLabel.Font = Enum.Font.Gotham
    ToggleLabel.Text = name
    ToggleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleLabel.TextSize = 14
    ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local ToggleButton = Instance.new("ImageButton")
    ToggleButton.Parent = ToggleFrame
    ToggleButton.BackgroundColor3 = default and Color3.fromRGB(30, 144, 255) or Color3.fromRGB(60, 60, 70)
    ToggleButton.Position = UDim2.new(1, -70, 0, 10)
    ToggleButton.Size = UDim2.new(0, 60, 0, 30)
    ToggleButton.AutoButtonColor = false
    
    local ToggleIndicator = Instance.new("Frame")
    ToggleIndicator.Parent = ToggleButton
    ToggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    ToggleIndicator.Position = default and UDim2.new(1, -28, 0, 4) or UDim2.new(0, 4, 0, 4)
    ToggleIndicator.Size = UDim2.new(0, 22, 0, 22)
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 11)
    UICorner.Parent = ToggleIndicator
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Thickness = 1
    UIStroke.Color = Color3.fromRGB(255, 255, 255)
    UIStroke.Parent = ToggleIndicator
    
    local enabled = default
    
    ToggleButton.MouseButton1Click:Connect(function()
        enabled = not enabled
        ToggleButton.BackgroundColor3 = enabled and Color3.fromRGB(30, 144, 255) or Color3.fromRGB(60, 60, 70)
        
        local goal = enabled and {Position = UDim2.new(1, -28, 0, 4)} or {Position = UDim2.new(0, 4, 0, 4)}
        local tween = TweenService:Create(ToggleIndicator, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), goal)
        tween:Play()
        
        callback(enabled)
    end)
    
    return ToggleFrame
end

-- Função para criar dropdown
local function CreateDropdown(parent, name, options, default, callback)
    local DropdownFrame = Instance.new("Frame")
    DropdownFrame.Name = name.."Dropdown"
    DropdownFrame.Parent = parent
    DropdownFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    DropdownFrame.BorderSizePixel = 0
    DropdownFrame.Size = UDim2.new(1, -20, 0, 50)
    
    local DropdownLabel = Instance.new("TextLabel")
    DropdownLabel.Parent = DropdownFrame
    DropdownLabel.BackgroundTransparency = 1
    DropdownLabel.Position = UDim2.new(0, 10, 0, 0)
    DropdownLabel.Size = UDim2.new(0, 150, 1, 0)
    DropdownLabel.Font = Enum.Font.Gotham
    DropdownLabel.Text = name
    DropdownLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    DropdownLabel.TextSize = 14
    DropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local DropdownButton = Instance.new("ImageButton")
    DropdownButton.Parent = DropdownFrame
    DropdownButton.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    DropdownButton.Position = UDim2.new(1, -150, 0, 10)
    DropdownButton.Size = UDim2.new(0, 140, 0, 30)
    DropdownButton.AutoButtonColor = false
    
    local DropdownText = Instance.new("TextLabel")
    DropdownText.Parent = DropdownButton
    DropdownText.BackgroundTransparency = 1
    DropdownText.Size = UDim2.new(1, -10, 1, 0)
    DropdownText.Position = UDim2.new(0, 10, 0, 0)
    DropdownText.Font = Enum.Font.Gotham
    DropdownText.Text = default or options[1]
    DropdownText.TextColor3 = Color3.fromRGB(255, 255, 255)
    DropdownText.TextSize = 14
    DropdownText.TextXAlignment = Enum.TextXAlignment.Left
    
    local Arrow = Instance.new("ImageLabel")
    Arrow.Parent = DropdownButton
    Arrow.BackgroundTransparency = 1
    Arrow.Position = UDim2.new(1, -25, 0, 8)
    Arrow.Size = UDim2.new(0, 15, 0, 15)
    Arrow.Image = "rbxassetid://10709840506"
    Arrow.ImageColor3 = Color3.fromRGB(255, 255, 255)
    Arrow.Rotation = 180
    
    return DropdownFrame
end

-- Função para criar botão
local function CreateButton(parent, text, color, callback)
    local Button = Instance.new("ImageButton")
    Button.Parent = parent
    Button.BackgroundColor3 = color or Color3.fromRGB(30, 144, 255)
    Button.Size = UDim2.new(1, -20, 0, 40)
    Button.AutoButtonColor = false
    
    local ButtonText = Instance.new("TextLabel")
    ButtonText.Parent = Button
    ButtonText.BackgroundTransparency = 1
    ButtonText.Size = UDim2.new(1, 0, 1, 0)
    ButtonText.Font = Enum.Font.GothamBold
    ButtonText.Text = text
    ButtonText.TextColor3 = Color3.fromRGB(255, 255, 255)
    ButtonText.TextSize = 14
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = Button
    
    Button.MouseButton1Click:Connect(callback)
    
    return Button
end

-- Função para atualizar conteúdo baseado na aba atual
function UpdateContent()
    for _, child in pairs(ContentFrame:GetChildren()) do
        child:Destroy()
    end
    
    local ScrollFrame = Instance.new("ScrollingFrame")
    ScrollFrame.Parent = ContentFrame
    ScrollFrame.BackgroundTransparency = 1
    ScrollFrame.Size = UDim2.new(1, 0, 1, 0)
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    ScrollFrame.ScrollBarThickness = 6
    ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(30, 144, 255)
    
    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Parent = ScrollFrame
    UIListLayout.Padding = UDim.new(0, 10)
    UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    
    if CurrentTab == "FARM" then
        -- Auto Farm
        local FarmToggle = CreateToggle(ScrollFrame, "Auto Farm", Settings.AutoFarm, function(value)
            Settings.AutoFarm = value
            Notify("Auto Farm "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Auto Quest
        local QuestToggle = CreateToggle(ScrollFrame, "Auto Quest", Settings.AutoQuest, function(value)
            Settings.AutoQuest = value
            Notify("Auto Quest "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Auto Mastery
        local MasteryToggle = CreateToggle(ScrollFrame, "Auto Mastery", Settings.AutoMastery, function(value)
            Settings.AutoMastery = value
            Notify("Auto Mastery "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Tipo de Mastery
        local MasteryType = CreateDropdown(ScrollFrame, "Mastery Type", {"Sword", "Gun", "Fruit"}, Settings.MasteryType, function(value)
            Settings.MasteryType = value
        end)
        
        -- Auto Farm Fruit
        local FruitToggle = CreateToggle(ScrollFrame, "Auto Farm Fruit", Settings.AutoFruit, function(value)
            Settings.AutoFruit = value
            Notify("Auto Farm Fruit "..(value and "Ativado" or "Desativado"))
        end)
        
    elseif CurrentTab == "BOSS" then
        -- Auto Boss
        local BossToggle = CreateToggle(ScrollFrame, "Auto Boss", Settings.AutoBoss, function(value)
            Settings.AutoBoss = value
            Notify("Auto Boss "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Botões para bosses específicos
        local BossList = {"Saber Expert", "Greybeard", "Diamond", "Rip Indra", "Beautiful Pirate"}
        for _, boss in ipairs(BossList) do
            local BossButton = CreateButton(ScrollFrame, "Farm "..boss, Color3.fromRGB(147, 112, 219), function()
                Notify("Focando em "..boss)
            end)
        end
        
    elseif CurrentTab == "TELEPORT" then
        -- Botões de teleporte
        for _, island in ipairs(Islands) do
            local TeleportButton = CreateButton(ScrollFrame, island.Name, Color3.fromRGB(255, 140, 0), function()
                TeleportToIsland(island.Position)
                Notify("Teleportando para "..island.Name)
            end)
        end
        
    elseif CurrentTab == "PLAYER" then
        -- Auto Stats
        local StatsToggle = CreateToggle(ScrollFrame, "Auto Stats", Settings.AutoStats, function(value)
            Settings.AutoStats = value
            Notify("Auto Stats "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Tipo de stat
        local StatType = CreateDropdown(ScrollFrame, "Stat Priority", {"Melee", "Defense", "Sword", "Gun", "Fruit"}, Settings.SelectedStat, function(value)
            Settings.SelectedStat = value
        end)
        
        -- Auto Haki
        local HakiToggle = CreateToggle(ScrollFrame, "Auto Haki", Settings.AutoHaki, function(value)
            Settings.AutoHaki = value
            Notify("Auto Haki "..(value and "Ativado" or "Desativado"))
        end)
        
        -- Auto Equip
        local EquipToggle = CreateToggle(ScrollFrame, "Auto Equip Best", Settings.AutoEquip, function(value)
            Settings.AutoEquip = value
            Notify("Auto Equip "..(value and "Ativado" or "Desativado"))
        end)
        
    elseif CurrentTab == "MISC" then
        -- Safe Mode
        local SafeToggle = CreateToggle(ScrollFrame, "Safe Mode", Settings.SafeMode, function(value)
            Settings.SafeMode = value
        end)
        
        -- Notificações
        local NotifToggle = CreateToggle(ScrollFrame, "Notificações", Settings.Notifications, function(value)
            Settings.Notifications = value
        end)
        
        -- Anti Lag
        local AntiLagToggle = CreateToggle(ScrollFrame, "Anti Lag", Settings.AntiLag, function(value)
            Settings.AntiLag = value
            if value then
                game:GetService("RunService"):Set3dRenderingEnabled(false)
            else
                game:GetService("RunService"):Set3dRenderingEnabled(true)
            end
        end)
        
        -- Auto Rejoin
        local RejoinButton = CreateButton(ScrollFrame, "Auto Rejoin (Hold)", Color3.fromRGB(220, 20, 60), function()
            AutoRejoin()
        end)
    end
    
    -- Ajustar canvas size
    local function UpdateCanvas()
        local contentHeight = UIListLayout.AbsoluteContentSize.Y
        ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, contentHeight + 20)
    end
    
    UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize= UDim2.new(0, 0, 0, contentHeight + 20)
    end
    
    UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(UpdateCanvas)
    UpdateCanvas()
end

-- Função de notificação
function Notify(message, duration)
    if not Settings.Notifications then return end
    
    duration = duration or 3
    
    local Notification = Instance.new("Frame")
    Notification.Name = "Notification"
    Notification.Parent = AlexGGHub
    Notification.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    Notification.BorderSizePixel = 0
    Notification.Position = UDim2.new(1, -320, 0, 50)
    Notification.Size = UDim2.new(0, 300, 0, 60)
    Notification.AnchorPoint = Vector2.new(1, 0)
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Notification
    
    local NotificationText = Instance.new("TextLabel")
    NotificationText.Parent = Notification
    NotificationText.BackgroundTransparency = 1
    NotificationText.Size = UDim2.new(1, -20, 1, 0)
    NotificationText.Position = UDim2.new(0, 10, 0, 0)
    NotificationText.Font = Enum.Font.Gotham
    NotificationText.Text = message
    NotificationText.TextColor3 = Color3.fromRGB(255, 255, 255)
    NotificationText.TextSize = 14
    NotificationText.TextWrapped = true
    
    Notification:TweenPosition(UDim2.new(1, -330, 0, 50), "Out", "Quad", 0.3, true)
    
    task.delay(duration, function()
        Notification:TweenPosition(UDim2.new(1, -20, 0, 50), "Out", "Quad", 0.3, true)
        task.delay(0.3, function()
            Notification:Destroy()
        end)
    end)
end

-- Função de teleporte
function TeleportToIsland(position)
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        character.HumanoidRootPart.CFrame = CFrame.new(position)
    end
end

-- Função de auto rejoin
function AutoRejoin()
    local placeId = game.PlaceId
    local jobId = game.JobId
    
    TeleportService:TeleportToPlaceInstance(placeId, jobId, player)
end

-- Sistema de Key
local KeyFrame = Instance.new("Frame")
KeyFrame.Name = "KeyFrame"
KeyFrame.Parent = AlexGGHub
KeyFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
KeyFrame.BackgroundTransparency = 0.1
KeyFrame.BorderSizePixel = 0
KeyFrame.Position = UDim2.new(0.5, -200, 0.5, -100)
KeyFrame.Size = UDim2.new(0, 400, 0, 200)
KeyFrame.Visible = true

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Parent = KeyFrame
KeyTitle.BackgroundTransparency = 1
KeyTitle.Position = UDim2.new(0, 0, 0, 20)
KeyTitle.Size = UDim2.new(1, 0, 0, 40)
KeyTitle.Font = Enum.Font.GothamBold
KeyTitle.Text = "ALEX GG HUB - KEY SYSTEM"
KeyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyTitle.TextSize = 20

local KeyInput = Instance.new("TextBox")
KeyInput.Parent = KeyFrame
KeyInput.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
KeyInput.Position = UDim2.new(0.1, 0, 0.4, 0)
KeyInput.Size = UDim2.new(0.8, 0, 0, 40)
KeyInput.Font = Enum.Font.Gotham
KeyInput.PlaceholderText = "Insira a Key"
KeyInput.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
KeyInput.Text = ""
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.TextSize = 16

local UICornerInput = Instance.new("UICorner")
UICornerInput.CornerRadius = UDim.new(0, 6)
UICornerInput.Parent = KeyInput

local KeyButton = Instance.new("ImageButton")
KeyButton.Parent = KeyFrame
KeyButton.BackgroundColor3 = Color3.fromRGB(30, 144, 255)
KeyButton.Position = UDim2.new(0.3, 0, 0.6, 0)
KeyButton.Size = UDim2.new(0.4, 0, 0, 40)
KeyButton.AutoButtonColor = false

local KeyButtonText = Instance.new("TextLabel")
KeyButtonText.Parent = KeyButton
KeyButtonText.BackgroundTransparency = 1
KeyButtonText.Size = UDim2.new(1, 0, 1, 0)
KeyButtonText.Font = Enum.Font.GothamBold
KeyButtonText.Text = "VERIFICAR"
KeyButtonText.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyButtonText.TextSize = 16

local UICornerButton = Instance.new("UICorner")
UICornerButton.CornerRadius = UDim.new(0, 6)
UICornerButton.Parent = KeyButton

KeyButton.MouseButton1Click:Connect(function()
    if KeyInput.Text == KeySystem.RequiredKey then
        KeySystem.IsAuthenticated = true
        KeyFrame.Visible = false
        AlexGGHub.Enabled = true
        Notify("Bem-vindo ao Alex GG Hub!")
    else
        KeyInput.Text = ""
        KeyInput.PlaceholderText = "Key Inválida!"
        KeyInput.PlaceholderColor3 = Color3.fromRGB(255, 0, 0)
    end
end)

-- Funções principais do farm
local function FindNearestEnemy()
    local character = player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return nil end
    
    local rootPart = character.HumanoidRootPart
    local nearestEnemy = nil
    local shortestDistance = Settings.FarmRadius
    
    for _, enemy in pairs(workspace:GetDescendants()) do
        if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy:FindFirstChild("HumanoidRootPart") then
            local humanoid = enemy.Humanoid
            if humanoid.Health > 0 and humanoid.MaxHealth > 0 then
                -- Verificar se é um inimigo válido (NPC)
                if enemy.Name:find("NPC") or enemy.Name:find("Boss") or enemy.Name:find("Enemy") then
                    local distance = (rootPart.Position - enemy.HumanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        nearestEnemy = enemy
                    end
                end
            end
        end
    end
    
    return nearestEnemy
end

-- Loop principal
local function MainLoop()
    while AlexGGHub.Enabled do
        task.wait(0.1)
        
        -- Anti AFK
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
        
        -- Auto Farm
        if Settings.AutoFarm then
            local enemy = FindNearestEn
