-- DOGFIRE HUB | Interface inicial
-- LocalScript para Roblox Studio (StarterPlayerScripts ou StarterGui).
-- Esta versão cria a interface e guarda os estados dos controles.
-- Os sistemas reais de combate e ESP serão integrados separadamente.

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local old = playerGui:FindFirstChild("DogfireHub")
if old then old:Destroy() end

local C = {
    White = Color3.fromRGB(248, 251, 255),
    Panel = Color3.fromRGB(255, 255, 255),
    PanelSoft = Color3.fromRGB(239, 245, 255),
    Blue = Color3.fromRGB(35, 91, 255),       -- azul safira elétrico
    Cyan = Color3.fromRGB(42, 207, 255),
    Navy = Color3.fromRGB(22, 37, 75),
    Text = Color3.fromRGB(38, 51, 78),
    Muted = Color3.fromRGB(116, 130, 158),
    Stroke = Color3.fromRGB(214, 226, 248),
    Lime = Color3.fromRGB(174, 255, 0),
    Emerald = Color3.fromRGB(0, 210, 125),
    Purple = Color3.fromRGB(174, 105, 255),
    Gold = Color3.fromRGB(255, 190, 55),
    Pink = Color3.fromRGB(255, 105, 190),
}

local Settings = {
    AttackDelay = 0.5,
    ESP = {
        Frutas = false,
        NPCs = false,
        Jogadores = false,
        Bosses = false,
        Baús = false,
        Flores = false,
    },
    Features = {
        FastAttack = false,
    }
}

local gui = Instance.new("ScreenGui")
gui.Name = "DogfireHub"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = playerGui

local function corner(obj, radius)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, radius or 12)
    c.Parent = obj
    return c
end

local function stroke(obj, color, thickness, transparency)
    local s = Instance.new("UIStroke")
    s.Color = color or C.Stroke
    s.Thickness = thickness or 1
    s.Transparency = transparency or 0
    s.Parent = obj
    return s
end

local function make(class, props, parent)
    local obj = Instance.new(class)
    for k, v in pairs(props or {}) do obj[k] = v end
    obj.Parent = parent
    return obj
end

local scale = make("UIScale", {Scale = 1}, nil)
local camera = workspace.CurrentCamera
local function updateScale()
    local v = camera and camera.ViewportSize or Vector2.new(900, 600)
    scale.Scale = math.clamp(math.min(v.X / 900, v.Y / 650), 0.68, 1)
end
scale.Parent = gui
updateScale()
if camera then camera:GetPropertyChangedSignal("ViewportSize"):Connect(updateScale) end

local main = make("Frame", {
    Name = "Main",
    AnchorPoint = Vector2.new(0.5, 0.5),
    Position = UDim2.fromScale(0.5, 0.52),
    Size = UDim2.fromOffset(760, 500),
    BackgroundColor3 = C.White,
    BackgroundTransparency = 0.06,
    BorderSizePixel = 0,
}, gui)
corner(main, 18)
stroke(main, C.Blue, 1.5, 0.18)

local top = make("Frame", {
    Size = UDim2.new(1, 0, 0, 62),
    BackgroundColor3 = C.Panel,
    BackgroundTransparency = 0.08,
    BorderSizePixel = 0,
}, main)
corner(top, 18)

local accent = make("Frame", {
    Position = UDim2.new(0, 0, 1, -3),
    Size = UDim2.new(1, 0, 0, 3),
    BackgroundColor3 = C.Blue,
    BorderSizePixel = 0,
}, top)
make("UIGradient", {
    Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, C.Blue),
        ColorSequenceKeypoint.new(0.55, C.Cyan),
        ColorSequenceKeypoint.new(1, C.Blue),
    })
}, accent)

local logo = make("Frame", {
    Position = UDim2.fromOffset(15, 11),
    Size = UDim2.fromOffset(40, 40),
    BackgroundColor3 = C.PanelSoft,
    BorderSizePixel = 0,
}, top)
corner(logo, 13)
stroke(logo, C.Stroke, 1)
make("TextLabel", {
    BackgroundTransparency = 1,
    Size = UDim2.fromScale(1, 1),
    Text = "D",
    Font = Enum.Font.GothamBlack,
    TextSize = 25,
    TextColor3 = C.Blue,
}, logo)

make("TextLabel", {
    Position = UDim2.fromOffset(66, 8),
    Size = UDim2.new(0, 260, 0, 27),
    BackgroundTransparency = 1,
    Text = "DOGFIRE HUB",
    TextXAlignment = Enum.TextXAlignment.Left,
    Font = Enum.Font.GothamBlack,
    TextSize = 20,
    TextColor3 = C.Navy,
}, top)

make("TextLabel", {
    Position = UDim2.fromOffset(67, 34),
    Size = UDim2.new(0, 280, 0, 17),
    BackgroundTransparency = 1,
    Text = "BLOX FRUITS  •  CONTROL CENTER",
    TextXAlignment = Enum.TextXAlignment.Left,
    Font = Enum.Font.GothamMedium,
    TextSize = 9,
    TextColor3 = C.Muted,
}, top)

local close = make("TextButton", {
    AnchorPoint = Vector2.new(1, 0.5),
    Position = UDim2.new(1, -15, 0.5, 0),
    Size = UDim2.fromOffset(34, 34),
    BackgroundColor3 = C.PanelSoft,
    Text = "×",
    Font = Enum.Font.GothamBold,
    TextSize = 23,
    TextColor3 = C.Navy,
    AutoButtonColor = true,
}, top)
corner(close, 10)
close.MouseButton1Click:Connect(function() main.Visible = false end)

local sidebar = make("Frame", {
    Position = UDim2.fromOffset(14, 76),
    Size = UDim2.new(0, 190, 1, -90),
    BackgroundColor3 = C.Panel,
    BackgroundTransparency = 0.12,
    BorderSizePixel = 0,
}, main)
corner(sidebar, 14)
stroke(sidebar, C.Stroke, 1)

local search = make("TextBox", {
    Position = UDim2.fromOffset(10, 10),
    Size = UDim2.new(1, -20, 0, 36),
    BackgroundColor3 = C.PanelSoft,
    BorderSizePixel = 0,
    PlaceholderText = "Pesquisar seção...",
    PlaceholderColor3 = C.Muted,
    Text = "",
    TextColor3 = C.Text,
    Font = Enum.Font.Gotham,
    TextSize = 11,
    ClearTextOnFocus = false,
}, sidebar)
corner(search, 9)

local nav = make("Frame", {
    Position = UDim2.fromOffset(8, 56),
    Size = UDim2.new(1, -16, 1, -64),
    BackgroundTransparency = 1,
}, sidebar)
make("UIListLayout", {
    Padding = UDim.new(0, 6),
    SortOrder = Enum.SortOrder.LayoutOrder,
}, nav)

local content = make("Frame", {
    Position = UDim2.fromOffset(216, 76),
    Size = UDim2.new(1, -230, 1, -90),
    BackgroundColor3 = C.Panel,
    BackgroundTransparency = 0.12,
    BorderSizePixel = 0,
}, main)
corner(content, 14)
stroke(content, C.Stroke, 1)

local pageTitle = make("TextLabel", {
    Position = UDim2.fromOffset(18, 12),
    Size = UDim2.new(1, -36, 0, 28),
    BackgroundTransparency = 1,
    Text = "Painel inicial",
    TextXAlignment = Enum.TextXAlignment.Left,
    Font = Enum.Font.GothamBold,
    TextSize = 17,
    TextColor3 = C.Navy,
}, content)

local pageHint = make("TextLabel", {
    Position = UDim2.fromOffset(18, 40),
    Size = UDim2.new(1, -36, 0, 20),
    BackgroundTransparency = 1,
    Text = "Configure os módulos do Dogfire Hub.",
    TextXAlignment = Enum.TextXAlignment.Left,
    Font = Enum.Font.Gotham,
    TextSize = 10,
    TextColor3 = C.Muted,
}, content)

local body = make("ScrollingFrame", {
    Position = UDim2.fromOffset(14, 70),
    Size = UDim2.new(1, -28, 1, -84),
    BackgroundTransparency = 1,
    BorderSizePixel = 0,
    ScrollBarThickness = 3,
    ScrollBarImageColor3 = C.Blue,
    CanvasSize = UDim2.new(),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
}, content)
make("UIListLayout", {Padding = UDim.new(0, 9), SortOrder = Enum.SortOrder.LayoutOrder}, body)
make("UIPadding", {PaddingBottom = UDim.new(0, 8)}, body)

local pages = {}
local navButtons = {}

local function clearBody()
    for _, child in ipairs(body:GetChildren()) do
        if not child:IsA("UIListLayout") and not child:IsA("UIPadding") then child:Destroy() end
    end
end

local function card(title, subtitle)
    local f = make("Frame", {
        Size = UDim2.new(1, -4, 0, 66),
        BackgroundColor3 = C.PanelSoft,
        BorderSizePixel = 0,
    }, body)
    corner(f, 11)
    stroke(f, C.Stroke, 1)
    make("TextLabel", {
        Position = UDim2.fromOffset(13, 9),
        Size = UDim2.new(1, -26, 0, 20),
        BackgroundTransparency = 1,
        Text = title,
        TextXAlignment = Enum.TextXAlignment.Left,
        Font = Enum.Font.GothamBold,
        TextSize = 12,
        TextColor3 = C.Text,
    }, f)
    if subtitle then
        make("TextLabel", {
            Position = UDim2.fromOffset(13, 31),
            Size = UDim2.new(1, -26, 0, 17),
            BackgroundTransparency = 1,
            Text = subtitle,
            TextXAlignment = Enum.TextXAlignment.Left,
            Font = Enum.Font.Gotham,
            TextSize = 9,
            TextColor3 = C.Muted,
        }, f)
    end
    return f
end

local function addToggle(title, subtitle, getter, setter, tint)
    local f = card(title, subtitle)
    local b = make("TextButton", {
        AnchorPoint = Vector2.new(1, 0.5),
        Position = UDim2.new(1, -13, 0.5, 0),
        Size = UDim2.fromOffset(43, 23),
        BackgroundColor3 = getter() and (tint or C.Blue) or Color3.fromRGB(205, 215, 231),
        BorderSizePixel = 0,
        Text = getter() and "ON" or "OFF",
        Font = Enum.Font.GothamBold,
        TextSize = 9,
        TextColor3 = Color3.new(1, 1, 1),
    }, f)
    corner(b, 8)
    b.MouseButton1Click:Connect(function()
        local value = not getter()
        setter(value)
        b.Text = value and "ON" or "OFF"
        TweenService:Create(b, TweenInfo.new(0.16), {
            BackgroundColor3 = value and (tint or C.Blue) or Color3.fromRGB(205, 215, 231)
        }):Play()
    end)
end

local function addSection(text)
    local label = make("TextLabel", {
        Size = UDim2.new(1, -4, 0, 23),
        BackgroundTransparency = 1,
        Text = text,
        TextXAlignment = Enum.TextXAlignment.Left,
        Font = Enum.Font.GothamBold,
        TextSize = 11,
        TextColor3 = C.Blue,
    }, body)
    return label
end

local function renderHome()
    clearBody()
    addSection("BEM-VINDO AO DOGFIRE")
    card("Interface carregada", "Visual inicial pronto para receber os módulos.")
    card("Identidade visual", "Branco translúcido • Azul safira elétrico • detalhes ciano")
    card("Configuração de ataque", "Delay padrão definido: 0,5 segundo")
    card("ESP por categoria", "Cada tipo de alvo terá uma cor própria.")
end

local function renderCombat()
    clearBody()
    addSection("CONFIGURAÇÕES DE COMBATE")
    addToggle("Fast Attack", "Controle visual — integração do sistema será feita depois.",
        function() return Settings.Features.FastAttack end,
        function(v) Settings.Features.FastAttack = v end)
    local f = card("Delay do ataque", "Padrão solicitado: 0,5 segundo")
    local value = make("TextLabel", {
        AnchorPoint = Vector2.new(1, 0.5),
        Position = UDim2.new(1, -14, 0.5, 0),
        Size = UDim2.fromOffset(70, 28),
        BackgroundColor3 = C.Blue,
        Text = string.format("%.1f s", Settings.AttackDelay),
        Font = Enum.Font.GothamBold,
        TextSize = 12,
        TextColor3 = Color3.new(1, 1, 1),
    }, f)
    corner(value, 8)
end

local function renderESP()
    clearBody()
    addSection("ESP • CORES POR CATEGORIA")
    local items = {
        {"Frutas", C.Lime, "Verde-limão"},
        {"NPCs", C.Emerald, "Verde-esmeralda"},
        {"Jogadores", C.Cyan, "Azul-ciano"},
        {"Bosses", C.Purple, "Roxo"},
        {"Baús", C.Gold, "Dourado"},
        {"Flores", C.Pink, "Rosa"},
    }
    for _, item in ipairs(items) do
        local name, tint, colorName = item[1], item[2], item[3]
        addToggle("ESP " .. name, colorName .. " • cor reservada para esta categoria",
            function() return Settings.ESP[name] end,
            function(v) Settings.ESP[name] = v end, tint)
    end
end

local function renderPlayer()
    clearBody()
    addSection("JOGADOR E UTILIDADES")
    card("Anti-AFK", "Espaço reservado para integração segura.")
    card("Movimento", "Controles de movimento serão adicionados nesta seção.")
    card("Otimização", "Ajustes visuais e de desempenho serão organizados aqui.")
end

local function renderSettings()
    clearBody()
    addSection("CONFIGURAÇÕES")
    card("Perfil", "DOGFIRE HUB • versão inicial da interface")
    card("Escala para celular", "A interface ajusta o tamanho conforme a tela.")
    card("Logo", "Logo atual mantida temporariamente, conforme solicitado.")
end

pages = {
    ["Início"] = renderHome,
    ["Combate"] = renderCombat,
    ["ESP Visual"] = renderESP,
    ["Jogador"] = renderPlayer,
    ["Configurações"] = renderSettings,
}

local function openPage(name)
    pageTitle.Text = name
    pageHint.Text = "DOGFIRE HUB  •  " .. string.upper(name)
    for navName, button in pairs(navButtons) do
        local selected = navName == name
        button.BackgroundColor3 = selected and C.Blue or C.Panel
        button.TextColor3 = selected and Color3.new(1,1,1) or C.Text
    end
    pages[name]()
end

for index, name in ipairs({"Início", "Combate", "ESP Visual", "Jogador", "Configurações"}) do
    local b = make("TextButton", {
        Name = name,
        Size = UDim2.new(1, 0, 0, 38),
        BackgroundColor3 = index == 1 and C.Blue or C.Panel,
        BorderSizePixel = 0,
        Text = "   " .. name,
        TextXAlignment = Enum.TextXAlignment.Left,
        Font = Enum.Font.GothamSemibold,
        TextSize = 11,
        TextColor3 = index == 1 and Color3.new(1,1,1) or C.Text,
        LayoutOrder = index,
    }, nav)
    corner(b, 9)
    navButtons[name] = b
    b.MouseButton1Click:Connect(function() openPage(name) end)
end

search:GetPropertyChangedSignal("Text"):Connect(function()
    local query = string.lower(search.Text)
    for name, b in pairs(navButtons) do
        b.Visible = query == "" or string.find(string.lower(name), query, 1, true) ~= nil
    end
end)

-- Arrastar pelo cabeçalho (compatível com mouse e toque).
local dragging, dragStart, startPos
top.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = main.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Botão flutuante para reabrir o menu depois de fechar.
local reopen = make("TextButton", {
    AnchorPoint = Vector2.new(1, 1),
    Position = UDim2.new(1, -18, 1, -18),
    Size = UDim2.fromOffset(54, 54),
    BackgroundColor3 = C.Blue,
    Text = "D",
    Font = Enum.Font.GothamBlack,
    TextSize = 24,
    TextColor3 = Color3.new(1, 1, 1),
    Visible = false,
}, gui)
corner(reopen, 18)
stroke(reopen, C.Cyan, 1.5)
close.MouseButton1Click:Connect(function()
    main.Visible = false
    reopen.Visible = true
end)
reopen.MouseButton1Click:Connect(function()
    main.Visible = true
    reopen.Visible = false
end)

openPage("Início")
