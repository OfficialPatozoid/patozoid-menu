--[[
    PATOZOID MENU - Roblox Lua Script (Aperfeiçoado)
    Baseado no sistema original de Key, agora com design em tela cheia, 
    fundo animado e paleta Azul Bebê/Preto.
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- ╔════════════════════════════════════════════════════════════╗
-- ║                 DATABASE DE KEYS + LOADSTRINGS             ║
-- ╚════════════════════════════════════════════════════════════╝

local KEYS = {
    ["PLAYER1"] = "123456",
    ["PLAYER2"] = "654321",
    ["ADMIN"] = "SUPERADMIN",
    ["PATOZOID1"] = "KEY-8100F89F-18160C8F-53500430",
    ["GS_GOSOTOSO"] = "KEY-24529ECB-4AF220C5-A7E789C4",
    ["GS_GOSTOSO"] = "KEY-47183E89-4F5A83D8-955C255F",
    ["PATOZOID"] = "KEY-F711CBF2-970C8AEB-9EA46CF2",
    ["WWU"] = "KEY-6CB68635-F5881138-38351A02",
    ["PATO"] = "KEY-4502C019-722B8CD3-5B30977C"
}

local GAME_SCRIPTS = {
    [286090429] = "print('Bloxburg Script Rodando')", -- Bloxburg
    [155615604] = "print('Jailbreak Script Rodando')", -- Jailbreak
}

local DEFAULT_LOADSTRING = [[
    print('Executando Script Padrão - PATOZOID MENU Ativado')
]]

-- ╔════════════════════════════════════════════════════════════╗
-- ║                    INTERFACE DE USUÁRIO                   ║
-- ╚════════════════════════════════════════════════════════════╝

-- Criar a ScreenGui Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PatozoidMenu"
ScreenGui.IgnoreGuiInset = true -- Faz cobrir a tela toda
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

-- Container Principal (Fundo da Tela Toda)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(1, 0, 1, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(173, 216, 230) -- Azul Bebê
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

-- Gradiente para o Fundo (Azul Bebê para tons mais escuros)
local UIGradient = Instance.new("UIGradient")
UIGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(173, 216, 230)), -- Azul Bebê
    ColorSequenceKeypoint.new(0.6, Color3.fromRGB(100, 149, 237)), -- Azul Cornflower
    ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 10, 10)) -- Preto
})
UIGradient.Rotation = 45
UIGradient.Parent = MainFrame

-- Camada de Elementos Animados (Fundo)
local AnimationContainer = Instance.new("Frame")
AnimationContainer.Name = "AnimationContainer"
AnimationContainer.Size = UDim2.new(1, 0, 1, 0)
AnimationContainer.BackgroundTransparency = 1
AnimationContainer.Parent = MainFrame

-- Função para criar elementos flutuantes no fundo
local function createFloatingElement()
    local element = Instance.new("Frame")
    element.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    element.BackgroundTransparency = 0.85
    local size = math.random(20, 100)
    element.Size = UDim2.new(0, size, 0, size)
    element.Position = UDim2.new(math.random(), 0, 1.1, 0)
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0.3, 0)
    corner.Parent = element
    
    element.Parent = AnimationContainer
    
    local duration = math.random(8, 15)
    local tween = TweenService:Create(element, TweenInfo.new(duration, Enum.EasingStyle.Linear), {
        Position = UDim2.new(element.Position.X.Scale, math.random(-100, 100), -0.2, 0),
        Rotation = math.random(-360, 360),
        BackgroundTransparency = 1
    })
    
    tween:Play()
    tween.Completed:Connect(function() element:Destroy() end)
end

task.spawn(function()
    while true do
        createFloatingElement()
        task.wait(1.5)
    end
end)

-- Painel Central (Onde fica o sistema de Key)
local ContentPanel = Instance.new("Frame")
ContentPanel.Name = "ContentPanel"
ContentPanel.Size = UDim2.new(0, 360, 0, 300)
ContentPanel.Position = UDim2.new(0.5, 0, 0.5, 0)
ContentPanel.AnchorPoint = Vector2.new(0.5, 0.5)
ContentPanel.BackgroundColor3 = Color3.fromRGB(20, 20, 20) -- Preto Suave
ContentPanel.BackgroundTransparency = 0.1
ContentPanel.Parent = MainFrame

local PanelCorner = Instance.new("UICorner")
PanelCorner.CornerRadius = UDim.new(0, 20)
PanelCorner.Parent = ContentPanel

local PanelStroke = Instance.new("UIStroke")
PanelStroke.Color = Color3.fromRGB(173, 216, 230) -- Borda Azul Bebê
PanelStroke.Thickness = 3
PanelStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
PanelStroke.Parent = ContentPanel

-- Título: PATOZOID MENU
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Text = "PATOZOID MENU"
Title.Size = UDim2.new(1, 0, 0, 70)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextColor3 = Color3.fromRGB(173, 216, 230) -- Azul Bebê
Title.TextSize = 28
Title.Parent = ContentPanel

-- Entrada da Key (TextBox Aperfeiçoada)
local keyBox = Instance.new("TextBox")
keyBox.Name = "KeyBox"
keyBox.Size = UDim2.new(0.85, 0, 0, 55)
keyBox.Position = UDim2.new(0.5, 0, 0.4, 0)
keyBox.AnchorPoint = Vector2.new(0.5, 0.5)
keyBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
keyBox.Text = ""
keyBox.PlaceholderText = "INSIRA SUA KEY AQUI..."
keyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
keyBox.Font = Enum.Font.GothamSemibold
keyBox.TextSize = 16
keyBox.Parent = ContentPanel

local keyCorner = Instance.new("UICorner")
keyCorner.CornerRadius = UDim.new(0, 10)
keyCorner.Parent = keyBox

local keyStroke = Instance.new("UIStroke")
keyStroke.Color = Color3.fromRGB(173, 216, 230)
keyStroke.Thickness = 1
keyStroke.Transparency = 0.6
keyStroke.Parent = keyBox

-- Botão de Validação (Aperfeiçoado)
local validateButton = Instance.new("TextButton")
validateButton.Name = "ValidateButton"
validateButton.Size = UDim2.new(0.85, 0, 0, 50)
validateButton.Position = UDim2.new(0.5, 0, 0.65, 0)
validateButton.AnchorPoint = Vector2.new(0.5, 0.5)
validateButton.BackgroundColor3 = Color3.fromRGB(173, 216, 230) -- Azul Bebê
validateButton.Text = "VALIDAR ACESSO"
validateButton.TextSize = 18
validateButton.Font = Enum.Font.GothamBold
validateButton.TextColor3 = Color3.fromRGB(20, 20, 20) -- Texto Preto
validateButton.AutoButtonColor = false
validateButton.Parent = ContentPanel

local validateCorner = Instance.new("UICorner")
validateCorner.CornerRadius = UDim.new(0, 10)
validateCorner.Parent = validateButton

-- Efeito Hover no Botão
validateButton.MouseEnter:Connect(function()
    TweenService:Create(validateButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(255, 255, 255)}):Play()
end)
validateButton.MouseLeave:Connect(function()
    TweenService:Create(validateButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(173, 216, 230)}):Play()
end)

-- Status de Validação
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(0.9, 0, 0, 40)
statusLabel.Position = UDim2.new(0.5, 0, 0.85, 0)
statusLabel.AnchorPoint = Vector2.new(0.5, 0.5)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Aguardando Key..."
statusLabel.TextColor3 = Color3.fromRGB(173, 216, 230)
statusLabel.Font = Enum.Font.Gotham
statusLabel.TextSize = 14
statusLabel.Parent = ContentPanel

-- ╔════════════════════════════════════════════════════════════╗
-- ║                  FUNÇÕES DO SISTEMA                        ║
-- ╚════════════════════════════════════════════════════════════╗

local function loadGameScript()
    local gameId = game.GameId
    if GAME_SCRIPTS[gameId] then
        loadstring(GAME_SCRIPTS[gameId])()
    else
        loadstring(DEFAULT_LOADSTRING)()
    end
end

local function verifyKey()
    local userKey = keyBox.Text
    if KEYS[player.Name] and KEYS[player.Name] == userKey or userKey == KEYS["ADMIN"] then
        statusLabel.Text = "✓ ACESSO LIBERADO! CARREGANDO..."
        statusLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
        validateButton.Enabled = false
        
        -- Animação de saída antes de carregar
        TweenService:Create(ContentPanel, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
            Size = UDim2.new(0, 0, 0, 0),
            BackgroundTransparency = 1
        }):Play()
        
        wait(1)
        ScreenGui:Destroy()
        loadGameScript()
    else
        statusLabel.Text = "✗ KEY INVÁLIDA OU INCORRETA!"
        statusLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
        
        -- Shake effect
        local originalPos = ContentPanel.Position
        for i = 1, 5 do
            ContentPanel.Position = originalPos + UDim2.new(0, math.random(-5, 5), 0, 0)
            wait(0.05)
        end
        ContentPanel.Position = originalPos
    end
end

validateButton.MouseButton1Click:Connect(verifyKey)

-- Animação de Entrada
ContentPanel.Size = UDim2.new(0, 0, 0, 0)
TweenService:Create(ContentPanel, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size = UDim2.new(0, 360, 0, 300)
}):Play()

print("PATOZOID MENU v2 Carregado com Sucesso!")
