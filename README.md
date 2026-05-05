-- Elements para GROSS UI Library
-- Contém todos os elementos de UI (Toggle, Button, Slider, etc)

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local Elements = {}

local function Tween(instance, properties, duration, style, direction)
    duration = duration or 0.2
    style = style or Enum.EasingStyle.Quad
    direction = direction or Enum.EasingDirection.Out
    
    local tween = TweenService:Create(instance, TweenInfo.new(duration, style, direction), properties)
    tween:Play()
    return tween
end

-- TOGGLE
function Elements.CreateToggle(parent, idx, options, yPos)
    options = options or {}
    local text = options.Text or "Toggle"
    local default = options.Default or false
    local tooltip = options.Tooltip
    local callback = options.Callback
    
    local Toggle = Instance.new("Frame")
    Toggle.Name = idx
    Toggle.Size = UDim2.new(1, -30, 0, 25)
    Toggle.Position = UDim2.new(0, 15, 0, yPos)
    Toggle.BackgroundTransparency = 1
    Toggle.Parent = parent.Content
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -60, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Toggle
    
    local SwitchContainer = Instance.new("Frame")
    SwitchContainer.Size = UDim2.new(0, 40, 0, 20)
    SwitchContainer.Position = UDim2.new(1, -40, 0.5, -10)
    SwitchContainer.BackgroundColor3 = default and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
    SwitchContainer.BorderSizePixel = 0
    SwitchContainer.Parent = Toggle
    
    local SwitchCorner = Instance.new("UICorner")
    SwitchCorner.CornerRadius = UDim.new(1, 0)
    SwitchCorner.Parent = SwitchContainer
    
    local SwitchKnob = Instance.new("Frame")
    SwitchKnob.Size = UDim2.new(0, 16, 0, 16)
    SwitchKnob.Position = default and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
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
    
    local ToggleObject = {
        Type = "Toggle",
        Value = default,
        Height = 30,
        Callback = callback,
        OnChangedCallbacks = {}
    }
    
    function ToggleObject:SetValue(value)
        self.Value = value
        
        Tween(SwitchContainer, {
            BackgroundColor3 = value and Color3.fromRGB(255, 80, 120) or Color3.fromRGB(50, 50, 50)
        })
        
        Tween(SwitchKnob, {
            Position = value and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
        })
        
        if self.Callback then
            self.Callback(value)
        end
        
        for _, func in ipairs(self.OnChangedCallbacks) do
            func(value)
        end
    end
    
    function ToggleObject:OnChanged(func)
        table.insert(self.OnChangedCallbacks, func)
        return self
    end
    
    Button.MouseButton1Click:Connect(function()
        ToggleObject:SetValue(not ToggleObject.Value)
    end)
    
    getgenv().Toggles[idx] = ToggleObject
    return ToggleObject
end

-- BUTTON
function Elements.CreateButton(parent, options, yPos)
    options = options or {}
    local text = options.Text or "Button"
    local func = options.Func
    local doubleClick = options.DoubleClick or false
    local tooltip = options.Tooltip
    
    local ButtonFrame = Instance.new("Frame")
    ButtonFrame.Size = UDim2.new(1, -30, 0, 32)
    ButtonFrame.Position = UDim2.new(0, 15, 0, yPos)
    ButtonFrame.BackgroundTransparency = 1
    ButtonFrame.Parent = parent.Content
    
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, 0, 1, 0)
    Button.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    Button.BorderSizePixel = 0
    Button.Text = text
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.Font = Enum.Font.GothamBold
    Button.TextSize = 12
    Button.Parent = ButtonFrame
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 4)
    ButtonCorner.Parent = Button
    
    local lastClick = 0
    
    Button.MouseButton1Click:Connect(function()
        if doubleClick then
            local now = tick()
            if now - lastClick < 0.3 then
                if func then func() end
            end
            lastClick = now
        else
            if func then func() end
        end
        
        Tween(Button, {BackgroundColor3 = Color3.fromRGB(220, 60, 100)}, 0.1)
        wait(0.1)
        Tween(Button, {BackgroundColor3 = Color3.fromRGB(255, 80, 120)}, 0.1)
    end)
    
    local ButtonObject = {
        Type = "Button",
        Height = 37,
        Frame = ButtonFrame,
        Button = Button
    }
    
    function ButtonObject:AddButton(subOptions)
        -- Criar sub-botão
        return Elements.CreateButton(parent, subOptions, yPos + 37)
    end
    
    return ButtonObject
end

-- LABEL
function Elements.CreateLabel(parent, text, wrap, yPos)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -30, 0, wrap and 60 or 20)
    Label.Position = UDim2.new(0, 15, 0, yPos)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextYAlignment = Enum.TextYAlignment.Top
    Label.TextWrapped = wrap or false
    Label.Parent = parent.Content
    
    return {
        Type = "Label",
        Height = wrap and 65 or 25,
        Label = Label,
        
        AddColorPicker = function(self, idx, options)
            return Elements.CreateColorPicker(parent, idx, options, yPos)
        end,
        
        AddKeyPicker = function(self, idx, options)
            return Elements.CreateKeyPicker(parent, idx, options, yPos)
        end
    }
end

-- DIVIDER
function Elements.CreateDivider(parent, yPos)
    local Divider = Instance.new("Frame")
    Divider.Size = UDim2.new(1, -30, 0, 1)
    Divider.Position = UDim2.new(0, 15, 0, yPos)
    Divider.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Divider.BorderSizePixel = 0
    Divider.Parent = parent.Content
    
    return {
        Type = "Divider",
        Height = 10
    }
end

-- SLIDER
function Elements.CreateSlider(parent, idx, options, yPos)
    options = options or {}
    local text = options.Text or "Slider"
    local default = options.Default or 0
    local min = options.Min or 0
    local max = options.Max or 100
    local rounding = options.Rounding or 0
    local callback = options.Callback
    
    local Slider = Instance.new("Frame")
    Slider.Size = UDim2.new(1, -30, 0, 50)
    Slider.Position = UDim2.new(0, 15, 0, yPos)
    Slider.BackgroundTransparency = 1
    Slider.Parent = parent.Content
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Slider
    
    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Size = UDim2.new(0, 80, 0, 20)
    ValueLabel.Position = UDim2.new(1, -80, 0, 0)
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Text = string.format("%." .. rounding .. "f", default)
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
    Fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Fill.BackgroundColor3 = Color3.fromRGB(255, 80, 120)
    Fill.BorderSizePixel = 0
    Fill.Parent = SliderBar
    
    local FillCorner = Instance.new("UICorner")
    FillCorner.CornerRadius = UDim.new(0, 3)
    FillCorner.Parent = Fill
    
    local SliderObject = {
        Type = "Slider",
        Value = default,
        Height = 55,
        Min = min,
        Max = max,
        Rounding = rounding,
        Callback = callback,
        OnChangedCallbacks = {}
    }
    
    local dragging = false
    
    function SliderObject:SetValue(value)
        value = math.clamp(value, self.Min, self.Max)
        
        if self.Rounding > 0 then
            value = math.floor(value * (10 ^ self.Rounding) + 0.5) / (10 ^ self.Rounding)
        else
            value = math.floor(value + 0.5)
        end
        
        self.Value = value
        local percent = (value - self.Min) / (self.Max - self.Min)
        
        Tween(Fill, {Size = UDim2.new(percent, 0, 1, 0)}, 0.1)
        ValueLabel.Text = string.format("%." .. self.Rounding .. "f", value)
        
        if self.Callback then
            self.Callback(value)
        end
        
        for _, func in ipairs(self.OnChangedCallbacks) do
            func(value)
        end
    end
    
    function SliderObject:OnChanged(func)
        table.insert(self.OnChangedCallbacks, func)
        return self
    end
    
    SliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            local mouse = game:GetService("Players").LocalPlayer:GetMouse()
            local percent = math.clamp((mouse.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
            SliderObject:SetValue(min + (max - min) * percent)
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
            SliderObject:SetValue(min + (max - min) * percent)
        end
    end)
    
    getgenv().Options[idx] = SliderObject
    return SliderObject
end

-- INPUT (Textbox)
function Elements.CreateInput(parent, idx, options, yPos)
    options = options or {}
    local text = options.Text or "Input"
    local default = options.Default or ""
    local callback = options.Callback
    local numeric = options.Numeric or false
    local finished = options.Finished or false
    local placeholder = options.Placeholder or ""
    
    local InputFrame = Instance.new("Frame")
    InputFrame.Size = UDim2.new(1, -30, 0, 50)
    InputFrame.Position = UDim2.new(0, 15, 0, yPos)
    InputFrame.BackgroundTransparency = 1
    InputFrame.Parent = parent.Content
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = InputFrame
    
    local TextBox = Instance.new("TextBox")
    TextBox.Size = UDim2.new(1, 0, 0, 26)
    TextBox.Position = UDim2.new(0, 0, 0, 24)
    TextBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    TextBox.BorderSizePixel = 0
    TextBox.Text = default
    TextBox.PlaceholderText = placeholder
    TextBox.TextColor3 = Color3.fromRGB(220, 220, 220)
    TextBox.PlaceholderColor3 = Color3.fromRGB(100, 100, 100)
    TextBox.Font = Enum.Font.Gotham
    TextBox.TextSize = 11
    TextBox.ClearTextOnFocus = false
    TextBox.Parent = InputFrame
    
    local BoxCorner = Instance.new("UICorner")
    BoxCorner.CornerRadius = UDim.new(0, 4)
    BoxCorner.Parent = TextBox
    
    local InputObject = {
        Type = "Input",
        Value = default,
        Height = 55,
        Callback = callback,
        OnChangedCallbacks = {}
    }
    
    function InputObject:SetValue(value)
        self.Value = value
        TextBox.Text = value
        
        if self.Callback then
            self.Callback(value)
        end
        
        for _, func in ipairs(self.OnChangedCallbacks) do
            func(value)
        end
    end
    
    function InputObject:OnChanged(func)
        table.insert(self.OnChangedCallbacks, func)
        return self
    end
    
    if finished then
        TextBox.FocusLost:Connect(function(enterPressed)
            if enterPressed then
                local value = TextBox.Text
                if numeric then
                    value = tonumber(value) or 0
                end
                InputObject:SetValue(value)
            end
        end)
    else
        TextBox:GetPropertyChangedSignal("Text"):Connect(function()
            local value = TextBox.Text
            if numeric then
                value = tonumber(value) or 0
                TextBox.Text = tostring(value)
            end
            InputObject:SetValue(value)
        end)
    end
    
    getgenv().Options[idx] = InputObject
    return InputObject
end

-- DROPDOWN
function Elements.CreateDropdown(parent, idx, options, yPos)
    options = options or {}
    local text = options.Text or "Dropdown"
    local values = options.Values or {}
    local default = options.Default or (values[1] or "None")
    local multi = options.Multi or false
    local callback = options.Callback
    
    if type(default) == "number" then
        default = values[default] or values[1]
    end
    
    local Dropdown = Instance.new("Frame")
    Dropdown.Size = UDim2.new(1, -30, 0, 50)
    Dropdown.Position = UDim2.new(0, 15, 0, yPos)
    Dropdown.BackgroundTransparency = 1
    Dropdown.Parent = parent.Content
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 18)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(200, 200, 200)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 12
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Dropdown
    
    local SelectorContainer = Instance.new("Frame")
    SelectorContainer.Size = UDim2.new(1, 0, 0, 28)
    SelectorContainer.Position = UDim2.new(0, 0, 0, 22)
    SelectorContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    SelectorContainer.BorderSizePixel = 0
    SelectorContainer.Parent = Dropdown
    
    local SelectorCorner = Instance.new("UICorner")
    SelectorCorner.CornerRadius = UDim.new(0, 6)
    SelectorCorner.Parent = SelectorContainer
    
    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Size = UDim2.new(1, -40, 1, 0)
    ValueLabel.Position = UDim2.new(0, 12, 0, 0)
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Text = default
    ValueLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
    ValueLabel.Font = Enum.Font.GothamBold
    ValueLabel.TextSize = 12
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Left
    ValueLabel.Parent = SelectorContainer
    
    local Arrow = Instance.new("ImageLabel")
    Arrow.Size = UDim2.new(0, 16, 0, 16)
    Arrow.Position = UDim2.new(1, -24, 0.5, -8)
    Arrow.BackgroundTransparency = 1
    Arrow.Image = "rbxassetid://7733964370"
    Arrow.ImageColor3 = Color3.fromRGB(150, 150, 150)
    Arrow.Rotation = 0
    Arrow.Parent = SelectorContainer
    
    local DropdownObject = {
        Type = "Dropdown",
        Value = multi and {[default] = true} or default,
        Values = values,
        Height = 55,
        Multi = multi,
        Callback = callback,
        OnChangedCallbacks = {}
    }
    
    function DropdownObject:SetValue(value)
        if self.Multi then
            self.Value = value
            -- Atualizar label para multi
            local selected = {}
            for k, v in pairs(value) do
                if v then table.insert(selected, k) end
            end
            ValueLabel.Text = #selected > 0 and table.concat(selected, ", ") or "None"
        else
            self.Value = value
            ValueLabel.Text = value
        end
        
        if self.Callback then
            self.Callback(value)
        end
        
        for _, func in ipairs(self.OnChangedCallbacks) do
            func(value)
        end
    end
    
    function DropdownObject:OnChanged(func)
        table.insert(self.OnChangedCallbacks, func)
        return self
    end
    
    -- TODO: Implementar lista de opções expansível
    
    getgenv().Options[idx] = DropdownObject
    return DropdownObject
end

-- COLOR PICKER (placeholder)
function Elements.CreateColorPicker(parent, idx, options, yPos)
    -- Implementação básica
    return {
        Type = "ColorPicker",
        Value = options.Default or Color3.new(1, 1, 1),
        Height = 0
    }
end

-- KEY PICKER (placeholder)
function Elements.CreateKeyPicker(parent, idx, options, yPos)
    -- Implementação básica
    return {
        Type = "KeyPicker",
        Value = options.Default or "None",
        Height = 0
    }
end

return Elements
