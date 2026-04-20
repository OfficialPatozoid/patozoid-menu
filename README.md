-- BLVCK MENU - Key System Universal (Delta, Xeno, Hydrogen e Mobile)
-- Feito por Copilot

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local KEYS = {
    ["PLAYER1"] = "123456",
    ["PLAYER2"] = "654321",
    ["ADMIN"]    = "SUPERADMIN",
}
local GAME_RAWS = {
    ["123456"]   = "raw.githubusercontent.com/seuusuario/seurepo/main/script1.lua",
    ["654321"]   = "raw.githubusercontent.com/seuusuario/seurepo/main/script2.lua",
    ["SUPERADMIN"] = "raw.githubusercontent.com/seuusuario/seurepo/main/admin.lua"
}

-- Remove GUIs antigos
pcall(function() PlayerGui.BLVCK_MENU:Destroy() end)

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BLVCK_MENU"
ScreenGui.Parent = PlayerGui
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false

-- Fundo preto
local BG = Instance.new("Frame")
BG.Size = UDim2.new(1, 0, 1, 0)
BG.Position = UDim2.new(0, 0, 0, 0)
BG.BackgroundColor3 = Color3.new(0,0,0)
BG.Parent = ScreenGui

-- Elementos móveis brancos
local particleList = {}
for i = 1, 18 do
    local f = Instance.new("Frame")
    f.Size = UDim2.new(0,math.random(6,16),0,math.random(6,16))
    f.Position = UDim2.new(math.random(),0,math.random(),0)
    f.BackgroundTransparency = math.random(15,80)/100
    f.BorderSizePixel = 0
    f.BackgroundColor3 = Color3.new(1,1,1)
    f.ZIndex = 3
    f.Parent = BG
    table.insert(particleList, {frame=f,dx=(math.random()-0.5)/300,dy=(math.random()-0.5)/300})
end

game:GetService("RunService").RenderStepped:Connect(function()
    for _,p in ipairs(particleList) do
        local pos = p.frame.Position
        local nx, ny = pos.X.Scale + p.dx, pos.Y.Scale + p.dy
        if nx < 0 or nx > 1 then p.dx = -p.dx end
        if ny < 0 or ny > 1 then p.dy = -p.dy end
        p.frame.Position = UDim2.new(math.clamp(nx,0,1),0,math.clamp(ny,0,1),0)
    end
end)

-- Caixa
local Holder = Instance.new("Frame", ScreenGui)
Holder.Size = UDim2.new(0,340,0,260)
Holder.Position = UDim2.new(0.5,-170,0.5,-130)
Holder.BackgroundColor3 = Color3.fromRGB(10,10,10)
Holder.BorderSizePixel = 0
Holder.ZIndex = 10
local UIStroke = Instance.new("UIStroke",Holder)
UIStroke.Color = Color3.new(1,1,1)
UIStroke.Thickness = 2

local Title = Instance.new("TextLabel",Holder)
Title.Size = UDim2.new(1,0,0,48)
Title.Position = UDim2.new(0,0,0,12)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.new(1,1,1)
Title.Text = "BLVCK MENU"
Title.Font = Enum.Font.GothamBold
Title.TextScaled = true
Title.ZIndex = 20

local Desc = Instance.new("TextLabel",Holder)
Desc.Size = UDim2.new(1,-36,0,18)
Desc.Position = UDim2.new(0,18,0,60)
Desc.BackgroundTransparency = 1
Desc.TextColor3 = Color3.fromRGB(180,180,180)
Desc.Text = "Insira sua KEY para liberar o menu"
Desc.Font = Enum.Font.Gotham
Desc.TextScaled = true
Desc.ZIndex = 20

local Input = Instance.new("TextBox",Holder)
Input.Size = UDim2.new(1,-36,0,34)
Input.Position = UDim2.new(0,18,0,88)
Input.BackgroundColor3 = Color3.fromRGB(20,20,20)
Input.TextColor3 = Color3.fromRGB(255,255,255)
Input.PlaceholderText = "Sua key aqui"
Input.Font = Enum.Font.Gotham
Input.TextScaled = true
Input.ZIndex = 20
Input.ClearTextOnFocus = false

local Button = Instance.new("TextButton",Holder)
Button.Size = UDim2.new(1,-36,0,36)
Button.Position = UDim2.new(0,18,0,136)
Button.BackgroundColor3 = Color3.fromRGB(255,255,255)
Button.TextColor3 = Color3.fromRGB(0,0,0)
Button.Font = Enum.Font.GothamBold
Button.Text = "VERIFICAR"
Button.TextScaled = true
Button.ZIndex = 20

local Status = Instance.new("TextLabel",Holder)
Status.Size = UDim2.new(1,-36,0,22)
Status.Position = UDim2.new(0,18,0,186)
Status.BackgroundTransparency = 1
Status.TextColor3 = Color3.new(1,1,1)
Status.Text = ""
Status.Font = Enum.Font.Gotham
Status.TextScaled = true
Status.ZIndex = 20

-- loading
local function showLoading()
    Holder.Visible = false
    local loading = Instance.new("Frame")
    loading.Size = UDim2.new(0.55,0,0,80)
    loading.Position = UDim2.new(0.225,0,0.5,-40)
    loading.BackgroundTransparency = 0.15
    loading.BackgroundColor3 = Color3.fromRGB(0,0,0)
    loading.Parent = ScreenGui
    loading.ZIndex = 999

    local text = Instance.new("TextLabel",loading)
    text.Size = UDim2.new(1,0,1,0)
    text.TextColor3 = Color3.new(1,1,1)
    text.Text = "Carregando..."
    text.TextScaled = true
    text.BackgroundTransparency = 1
    text.Font = Enum.Font.GothamBold
    text.ZIndex = 1000

    local barra = Instance.new("Frame",loading)
    barra.Position = UDim2.new(0.06,0,1,-14)
    barra.Size = UDim2.new(0,0,0,4)
    barra.BackgroundColor3 = Color3.new(1,1,1)
    barra.BorderSizePixel = 0
    barra.ZIndex = 1000

    for i=1,40 do
        barra.Size = UDim2.new(i/40 * 0.88,0,0,4)
        wait(0.018)
    end
end

local function verifyKey(key)
    for user, v in pairs(KEYS) do
        if v == key then return user end
    end
    return nil
end

Button.MouseButton1Click:Connect(function()
    local key = Input.Text or ""
    local tipo = verifyKey(key)
    if not tipo then
        Status.TextColor3 = Color3.new(1,0,0)
        Status.Text = "Key inválida!"
        return
    end

    Status.TextColor3 = Color3.new(0,.7,0)
    Status.Text = "Key correta!"
    showLoading()
    wait(0.2)
    local url = GAME_RAWS[key]
    if url then
        local ok, err = pcall(function()
            -- No Delta, Xeno, Hydrogen, Studio etc: http/https aceito
            loadstring(game:HttpGet("https://"..url,true))()
        end)
        if not ok then
            Status.Visible = true
            Status.TextColor3 = Color3.fromRGB(255,110,0)
            Status.Text = "Erro ao executar Raw!"
            wait(3)
            ScreenGui:Destroy()
        else
            wait(0.3)
            ScreenGui:Destroy()
        end
    else
        Status.TextColor3 = Color3.fromRGB(255,255,0)
        Status.Text = "RAW não configurado para essa key."
    end
end)

Input.FocusLost:Connect(function(enter)
    if enter then Button:Fire("MouseButton1Click") end
end)
