-- Menu GUI System - Roblox (VERSÃO MELHORADA)
-- Interface completa com abas funcionais, drag suave e sliders corrigidos

local Library = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalMenu"
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
MainFrame.Size = UDim2.new(0, 800, 0, 470)
MainFrame.Position = UDim2.new(0.5, -400, 0.5, -235)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
MainFrame.BorderSizePixel = 0
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
Logo.Name = "Logo"
Logo.Size = UDim2.new(0, 24, 0, 24)
Logo.Position = UDim2.new(0, 12, 0.5, -12)
Logo.BackgroundTransparency = 1
Logo.Image = "rbxassetid://7733993369"
Logo.ImageColor3 = Color3.fromRGB(255, 255, 255)
Logo.Parent = Header

-- Título
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(0, 200, 1, 0)
Title.Position = UDim2.new(0, 42, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Universal"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Header

local BetaLabel = Instance.new("TextLabel")
BetaLabel.Name = "BetaLabel"
BetaLabel.Size = UDim2.new(0, 100, 1, 0)
BetaLabel.Position = UDim2.new(0, 105, 0, 0)
BetaLabel.BackgroundTransparency = 1
BetaLabel.Text = "(Public Beta)"
BetaLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
BetaLabel.Font = Enum.Font.Gotham
BetaLabel.TextSize = 12
BetaLabel.TextXAlignment = Enum.TextXAlignment.Left
BetaLabel.Parent = Header

-- Botões de Controle (Minimizar, Expandir, Fechar)
local ControlButtons = Instance.new("Frame")
ControlButtons.Name = "ControlButtons"
ControlButtons.Size = UDim2.new(0, 80, 0, 24)
ControlButtons.Position = UDim2.new(1, -90, 0.5, -12)
ControlButtons.BackgroundTransparency = 1
ControlButtons.Parent = Header

-- Botão Minimizar
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Name = "Minimize"
MinimizeBtn.Size = UDim2.new(0, 24, 0, 24)
MinimizeBtn.Position = UDim2.new(0, 0, 0, 0)
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

-- Variável para controlar estado minimizado
local minimized = false

-- Funcionalidade de Minimizar
MinimizeBtn.MouseButton1Click:Connect(function()
    minimized = true
    MainFrame.Visible = false
    
    -- Animar botão
    TweenService:Create(MinimizeBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    }):Play()
    wait(0.1)
    TweenService:Create(MinimizeBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    }):Play()
end)

-- Botão Expandir
local ExpandBtn = Instance.new("TextButton")
ExpandBtn.Name = "Expand"
ExpandBtn.Size = UDim2.new(0, 24, 0, 24)
ExpandBtn.Position = UDim2.new(0, 28, 0, 0)
ExpandBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ExpandBtn.BorderSizePixel = 0
ExpandBtn.Text = "□"
ExpandBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
ExpandBtn.Font = Enum.Font.GothamBold
ExpandBtn.TextSize = 14
ExpandBtn.Parent = ControlButtons

local ExpCorner = Instance.new("UICorner")
ExpCorner.CornerRadius = UDim.new(0, 4)
ExpCorner.Parent = ExpandBtn

-- Variável para controlar estado expandido
local expanded = false
local originalSize = MainFrame.Size
local originalPosition = MainFrame.Position

-- Funcionalidade de Expandir/Restaurar
ExpandBtn.MouseButton1Click:Connect(function()
    expanded = not expanded
    
    -- Animar botão
    TweenService:Create(ExpandBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    }):Play()
    
    if expanded then
        -- Expandir para tela cheia
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
            Size = UDim2.new(1, -20, 1, -20),
            Position = UDim2.new(0, 10, 0, 10)
        }):Play()
        ExpandBtn.Text = "❐"
    else
        -- Restaurar tamanho original
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
            Size = originalSize,
            Position = originalPosition
        }):Play()
        ExpandBtn.Text = "□"
    end
    
    wait(0.1)
    TweenService:Create(ExpandBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    }):Play()
end)

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Name = "Close"
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
TabButtons.Name = "TabButtons"
TabButtons.Size = UDim2.new(0, 400, 1, 0)
TabButtons.Position = UDim2.new(0.5, -200, 0, 0)
TabButtons.BackgroundTransparency = 1
TabButtons.Parent = Header

-- Modal de Confirmação de Fechamento
local CloseModal = Instance.new("Frame")
CloseModal.Name = "CloseModal"
CloseModal.Size = UDim2.new(0, 360, 0, 140)
CloseModal.Position = UDim2.new(0.5, -180, 0.5, -70)
CloseModal.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
CloseModal.BorderSizePixel = 0
CloseModal.Visible = false
CloseModal.ZIndex = 10
CloseModal.Parent = ScreenGui

local ModalCorner = Instance.new("UICorner")
ModalCorner.CornerRadius = UDim.new(0, 8)
ModalCorner.Parent = CloseModal

local ModalTitle = Instance.new("TextLabel")
ModalTitle.Size = UDim2.new(1, -40, 0, 40)
ModalTitle.Position = UDim2.new(0, 20, 0, 15)
ModalTitle.BackgroundTransparency = 1
ModalTitle.Text = "Close"
ModalTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
ModalTitle.Font = Enum.Font.GothamBold
ModalTitle.TextSize = 16
ModalTitle.TextXAlignment = Enum.TextXAlignment.Left
ModalTitle.ZIndex = 11
ModalTitle.Parent = CloseModal

local ModalMessage = Instance.new("TextLabel")
ModalMessage.Size = UDim2.new(1, -40, 0, 30)
ModalMessage.Position = UDim2.new(0, 20, 0, 45)
ModalMessage.BackgroundTransparency = 1
ModalMessage.Text = "Are you sure you want to unload the interface?"
ModalMessage.TextColor3 = Color3.fromRGB(180, 180, 180)
ModalMessage.Font = Enum.Font.Gotham
ModalMessage.TextSize = 13
ModalMessage.TextXAlignment = Enum.TextXAlignment.Left
ModalMessage.TextWrapped = true
ModalMessage.ZIndex = 11
ModalMessage.Parent = CloseModal

-- Botão Yes
local YesButton = Instance.new("TextButton")
YesButton.Size = UDim2.new(0, 160, 0, 35)
YesButton.Position = UDim2.new(0, 20, 1, -50)
YesButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
YesButton.BorderSizePixel = 0
YesButton.Text = "Yes"
YesButton.TextColor3 = Color3.fromRGB(220, 220, 220)
YesButton.Font = Enum.Font.GothamBold
YesButton.TextSize = 13
YesButton.ZIndex = 11
YesButton.Parent = CloseModal

local YesCorner = Instance.new("UICorner")
YesCorner.CornerRadius = UDim.new(0, 6)
YesCorner.Parent = YesButton

-- Botão No
local NoButton = Instance.new("TextButton")
NoButton.Size = UDim2.new(0, 160, 0, 35)
NoButton.Position = UDim2.new(1, -180, 1, -50)
NoButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NoButton.BorderSizePixel = 0
NoButton.Text = "No"
NoButton.TextColor3 = Color3.fromRGB(220, 220, 220)
NoButton.Font = Enum.Font.GothamBold
NoButton.TextSize = 13
NoButton.ZIndex = 11
NoButton.Parent = CloseModal

local NoCorner = Instance.new("UICorner")
NoCorner.CornerRadius = UDim.new(0, 6)
NoCorner.Parent = NoButton

-- Overlay escuro atrás do modal
local ModalOverlay = Instance.new("Frame")
ModalOverlay.Name = "Overlay"
ModalOverlay.Size = UDim2.new(1, 0, 1, 0)
ModalOverlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ModalOverlay.BackgroundTransparency = 0.5
ModalOverlay.BorderSizePixel = 0
ModalOverlay.Visible = false
ModalOverlay.ZIndex = 9
ModalOverlay.Parent = ScreenGui

-- Funcionalidade do Botão Fechar
CloseBtn.MouseButton1Click:Connect(function()
    -- Mostrar modal de confirmação
    ModalOverlay.Visible = true
    CloseModal.Visible = true
    
    -- Animar entrada do modal
    CloseModal.Size = UDim2.new(0, 0, 0, 0)
    TweenService:Create(CloseModal, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 360, 0, 140)
    }):Play()
    
    -- Animar botão
    TweenService:Create(CloseBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    }):Play()
    wait(0.1)
    TweenService:Create(CloseBtn, TweenInfo.new(0.1), {
        BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    }):Play()
end)

-- Botão Yes - Fecha o menu completamente
YesButton.MouseButton1Click:Connect(function()
    -- Animar saída
    TweenService:Create(CloseModal, TweenInfo.new(0.2), {
        Size = UDim2.new(0, 0, 0, 0)
    }):Play()
    
    TweenService:Create(ModalOverlay, TweenInfo.new(0.2), {
        BackgroundTransparency = 1
    }):Play()
    
    wait(0.2)
    
    -- Destruir a GUI
    ScreenGui:Destroy()
    print("Menu GUI fechado pelo usuário")
end)

-- Botão No - Cancela o fechamento
NoButton.MouseButton1Click:Connect(function()
    -- Animar saída do modal
    TweenService:Create(CloseModal, TweenInfo.new(0.2), {
        Size = UDim2.new(0, 0, 0, 0)
    }):Play()
    
    TweenService:Create(ModalOverlay, TweenInfo.new(0.2), {
        BackgroundTransparency = 1
    }):Play()
    
    wait(0.2)
    
    ModalOverlay.Visible = false
    CloseModal.Visible = false
    ModalOverlay.BackgroundTransparency = 0.5
end)

-- Container de Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "ContentFrame"
ContentFrame.Size = UDim2.new(1, -20, 1, -60)
ContentFrame.Position = UDim2.new(0, 10, 0, 50)
ContentFrame.BackgroundTransparency = 1
ContentFrame.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════
-- SISTEMA DE ABAS
-- ═══════════════════════════════════════════════════════════

local tabs = {"MAIN", "VISUALS", "MISC", "SETTINGS"}
local tabWidth = 80
local activeTab = "MAIN"
local tabContents = {}
local tabButtonElements = {}

-- Criar conteúdo para cada aba
for _, tabName in ipairs(tabs) do
    local TabContent = Instance.new("Frame")
    TabContent.Name = tabName .. "Content"
    TabContent.Size = UDim2.new(1, 0, 1, 0)
    TabContent.BackgroundTransparency = 1
    TabContent.Visible = (tabName == activeTab)
    TabContent.Parent = ContentFrame
    tabContents[tabName] = TabContent
end

-- Função para trocar de aba
local function SwitchTab(tabName)
    if activeTab == tabName then return end
    
    activeTab = tabName
    
    -- Atualizar visibilidade do conteúdo
    for name, content in pairs(tabContents) do
        content.Visible = (name == tabName)
    end
    
    -- Atualizar aparência dos botões
    for name, button in pairs(tabButtonElements) do
        local isActive = (name == tabName)
        
        -- Animar cor do texto
        TweenService:Create(button, TweenInfo.new(0.2), {
            TextColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(120, 120, 120)
        }):Play()
        
        -- Mostrar/esconder underline
        local underline = button:FindFirstChild("Underline")
        if underline then
            underline.Visible = isActive
        end
    end
end

-- Criar botões de aba
local function CreateTabButton(text, index)
    local Button = Instance.new("TextButton")
    Button.Name = text
    Button.Size = UDim2.new(0, tabWidth, 1, 0)
    Button.Position = UDim2.new(0, (index - 1) * (tabWidth + 10), 0, 0)
    Button.BackgroundTransparency = 1
    Button.Text = text
    Button.TextColor3 = (text == activeTab) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(120, 120, 120)
    Button.Font = Enum.Font.GothamBold
    Button.TextSize = 12
    Button.Parent = TabButtons
    
    -- Underline
    local Underline = Instance.new("Frame")
    Underline.Name = "Underline"
    Underline.Size = UDim2.new(1, -20, 0, 2)
    Underline.Position = UDim2.new(0, 10, 1, -2)
    Underline.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    Underline.BorderSizePixel = 0
    Underline.Visible = (text == activeTab)
    Underline.Parent = Button
    
    -- Evento de clique
    Button.MouseButton1Click:Connect(function()
        SwitchTab(text)
    end)
    
    tabButtonElements[text] = Button
    return Button
end

for i, tabName in ipairs(tabs) do
    CreateTabButton(tabName, i)
end

-- ═══════════════════════════════════════════════════════════
-- FUNÇÕES DE CRIAÇÃO DE ELEMENTOS
-- ═══════════════════════════════════════════════════════════

-- Toggle CORRIGIDO
local function CreateToggle(parent, text, posY, defaultValue, callback)
    local Toggle = Instance.new("Frame")
    Toggle.Name = text
    Toggle.Size = UDim2.new(1, -30, 0, 25)
    Toggle.Position = UDim2.new(0, 15, 0, posY)
    Toggle.BackgroundTransparency = 1
    Toggle.Parent = parent
    
    -- Label do texto
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -60, 1, 0)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Toggle
    
    -- Container do switch (estilo iOS)
    local SwitchContainer = Instance.new("Frame")
    SwitchContainer.Name = "Switch"
    SwitchContainer.Size = UDim2.new(0, 40, 0, 20)
    SwitchContainer.Position = UDim2.new(1, -40, 0.5, -10)
    SwitchContainer.BackgroundColor3 = defaultValue and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
    SwitchContainer.BorderSizePixel = 0
    SwitchContainer.Parent = Toggle
    
    local SwitchCorner = Instance.new("UICorner")
    SwitchCorner.CornerRadius = UDim.new(1, 0)
    SwitchCorner.Parent = SwitchContainer
    
    -- Bolinha do switch
    local SwitchKnob = Instance.new("Frame")
    SwitchKnob.Name = "Knob"
    SwitchKnob.Size = UDim2.new(0, 16, 0, 16)
    SwitchKnob.Position = defaultValue and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
    SwitchKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    SwitchKnob.BorderSizePixel = 0
    SwitchKnob.Parent = SwitchContainer
    
    local KnobCorner = Instance.new("UICorner")
    KnobCorner.CornerRadius = UDim.new(1, 0)
    KnobCorner.Parent = SwitchKnob
    
    -- Botão invisível para clique
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, 0, 1, 0)
    Button.BackgroundTransparency = 1
    Button.Text = ""
    Button.Parent = Toggle
    
    local enabled = defaultValue or false
    
    Button.MouseButton1Click:Connect(function()
        enabled = not enabled
        
        -- Anima a cor do fundo
        TweenService:Create(SwitchContainer, TweenInfo.new(0.2), {
            BackgroundColor3 = enabled and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
        }):Play()
        
        -- Anima a bolinha
        TweenService:Create(SwitchKnob, TweenInfo.new(0.2), {
            Position = enabled and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
        }):Play()
        
        if callback then
            callback(enabled)
        end
    end)
    
    return Toggle
end

-- Slider CORRIGIDO
local function CreateSlider(parent, text, posY, min, max, default, callback)
    local Slider = Instance.new("Frame")
    Slider.Name = text
    Slider.Size = UDim2.new(1, -30, 0, 50)
    Slider.Position = UDim2.new(0, 15, 0, posY)
    Slider.BackgroundTransparency = 1
    Slider.Parent = parent
    
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Slider
    
    -- CORRIGIDO: Valor agora fica à direita do label
    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Name = "Value"
    ValueLabel.Size = UDim2.new(0, 80, 0, 20)
    ValueLabel.Position = UDim2.new(1, -80, 0, 0)
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Text = string.format("%.1f", default)
    ValueLabel.TextColor3 = Color3.fromRGB(255, 80, 120)
    ValueLabel.Font = Enum.Font.GothamBold
    ValueLabel.TextSize = 12
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
    ValueLabel.Parent = Slider
    
    -- Barra do Slider
    local SliderBar = Instance.new("Frame")
    SliderBar.Name = "Bar"
    SliderBar.Size = UDim2.new(1, 0, 0, 6)
    SliderBar.Position = UDim2.new(0, 0, 0, 30)
    SliderBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    SliderBar.BorderSizePixel = 0
    SliderBar.Parent = Slider
    
    local BarCorner = Instance.new("UICorner")
    BarCorner.CornerRadius = UDim.new(0, 3)
    BarCorner.Parent = SliderBar
    
    local Fill = Instance.new("Frame")
    Fill.Name = "Fill"
    Fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Fill.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    Fill.BorderSizePixel = 0
    Fill.Parent = SliderBar
    
    local FillCorner = Instance.new("UICorner")
    FillCorner.CornerRadius = UDim.new(0, 3)
    FillCorner.Parent = Fill
    
    local currentValue = default
    local dragging = false
    
    local function UpdateSlider(value)
        currentValue = math.clamp(value, min, max)
        local percent = (currentValue - min) / (max - min)
        
        -- Animar a barra
        TweenService:Create(Fill, TweenInfo.new(0.1), {
            Size = UDim2.new(percent, 0, 1, 0)
        }):Play()
        
        ValueLabel.Text = string.format("%.1f", currentValue)
        
        if callback then
            callback(currentValue)
        end
    end
    
    -- Arrastar slider
    SliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            local mouse = game:GetService("Players").LocalPlayer:GetMouse()
            local percent = math.clamp((mouse.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
            UpdateSlider(min + (max - min) * percent)
        end
    end)
    
    SliderBar.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mouse = game:GetService("Players").LocalPlayer:GetMouse()
            local percent = math.clamp((mouse.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
            UpdateSlider(min + (max - min) * percent)
        end
    end)
    
    return Slider
end

-- Dropdown CORRIGIDO - Layout ajustado para não sobrepor o label
local function CreateDropdown(parent, text, posY, options, default, callback)
    local Dropdown = Instance.new("Frame")
    Dropdown.Name = text
    Dropdown.Size = UDim2.new(1, -30, 0, 48)  -- Aumentado para acomodar label + seletor
    Dropdown.Position = UDim2.new(0, 15, 0, posY)
    Dropdown.BackgroundTransparency = 1
    Dropdown.Parent = parent
    
    -- Label (nome do dropdown) - agora com altura fixa
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 18)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Dropdown
    
    -- Container do seletor (agora posicionado claramente abaixo do label)
    local SelectorContainer = Instance.new("Frame")
    SelectorContainer.Name = "Selector"
    SelectorContainer.Size = UDim2.new(1, 0, 0, 26)
    SelectorContainer.Position = UDim2.new(0, 0, 0, 22)  -- 22px do topo = 18px label + 4px espaçamento
    SelectorContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    SelectorContainer.BorderSizePixel = 0
    SelectorContainer.Parent = Dropdown
    
    local SelectorCorner = Instance.new("UICorner")
    SelectorCorner.CornerRadius = UDim.new(0, 4)
    SelectorCorner.Parent = SelectorContainer
    
    -- Valor atual
    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Size = UDim2.new(1, -30, 1, 0)
    ValueLabel.Position = UDim2.new(0, 8, 0, 0)
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Text = default or options[1]
    ValueLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    ValueLabel.Font = Enum.Font.Gotham
    ValueLabel.TextSize = 11
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Left
    ValueLabel.Parent = SelectorContainer
    
    -- Botão de seta
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
    
    local currentIndex = 1
    for i, opt in ipairs(options) do
        if opt == default then
            currentIndex = i
            break
        end
    end
    
    ArrowButton.MouseButton1Click:Connect(function()
        currentIndex = currentIndex % #options + 1
        ValueLabel.Text = options[currentIndex]
        
        -- Pequena animação no botão
        TweenService:Create(ArrowButton, TweenInfo.new(0.1), {
            BackgroundColor3 = Color3.fromRGB(255, 80, 120)
        }):Play()
        
        wait(0.1)
        
        TweenService:Create(ArrowButton, TweenInfo.new(0.1), {
            BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        }):Play()
        
        if callback then
            callback(options[currentIndex])
        end
    end)
    
    return Dropdown
end

-- Keybind NOVO - Sistema de detecção de teclas
local function CreateKeybind(parent, text, posY, defaultKey, callback)
    local Keybind = Instance.new("Frame")
    Keybind.Name = text
    Keybind.Size = UDim2.new(1, -30, 0, 25)
    Keybind.Position = UDim2.new(0, 15, 0, posY)
    Keybind.BackgroundTransparency = 1
    Keybind.Parent = parent
    
    -- Label do texto
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -80, 1, 0)
    Label.Position = UDim2.new(0, 0, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Keybind
    
    -- Botão de keybind
    local KeyButton = Instance.new("TextButton")
    KeyButton.Name = "KeyButton"
    KeyButton.Size = UDim2.new(0, 70, 0, 22)
    KeyButton.Position = UDim2.new(1, -70, 0.5, -11)
    KeyButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    KeyButton.BorderSizePixel = 0
    KeyButton.Text = defaultKey or "None"
    KeyButton.TextColor3 = Color3.fromRGB(200, 200, 200)
    KeyButton.Font = Enum.Font.GothamBold
    KeyButton.TextSize = 11
    KeyButton.Parent = Keybind
    
    local KeyCorner = Instance.new("UICorner")
    KeyCorner.CornerRadius = UDim.new(0, 4)
    KeyCorner.Parent = KeyButton
    
    local currentKey = defaultKey or "None"
    local listening = false
    local connection
    
    -- Mapeamento de teclas especiais para nomes legíveis
    local keyNames = {
        [Enum.KeyCode.LeftShift] = "LShift",
        [Enum.KeyCode.RightShift] = "RShift",
        [Enum.KeyCode.LeftControl] = "LCtrl",
        [Enum.KeyCode.RightControl] = "RCtrl",
        [Enum.KeyCode.LeftAlt] = "LAlt",
        [Enum.KeyCode.RightAlt] = "RAlt",
        [Enum.KeyCode.Space] = "Space",
        [Enum.KeyCode.CapsLock] = "Caps",
        [Enum.KeyCode.Tab] = "Tab",
        [Enum.KeyCode.Backspace] = "Back",
        [Enum.KeyCode.Return] = "Enter",
        [Enum.KeyCode.Escape] = "ESC",
        [Enum.KeyCode.Delete] = "Del",
        [Enum.KeyCode.Insert] = "Ins",
        [Enum.KeyCode.Home] = "Home",
        [Enum.KeyCode.End] = "End",
        [Enum.KeyCode.PageUp] = "PgUp",
        [Enum.KeyCode.PageDown] = "PgDn"
    }
    
    -- Função para obter nome da tecla
    local function GetKeyName(keyCode)
        if keyNames[keyCode] then
            return keyNames[keyCode]
        end
        
        local keyString = tostring(keyCode)
        return keyString:match("KeyCode%.(.+)") or keyString
    end
    
    -- Quando clica no botão
    KeyButton.MouseButton1Click:Connect(function()
        if listening then return end
        
        listening = true
        KeyButton.Text = "..."
        KeyButton.TextColor3 = Color3.fromRGB(255, 80, 120)
        
        -- Animar botão
        TweenService:Create(KeyButton, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        }):Play()
        
        -- Escutar próxima tecla
        connection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if input.UserInputType == Enum.UserInputType.Keyboard then
                listening = false
                
                -- ESC cancela
                if input.KeyCode == Enum.KeyCode.Escape then
                    KeyButton.Text = currentKey
                    KeyButton.TextColor3 = Color3.fromRGB(200, 200, 200)
                    TweenService:Create(KeyButton, TweenInfo.new(0.2), {
                        BackgroundColor3 = Color3.fromRGB(30, 30, 30)
                    }):Play()
                    connection:Disconnect()
                    return
                end
                
                -- Define nova tecla
                currentKey = GetKeyName(input.KeyCode)
                KeyButton.Text = currentKey
                KeyButton.TextColor3 = Color3.fromRGB(200, 200, 200)
                
                -- Anima de volta
                TweenService:Create(KeyButton, TweenInfo.new(0.2), {
                    BackgroundColor3 = Color3.fromRGB(30, 30, 30)
                }):Play()
                
                -- Callback com a tecla
                if callback then
                    callback(currentKey, input.KeyCode)
                end
                
                connection:Disconnect()
            end
        end)
    end)
    
    return Keybind, currentKey
end

-- ═══════════════════════════════════════════════════════════
-- ABA MAIN - Aimbot, FOV, TriggerBot
-- ═══════════════════════════════════════════════════════════

local MainTab = tabContents["MAIN"]

-- Aimbot Section
local AimbotSection = Instance.new("Frame")
AimbotSection.Name = "AimbotSection"
AimbotSection.Size = UDim2.new(0, 380, 0, 380)
AimbotSection.Position = UDim2.new(0, 5, 0, 5)
AimbotSection.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
AimbotSection.BorderSizePixel = 0
AimbotSection.Parent = MainTab

local AimbotCorner = Instance.new("UICorner")
AimbotCorner.CornerRadius = UDim.new(0, 4)
AimbotCorner.Parent = AimbotSection

local AimbotTitle = Instance.new("TextLabel")
AimbotTitle.Size = UDim2.new(1, -20, 0, 30)
AimbotTitle.Position = UDim2.new(0, 10, 0, 5)
AimbotTitle.BackgroundTransparency = 1
AimbotTitle.Text = "Aimbot"
AimbotTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
AimbotTitle.Font = Enum.Font.GothamBold
AimbotTitle.TextSize = 13
AimbotTitle.TextXAlignment = Enum.TextXAlignment.Left
AimbotTitle.Parent = AimbotSection

CreateToggle(AimbotSection, "Aimbot", 40, true)
CreateToggle(AimbotSection, "Team Check", 70, false)
CreateToggle(AimbotSection, "Prediction", 100, false)
CreateSlider(AimbotSection, "Prediction Strength", 130, 1.0, 5.0, 1.0)
CreateDropdown(AimbotSection, "Target Part", 195, {"Head", "Torso", "HumanoidRootPart"}, "Head")
CreateDropdown(AimbotSection, "Lock Mode", 248, {"Hold", "Toggle"}, "Hold")
CreateDropdown(AimbotSection, "First Person", 301, {"On", "Off"}, "Off")

-- Keybind para Aim Key (substitui o label estático)
CreateKeybind(AimbotSection, "Aim Key", 350, "E", function(keyName, keyCode)
    print("Nova tecla de Aim definida:", keyName)
    -- Aqui você pode salvar o keyCode para usar no seu aimbot
end)

-- FOV Section
local FOVSection = Instance.new("Frame")
FOVSection.Name = "FOVSection"
FOVSection.Size = UDim2.new(0, 380, 0, 185)
FOVSection.Position = UDim2.new(0, 395, 0, 5)
FOVSection.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
FOVSection.BorderSizePixel = 0
FOVSection.Parent = MainTab

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(0, 4)
FOVCorner.Parent = FOVSection

local FOVTitle = Instance.new("TextLabel")
FOVTitle.Size = UDim2.new(1, -20, 0, 30)
FOVTitle.Position = UDim2.new(0, 10, 0, 5)
FOVTitle.BackgroundTransparency = 1
FOVTitle.Text = "FOV Settings"
FOVTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVTitle.Font = Enum.Font.GothamBold
FOVTitle.TextSize = 13
FOVTitle.TextXAlignment = Enum.TextXAlignment.Left
FOVTitle.Parent = FOVSection

CreateToggle(FOVSection, "Use FOV", 40, false)
CreateToggle(FOVSection, "Show FOV", 70, true)
CreateSlider(FOVSection, "FOV Size", 100, 90, 500, 90)

local FOVColorLabel = Instance.new("TextLabel")
FOVColorLabel.Size = UDim2.new(1, -60, 0, 25)
FOVColorLabel.Position = UDim2.new(0, 15, 0, 155)
FOVColorLabel.BackgroundTransparency = 1
FOVColorLabel.Text = "FOV Color"
FOVColorLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
FOVColorLabel.Font = Enum.Font.Gotham
FOVColorLabel.TextSize = 12
FOVColorLabel.TextXAlignment = Enum.TextXAlignment.Left
FOVColorLabel.Parent = FOVSection

local ColorBox = Instance.new("Frame")
ColorBox.Size = UDim2.new(0, 30, 0, 20)
ColorBox.Position = UDim2.new(1, -45, 0, 158)
ColorBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ColorBox.BorderSizePixel = 1
ColorBox.BorderColor3 = Color3.fromRGB(60, 60, 60)
ColorBox.Parent = FOVSection

-- TriggerBot Section
local TriggerBotSection = Instance.new("Frame")
TriggerBotSection.Name = "TriggerBotSection"
TriggerBotSection.Size = UDim2.new(0, 380, 0, 190)
TriggerBotSection.Position = UDim2.new(0, 395, 0, 200)
TriggerBotSection.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
TriggerBotSection.BorderSizePixel = 0
TriggerBotSection.Parent = MainTab

local TriggerCorner = Instance.new("UICorner")
TriggerCorner.CornerRadius = UDim.new(0, 4)
TriggerCorner.Parent = TriggerBotSection

local TriggerTitle = Instance.new("TextLabel")
TriggerTitle.Size = UDim2.new(1, -20, 0, 30)
TriggerTitle.Position = UDim2.new(0, 10, 0, 5)
TriggerTitle.BackgroundTransparency = 1
TriggerTitle.Text = "TriggerBot"
TriggerTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
TriggerTitle.Font = Enum.Font.GothamBold
TriggerTitle.TextSize = 13
TriggerTitle.TextXAlignment = Enum.TextXAlignment.Left
TriggerTitle.Parent = TriggerBotSection

CreateToggle(TriggerBotSection, "TriggerBot", 40, false)
CreateToggle(TriggerBotSection, "Team Check", 70, false)
CreateSlider(TriggerBotSection, "Delay (ms)", 100, 0, 1000, 0)

-- ═══════════════════════════════════════════════════════════
-- OUTRAS ABAS (conteúdo de exemplo)
-- ═══════════════════════════════════════════════════════════

local VisualsTab = tabContents["VISUALS"]
local ExampleLabel1 = Instance.new("TextLabel")
ExampleLabel1.Size = UDim2.new(1, 0, 0, 50)
ExampleLabel1.BackgroundTransparency = 1
ExampleLabel1.Text = "Conteúdo da aba VISUALS\n(Adicione seus elementos aqui)"
ExampleLabel1.TextColor3 = Color3.fromRGB(150, 150, 150)
ExampleLabel1.Font = Enum.Font.Gotham
ExampleLabel1.TextSize = 14
ExampleLabel1.Parent = VisualsTab

local MiscTab = tabContents["MISC"]
local ExampleLabel2 = Instance.new("TextLabel")
ExampleLabel2.Size = UDim2.new(1, 0, 0, 50)
ExampleLabel2.BackgroundTransparency = 1
ExampleLabel2.Text = "Conteúdo da aba MISC\n(Adicione seus elementos aqui)"
ExampleLabel2.TextColor3 = Color3.fromRGB(150, 150, 150)
ExampleLabel2.Font = Enum.Font.Gotham
ExampleLabel2.TextSize = 14
ExampleLabel2.Parent = MiscTab

local SettingsTab = tabContents["SETTINGS"]
local ExampleLabel3 = Instance.new("TextLabel")
ExampleLabel3.Size = UDim2.new(1, 0, 0, 50)
ExampleLabel3.BackgroundTransparency = 1
ExampleLabel3.Text = "Conteúdo da aba SETTINGS\n(Adicione seus elementos aqui)"
ExampleLabel3.TextColor3 = Color3.fromRGB(150, 150, 150)
ExampleLabel3.Font = Enum.Font.Gotham
ExampleLabel3.TextSize = 14
ExampleLabel3.Parent = SettingsTab

-- ═══════════════════════════════════════════════════════════
-- SISTEMA DE DRAG COM PESO (MOVIMENTO SUAVE)
-- ═══════════════════════════════════════════════════════════

local dragging = false
local dragInput
local dragStart
local startPos

-- Variáveis para movimento suave
local targetPosition = MainFrame.Position
local currentVelocity = Vector2.new(0, 0)
local smoothness = 0.15 -- Quanto menor, mais "peso" tem (0.1 = muito pesado, 0.3 = leve)

-- Atualizar posição com suavização
game:GetService("RunService").RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        targetPosition = UDim2.new(
            startPos.X.Scale, 
            startPos.X.Offset + delta.X, 
            startPos.Y.Scale, 
            startPos.Y.Offset + delta.Y
        )
    end
    
    -- Interpolação suave
    local current = MainFrame.Position
    local targetX = targetPosition.X.Offset
    local targetY = targetPosition.Y.Offset
    local currentX = current.X.Offset
    local currentY = current.Y.Offset
    
    -- Lerp suave
    local newX = currentX + (targetX - currentX) * smoothness
    local newY = currentY + (targetY - currentY) * smoothness
    
    MainFrame.Position = UDim2.new(
        targetPosition.X.Scale,
        newX,
        targetPosition.Y.Scale,
        newY
    )
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

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

-- Restaurar menu com tecla INSERT (quando minimizado)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.Insert and minimized then
        minimized = false
        MainFrame.Visible = true
    end
end)
