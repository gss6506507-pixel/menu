-- GROSSLib - Biblioteca de UI para Roblox
-- Versão: 1.0.0

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

-- Função principal para criar janela
function GROSSLib:CreateWindow(Config)
    Config = Config or {}
    
    local Window = {
        Tabs = {},
        Unloaded = false,
        ToggleKeybind = nil,
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
    MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    MainFrame.BorderSizePixel = 0
    MainFrame.Visible = Config.AutoShow ~= false
    MainFrame.Parent = ScreenGui
    
    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 6)
    MainCorner.Parent = MainFrame
    
    -- Header
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 40)
    Header.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
    Header.BorderSizePixel = 0
    Header.Parent = MainFrame
    
    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 6)
    HeaderCorner.Parent = Header
    
    local HeaderBottomCover = Instance.new("Frame")
    HeaderBottomCover.Size = UDim2.new(1, 0, 0, 6)
    HeaderBottomCover.Position = UDim2.new(0, 0, 1, -6)
    HeaderBottomCover.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
    HeaderBottomCover.BorderSizePixel = 0
    HeaderBottomCover.Parent = Header
    
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
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 14
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    -- Botões de controle
    local ControlButtons = Instance.new("Frame")
    ControlButtons.Size = UDim2.new(0, 80, 0, 24)
    ControlButtons.Position = UDim2.new(1, -90, 0.5, -12)
    ControlButtons.BackgroundTransparency = 1
    ControlButtons.Parent = Header
    
    -- Botão Minimizar
    local MinimizeBtn = Instance.new("TextButton")
    MinimizeBtn.Size = UDim2.new(0, 24, 0, 24)
    MinimizeBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    MinimizeBtn.BorderSizePixel = 0
    MinimizeBtn.Text = "—"
    MinimizeBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    MinimizeBtn.Font = Enum.Font.GothamBold
    MinimizeBtn.TextSize = 14
    MinimizeBtn.Parent = ControlButtons
    
    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 4)
    MinCorner.Parent = MinimizeBtn
    
    -- Botão Fechar
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Size = UDim2.new(0, 24, 0, 24)
    CloseBtn.Position = UDim2.new(0, 56, 0, 0)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    CloseBtn.BorderSizePixel = 0
    CloseBtn.Text = "×"
    CloseBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.TextSize = 18
    CloseBtn.Parent = ControlButtons
    
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
    
    -- Restaurar com keybind
    UserInputService.InputBegan:Connect(function(input, gpe)
        if Window.ToggleKeybind and input.KeyCode == Window.ToggleKeybind.Value then
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
                TextColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(120, 120, 120)
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
        Button.TextColor3 = Color3.fromRGB(120, 120, 120)
        Button.Font = Enum.Font.GothamBold
        Button.TextSize = 12
        Button.Parent = TabButtons
        
        local Underline = Instance.new("Frame")
        Underline.Name = "Underline"
        Underline.Size = UDim2.new(1, -20, 0, 2)
        Underline.Position = UDim2.new(0, 10, 1, -2)
        Underline.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
        Underline.BorderSizePixel = 0
        Underline.Visible = false
        Underline.Parent = Button
        
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
        
        -- Função para adicionar Groupbox (lado esquerdo)
        function Tab:AddLeftGroupbox(title)
            return self:CreateGroupbox(title, "Left")
        end
        
        -- Função para adicionar Groupbox (lado direito)
        function Tab:AddRightGroupbox(title)
            return self:CreateGroupbox(title, "Right")
        end
        
        -- Função para criar Groupbox
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
            GroupFrame.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
            GroupFrame.BorderSizePixel = 0
            GroupFrame.ScrollBarThickness = 4
            GroupFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
            GroupFrame.Parent = TabContent
            
            local GroupCorner = Instance.new("UICorner")
            GroupCorner.CornerRadius = UDim.new(0, 4)
            GroupCorner.Parent = GroupFrame
            
            local GroupTitle = Instance.new("TextLabel")
            GroupTitle.Size = UDim2.new(1, -20, 0, 30)
            GroupTitle.Position = UDim2.new(0, 10, 0, 5)
            GroupTitle.BackgroundTransparency = 1
            GroupTitle.Text = title
            GroupTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
            GroupTitle.Font = Enum.Font.GothamBold
            GroupTitle.TextSize = 13
            GroupTitle.TextXAlignment = Enum.TextXAlignment.Left
            GroupTitle.Parent = GroupFrame
            
            Groupbox.Frame = GroupFrame
            Groupbox.Side = side
            table.insert(self.Groupboxes, Groupbox)
            
            -- Função para adicionar Toggle
            function Groupbox:AddToggle(idx, options)
                options = options or {}
                local posY = self.CurrentY
                
                local Toggle = Instance.new("Frame")
                Toggle.Size = UDim2.new(1, -30, 0, 25)
                Toggle.Position = UDim2.new(0, 15, 0, posY)
                Toggle.BackgroundTransparency = 1
                Toggle.Parent = self.Frame
                
                local Label = Instance.new("TextLabel")
                Label.Size = UDim2.new(1, -60, 1, 0)
                Label.BackgroundTransparency = 1
                Label.Text = options.Text or idx
                Label.TextColor3 = Color3.fromRGB(200, 200, 200)
                Label.Font = Enum.Font.Gotham
                Label.TextSize = 12
                Label.TextXAlignment = Enum.TextXAlignment.Left
                Label.Parent = Toggle
                
                local SwitchContainer = Instance.new("Frame")
                SwitchContainer.Size = UDim2.new(0, 40, 0, 20)
                SwitchContainer.Position = UDim2.new(1, -40, 0.5, -10)
                SwitchContainer.BackgroundColor3 = options.Default and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
                SwitchContainer.BorderSizePixel = 0
                SwitchContainer.Parent = Toggle
                
                local SwitchCorner = Instance.new("UICorner")
                SwitchCorner.CornerRadius = UDim.new(1, 0)
                SwitchCorner.Parent = SwitchContainer
                
                local SwitchKnob = Instance.new("Frame")
                SwitchKnob.Size = UDim2.new(0, 16, 0, 16)
                SwitchKnob.Position = options.Default and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
                SwitchKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
                SwitchKnob.BorderSizePixel = 0
                SwitchKnob.Parent = SwitchContainer
                
                local KnobCorner = Instance.new("UICorner")
                KnobCorner.CornerRadius = UDim.new(1, 0)
                KnobCorner.Parent = SwitchKnob
                
                local Button = Instance.new("TextButton")
                Button.Size = UDim2.new(1, 0, 1, 0)
                Button.BackgroundTransparency = 1
                Button.Text = ""
                Button.Parent = Toggle
                
                local ToggleObj = {
                    Value = options.Default or false,
                    OnChanged = function() end,
                }
                
                function ToggleObj:SetValue(value)
                    self.Value = value
                    
                    TweenService:Create(SwitchContainer, TweenInfo.new(0.2), {
                        BackgroundColor3 = value and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
                    }):Play()
                    
                    TweenService:Create(SwitchKnob, TweenInfo.new(0.2), {
                        Position = value and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
                    }):Play()
                    
                    if options.Callback then
                        options.Callback(value)
                    end
                    self:OnChanged()
                end
                
                function ToggleObj:OnChanged(func)
                    if func then
                        self.OnChanged = func
                    else
                        self.OnChanged()
                    end
                    return self
                end
                
                Button.MouseButton1Click:Connect(function()
                    ToggleObj:SetValue(not ToggleObj.Value)
                end)
                
                getgenv().Toggles[idx] = ToggleObj
                self.CurrentY = self.CurrentY + 30
                self.Frame.CanvasSize = UDim2.new(0, 0, 0, self.CurrentY + 20)
                
                return ToggleObj
            end
            
            -- Função para adicionar Slider
            function Groupbox:AddSlider(idx, options)
                options = options or {}
                local posY = self.CurrentY
                
                local Slider = Instance.new("Frame")
                Slider.Size = UDim2.new(1, -30, 0, 50)
                Slider.Position = UDim2.new(0, 15, 0, posY)
                Slider.BackgroundTransparency = 1
                Slider.Parent = self.Frame
                
                local Label = Instance.new("TextLabel")
                Label.Size = UDim2.new(1, -80, 0, 20)
                Label.BackgroundTransparency = 1
                Label.Text = options.Text or idx
                Label.TextColor3 = Color3.fromRGB(200, 200, 200)
                Label.Font = Enum.Font.Gotham
                Label.TextSize = 12
                Label.TextXAlignment = Enum.TextXAlignment.Left
                Label.Parent = Slider
                
                local ValueLabel = Instance.new("TextLabel")
                ValueLabel.Size = UDim2.new(0, 80, 0, 20)
                ValueLabel.Position = UDim2.new(1, -80, 0, 0)
                ValueLabel.BackgroundTransparency = 1
                ValueLabel.Text = string.format("%.1f", options.Default or 0)
                ValueLabel.TextColor3 = Color3.fromRGB(255, 80, 120)
                ValueLabel.Font = Enum.Font.GothamBold
                ValueLabel.TextSize = 12
                ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
                ValueLabel.Parent = Slider
                
                local SliderBar = Instance.new("Frame")
                SliderBar.Size = UDim2.new(1, 0, 0, 6)
                SliderBar.Position = UDim2.new(0, 0, 0, 30)
                SliderBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
                SliderBar.BorderSizePixel = 0
                SliderBar.Parent = Slider
                
                local BarCorner = Instance.new("UICorner")
                BarCorner.CornerRadius = UDim.new(0, 3)
                BarCorner.Parent = SliderBar
                
                local Fill = Instance.new("Frame")
                Fill.Size = UDim2.new(((options.Default or 0) - options.Min) / (options.Max - options.Min), 0, 1, 0)
                Fill.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
                Fill.BorderSizePixel = 0
                Fill.Parent = SliderBar
                
                local FillCorner = Instance.new("UICorner")
                FillCorner.CornerRadius = UDim.new(0, 3)
                FillCorner.Parent = Fill
                
                local SliderObj = {
                    Value = options.Default or options.Min,
                    Min = options.Min,
                    Max = options.Max,
                    Rounding = options.Rounding or 1,
                    OnChanged = function() end,
                }
                
                local draggingSlider = false
                
                function SliderObj:SetValue(value)
                    self.Value = math.clamp(value, self.Min, self.Max)
                    local percent = (self.Value - self.Min) / (self.Max - self.Min)
                    
                    TweenService:Create(Fill, TweenInfo.new(0.1), {
                        Size = UDim2.new(percent, 0, 1, 0)
                    }):Play()
                    
                    ValueLabel.Text = string.format("%." .. self.Rounding .. "f", self.Value)
                    
                    if options.Callback then
                        options.Callback(self.Value)
                    end
                    self:OnChanged()
                end
                
                function SliderObj:OnChanged(func)
                    if func then
                        self.OnChanged = func
                    else
                        self.OnChanged()
                    end
                    return self
                end
                
                SliderBar.InputBegan:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then
                        draggingSlider = true
                        local mouse = game:GetService("Players").LocalPlayer:GetMouse()
                        local percent = math.clamp((mouse.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
                        SliderObj:SetValue(SliderObj.Min + (SliderObj.Max - SliderObj.Min) * percent)
                    end
                end)
                
                SliderBar.InputEnded:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then
                        draggingSlider = false
                    end
                end)
                
                UserInputService.InputChanged:Connect(function(input)
                    if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
                        local mouse = game:GetService("Players").LocalPlayer:GetMouse()
                        local percent = math.clamp((mouse.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
                        SliderObj:SetValue(SliderObj.Min + (SliderObj.Max - SliderObj.Min) * percent)
                    end
                end)
                
                getgenv().Options[idx] = SliderObj
                self.CurrentY = self.CurrentY + 60
                self.Frame.CanvasSize = UDim2.new(0, 0, 0, self.CurrentY + 20)
                
                return SliderObj
            end
            
            -- Função para adicionar Dropdown
            function Groupbox:AddDropdown(idx, options)
                options = options or {}
                local posY = self.CurrentY
                
                local Dropdown = Instance.new("Frame")
                Dropdown.Size = UDim2.new(1, -30, 0, 48)
                Dropdown.Position = UDim2.new(0, 15, 0, posY)
                Dropdown.BackgroundTransparency = 1
                Dropdown.Parent = self.Frame
                
                local Label = Instance.new("TextLabel")
                Label.Size = UDim2.new(1, 0, 0, 18)
                Label.BackgroundTransparency = 1
                Label.Text = options.Text or idx
                Label.TextColor3 = Color3.fromRGB(200, 200, 200)
                Label.Font = Enum.Font.Gotham
                Label.TextSize = 12
                Label.TextXAlignment = Enum.TextXAlignment.Left
                Label.Parent = Dropdown
                
                local SelectorContainer = Instance.new("Frame")
                SelectorContainer.Size = UDim2.new(1, 0, 0, 26)
                SelectorContainer.Position = UDim2.new(0, 0, 0, 22)
                SelectorContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
                SelectorContainer.BorderSizePixel = 0
                SelectorContainer.Parent = Dropdown
                
                local SelectorCorner = Instance.new("UICorner")
                SelectorCorner.CornerRadius = UDim.new(0, 4)
                SelectorCorner.Parent = SelectorContainer
                
                local ValueLabel = Instance.new("TextLabel")
                ValueLabel.Size = UDim2.new(1, -30, 1, 0)
                ValueLabel.Position = UDim2.new(0, 8, 0, 0)
                ValueLabel.BackgroundTransparency = 1
                ValueLabel.Text = options.Default or options.Values[1]
                ValueLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
                ValueLabel.Font = Enum.Font.Gotham
                ValueLabel.TextSize = 11
                ValueLabel.TextXAlignment = Enum.TextXAlignment.Left
                ValueLabel.Parent = SelectorContainer
                
                local ArrowButton = Instance.new("TextButton")
                ArrowButton.Size = UDim2.new(0, 26, 1, 0)
                ArrowButton.Position = UDim2.new(1, -26, 0, 0)
                ArrowButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
                ArrowButton.BorderSizePixel = 0
                ArrowButton.Text = ">"
                ArrowButton.TextColor3 = Color3.fromRGB(255, 80, 120)
                ArrowButton.Font = Enum.Font.GothamBold
                ArrowButton.TextSize = 12
                ArrowButton.Parent = SelectorContainer
                
                local ArrowCorner = Instance.new("UICorner")
                ArrowCorner.CornerRadius = UDim.new(0, 4)
                ArrowCorner.Parent = ArrowButton
                
                local DropdownObj = {
                    Value = options.Default or options.Values[1],
                    Values = options.Values,
                    Index = 1,
                    OnChanged = function() end,
                }
                
                for i, v in ipairs(options.Values) do
                    if v == DropdownObj.Value then
                        DropdownObj.Index = i
                        break
                    end
                end
                
                function DropdownObj:SetValue(value)
                    for i, v in ipairs(self.Values) do
                        if v == value then
                            self.Index = i
                            self.Value = value
                            ValueLabel.Text = value
                            
                            if options.Callback then
                                options.Callback(value)
                            end
                            self:OnChanged()
                            break
                        end
                    end
                end
                
                function DropdownObj:OnChanged(func)
                    if func then
                        self.OnChanged = func
                    else
                        self.OnChanged()
                    end
                    return self
                end
                
                ArrowButton.MouseButton1Click:Connect(function()
                    DropdownObj.Index = DropdownObj.Index % #DropdownObj.Values + 1
                    DropdownObj:SetValue(DropdownObj.Values[DropdownObj.Index])
                end)
                
                getgenv().Options[idx] = DropdownObj
                self.CurrentY = self.CurrentY + 58
                self.Frame.CanvasSize = UDim2.new(0, 0, 0, self.CurrentY + 20)
                
                return DropdownObj
            end
            
            -- Adicionar mais funções conforme necessário (AddButton, AddLabel, etc.)
            
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
    
    -- Função OnUnload
    function Window:OnUnload(func)
        task.spawn(function()
            repeat task.wait() until self.Unloaded
            func()
        end)
    end
    
    -- Watermark (placeholder)
    function Window:SetWatermark(text)
        -- Implementar watermark se necessário
    end
    
    function Window:SetWatermarkVisibility(visible)
        -- Implementar visibilidade do watermark
    end
    
    return Window
end

return GROSSLib
