-- BLVCK MENU - Key System for Roblox (Xeno & Mobile)
-- Full-screen key verification with animated background
-- Compatible with Xeno executor and mobile devices

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

-- ==================== KEY DATABASE ====================
local KEYS = {
    ["PLAYER1"] = "123456",
    ["PLAYER2"] = "654321",
    ["ADMIN"] = "SUPERADMIN"
}

-- ==================== GAME CONFIGURATION ====================
local GAME_CONFIG = {
    ["123456"] = {
        name = "Game 1",
        rawId = "YOUR_RAW_ID_1",
        gameId = 1234567890
    },
    ["654321"] = {
        name = "Game 2",
        rawId = "YOUR_RAW_ID_2",
        gameId = 0987654321
    },
    ["SUPERADMIN"] = {
        name = "Admin Game",
        rawId = "YOUR_RAW_ID_ADMIN",
        gameId = 1111111111
    }
}

-- ==================== UI SETUP ====================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BLVCK_MENU"
screenGui.ResetOnSpawn = false
screenGui.ZIndex = 1000
screenGui.IgnoreGuiInset = true

-- Parent to CoreGui for better compatibility
if RunService:IsRunning() then
    screenGui.Parent = game:GetService("CoreGui")
else
    screenGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
end

-- ==================== BACKGROUND SETUP ====================
local function createAnimatedBackground()
    local background = Instance.new("Frame")
    background.Name = "Background"
    background.Size = UDim2.new(1, 0, 1, 0)
    background.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Preto
    background.BorderSizePixel = 0
    background.ZIndex = 1
    background.Parent = screenGui

    -- Criar partículas/elementos móveis
    local particles = {}
    for i = 1, 20 do
        local particle = Instance.new("Frame")
        particle.Name = "Particle_" .. i
        particle.Size = UDim2.new(0, math.random(2, 8), 0, math.random(2, 8))
        particle.BackgroundColor3 = Color3.fromRGB(255, 255, 255) -- Branco
        particle.BorderSizePixel = 0
        particle.Position = UDim2.new(math.random(0, 100) / 100, 0, math.random(0, 100) / 100, 0)
        particle.ZIndex = 2
        particle.Parent = background

        -- Adicionar transparency para efeito mais suave
        particle.BackgroundTransparency = math.random(40, 70) / 100

        table.insert(particles, {
            frame = particle,
            speedX = (math.random(50, 150) / 100),
            speedY = (math.random(50, 150) / 100),
            opacity = particle.BackgroundTransparency
        })
    end

    -- Animar partículas
    local connection
    connection = RunService.RenderStepped:Connect(function()
        for _, particle in ipairs(particles) do
            local pos = particle.frame.Position
            local newX = pos.X.Scale + (particle.speedX / 1000)
            local newY = pos.Y.Scale + (particle.speedY / 1000)

            -- Reset position quando sair da tela
            if newX > 1 then newX = -0.05 end
            if newY > 1 then newY = -0.05 end
            if newX < -0.05 then newX = 1 end
            if newY < -0.05 then newY = 1 end

            particle.frame.Position = UDim2.new(newX, 0, newY, 0)
        end
    end)

    return background, connection
end

local background, particleConnection = createAnimatedBackground()

-- ==================== KEY INPUT UI ====================
local function createKeyInputUI()
    local container = Instance.new("Frame")
    container.Name = "KeyInputContainer"
    container.Size = UDim2.new(1, 0, 1, 0)
    container.BackgroundTransparency = 1
    container.ZIndex = 3
    container.Parent = screenGui

    -- Título
    local title = Instance.new("TextLabel")
    title.Name = "Title"
    title.Size = UDim2.new(1, 0, 0, 80)
    title.Position = UDim2.new(0, 0, 0.2, 0)
    title.BackgroundTransparency = 1
    title.Text = "BLVCK MENU"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 60
    title.Font = Enum.Font.GothamBold
    title.ZIndex = 4
    title.Parent = container

    -- Subtítulo
    local subtitle = Instance.new("TextLabel")
    subtitle.Name = "Subtitle"
    subtitle.Size = UDim2.new(1, 0, 0, 30)
    subtitle.Position = UDim2.new(0, 0, 0.28, 0)
    subtitle.BackgroundTransparency = 1
    subtitle.Text = "KEY VERIFICATION SYSTEM"
    subtitle.TextColor3 = Color3.fromRGB(200, 200, 200)
    subtitle.TextSize = 18
    subtitle.Font = Enum.Font.Gotham
    subtitle.ZIndex = 4
    subtitle.Parent = container

    -- Input Box Background
    local inputBoxBg = Instance.new("Frame")
    inputBoxBg.Name = "InputBoxBackground"
    inputBoxBg.Size = UDim2.new(0, 400, 0, 60)
    inputBoxBg.Position = UDim2.new(0.5, -200, 0.4, 0)
    inputBoxBg.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    inputBoxBg.BorderColor3 = Color3.fromRGB(255, 255, 255)
    inputBoxBg.BorderSizePixel = 2
    inputBoxBg.ZIndex = 4
    inputBoxBg.Parent = container

    -- Input TextBox
    local inputBox = Instance.new("TextBox")
    inputBox.Name = "KeyInput"
    inputBox.Size = UDim2.new(1, -20, 1, 0)
    inputBox.Position = UDim2.new(0, 10, 0, 0)
    inputBox.BackgroundTransparency = 1
    inputBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    inputBox.TextSize = 24
    inputBox.Font = Enum.Font.Gotham
    inputBox.PlaceholderText = "Enter your key..."
    inputBox.PlaceholderColor3 = Color3.fromRGB(100, 100, 100)
    inputBox.ClearTextOnFocus = false
    inputBox.ZIndex = 5
    inputBox.Parent = inputBoxBg

    -- Submit Button
    local submitBtn = Instance.new("TextButton")
    submitBtn.Name = "SubmitButton"
    submitBtn.Size = UDim2.new(0, 150, 0, 50)
    submitBtn.Position = UDim2.new(0.5, -75, 0.5, 0)
    submitBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    submitBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
    submitBtn.Text = "VERIFY"
    submitBtn.TextSize = 20
    submitBtn.Font = Enum.Font.GothamBold
    submitBtn.BorderSizePixel = 0
    submitBtn.ZIndex = 4
    submitBtn.Parent = container

    -- Status Label
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Name = "StatusLabel"
    statusLabel.Size = UDim2.new(1, 0, 0, 40)
    statusLabel.Position = UDim2.new(0, 0, 0.58, 0)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = ""
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.TextSize = 16
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.ZIndex = 4
    statusLabel.Parent = container

    return {
        container = container,
        inputBox = inputBox,
        submitBtn = submitBtn,
        statusLabel = statusLabel
    }
end

local keyUI = createKeyInputUI()

-- ==================== LOADING SCREEN ====================
local function createLoadingScreen()
    local loadingContainer = Instance.new("Frame")
    loadingContainer.Name = "LoadingContainer"
    loadingContainer.Size = UDim2.new(1, 0, 1, 0)
    loadingContainer.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    loadingContainer.BorderSizePixel = 0
    loadingContainer.ZIndex = 10
    loadingContainer.Visible = false
    loadingContainer.Parent = screenGui

    -- Loading Text
    local loadingText = Instance.new("TextLabel")
    loadingText.Name = "LoadingText"
    loadingText.Size = UDim2.new(1, 0, 0, 100)
    loadingText.Position = UDim2.new(0, 0, 0.35, 0)
    loadingText.BackgroundTransparency = 1
    loadingText.Text = "LOADING..."
    loadingText.TextColor3 = Color3.fromRGB(255, 255, 255)
    loadingText.TextSize = 50
    loadingText.Font = Enum.Font.GothamBold
    loadingText.ZIndex = 11
    loadingText.Parent = loadingContainer

    -- Loading Bar Background
    local loadingBarBg = Instance.new("Frame")
    loadingBarBg.Name = "LoadingBarBg"
    loadingBarBg.Size = UDim2.new(0, 300, 0, 10)
    loadingBarBg.Position = UDim2.new(0.5, -150, 0.5, 0)
    loadingBarBg.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    loadingBarBg.BorderSizePixel = 0
    loadingBarBg.ZIndex = 11
    loadingBarBg.Parent = loadingContainer

    -- Loading Bar Fill
    local loadingBarFill = Instance.new("Frame")
    loadingBarFill.Name = "LoadingBarFill"
    loadingBarFill.Size = UDim2.new(0, 0, 1, 0)
    loadingBarFill.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    loadingBarFill.BorderSizePixel = 0
    loadingBarFill.ZIndex = 12
    loadingBarFill.Parent = loadingBarBg

    -- Game Name
    local gameName = Instance.new("TextLabel")
    gameName.Name = "GameName"
    gameName.Size = UDim2.new(1, 0, 0, 40)
    gameName.Position = UDim2.new(0, 0, 0.55, 0)
    gameName.BackgroundTransparency = 1
    gameName.Text = ""
    gameName.TextColor3 = Color3.fromRGB(200, 200, 200)
    gameName.TextSize = 18
    gameName.Font = Enum.Font.Gotham
    gameName.ZIndex = 11
    gameName.Parent = loadingContainer

    return {
        container = loadingContainer,
        text = loadingText,
        barFill = loadingBarFill,
        gameName = gameName
    }
end

local loadingScreen = createLoadingScreen()

-- ==================== KEY VERIFICATION ====================
local function verifyKey(key)
    local foundKeyType = nil
    
    -- Procurar a key nos KEYS
    for keyType, keyValue in pairs(KEYS) do
        if keyValue == key then
            foundKeyType = keyType
            break
        end
    end

    if foundKeyType then
        return true, foundKeyType, GAME_CONFIG[key]
    else
        return false, nil, nil
    end
end

-- ==================== LOAD GAME ====================
local function loadGame(rawId, gameConfig)
    loadingScreen.container.Visible = true
    keyUI.container.Visible = false
    
    if gameConfig then
        loadingScreen.gameName.Text = "Loading: " .. gameConfig.name
    end

    -- Animar loading bar
    local progress = 0
    local loadingConnection
    loadingConnection = RunService.Heartbeat:Connect(function()
        progress = progress + math.random(5, 15)
        if progress > 100 then progress = 100 end
        
        loadingScreen.barFill.Size = UDim2.new(progress / 100, 0, 1, 0)

        if progress >= 100 then
            loadingConnection:Disconnect()
            
            -- Simular execução do raw/script
            wait(1)
            
            -- Executar o raw do jogo
            if rawId and rawId ~= "" then
                executeRaw(rawId)
            end

            -- Fechar menu após carregar
            wait(2)
            screenGui:Destroy()
        end
    end)
end

-- ==================== EXECUTE RAW ====================
local function executeRaw(rawId)
    -- Para Xeno executor
    if getgenv then
        pcall(function()
            load(game:HttpGet("https://raw.githubusercontent.com/YOUR_USERNAME/" .. rawId, true))()
        end)
    end

    -- Para mobile e outros ambientes
    pcall(function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/YOUR_USERNAME/" .. rawId))()
    end)
end

-- ==================== BUTTON EVENTS ====================
keyUI.submitBtn.MouseButton1Click:Connect(function()
    local key = keyUI.inputBox.Text
    
    if key == "" then
        keyUI.statusLabel.Text = "❌ Please enter a key!"
        keyUI.statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        return
    end

    local isValid, keyType, gameConfig = verifyKey(key)

    if isValid then
        keyUI.statusLabel.Text = "✓ Valid key! Loading..."
        keyUI.statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        
        wait(1)
        
        -- Carregar o jogo
        if gameConfig then
            loadGame(gameConfig.rawId, gameConfig)
        else
            keyUI.statusLabel.Text = "❌ Game configuration not found!"
            keyUI.statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        end
    else
        keyUI.statusLabel.Text = "❌ Invalid key!"
        keyUI.statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        keyUI.inputBox.Text = ""
    end
end)

-- Permitir pressionar Enter para enviar
keyUI.inputBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        keyUI.submitBtn:Fire("MouseButton1Click")
    end
end)

-- ==================== CLEANUP ====================
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.Escape then
        -- Adicione lógica de escape se necessário
    end
end)
