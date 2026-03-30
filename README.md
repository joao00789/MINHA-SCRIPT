# MINHA-SCRIPT
--[[
    ███╗   ██╗██╗ ██████╗ ██╗  ██╗████████╗███████╗ █████╗ ██╗     ██╗
    ████╗  ██║██║██╔════╝ ██║  ██║╚══██╔══╝██╔════╝██╔══██╗██║     ██║
    ██╔██╗ ██║██║██║  ███╗███████║   ██║   █████╗  ███████║██║     ██║
    ██║╚██╗██║██║██║   ██║██╔══██║   ██║   ██╔══╝  ██╔══██║██║     ██║
    ██║ ╚████║██║╚██████╔╝██║  ██║   ██║   ██║     ██║  ██║███████╗███████╗
    ╚═╝  ╚═══╝╚═╝ ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚═╝     ╚═╝  ╚═╝╚══════╝╚══════╝

    NightFall Script v5.0 — PROFESSIONAL EDITION
    Compatible: Synapse X, Delta, Solara, Fluxus, Arceus X, Hydrogen, AWP
    Features: ESP Aura RGB | Silent Aim | Aimbot | SpinBot | Fly | Speed
]]

-- ══════════════════════════════════════════════════════
--                  CONFIGURAÇÃO
-- ══════════════════════════════════════════════════════
-- Substitua pela URL do seu servidor (ex: https://meusite.replit.app)
local SERVER_URL = "https://SEU_SERVIDOR_AQUI"

-- ══════════════════════════════════════════════════════
--               PROTEÇÃO ANTI-EXECUÇÃO DUPLA
-- ══════════════════════════════════════════════════════
local ENV_KEY = "__NightFall_v5__"
if rawget(_G, ENV_KEY) then return end
rawset(_G, ENV_KEY, true)
pcall(function()
    if getgenv then
        if getgenv()[ENV_KEY] then return end
        getgenv()[ENV_KEY] = true
    end
end)

-- ══════════════════════════════════════════════════════
--               IDENTITY / PROTEÇÃO
-- ══════════════════════════════════════════════════════
pcall(function() setidentity(8) end)
pcall(function() if syn then syn.set_thread_identity(8) end end)
pcall(function() if identifyexecutor then identifyexecutor() end end)

-- ══════════════════════════════════════════════════════
--               FUNÇÃO HTTP UNIVERSAL
--  Compatível com: request, syn.request, http.request,
--  fluxus_request, KRNL_Loaded, Arceus X, Delta, etc.
-- ══════════════════════════════════════════════════════
local function httpGet(url)
    local httpFunc = request or
        (syn and syn.request) or
        (http and http.request) or
        (fluxus and fluxus.request) or
        (KRNL_Loaded and http_request) or
        http_request or
        (function(cfg)
            -- Último recurso: HttpService (só funciona em alguns executores)
            local hs = game:GetService("HttpService")
            return { Body = hs:GetAsync(cfg.Url), StatusCode = 200 }
        end)
    local ok, result = pcall(httpFunc, {
        Url = url,
        Method = "GET",
    })
    if ok and result then return result.Body end
    return nil
end

local function httpPost(url, body)
    local hs = game:GetService("HttpService")
    local encoded = hs:JSONEncode(body)
    local httpFunc = request or
        (syn and syn.request) or
        (http and http.request) or
        (fluxus and fluxus.request) or
        http_request or
        nil
    if not httpFunc then return nil end
    local ok, result = pcall(httpFunc, {
        Url = url,
        Method = "POST",
        Headers = { ["Content-Type"] = "application/json" },
        Body = encoded,
    })
    if not ok or not result then return nil end
    local okJ, parsed = pcall(function() return hs:JSONDecode(result.Body) end)
    if okJ then return parsed end
    return nil
end

-- ══════════════════════════════════════════════════════
--               SERVIÇOS ROBLOX
-- ══════════════════════════════════════════════════════
local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UIS              = game:GetService("UserInputService")
local HttpService      = game:GetService("HttpService")
local Camera           = workspace.CurrentCamera
local LP               = Players.LocalPlayer

-- Detecção de plataforma
local isMobile = UIS.TouchEnabled and not UIS.KeyboardEnabled

-- Wrapper seguro para funções
local function safe(fn, ...)
    local ok, err = pcall(fn, ...)
    if not ok then
        -- silencia erros internos
    end
end

-- ══════════════════════════════════════════════════════
--               TELA DE KEY
-- ══════════════════════════════════════════════════════
local keyValidated = false

-- Monta a GUI de key
local KeyGui = Instance.new("ScreenGui")
KeyGui.Name = "NF_Key_" .. tostring(math.random(100000, 999999))
KeyGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
KeyGui.ResetOnSpawn = false
KeyGui.DisplayOrder = 999

-- Tenta parente privilegiado
local guiParented = false
pcall(function()
    KeyGui.Parent = game:GetService("CoreGui")
    guiParented = true
end)
if not guiParented then
    local pg = LP:FindFirstChildOfClass("PlayerGui") or LP:WaitForChild("PlayerGui", 5)
    if pg then KeyGui.Parent = pg end
end

-- Proteção syn
pcall(function() if syn and syn.protect_gui then syn.protect_gui(KeyGui) end end)

-- Fundo escuro
local BG = Instance.new("Frame")
BG.Name = "BG"
BG.Size = UDim2.new(1, 0, 1, 0)
BG.Position = UDim2.new(0, 0, 0, 0)
BG.BackgroundColor3 = Color3.fromRGB(3, 2, 10)
BG.BackgroundTransparency = 0.15
BG.BorderSizePixel = 0
BG.ZIndex = 10
BG.Parent = KeyGui

-- Card central
local Card = Instance.new("Frame")
Card.Size = UDim2.new(0, 440, 0, 320)
Card.Position = UDim2.new(0.5, -220, 0.5, -160)
Card.BackgroundColor3 = Color3.fromRGB(8, 4, 25)
Card.BorderSizePixel = 0
Card.ZIndex = 11
Card.Parent = BG
Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 14)
local CardStroke = Instance.new("UIStroke", Card)
CardStroke.Color = Color3.fromRGB(140, 0, 255)
CardStroke.Thickness = 2
CardStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

-- Título animado
local Logo = Instance.new("TextLabel")
Logo.Size = UDim2.new(1, -20, 0, 55)
Logo.Position = UDim2.new(0, 10, 0, 12)
Logo.BackgroundTransparency = 1
Logo.Text = "◈  NIGHTFALL SCRIPT  ◈"
Logo.TextColor3 = Color3.fromRGB(140, 0, 255)
Logo.TextScaled = true
Logo.Font = Enum.Font.GothamBold
Logo.ZIndex = 12
Logo.Parent = Card

-- Subtítulo
local Sub = Instance.new("TextLabel")
Sub.Size = UDim2.new(1, -30, 0, 22)
Sub.Position = UDim2.new(0, 15, 0, 70)
Sub.BackgroundTransparency = 1
Sub.Text = "Insira sua key de acesso"
Sub.TextColor3 = Color3.fromRGB(180, 120, 255)
Sub.TextScaled = true
Sub.Font = Enum.Font.Gotham
Sub.ZIndex = 12
Sub.Parent = Card

-- Campo de key
local KeyBox = Instance.new("TextBox")
KeyBox.Size = UDim2.new(1, -30, 0, 48)
KeyBox.Position = UDim2.new(0, 15, 0, 100)
KeyBox.BackgroundColor3 = Color3.fromRGB(15, 8, 40)
KeyBox.TextColor3 = Color3.fromRGB(220, 60, 160)
KeyBox.PlaceholderText = "NF-XXXXXXXX-XXXXXXXX-XXXXXXXX"
KeyBox.PlaceholderColor3 = Color3.fromRGB(100, 60, 120)
KeyBox.Text = ""
KeyBox.TextScaled = true
KeyBox.Font = Enum.Font.Code
KeyBox.ClearTextOnFocus = false
KeyBox.ZIndex = 12
KeyBox.Parent = Card
Instance.new("UICorner", KeyBox).CornerRadius = UDim.new(0, 8)
local BoxStroke = Instance.new("UIStroke", KeyBox)
BoxStroke.Color = Color3.fromRGB(100, 0, 200)
BoxStroke.Thickness = 1.5

-- Status
local StatusLbl = Instance.new("TextLabel")
StatusLbl.Size = UDim2.new(1, -30, 0, 28)
StatusLbl.Position = UDim2.new(0, 15, 0, 158)
StatusLbl.BackgroundTransparency = 1
StatusLbl.Text = ""
StatusLbl.TextColor3 = Color3.fromRGB(255, 80, 80)
StatusLbl.TextScaled = true
StatusLbl.Font = Enum.Font.Gotham
StatusLbl.ZIndex = 12
StatusLbl.Parent = Card

-- Botão validar
local BtnValidate = Instance.new("TextButton")
BtnValidate.Size = UDim2.new(1, -30, 0, 52)
BtnValidate.Position = UDim2.new(0, 15, 0, 194)
BtnValidate.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
BtnValidate.TextColor3 = Color3.fromRGB(255, 255, 255)
BtnValidate.Text = "▶  VALIDAR E ENTRAR"
BtnValidate.TextScaled = true
BtnValidate.Font = Enum.Font.GothamBold
BtnValidate.ZIndex = 12
BtnValidate.AutoButtonColor = true
BtnValidate.Parent = Card
Instance.new("UICorner", BtnValidate).CornerRadius = UDim.new(0, 8)

-- Link do site
local SiteLbl = Instance.new("TextLabel")
SiteLbl.Size = UDim2.new(1, -30, 0, 24)
SiteLbl.Position = UDim2.new(0, 15, 0, 254)
SiteLbl.BackgroundTransparency = 1
SiteLbl.Text = "🔑 Compre sua key em: " .. SERVER_URL
SiteLbl.TextColor3 = Color3.fromRGB(140, 0, 255)
SiteLbl.TextScaled = true
SiteLbl.Font = Enum.Font.Gotham
SiteLbl.ZIndex = 12
SiteLbl.Parent = Card

-- Animação RGB no logo e borda
local hueAnim = 0
local animConn = RunService.Heartbeat:Connect(function()
    hueAnim = (hueAnim + 0.005) % 1
    local c = Color3.fromHSV(hueAnim, 1, 1)
    Logo.TextColor3 = c
    CardStroke.Color = c
end)

-- Lógica de validação
local function doValidate()
    local k = KeyBox.Text:match("^%s*(.-)%s*$")
    if not k or k == "" then
        StatusLbl.Text = "⚠ Digite sua key primeiro"
        StatusLbl.TextColor3 = Color3.fromRGB(255, 200, 0)
        return
    end
    k = k:upper()
    BtnValidate.Text = "⏳ Validando..."
    BtnValidate.BackgroundColor3 = Color3.fromRGB(80, 20, 140)
    StatusLbl.Text = ""

    -- Tenta validar via HTTP do executor
    local result = httpPost(SERVER_URL .. "/api/keys/validate", { key = k })

    if result == nil then
        -- Sem conexão com servidor: avisa mas permite continuar (modo offline)
        StatusLbl.Text = "⚠ Sem conexão com servidor"
        StatusLbl.TextColor3 = Color3.fromRGB(255, 165, 0)
        task.wait(1.5)
        -- Aceita a key localmente para não bloquear o usuário
        keyValidated = true
        BtnValidate.Text = "✓ ABRINDO NIGHTFALL..."
        task.wait(1)
        animConn:Disconnect()
        KeyGui:Destroy()
        return
    end

    if result.valid then
        StatusLbl.Text = "✓ " .. tostring(result.message or "Key válida!")
        StatusLbl.TextColor3 = Color3.fromRGB(60, 255, 120)
        BtnValidate.Text = "✓ ABRINDO NIGHTFALL..."
        keyValidated = true
        task.wait(1.2)
        animConn:Disconnect()
        KeyGui:Destroy()
    else
        StatusLbl.Text = "✗ " .. tostring(result.message or "Key inválida")
        StatusLbl.TextColor3 = Color3.fromRGB(255, 80, 80)
        BtnValidate.Text = "▶  VALIDAR E ENTRAR"
        BtnValidate.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
    end
end

BtnValidate.MouseButton1Click:Connect(doValidate)
KeyBox.FocusLost:Connect(function(enter)
    if enter then doValidate() end
end)

-- Espera validação
repeat task.wait(0.1) until keyValidated

-- ══════════════════════════════════════════════════════
--               CARREGA RAYFIELD UI
-- ══════════════════════════════════════════════════════
local Rayfield
local ok, err = pcall(function()
    Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
end)
if not ok or not Rayfield then
    -- Fallback: tenta via executor
    ok, err = pcall(function()
        local src = httpGet("https://sirius.menu/rayfield")
        if src then Rayfield = loadstring(src)() end
    end)
end
if not Rayfield then
    -- Continua sem Rayfield (modo sem UI)
    warn("[NightFall] Não foi possível carregar Rayfield: " .. tostring(err))
    return
end

-- ══════════════════════════════════════════════════════
--               CONFIGURAÇÃO GLOBAL
-- ══════════════════════════════════════════════════════
local CFG = {
    -- ESP
    ESPAura = false, ESPAuraSize = 3,
    ESPBox = false, ESPBoxThick = 1,
    ESPName = false, ESPDist = false,
    ESPHead = false,
    ESPLine = false, ESPLineThick = 2,
    -- Aimbot
    Aimbot = false, AimLock = false,
    AimKill = false, SilentAim = false,
    AimFOV = 150, AimSmooth = 0.12,
    ShowFOV = true,
    -- Extras
    SpinBot = false, SpinSpeed = 5,
    Speed = false, SpeedVal = 16,
    Fly = false,
    InfJump = false, AutoJump = false,
}

-- ══════════════════════════════════════════════════════
--               HELPERS
-- ══════════════════════════════════════════════════════
local function getChar(p) return p and p.Character end
local function getRoot(p)
    local c = getChar(p)
    return c and c:FindFirstChild("HumanoidRootPart")
end
local function getHead(p)
    local c = getChar(p)
    return c and c:FindFirstChild("Head")
end
local function getHum(p)
    local c = getChar(p)
    return c and c:FindFirstChildOfClass("Humanoid")
end
local function isAlive(p)
    local h = getHum(p)
    return h and h.Health > 0
end
local function getEnemies()
    local t = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP and isAlive(p) then t[#t+1] = p end
    end
    return t
end
local function w2v(pos)
    local v, onScreen = Camera:WorldToViewportPoint(pos)
    return Vector2.new(v.X, v.Y), v.Z > 0 and onScreen
end
local function dist(p)
    local a, b = getRoot(LP), getRoot(p)
    return (a and b) and (a.Position - b.Position).Magnitude or math.huge
end
local function closestEnemy()
    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local best, bestD = nil, math.huge
    for _, p in ipairs(getEnemies()) do
        local r = getRoot(p)
        if r then
            local sp, onS = w2v(r.Position)
            if onS then
                local d = (sp - center).Magnitude
                if d < CFG.AimFOV and d < bestD then
                    bestD = d
                    best = p
                end
            end
        end
    end
    return best
end

-- ══════════════════════════════════════════════════════
--               ESP DRAWINGS
-- ══════════════════════════════════════════════════════
local ESP = {}

local function removeESP(p)
    if ESP[p] then
        for _, o in pairs(ESP[p]) do pcall(function() o:Remove() end) end
        ESP[p] = nil
    end
end

local function makeESP(p)
    removeESP(p)
    local function newDraw(type, props)
        local d = Drawing.new(type)
        for k,v in pairs(props) do d[k] = v end
        return d
    end
    ESP[p] = {
        Aura  = newDraw("Circle",  {Visible=false, Filled=false, Thickness=3, ZIndex=5}),
        BoxT  = newDraw("Line",    {Visible=false, Thickness=1, ZIndex=5}),
        BoxB  = newDraw("Line",    {Visible=false, Thickness=1, ZIndex=5}),
        BoxL  = newDraw("Line",    {Visible=false, Thickness=1, ZIndex=5}),
        BoxR  = newDraw("Line",    {Visible=false, Thickness=1, ZIndex=5}),
        Name  = newDraw("Text",    {Visible=false, Size=14, Center=true, Outline=true, Font=Drawing.Fonts.UI, ZIndex=5}),
        Dist  = newDraw("Text",    {Visible=false, Size=12, Center=true, Outline=true, Font=Drawing.Fonts.UI, ZIndex=5}),
        Head  = newDraw("Circle",  {Visible=false, Filled=false, Thickness=1.5, ZIndex=5}),
        Line  = newDraw("Line",    {Visible=false, Thickness=2, ZIndex=4}),
    }
end

for _, p in ipairs(Players:GetPlayers()) do
    if p ~= LP then makeESP(p) end
end
Players.PlayerAdded:Connect(function(p) task.wait(0.5); makeESP(p) end)
Players.PlayerRemoving:Connect(removeESP)

local rgbH = 0
local function tickESP()
    rgbH = (rgbH + 0.007) % 1
    local col = Color3.fromHSV(rgbH, 1, 1)
    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)

    for _, p in ipairs(Players:GetPlayers()) do
        if p == LP then continue end
        local obj = ESP[p]; if not obj then continue end
        local char = getChar(p)
        local root = getRoot(p)
        local hum  = getHum(p)
        local alive = char and root and hum and hum.Health > 0

        -- AURA RGB (círculo brilhante visível através de paredes)
        if CFG.ESPAura and alive then
            local sp, onS = w2v(root.Position)
            local d = dist(p)
            obj.Aura.Visible  = onS
            obj.Aura.Position = sp
            obj.Aura.Radius   = math.clamp(1800/d, 18, 220)
            obj.Aura.Color    = col
            obj.Aura.Thickness = CFG.ESPAuraSize
        else obj.Aura.Visible = false end

        -- BOX RGB (8 pontos projetados em 2D)
        if CFG.ESPBox and alive then
            local cf = root.CFrame
            local sx, sy, sz = 2.4, 5.8, 1
            local corners = {
                cf * Vector3.new(-sx/2,  sy/2, -sz/2), cf * Vector3.new( sx/2,  sy/2, -sz/2),
                cf * Vector3.new(-sx/2, -sy/2, -sz/2), cf * Vector3.new( sx/2, -sy/2, -sz/2),
                cf * Vector3.new(-sx/2,  sy/2,  sz/2), cf * Vector3.new( sx/2,  sy/2,  sz/2),
                cf * Vector3.new(-sx/2, -sy/2,  sz/2), cf * Vector3.new( sx/2, -sy/2,  sz/2),
            }
            local minX, minY, maxX, maxY = math.huge, math.huge, -math.huge, -math.huge
            for _, corner in ipairs(corners) do
                local sv = w2v(corner)
                if sv.X < minX then minX = sv.X end; if sv.Y < minY then minY = sv.Y end
                if sv.X > maxX then maxX = sv.X end; if sv.Y > maxY then maxY = sv.Y end
            end
            local function setLine(l, fx, fy, tx, ty)
                l.From = Vector2.new(fx, fy); l.To = Vector2.new(tx, ty)
                l.Color = col; l.Thickness = CFG.ESPBoxThick; l.Visible = true
            end
            setLine(obj.BoxT, minX, minY, maxX, minY)
            setLine(obj.BoxB, minX, maxY, maxX, maxY)
            setLine(obj.BoxL, minX, minY, minX, maxY)
            setLine(obj.BoxR, maxX, minY, maxX, maxY)
        else
            obj.BoxT.Visible=false; obj.BoxB.Visible=false
            obj.BoxL.Visible=false; obj.BoxR.Visible=false
        end

        -- NOME
        if CFG.ESPName and alive then
            local sp, onS = w2v(root.Position + Vector3.new(0, 4, 0))
            obj.Name.Visible = onS; obj.Name.Position = sp
            obj.Name.Text = p.DisplayName; obj.Name.Color = col
        else obj.Name.Visible = false end

        -- DISTÂNCIA
        if CFG.ESPDist and alive then
            local sp, onS = w2v(root.Position - Vector3.new(0, 3.5, 0))
            obj.Dist.Visible = onS; obj.Dist.Position = sp
            obj.Dist.Text = math.floor(dist(p)) .. "m"; obj.Dist.Color = col
        else obj.Dist.Visible = false end

        -- CABEÇA
        if CFG.ESPHead and alive then
            local head = getHead(p)
            if head then
                local sp, onS = w2v(head.Position)
                obj.Head.Visible = onS; obj.Head.Position = sp
                obj.Head.Radius = 14; obj.Head.Color = col
            end
        else obj.Head.Visible = false end

        -- TRACER (linha do centro da tela ao inimigo)
        if CFG.ESPLine and alive then
            local sp, onS = w2v(root.Position)
            obj.Line.Visible = onS; obj.Line.From = center; obj.Line.To = sp
            obj.Line.Color = col; obj.Line.Thickness = CFG.ESPLineThick
        else obj.Line.Visible = false end
    end
end

-- ══════════════════════════════════════════════════════
--               CÍRCULO DE FOV
-- ══════════════════════════════════════════════════════
local FOVCircle = Drawing.new("Circle")
FOVCircle.Filled = false
FOVCircle.Color = Color3.fromRGB(255, 255, 255)
FOVCircle.Thickness = 1.2
FOVCircle.Transparency = 0.5
FOVCircle.Visible = false

-- ══════════════════════════════════════════════════════
--               AIMBOT + SILENT AIM
-- ══════════════════════════════════════════════════════
local restoreCF = nil
local silentFrame = false

local function tickAimbot()
    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    FOVCircle.Position = center
    FOVCircle.Radius   = CFG.AimFOV
    FOVCircle.Visible  = CFG.ShowFOV

    local target = closestEnemy()
    if not target then return end
    local root = getRoot(target); if not root then return end
    local head = getHead(target)

    -- Previsão de movimento baseada na velocidade
    local vel = pcall(function() return root.AssemblyLinearVelocity end) and root.AssemblyLinearVelocity or Vector3.zero
    local predict = 0.07
    local aimPos = head and (head.Position + vel * predict) or (root.Position + vel * predict)
    local targetCF = CFrame.new(Camera.CFrame.Position, aimPos)

    if CFG.AimKill then
        Camera.CFrame = targetCF
    elseif CFG.AimLock then
        Camera.CFrame = Camera.CFrame:Lerp(targetCF, CFG.AimSmooth * 2)
    elseif CFG.Aimbot then
        if UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) or
           (isMobile and UIS.TouchEnabled) then
            Camera.CFrame = Camera.CFrame:Lerp
