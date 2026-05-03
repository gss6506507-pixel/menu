-- GROSSLib - Biblioteca de UI para Roblox
-- Versão: 2.0.0 COMPLETA - Com Temas e Todos os Elementos

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
    
    -- ═══════════════════════════════════════════════════════════
    -- ADICIONAR TAB
    -- ═══════════════════════════════════════════════════════════
    
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
        
        -- ═══════════════════════════════════════════════════════════
        -- CRIAR GROUPBOX
        -- ═══════════════════════════════════════════════════════════
        
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
            
            -- Função helper para atualizar canvas
            local function UpdateCanvasSize()
                GroupFrame.CanvasSize = UDim2.new(0, 0, 0, Groupbox.CurrentY + 10)
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: TOGGLE
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddToggle(id, options)
                options = options or {}
                local Toggle = {
                    Value = options.Default or false,
                    Callback = options.Callback or function() end,
                }
                
                local ToggleFrame = Instance.new("Frame")
                ToggleFrame.Size = UDim2.new(1, -20, 0, 30)
                ToggleFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                ToggleFrame.BackgroundColor3 = Window.CurrentTheme.Element
                ToggleFrame.BorderSizePixel = 0
                ToggleFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = ToggleFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local ToggleCorner = Instance.new("UICorner")
                ToggleCorner.CornerRadius = UDim.new(0, 4)
                ToggleCorner.Parent = ToggleFrame
                
                local ToggleLabel = Instance.new("TextLabel")
                ToggleLabel.Size = UDim2.new(1, -50, 1, 0)
                ToggleLabel.Position = UDim2.new(0, 10, 0, 0)
                ToggleLabel.BackgroundTransparency = 1
                ToggleLabel.Text = options.Text or "Toggle"
                ToggleLabel.TextColor3 = Window.CurrentTheme.Text
                ToggleLabel.Font = Enum.Font.Gotham
                ToggleLabel.TextSize = 12
                ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
                ToggleLabel.Parent = ToggleFrame
                
                table.insert(Window.ThemeableObjects, {Object = ToggleLabel, Property = "TextColor3", ThemeKey = "Text"})
                
                local ToggleButton = Instance.new("TextButton")
                ToggleButton.Size = UDim2.new(0, 40, 0, 20)
                ToggleButton.Position = UDim2.new(1, -50, 0.5, -10)
                ToggleButton.BackgroundColor3 = Toggle.Value and Window.CurrentTheme.Accent or Window.CurrentTheme.Border
                ToggleButton.BorderSizePixel = 0
                ToggleButton.Text = ""
                ToggleButton.Parent = ToggleFrame
                
                local ToggleBtnCorner = Instance.new("UICorner")
                ToggleBtnCorner.CornerRadius = UDim.new(1, 0)
                ToggleBtnCorner.Parent = ToggleButton
                
                local ToggleIndicator = Instance.new("Frame")
                ToggleIndicator.Size = UDim2.new(0, 16, 0, 16)
                ToggleIndicator.Position = Toggle.Value and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
                ToggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
                ToggleIndicator.BorderSizePixel = 0
                ToggleIndicator.Parent = ToggleButton
                
                local IndicatorCorner = Instance.new("UICorner")
                IndicatorCorner.CornerRadius = UDim.new(1, 0)
                IndicatorCorner.Parent = ToggleIndicator
                
                function Toggle:SetValue(value)
                    self.Value = value
                    
                    TweenService:Create(ToggleButton, TweenInfo.new(0.2), {
                        BackgroundColor3 = value and Window.CurrentTheme.Accent or Window.CurrentTheme.Border
                    }):Play()
                    
                    TweenService:Create(ToggleIndicator, TweenInfo.new(0.2), {
                        Position = value and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
                    }):Play()
                    
                    self.Callback(value)
                end
                
                ToggleButton.MouseButton1Click:Connect(function()
                    Toggle:SetValue(not Toggle.Value)
                end)
                
                if id then
                    getgenv().Toggles[id] = Toggle
                end
                
                self.CurrentY = self.CurrentY + 35
                UpdateCanvasSize()
                
                return Toggle
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: SLIDER
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddSlider(id, options)
                options = options or {}
                local Slider = {
                    Value = options.Default or options.Min or 0,
                    Min = options.Min or 0,
                    Max = options.Max or 100,
                    Rounding = options.Rounding or 1,
                    Callback = options.Callback or function() end,
                }
                
                local SliderFrame = Instance.new("Frame")
                SliderFrame.Size = UDim2.new(1, -20, 0, 45)
                SliderFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                SliderFrame.BackgroundColor3 = Window.CurrentTheme.Element
                SliderFrame.BorderSizePixel = 0
                SliderFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = SliderFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local SliderCorner = Instance.new("UICorner")
                SliderCorner.CornerRadius = UDim.new(0, 4)
                SliderCorner.Parent = SliderFrame
                
                local SliderLabel = Instance.new("TextLabel")
                SliderLabel.Size = UDim2.new(1, -20, 0, 20)
                SliderLabel.Position = UDim2.new(0, 10, 0, 5)
                SliderLabel.BackgroundTransparency = 1
                SliderLabel.Text = options.Text or "Slider"
                SliderLabel.TextColor3 = Window.CurrentTheme.Text
                SliderLabel.Font = Enum.Font.Gotham
                SliderLabel.TextSize = 12
                SliderLabel.TextXAlignment = Enum.TextXAlignment.Left
                SliderLabel.Parent = SliderFrame
                
                table.insert(Window.ThemeableObjects, {Object = SliderLabel, Property = "TextColor3", ThemeKey = "Text"})
                
                local SliderValue = Instance.new("TextLabel")
                SliderValue.Size = UDim2.new(0, 50, 0, 20)
                SliderValue.Position = UDim2.new(1, -60, 0, 5)
                SliderValue.BackgroundTransparency = 1
                SliderValue.Text = tostring(Slider.Value)
                SliderValue.TextColor3 = Window.CurrentTheme.SubText
                SliderValue.Font = Enum.Font.GothamBold
                SliderValue.TextSize = 11
                SliderValue.TextXAlignment = Enum.TextXAlignment.Right
                SliderValue.Parent = SliderFrame
                
                table.insert(Window.ThemeableObjects, {Object = SliderValue, Property = "TextColor3", ThemeKey = "SubText"})
                
                local SliderBar = Instance.new("Frame")
                SliderBar.Size = UDim2.new(1, -20, 0, 4)
                SliderBar.Position = UDim2.new(0, 10, 1, -10)
                SliderBar.BackgroundColor3 = Window.CurrentTheme.Border
                SliderBar.BorderSizePixel = 0
                SliderBar.Parent = SliderFrame
                
                table.insert(Window.ThemeableObjects, {Object = SliderBar, Property = "BackgroundColor3", ThemeKey = "Border"})
                
                local SliderBarCorner = Instance.new("UICorner")
                SliderBarCorner.CornerRadius = UDim.new(1, 0)
                SliderBarCorner.Parent = SliderBar
                
                local SliderFill = Instance.new("Frame")
                SliderFill.Size = UDim2.new(0, 0, 1, 0)
                SliderFill.BackgroundColor3 = Window.CurrentTheme.Accent
                SliderFill.BorderSizePixel = 0
                SliderFill.Parent = SliderBar
                
                table.insert(Window.ThemeableObjects, {Object = SliderFill, Property = "BackgroundColor3", ThemeKey = "Accent"})
                
                local SliderFillCorner = Instance.new("UICorner")
                SliderFillCorner.CornerRadius = UDim.new(1, 0)
                SliderFillCorner.Parent = SliderFill
                
                local dragging = false
                
                function Slider:SetValue(value)
                    value = math.clamp(value, self.Min, self.Max)
                    value = math.floor(value / self.Rounding + 0.5) * self.Rounding
                    self.Value = value
                    
                    local percent = (value - self.Min) / (self.Max - self.Min)
                    SliderFill.Size = UDim2.new(percent, 0, 1, 0)
                    SliderValue.Text = tostring(value)
                    
                    self.Callback(value)
                end
                
                SliderBar.InputBegan:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then
                        dragging = true
                    end
                end)
                
                UserInputService.InputEnded:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then
                        dragging = false
                    end
                end)
                
                UserInputService.InputChanged:Connect(function(input)
                    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                        local pos = (input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X
                        pos = math.clamp(pos, 0, 1)
                        local value = Slider.Min + (Slider.Max - Slider.Min) * pos
                        Slider:SetValue(value)
                    end
                end)
                
                Slider:SetValue(Slider.Value)
                
                if id then
                    getgenv().Options[id] = Slider
                end
                
                self.CurrentY = self.CurrentY + 50
                UpdateCanvasSize()
                
                return Slider
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: DROPDOWN
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddDropdown(id, options)
                options = options or {}
                local Dropdown = {
                    Value = options.Default or "",
                    Values = options.Values or {},
                    Multi = options.Multi or false,
                    Callback = options.Callback or function() end,
                    Open = false,
                }
                
                local DropdownFrame = Instance.new("Frame")
                DropdownFrame.Size = UDim2.new(1, -20, 0, 35)
                DropdownFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                DropdownFrame.BackgroundColor3 = Window.CurrentTheme.Element
                DropdownFrame.BorderSizePixel = 0
                DropdownFrame.ClipsDescendants = false
                DropdownFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = DropdownFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local DropdownCorner = Instance.new("UICorner")
                DropdownCorner.CornerRadius = UDim.new(0, 4)
                DropdownCorner.Parent = DropdownFrame
                
                local DropdownButton = Instance.new("TextButton")
                DropdownButton.Size = UDim2.new(1, 0, 0, 35)
                DropdownButton.BackgroundTransparency = 1
                DropdownButton.Text = ""
                DropdownButton.Parent = DropdownFrame
                
                local DropdownLabel = Instance.new("TextLabel")
                DropdownLabel.Size = UDim2.new(1, -40, 1, 0)
                DropdownLabel.Position = UDim2.new(0, 10, 0, 0)
                DropdownLabel.BackgroundTransparency = 1
                DropdownLabel.Text = options.Text or "Dropdown"
                DropdownLabel.TextColor3 = Window.CurrentTheme.Text
                DropdownLabel.Font = Enum.Font.Gotham
                DropdownLabel.TextSize = 12
                DropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
                DropdownLabel.Parent = DropdownFrame
                
                table.insert(Window.ThemeableObjects, {Object = DropdownLabel, Property = "TextColor3", ThemeKey = "Text"})
                
                local DropdownIndicator = Instance.new("TextLabel")
                DropdownIndicator.Size = UDim2.new(0, 20, 1, 0)
                DropdownIndicator.Position = UDim2.new(1, -30, 0, 0)
                DropdownIndicator.BackgroundTransparency = 1
                DropdownIndicator.Text = "▼"
                DropdownIndicator.TextColor3 = Window.CurrentTheme.SubText
                DropdownIndicator.Font = Enum.Font.Gotham
                DropdownIndicator.TextSize = 10
                DropdownIndicator.Parent = DropdownFrame
                
                table.insert(Window.ThemeableObjects, {Object = DropdownIndicator, Property = "TextColor3", ThemeKey = "SubText"})
                
                local DropdownList = Instance.new("Frame")
                DropdownList.Size = UDim2.new(1, 0, 0, 0)
                DropdownList.Position = UDim2.new(0, 0, 0, 35)
                DropdownList.BackgroundColor3 = Window.CurrentTheme.Groupbox
                DropdownList.BorderSizePixel = 0
                DropdownList.Visible = false
                DropdownList.ClipsDescendants = true
                DropdownList.ZIndex = 10
                DropdownList.Parent = DropdownFrame
                
                table.insert(Window.ThemeableObjects, {Object = DropdownList, Property = "BackgroundColor3", ThemeKey = "Groupbox"})
                
                local ListCorner = Instance.new("UICorner")
                ListCorner.CornerRadius = UDim.new(0, 4)
                ListCorner.Parent = DropdownList
                
                local ListLayout = Instance.new("UIListLayout")
                ListLayout.Padding = UDim.new(0, 2)
                ListLayout.Parent = DropdownList
                
                function Dropdown:Refresh(values)
                    self.Values = values or self.Values
                    
                    for _, child in pairs(DropdownList:GetChildren()) do
                        if child:IsA("TextButton") then
                            child:Destroy()
                        end
                    end
                    
                    for _, value in pairs(self.Values) do
                        local Option = Instance.new("TextButton")
                        Option.Size = UDim2.new(1, -4, 0, 25)
                        Option.BackgroundColor3 = Window.CurrentTheme.Element
                        Option.BorderSizePixel = 0
                        Option.Text = value
                        Option.TextColor3 = Window.CurrentTheme.Text
                        Option.Font = Enum.Font.Gotham
                        Option.TextSize = 11
                        Option.Parent = DropdownList
                        
                        table.insert(Window.ThemeableObjects, {Object = Option, Property = "BackgroundColor3", ThemeKey = "Element"})
                        table.insert(Window.ThemeableObjects, {Object = Option, Property = "TextColor3", ThemeKey = "Text"})
                        
                        local OptionCorner = Instance.new("UICorner")
                        OptionCorner.CornerRadius = UDim.new(0, 3)
                        OptionCorner.Parent = Option
                        
                        Option.MouseButton1Click:Connect(function()
                            Dropdown:SetValue(value)
                            Dropdown.Open = false
                            DropdownList.Visible = false
                            TweenService:Create(DropdownList, TweenInfo.new(0.2), {Size = UDim2.new(1, 0, 0, 0)}):Play()
                        end)
                    end
                    
                    local listHeight = #self.Values * 27
                    DropdownList.Size = UDim2.new(1, 0, 0, Dropdown.Open and listHeight or 0)
                end
                
                function Dropdown:SetValue(value)
                    self.Value = value
                    DropdownLabel.Text = (options.Text or "Dropdown") .. ": " .. tostring(value)
                    self.Callback(value)
                end
                
                DropdownButton.MouseButton1Click:Connect(function()
                    Dropdown.Open = not Dropdown.Open
                    DropdownList.Visible = Dropdown.Open
                    
                    local listHeight = #Dropdown.Values * 27
                    TweenService:Create(DropdownList, TweenInfo.new(0.2), {
                        Size = UDim2.new(1, 0, 0, Dropdown.Open and listHeight or 0)
                    }):Play()
                    
                    if Dropdown.Open then
                        DropdownFrame.Size = UDim2.new(1, -20, 0, 35 + listHeight)
                    else
                        DropdownFrame.Size = UDim2.new(1, -20, 0, 35)
                    end
                end)
                
                Dropdown:Refresh()
                
                if id then
                    getgenv().Options[id] = Dropdown
                end
                
                self.CurrentY = self.CurrentY + 40
                UpdateCanvasSize()
                
                return Dropdown
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: BUTTON
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddButton(options)
                options = options or {}
                
                local ButtonFrame = Instance.new("TextButton")
                ButtonFrame.Size = UDim2.new(1, -20, 0, 32)
                ButtonFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                ButtonFrame.BackgroundColor3 = Window.CurrentTheme.Accent
                ButtonFrame.BorderSizePixel = 0
                ButtonFrame.Text = options.Text or "Button"
                ButtonFrame.TextColor3 = Color3.fromRGB(255, 255, 255)
                ButtonFrame.Font = Enum.Font.GothamBold
                ButtonFrame.TextSize = 12
                ButtonFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = ButtonFrame, Property = "BackgroundColor3", ThemeKey = "Accent"})
                
                local ButtonCorner = Instance.new("UICorner")
                ButtonCorner.CornerRadius = UDim.new(0, 4)
                ButtonCorner.Parent = ButtonFrame
                
                ButtonFrame.MouseButton1Click:Connect(function()
                    if options.Callback then
                        options.Callback()
                    end
                end)
                
                self.CurrentY = self.CurrentY + 37
                UpdateCanvasSize()
                
                return ButtonFrame
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: INPUT (TEXTBOX)
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddInput(id, options)
                options = options or {}
                local Input = {
                    Value = options.Default or "",
                    Callback = options.Callback or function() end,
                }
                
                local InputFrame = Instance.new("Frame")
                InputFrame.Size = UDim2.new(1, -20, 0, 35)
                InputFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                InputFrame.BackgroundColor3 = Window.CurrentTheme.Element
                InputFrame.BorderSizePixel = 0
                InputFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = InputFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local InputCorner = Instance.new("UICorner")
                InputCorner.CornerRadius = UDim.new(0, 4)
                InputCorner.Parent = InputFrame
                
                local InputBox = Instance.new("TextBox")
                InputBox.Size = UDim2.new(1, -20, 1, 0)
                InputBox.Position = UDim2.new(0, 10, 0, 0)
                InputBox.BackgroundTransparency = 1
                InputBox.PlaceholderText = options.Text or "Enter text..."
                InputBox.Text = Input.Value
                InputBox.TextColor3 = Window.CurrentTheme.Text
                InputBox.PlaceholderColor3 = Window.CurrentTheme.SubText
                InputBox.Font = Enum.Font.Gotham
                InputBox.TextSize = 12
                InputBox.TextXAlignment = Enum.TextXAlignment.Left
                InputBox.ClearTextOnFocus = false
                InputBox.Parent = InputFrame
                
                table.insert(Window.ThemeableObjects, {Object = InputBox, Property = "TextColor3", ThemeKey = "Text"})
                table.insert(Window.ThemeableObjects, {Object = InputBox, Property = "PlaceholderColor3", ThemeKey = "SubText"})
                
                function Input:SetValue(value)
                    self.Value = value
                    InputBox.Text = value
                    self.Callback(value)
                end
                
                InputBox.FocusLost:Connect(function()
                    Input:SetValue(InputBox.Text)
                end)
                
                if id then
                    getgenv().Options[id] = Input
                end
                
                self.CurrentY = self.CurrentY + 40
                UpdateCanvasSize()
                
                return Input
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: LABEL
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddLabel(text)
                local LabelFrame = Instance.new("TextLabel")
                LabelFrame.Size = UDim2.new(1, -20, 0, 25)
                LabelFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                LabelFrame.BackgroundTransparency = 1
                LabelFrame.Text = text or "Label"
                LabelFrame.TextColor3 = Window.CurrentTheme.SubText
                LabelFrame.Font = Enum.Font.Gotham
                LabelFrame.TextSize = 11
                LabelFrame.TextXAlignment = Enum.TextXAlignment.Left
                LabelFrame.TextWrapped = true
                LabelFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = LabelFrame, Property = "TextColor3", ThemeKey = "SubText"})
                
                self.CurrentY = self.CurrentY + 28
                UpdateCanvasSize()
                
                return {
                    SetText = function(_, newText)
                        LabelFrame.Text = newText
                    end
                }
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: DIVIDER
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddDivider()
                local Divider = Instance.new("Frame")
                Divider.Size = UDim2.new(1, -20, 0, 1)
                Divider.Position = UDim2.new(0, 10, 0, self.CurrentY)
                Divider.BackgroundColor3 = Window.CurrentTheme.Border
                Divider.BorderSizePixel = 0
                Divider.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = Divider, Property = "BackgroundColor3", ThemeKey = "Border"})
                
                self.CurrentY = self.CurrentY + 10
                UpdateCanvasSize()
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: KEYBIND
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddKeyPicker(id, options)
                options = options or {}
                local KeyPicker = {
                    Value = options.Default or Enum.KeyCode.E,
                    Mode = options.Mode or "Toggle",
                    Callback = options.Callback or function() end,
                    Listening = false,
                }
                
                local KeyFrame = Instance.new("Frame")
                KeyFrame.Size = UDim2.new(1, -20, 0, 30)
                KeyFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                KeyFrame.BackgroundColor3 = Window.CurrentTheme.Element
                KeyFrame.BorderSizePixel = 0
                KeyFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = KeyFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local KeyCorner = Instance.new("UICorner")
                KeyCorner.CornerRadius = UDim.new(0, 4)
                KeyCorner.Parent = KeyFrame
                
                local KeyLabel = Instance.new("TextLabel")
                KeyLabel.Size = UDim2.new(1, -90, 1, 0)
                KeyLabel.Position = UDim2.new(0, 10, 0, 0)
                KeyLabel.BackgroundTransparency = 1
                KeyLabel.Text = options.Text or "Keybind"
                KeyLabel.TextColor3 = Window.CurrentTheme.Text
                KeyLabel.Font = Enum.Font.Gotham
                KeyLabel.TextSize = 12
                KeyLabel.TextXAlignment = Enum.TextXAlignment.Left
                KeyLabel.Parent = KeyFrame
                
                table.insert(Window.ThemeableObjects, {Object = KeyLabel, Property = "TextColor3", ThemeKey = "Text"})
                
                local KeyButton = Instance.new("TextButton")
                KeyButton.Size = UDim2.new(0, 70, 0, 22)
                KeyButton.Position = UDim2.new(1, -80, 0.5, -11)
                KeyButton.BackgroundColor3 = Window.CurrentTheme.Border
                KeyButton.BorderSizePixel = 0
                KeyButton.Text = KeyPicker.Value.Name
                KeyButton.TextColor3 = Window.CurrentTheme.Text
                KeyButton.Font = Enum.Font.GothamBold
                KeyButton.TextSize = 10
                KeyButton.Parent = KeyFrame
                
                table.insert(Window.ThemeableObjects, {Object = KeyButton, Property = "BackgroundColor3", ThemeKey = "Border"})
                table.insert(Window.ThemeableObjects, {Object = KeyButton, Property = "TextColor3", ThemeKey = "Text"})
                
                local KeyBtnCorner = Instance.new("UICorner")
                KeyBtnCorner.CornerRadius = UDim.new(0, 3)
                KeyBtnCorner.Parent = KeyButton
                
                function KeyPicker:SetValue(key)
                    self.Value = key
                    KeyButton.Text = key.Name
                end
                
                KeyButton.MouseButton1Click:Connect(function()
                    KeyPicker.Listening = true
                    KeyButton.Text = "..."
                end)
                
                UserInputService.InputBegan:Connect(function(input, gpe)
                    if KeyPicker.Listening and not gpe then
                        if input.UserInputType == Enum.UserInputType.Keyboard then
                            KeyPicker:SetValue(input.KeyCode)
                            KeyPicker.Listening = false
                        end
                    end
                    
                    if not gpe and input.KeyCode == KeyPicker.Value then
                        KeyPicker.Callback(input.KeyCode)
                    end
                end)
                
                if id then
                    getgenv().Options[id] = KeyPicker
                end
                
                self.CurrentY = self.CurrentY + 35
                UpdateCanvasSize()
                
                return KeyPicker
            end
            
            -- ═══════════════════════════════════════════════════════════
            -- ELEMENTO: COLOR PICKER
            -- ═══════════════════════════════════════════════════════════
            
            function Groupbox:AddColorPicker(id, options)
                options = options or {}
                local ColorPicker = {
                    Value = options.Default or Color3.fromRGB(255, 255, 255),
                    Callback = options.Callback or function() end,
                }
                
                local ColorFrame = Instance.new("Frame")
                ColorFrame.Size = UDim2.new(1, -20, 0, 30)
                ColorFrame.Position = UDim2.new(0, 10, 0, self.CurrentY)
                ColorFrame.BackgroundColor3 = Window.CurrentTheme.Element
                ColorFrame.BorderSizePixel = 0
                ColorFrame.Parent = GroupFrame
                
                table.insert(Window.ThemeableObjects, {Object = ColorFrame, Property = "BackgroundColor3", ThemeKey = "Element"})
                
                local ColorCorner = Instance.new("UICorner")
                ColorCorner.CornerRadius = UDim.new(0, 4)
                ColorCorner.Parent = ColorFrame
                
                local ColorLabel = Instance.new("TextLabel")
                ColorLabel.Size = UDim2.new(1, -60, 1, 0)
                ColorLabel.Position = UDim2.new(0, 10, 0, 0)
                ColorLabel.BackgroundTransparency = 1
                ColorLabel.Text = options.Text or "Color"
                ColorLabel.TextColor3 = Window.CurrentTheme.Text
                ColorLabel.Font = Enum.Font.Gotham
                ColorLabel.TextSize = 12
                ColorLabel.TextXAlignment = Enum.TextXAlignment.Left
                ColorLabel.Parent = ColorFrame
                
                table.insert(Window.ThemeableObjects, {Object = ColorLabel, Property = "TextColor3", ThemeKey = "Text"})
                
                local ColorDisplay = Instance.new("Frame")
                ColorDisplay.Size = UDim2.new(0, 40, 0, 20)
                ColorDisplay.Position = UDim2.new(1, -50, 0.5, -10)
                ColorDisplay.BackgroundColor3 = ColorPicker.Value
                ColorDisplay.BorderSizePixel = 0
                ColorDisplay.Parent = ColorFrame
                
                local ColorDisplayCorner = Instance.new("UICorner")
                ColorDisplayCorner.CornerRadius = UDim.new(0, 3)
                ColorDisplayCorner.Parent = ColorDisplay
                
                function ColorPicker:SetValue(color)
                    self.Value = color
                    ColorDisplay.BackgroundColor3 = color
                    self.Callback(color)
                end
                
                -- Exemplo básico de color picker (você pode expandir isso)
                local ColorButton = Instance.new("TextButton")
                ColorButton.Size = UDim2.new(1, 0, 1, 0)
                ColorButton.BackgroundTransparency = 1
                ColorButton.Text = ""
                ColorButton.Parent = ColorFrame
                
                ColorButton.MouseButton1Click:Connect(function()
                    -- Aqui você pode adicionar um sistema de color picker mais complexo
                    local randomColor = Color3.fromRGB(
                        math.random(0, 255),
                        math.random(0, 255),
                        math.random(0, 255)
                    )
                    ColorPicker:SetValue(randomColor)
                end)
                
                if id then
                    getgenv().Options[id] = ColorPicker
                end
                
                self.CurrentY = self.CurrentY + 35
                UpdateCanvasSize()
                
                return ColorPicker
            end
            
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
