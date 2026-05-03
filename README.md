-- GROSSLib - Biblioteca de UI para Roblox
-- Versão: 2.0.0 - Com Temas e Customização

local GROSSLib = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- Configurações padrão
local Defaults = {
    MenuFadeTime = 0.2,
    TabPadding = 8,
    SmoothDrag = 0.15,
}

-- Tabelas globais para elementos
getgenv().Toggles = getgenv().Toggles or {}
getgenv().Options = getgenv().Options or {}

-- ═══════════════════════════════════════════════════════════
-- SISTEMA DE TEMAS
-- ═══════════════════════════════════════════════════════════

local Themes = {
    Dark = {
        Name = "Dark",
        Accent = Color3.fromRGB(255, 80, 120),
        Background = Color3.fromRGB(18, 18, 18),
        Header = Color3.fromRGB(12, 12, 12),
        Groupbox = Color3.fromRGB(22, 22, 22),
        Element = Color3.fromRGB(30, 30, 30),
        Text = Color3.fromRGB(240, 240, 240),
        SubText = Color3.fromRGB(200, 200, 200),
        Border = Color3.fromRGB(40, 40, 40),
    },
    Darker = {
        Name = "Darker",
        Accent = Color3.fromRGB(72, 138, 182),
        Background = Color3.fromRGB(15, 15, 15),
        Header = Color3.fromRGB(10, 10, 10),
        Groupbox = Color3.fromRGB(18, 18, 18),
        Element = Color3.fromRGB(25, 25, 25),
        Text = Color3.fromRGB(240, 240, 240),
        SubText = Color3.fromRGB(170, 170, 170),
        Border = Color3.fromRGB(35, 35, 35),
    },
    Light = {
        Name = "Light",
        Accent = Color3.fromRGB(0, 103, 192),
        Background = Color3.fromRGB(240, 240, 240),
        Header = Color3.fromRGB(255, 255, 255),
        Groupbox = Color3.fromRGB(250, 250, 250),
        Element = Color3.fromRGB(255, 255, 255),
        Text = Color3.fromRGB(0, 0, 0),
        SubText = Color3.fromRGB(80, 80, 80),
        Border = Color3.fromRGB(200, 200, 200),
    },
    Aqua = {
        Name = "Aqua",
        Accent = Color3.fromRGB(60, 165, 165),
        Background = Color3.fromRGB(18, 18, 18),
        Header = Color3.fromRGB(12, 12, 12),
        Groupbox = Color3.fromRGB(22, 22, 22),
        Element = Color3.fromRGB(30, 30, 30),
        Text = Color3.fromRGB(240, 240, 240),
        SubText = Color3.fromRGB(170, 170, 170),
        Border = Color3.fromRGB(40, 70, 70),
    },
    Amethyst = {
        Name = "Amethyst",
        Accent = Color3.fromRGB(97, 62, 167),
        Background = Color3.fromRGB(18, 18, 18),
        Header = Color3.fromRGB(12, 12, 12),
        Groupbox = Color3.fromRGB(22, 22, 22),
        Element = Color3.fromRGB(30, 30, 30),
        Text = Color3.fromRGB(240, 240, 240),
        SubText = Color3.fromRGB(170, 170, 170),
        Border = Color3.fromRGB(60, 50, 70),
    },
    Rose = {
        Name = "Rose",
        Accent = Color3.fromRGB(180, 55, 90),
        Background = Color3.fromRGB(18, 18, 18),
        Header = Color3.fromRGB(12, 12, 12),
        Groupbox = Color3.fromRGB(22, 22, 22),
        Element = Color3.fromRGB(30, 30, 30),
        Text = Color3.fromRGB(240, 240, 240),
        SubText = Color3.fromRGB(170, 170, 170),
        Border = Color3.fromRGB(110, 70, 85),
    },
}

-- Função principal para criar janela
function GROSSLib:CreateWindow(Config)
    Config = Config or {}
    
    local Window = {
        Tabs = {},
        Unloaded = false,
        ToggleKeybind = nil,
        CurrentTheme = Themes[Config.Theme or "Dark"],
        ThemeableObjects = {},
    }
    
    -- Criar ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "GROSSMenu_" .. tick()
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Proteção contra detecção
    if gethui then
        ScreenGui.Parent = gethui()
    elseif syn and syn.protect_gui then
        syn.protect_gui(ScreenGui)
        ScreenGui.Parent = game.CoreGui
    else
        ScreenGui.Parent = game.CoreGui
    end
    
    -- Frame Principal
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = Config.Size or UDim2.new(0, 800, 0, 470)
    MainFrame.Position = Config.Position or UDim2.new(0.5, -400, 0.5, -235)
    MainFrame.BackgroundColor3 = Window.CurrentTheme.Background
    MainFrame.BackgroundTransparency = 0
    MainFrame.BorderSizePixel = 0
    MainFrame.Visible = Config.AutoShow ~= false
    MainFrame.Parent = ScreenGui
    
    table.insert(Window.ThemeableObjects, {Object = MainFrame, Property = "BackgroundColor3", ThemeKey = "Background"})
    
    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 6)
    MainCorner.Parent = MainFrame
    
    -- Header
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 40)
    Header.BackgroundColor3 = Window.CurrentTheme.Header
    Header.BorderSizePixel = 0
    Header.Parent = MainFrame
    
    table.insert(Window.ThemeableObjects, {Object = Header, Property = "BackgroundColor3", ThemeKey = "Header"})
    
    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 6)
    HeaderCorner.Parent = Header
    
    local HeaderBottomCover = Instance.new("Frame")
    HeaderBottomCover.Size = UDim2.new(1, 0, 0, 6)
    HeaderBottomCover.Position = UDim2.new(0, 0, 1, -6)
    HeaderBottomCover.BackgroundColor3 = Window.CurrentTheme.Header
    HeaderBottomCover.BorderSizePixel = 0
    HeaderBottomCover.Parent = Header
    
    table.insert(Window.ThemeableObjects, {Object = HeaderBottomCover, Property = "BackgroundColor3", ThemeKey = "Header"})
    
    -- Logo
    local Logo = Instance.new("ImageLabel")
    Logo.Size = UDim2.new(0, 24, 0, 24)
    Logo.Position = UDim2.new(0, 12, 0.5, -12)
    Logo.BackgroundTransparency = 1
    Logo.Image = "rbxassetid://7733993369"
    Logo.ImageColor3 = Color3.fromRGB(255, 255, 255)
    Logo.Parent = Header
    
    -- Título
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(0, 300, 1, 0)
    Title.Position = UDim2.new(0, 42, 0, 0)
    Title.BackgroundTransparency = 1
    Title.Text = Config.Title or "GROSSLib Menu"
    Title.TextColor3 = Window.CurrentTheme.Text
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 14
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    table.insert(Window.ThemeableObjects, {Object = Title, Property = "TextColor3", ThemeKey = "Text"})
    
    -- Botões de controle
    local ControlButtons = Instance.new("Frame")
    ControlButtons.Size = UDim2.new(0, 80, 0, 24)
    ControlButtons.Position = UDim2.new(1, -90, 0.5, -12)
    ControlButtons.BackgroundTransparency = 1
    ControlButtons.Parent = Header
    
    -- Botão Minimizar
    local MinimizeBtn = Instance.new("TextButton")
    MinimizeBtn.Size = UDim2.new(0, 24, 0, 24)
    MinimizeBtn.BackgroundColor3 = Window.CurrentTheme.Border
    MinimizeBtn.BorderSizePixel = 0
    MinimizeBtn.Text = "—"
    MinimizeBtn.TextColor3 = Window.CurrentTheme.SubText
    MinimizeBtn.Font = Enum.Font.GothamBold
    MinimizeBtn.TextSize = 14
    MinimizeBtn.Parent = ControlButtons
    
    table.insert(Window.ThemeableObjects, {Object = MinimizeBtn, Property = "BackgroundColor3", ThemeKey = "Border"})
    table.insert(Window.ThemeableObjects, {Object = MinimizeBtn, Property = "TextColor3", ThemeKey = "SubText"})
    
    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 4)
    MinCorner.Parent = MinimizeBtn
    
    -- Botão Fechar
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Size = UDim2.new(0, 24, 0, 24)
    CloseBtn.Position = UDim2.new(0, 56, 0, 0)
    CloseBtn.BackgroundColor3 = Window.CurrentTheme.Border
    CloseBtn.BorderSizePixel = 0
    CloseBtn.Text = "×"
    CloseBtn.TextColor3 = Window.CurrentTheme.SubText
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.TextSize = 18
    CloseBtn.Parent = ControlButtons
    
    table.insert(Window.ThemeableObjects, {Object = CloseBtn, Property = "BackgroundColor3", ThemeKey = "Border"})
    table.insert(Window.ThemeableObjects, {Object = CloseBtn, Property = "TextColor3", ThemeKey = "SubText"})
    
    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 4)
    CloseCorner.Parent = CloseBtn
    
    -- Container de Abas
    local TabButtons = Instance.new("Frame")
    TabButtons.Size = UDim2.new(0, 500, 1, 0)
    TabButtons.Position = UDim2.new(0.5, -250, 0, 0)
    TabButtons.BackgroundTransparency = 1
    TabButtons.Parent = Header
    
    -- Container de Conteúdo
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Size = UDim2.new(1, -20, 1, -60)
    ContentFrame.Position = UDim2.new(0, 10, 0, 50)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.Parent = MainFrame
    
    -- Variáveis de estado
    local minimized = false
    local activeTab = nil
    local tabContents = {}
    local tabButtonElements = {}
    local tabCount = 0
    local menuKeybind = Config.MenuKeybind or Enum.KeyCode.RightControl
    
    -- Sistema de Drag
    local dragging = false
    local dragInput, dragStart, startPos
    local targetPosition = MainFrame.Position
    local smoothness = Defaults.SmoothDrag
    
    RunService.RenderStepped:Connect(function()
        if dragging and dragInput then
            local delta = dragInput.Position - dragStart
            targetPosition = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
        
        local current = MainFrame.Position
        local newX = current.X.Offset + (targetPosition.X.Offset - current.X.Offset) * smoothness
        local newY = current.Y.Offset + (targetPosition.Y.Offset - current.Y.Offset) * smoothness
        
        MainFrame.Position = UDim2.new(targetPosition.X.Scale, newX, targetPosition.Y.Scale, newY)
    end)
    
    Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            targetPosition = MainFrame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    Header.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    
    -- Minimizar
    MinimizeBtn.MouseButton1Click:Connect(function()
        minimized = true
        MainFrame.Visible = false
    end)
    
    -- Keybind para abrir/fechar menu
    UserInputService.InputBegan:Connect(function(input, gpe)
        if not gpe and input.KeyCode == menuKeybind then
            if minimized then
                minimized = false
                MainFrame.Visible = true
            else
                MainFrame.Visible = not MainFrame.Visible
            end
        end
    end)
    
    -- Fechar
    CloseBtn.MouseButton1Click:Connect(function()
        Window:Unload()
    end)
    
    -- Função para aplicar tema
    function Window:SetTheme(themeName)
        local theme = Themes[themeName]
        if not theme then return end
        
        Window.CurrentTheme = theme
        
        for _, data in pairs(Window.ThemeableObjects) do
            if data.Object and data.Object.Parent then
                data.Object[data.Property] = theme[data.ThemeKey]
            end
        end
    end
    
    -- Função para definir transparência
    function Window:SetTransparency(transparency)
        MainFrame.BackgroundTransparency = math.clamp(transparency, 0, 1)
    end
    
    -- Função para mudar título
    function Window:SetTitle(newTitle)
        Title.Text = newTitle
    end
    
    -- Função para trocar keybind
    function Window:SetKeybind(keyCode)
        menuKeybind = keyCode
    end
    
    -- Função para trocar de aba
    local function SwitchTab(tabName)
        if activeTab == tabName then return end
        activeTab = tabName
        
        for name, content in pairs(tabContents) do
            content.Visible = (name == tabName)
        end
        
        for name, button in pairs(tabButtonElements) do
            local isActive = (name == tabName)
            TweenService:Create(button, TweenInfo.new(0.2), {
                TextColor3 = isActive and Window.CurrentTheme.Text or Window.CurrentTheme.SubText
            }):Play()
            
            local underline = button:FindFirstChild("Underline")
            if underline then
                underline.Visible = isActive
            end
        end
    end
    
    -- Adicionar Tab
    function Window:AddTab(name)
        local Tab = {
            Name = name,
            Groupboxes = {},
        }
        
        tabCount = tabCount + 1
        local tabWidth = 90
        
        -- Botão da Tab
        local Button = Instance.new("TextButton")
        Button.Name = name
        Button.Size = UDim2.new(0, tabWidth, 1, 0)
        Button.Position = UDim2.new(0, (tabCount - 1) * (tabWidth + 10), 0, 0)
        Button.BackgroundTransparency = 1
        Button.Text = name
        Button.TextColor3 = Window.CurrentTheme.SubText
        Button.Font = Enum.Font.GothamBold
        Button.TextSize = 12
        Button.Parent = TabButtons
        
        local Underline = Instance.new("Frame")
        Underline.Name = "Underline"
        Underline.Size = UDim2.new(1, -20, 0, 2)
        Underline.Position = UDim2.new(0, 10, 1, -2)
        Underline.BackgroundColor3 = Window.CurrentTheme.Accent
        Underline.BorderSizePixel = 0
        Underline.Visible = false
        Underline.Parent = Button
        
        table.insert(Window.ThemeableObjects, {Object = Underline, Property = "BackgroundColor3", ThemeKey = "Accent"})
        
        Button.MouseButton1Click:Connect(function()
            SwitchTab(name)
        end)
        
        -- Conteúdo da Tab
        local TabContent = Instance.new("Frame")
        TabContent.Name = name .. "Content"
        TabContent.Size = UDim2.new(1, 0, 1, 0)
        TabContent.BackgroundTransparency = 1
        TabContent.Visible = false
        TabContent.Parent = ContentFrame
        
        tabContents[name] = TabContent
        tabButtonElements[name] = Button
        
        if tabCount == 1 then
            SwitchTab(name)
        end
        
        -- [CONTINUA COM AS MESMAS FUNÇÕES DE GROUPBOX E ELEMENTOS...]
        
        function Tab:AddLeftGroupbox(title)
            return self:CreateGroupbox(title, "Left")
        end
        
        function Tab:AddRightGroupbox(title)
            return self:CreateGroupbox(title, "Right")
        end
        
        function Tab:CreateGroupbox(title, side)
            local Groupbox = {
                Elements = {},
                CurrentY = 40,
            }
            
            local existingGroupboxes = 0
            for _, gb in pairs(self.Groupboxes) do
                if gb.Side == side then
                    existingGroupboxes = existingGroupboxes + 1
                end
            end
            
            local GroupFrame = Instance.new("ScrollingFrame")
            GroupFrame.Name = title
            GroupFrame.Size = UDim2.new(0, 380, 1, -10)
            GroupFrame.Position = side == "Left" and UDim2.new(0, 5, 0, existingGroupboxes * 10) or UDim2.new(0, 395, 0, existingGroupboxes * 10)
            GroupFrame.BackgroundColor3 = Window.CurrentTheme.Groupbox
            GroupFrame.BorderSizePixel = 0
            GroupFrame.ScrollBarThickness = 4
            GroupFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
            GroupFrame.Parent = TabContent
            
            table.insert(Window.ThemeableObjects, {Object = GroupFrame, Property = "BackgroundColor3", ThemeKey = "Groupbox"})
            
            local GroupCorner = Instance.new("UICorner")
            GroupCorner.CornerRadius = UDim.new(0, 4)
            GroupCorner.Parent = GroupFrame
            
            local GroupTitle = Instance.new("TextLabel")
            GroupTitle.Size = UDim2.new(1, -20, 0, 30)
            GroupTitle.Position = UDim2.new(0, 10, 0, 5)
            GroupTitle.BackgroundTransparency = 1
            GroupTitle.Text = title
            GroupTitle.TextColor3 = Window.CurrentTheme.Text
            GroupTitle.Font = Enum.Font.GothamBold
            GroupTitle.TextSize = 13
            GroupTitle.TextXAlignment = Enum.TextXAlignment.Left
            GroupTitle.Parent = GroupFrame
            
            table.insert(Window.ThemeableObjects, {Object = GroupTitle, Property = "TextColor3", ThemeKey = "Text"})
            
            Groupbox.Frame = GroupFrame
            Groupbox.Side = side
            table.insert(self.Groupboxes, Groupbox)
            
            -- [CONTINUA COM OS ELEMENTOS - TOGGLE, SLIDER, DROPDOWN...]
            -- Código dos elementos permanece o mesmo
            
            return Groupbox
        end
        
        Window.Tabs[name] = Tab
        return Tab
    end
    
    -- Função para descarregar
    function Window:Unload()
        self.Unloaded = true
        ScreenGui:Destroy()
    end
    
    function Window:OnUnload(func)
        task.spawn(function()
            repeat task.wait() until self.Unloaded
            func()
        end)
    end
    
    return Window
end

return GROSSLib
