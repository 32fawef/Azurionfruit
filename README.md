--[[
    ZURION SUPREME v124 - DISCORD LINK CORRIGIDO
    ✅ Botão Discord funciona corretamente
    ✅ Link válido e direto
]]--

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- [ CONFIG ]
local CONFIG = {
    COMBAT = {
        ENABLED = false,
        AUTO_SHOOT = false,
        FOV = 280,
        SMOOTH = 0.75,
        SNAP_DIST = 55,
        PRED = 0.12,
        PART = "Head",
        WALL_CHECK = true,
        USE_RAYCAST = true,
        AUTO_SHOOT_DELAY = 0.08,
        PROXIMITY_RANGE = 1000,
        PROXIMITY_BLOCK = 7,
        MAX_DISTANCE = 1000,
    },
    VISUALS = {
        ENABLED = false,
        BOXES = false,
        NAMES = false,
        TRACERS = false,
        DISTANCE_TEXT = false,
        ACCENT = Color3.fromRGB(0, 170, 255),
        FRIEND_COLOR = Color3.fromRGB(0, 255, 120)
    },
    MOVEMENT = {
        FLY_ENABLED = false,
        FLY_SPEED = 180,
        NOCLIP = false,
        SPEED_ENABLED = false,
        SPEED_VALUE = 50,
        SUPERJUMP_ENABLED = false,
        SUPERJUMP_POWER = 100,
        KEYS = {W = false, S = false, A = false, D = false, SPACE = false}
    },
    MENU = {
        ACCENT = Color3.fromRGB(0, 170, 255),
        BG = Color3.fromRGB(10, 10, 15),
        SECONDARY = Color3.fromRGB(18, 18, 25),
        TERTIARY = Color3.fromRGB(25, 25, 35)
    }
}

local WHITELIST = {}
local UI_PAGES = {}
local DRAWINGS = {ESP = {}}
local LAST_SHOT_TIME = 0
local CURRENT_TARGET = nil
local PANEL_OPEN = true
local NEARBY_PLAYERS_COUNT = 0
local AIMBOT_BLOCKED = false
local LAST_JUMP_TIME = 0

-- [ UI SETUP ]
local Screen = Instance.new("ScreenGui")
Screen.Name = "Zurion_v124_RivalsAimbot"
Screen.ResetOnSpawn = false
Screen.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
pcall(function() Screen.Parent = CoreGui end)
if Screen.Parent == nil then Screen.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- [ FOV CIRCLE ]
local FOV_CIRCLE = Drawing.new("Circle")
FOV_CIRCLE.Thickness = 2
FOV_CIRCLE.NumSides = 60
FOV_CIRCLE.Radius = CONFIG.COMBAT.FOV
FOV_CIRCLE.Filled = false
FOV_CIRCLE.Visible = false
FOV_CIRCLE.Color = CONFIG.MENU.ACCENT
FOV_CIRCLE.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

-- [ STATUS TEXT ]
local StatusText = Drawing.new("Text")
StatusText.Size = 18
StatusText.Color = Color3.fromRGB(0, 170, 255)
StatusText.Position = Vector2.new(10, 10)
StatusText.Visible = true
StatusText.Text = "Players: 0"

-- [ ESP BUILDER ]
local function CreateESP(p)
    if DRAWINGS.ESP[p] then return end
    DRAWINGS.ESP[p] = {
        Box = Drawing.new("Square"),
        Name = Drawing.new("Text"),
        Line = Drawing.new("Line"),
        Distance = Drawing.new("Text")
    }
    local d = DRAWINGS.ESP[p]
    d.Box.Thickness = 2
    d.Box.Filled = false
    d.Name.Size = 13
    d.Name.Center = true
    d.Name.Outline = true
    d.Distance.Size = 11
    d.Distance.Center = true
    d.Distance.Outline = true
    d.Line.Thickness = 1
end

-- [ MAIN UI ]
local Main = Instance.new("Frame", Screen)
Main.Size = UDim2.new(0, 780, 0, 600)
Main.Position = UDim2.new(0.5, -390, 0.5, -300)
Main.BackgroundColor3 = CONFIG.MENU.BG
Main.BorderSizePixel = 0
Main.Visible = true
Main.ClipsDescendants = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 15)

local MStroke = Instance.new("UIStroke", Main)
MStroke.Color = CONFIG.MENU.ACCENT
MStroke.Thickness = 2

local Gradient = Instance.new("UIGradient", Main)
Gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, CONFIG.MENU.BG),
    ColorSequenceKeypoint.new(1, CONFIG.MENU.SECONDARY)
})
Gradient.Rotation = 45

local Sidebar = Instance.new("Frame", Main)
Sidebar.Size = UDim2.new(0, 240, 1, 0)
Sidebar.BackgroundColor3 = CONFIG.MENU.SECONDARY
Sidebar.BorderSizePixel = 0
Instance.new("UICorner", Sidebar)

local SideGradient = Instance.new("UIGradient", Sidebar)
SideGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, CONFIG.MENU.SECONDARY),
    ColorSequenceKeypoint.new(1, CONFIG.MENU.TERTIARY)
})

local Logo = Instance.new("TextLabel", Sidebar)
Logo.Size = UDim2.new(1, 0, 0, 90)
Logo.Text = "⚡ ZURION\nv124"
Logo.Font = "GothamBold"
Logo.TextSize = 22
Logo.TextColor3 = CONFIG.MENU.ACCENT
Logo.BackgroundTransparency = 1

-- [ DISCORD BUTTON - CORRIGIDO ]
local DiscordBtn = Instance.new("TextButton", Sidebar)
DiscordBtn.Size = UDim2.new(1, -10, 0, 50)
DiscordBtn.Position = UDim2.new(0, 5, 0, 95)
DiscordBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
DiscordBtn.Text = "💬 DISCORD SERVER"
DiscordBtn.Font = "GothamBold"
DiscordBtn.TextColor3 = Color3.new(1, 1, 1)
DiscordBtn.TextSize = 12
DiscordBtn.BorderSizePixel = 0
Instance.new("UICorner", DiscordBtn).CornerRadius = UDim.new(0, 8)

-- ✅ CORRIGIDO: Link do Discord funciona agora
DiscordBtn.MouseButton1Click:Connect(function()
    pcall(function()
        -- ✅ Método 1: Tentar abrir diretamente no Discord
        game:GetService("GuiService"):OpenBrowserWindow("https://discord.gg/fnRvBUDx9s")
    end)
end)

local TabContainer = Instance.new("Frame", Sidebar)
TabContainer.Size = UDim2.new(1, 0, 1, -155)
TabContainer.Position = UDim2.new(0, 0, 0, 155)
TabContainer.BackgroundTransparency = 1
local TabLayout = Instance.new("UIListLayout", TabContainer)
TabLayout.HorizontalAlignment = "Center"
TabLayout.Padding = UDim.new(0, 10)

local Content = Instance.new("Frame", Main)
Content.Size = UDim2.new(1, -270, 1, -50)
Content.Position = UDim2.new(0, 260, 0, 30)
Content.BackgroundTransparency = 1

local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 30)
TopBar.BackgroundColor3 = CONFIG.MENU.ACCENT
TopBar.BorderSizePixel = 0
local TopLabel = Instance.new("TextLabel", TopBar)
TopLabel.Size = UDim2.new(1, 0, 1, 0)
TopLabel.Text = "🎯 PAINEL DE CONTROLE"
TopLabel.Font = "GothamBold"
TopLabel.TextColor3 = Color3.new(1, 1, 1)
TopLabel.TextSize = 14
TopLabel.BackgroundTransparency = 1

-- [ UI FUNCTIONS ]
local function NewPage(name)
    local p = Instance.new("ScrollingFrame", Content)
    p.Size = UDim2.new(1, 0, 1, 0)
    p.BackgroundTransparency = 1
    p.Visible = false
    p.ScrollBarThickness = 5
    p.ScrollBarImageColor3 = CONFIG.MENU.ACCENT
    p.CanvasSize = UDim2.new(0, 0, 0, 0)
    p.AutomaticCanvasSize = "Y"
    Instance.new("UIListLayout", p).Padding = UDim.new(0, 15)
    UI_PAGES[name] = p
    return p
end

local function NewTab(name, page)
    local b = Instance.new("TextButton", TabContainer)
    b.Size = UDim2.new(0, 220, 0, 50)
    b.BackgroundColor3 = CONFIG.MENU.TERTIARY
    b.Text = "📌 " .. name
    b.Font = "GothamBold"
    b.TextColor3 = Color3.new(0.6, 0.6, 0.6)
    b.TextSize = 13
    b.BorderSizePixel = 0
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    
    local bStroke = Instance.new("UIStroke", b)
    bStroke.Color = Color3.fromRGB(40, 40, 50)
    bStroke.Thickness = 1
    
    b.MouseButton1Click:Connect(function()
        for _, pg in pairs(UI_PAGES) do pg.Visible = false end
        page.Visible = true
        for _, btn in pairs(TabContainer:GetChildren()) do
            if btn:IsA("TextButton") then
                btn.TextColor3 = Color3.new(0.6, 0.6, 0.6)
                btn.BackgroundColor3 = CONFIG.MENU.TERTIARY
            end
        end
        b.TextColor3 = CONFIG.MENU.ACCENT
        b.BackgroundColor3 = CONFIG.MENU.SECONDARY
    end)
end

local function NewToggle(parent, text, tbl, key, call)
    local f = Instance.new("Frame", parent)
    f.Size = UDim2.new(1, -15, 0, 60)
    f.BackgroundColor3 = CONFIG.MENU.TERTIARY
    f.BorderSizePixel = 0
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
    
    local fStroke = Instance.new("UIStroke", f)
    fStroke.Color = Color3.fromRGB(50, 50, 60)
    fStroke.Thickness = 1
    
    local l = Instance.new("TextLabel", f)
    l.Size = UDim2.new(1, -80, 1, 0)
    l.Position = UDim2.new(0, 15, 0, 0)
    l.Text = text
    l.Font = "GothamBold"
    l.TextColor3 = Color3.new(1, 1, 1)
    l.TextXAlignment = "Left"
    l.BackgroundTransparency = 1
    l.TextSize = 13
    
    local btn = Instance.new("TextButton", f)
    btn.Size = UDim2.new(0, 50, 0, 28)
    btn.Position = UDim2.new(1, -65, 0.5, -14)
    btn.BackgroundColor3 = tbl[key] and CONFIG.MENU.ACCENT or Color3.fromRGB(40, 40, 50)
    btn.Text = ""
    btn.BorderSizePixel = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(1, 0)
    
    btn.MouseButton1Click:Connect(function()
        tbl[key] = not tbl[key]
        TweenService:Create(btn, TweenInfo.new(0.2), {
            BackgroundColor3 = tbl[key] and CONFIG.MENU.ACCENT or Color3.fromRGB(40, 40, 50)
        }):Play()
        if call then call(tbl[key]) end
    end)
end

local function NewSlider(parent, text, min, max, tbl, key, callback)
    local f = Instance.new("Frame", parent)
    f.Size = UDim2.new(1, -15, 0, 85)
    f.BackgroundColor3 = CONFIG.MENU.TERTIARY
    f.BorderSizePixel = 0
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
    
    local fStroke = Instance.new("UIStroke", f)
    fStroke.Color = Color3.fromRGB(50, 50, 60)
    fStroke.Thickness = 1
    
    local l = Instance.new("TextLabel", f)
    l.Size = UDim2.new(1, 0, 0, 45)
    l.Position = UDim2.new(0, 15, 0, 0)
    l.Text = text .. ": " .. tbl[key]
    l.TextColor3 = Color3.new(1, 1, 1)
    l.Font = "GothamBold"
    l.TextXAlignment = "Left"
    l.BackgroundTransparency = 1
    l.TextSize = 13
    
    local bar = Instance.new("TextButton", f)
    bar.Size = UDim2.new(0.9, 0, 0, 8)
    bar.Position = UDim2.new(0.05, 0, 0.65, 0)
    bar.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    bar.Text = ""
    bar.BorderSizePixel = 0
    Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)
    
    local fill = Instance.new("Frame", bar)
    fill.Size = UDim2.new((tbl[key] - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = CONFIG.MENU.ACCENT
    fill.BorderSizePixel = 0
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)
    
    bar.MouseButton1Down:Connect(function()
        local m
        m = RunService.RenderStepped:Connect(function()
            local p = math.clamp((UIS:GetMouseLocation().X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            local v = math.floor(min + (max - min) * p)
            tbl[key] = v
            l.Text = text .. ": " .. v
            fill.Size = UDim2.new(p, 0, 1, 0)
            if callback then callback(v) end
            if not UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                m:Disconnect()
            end
        end)
    end)
end

-- [ PAGES ]
local CombatPg = NewPage("Combat")
local VisualPg = NewPage("Visuals")
local MovePg = NewPage("Movement")
local WLPg = NewPage("Whitelist")

NewTab("COMBAT", CombatPg)
NewTab("VISUALS", VisualPg)
NewTab("MOVEMENT", MovePg)
NewTab("WHITELIST", WLPg)

-- ============================================================
-- ✅ SISTEMA DE PROXIMIDADE
-- ============================================================

local function CountNearbyPlayers()
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return 0
    end
    
    local playerPos = LocalPlayer.Character.HumanoidRootPart.Position
    local count = 0
    
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (p.Character.HumanoidRootPart.Position - playerPos).Magnitude
            if distance <= CONFIG.COMBAT.PROXIMITY_RANGE then
                count += 1
            end
        end
    end
    
    return count
end

local function UpdateProximityStatus()
    NEARBY_PLAYERS_COUNT = CountNearbyPlayers()
    
    if NEARBY_PLAYERS_COUNT >= CONFIG.COMBAT.PROXIMITY_BLOCK then
        AIMBOT_BLOCKED = true
        StatusText.Color = Color3.fromRGB(255, 0, 0)
        StatusText.Text = "🚫 Players: " .. NEARBY_PLAYERS_COUNT .. " | AIMBOT BLOQUEADO"
    else
        AIMBOT_BLOCKED = false
        StatusText.Color = Color3.fromRGB(0, 255, 0)
        StatusText.Text = "✅ Players: " .. NEARBY_PLAYERS_COUNT .. " | AIMBOT ATIVO"
    end
end

-- ============================================================
-- ✅ AIMBOT COM LIMITE DE DISTÂNCIA 1000M
-- ============================================================

local function GetTarget()
    local best = nil
    local dist = CONFIG.COMBAT.FOV
    local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return nil, dist
    end
    
    local playerPos = LocalPlayer.Character.HumanoidRootPart.Position

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and not WHITELIST[p.Name] and p.Character then
            local head = p.Character:FindFirstChild(CONFIG.COMBAT.PART)
            local hum = p.Character:FindFirstChild("Humanoid")
            local root = p.Character:FindFirstChild("HumanoidRootPart")
            
            if head and hum and hum.Health > 0 and root then
                local distance3D = (root.Position - playerPos).Magnitude
                
                if distance3D > CONFIG.COMBAT.MAX_DISTANCE then
                    continue
                end
                
                local pos, onScreen = Camera:WorldToViewportPoint(head.Position)
                if onScreen then
                    local mag = (Vector2.new(pos.X, pos.Y) - center).Magnitude
                    if mag < dist then
                        if CONFIG.COMBAT.USE_RAYCAST then
                            local ray = workspace:Raycast(Camera.CFrame.Position, (head.Position - Camera.CFrame.Position).Unit * 1000, RaycastParams.new())
                            if ray and not ray.Instance:IsDescendantOf(p.Character) then continue end
                        end
                        dist = mag
                        best = head
                    end
                end
            end
        end
    end
    return best, dist
end

local function AimbotProfissional(dt)
    if AIMBOT_BLOCKED then return end
    if not CONFIG.COMBAT.ENABLED then return end
    
    local target, pixelDist = GetTarget()
    if not target then 
        CURRENT_TARGET = nil
        return 
    end
    
    CURRENT_TARGET = target
    
    local velocity = target.Parent.PrimaryPart and target.Parent.PrimaryPart.AssemblyLinearVelocity or Vector3.new(0,0,0)
    local predictedPos = target.Position + (velocity * CONFIG.COMBAT.PRED)
    
    local screenPos, onScreen = Camera:WorldToViewportPoint(predictedPos)
    if not onScreen then return end
    
    local smoothValue = CONFIG.COMBAT.SMOOTH
    if pixelDist < CONFIG.COMBAT.SNAP_DIST then
        smoothValue = 1.0
    end
    
    local factor = math.clamp(smoothValue * (dt * 65), 0, 1)
    Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, predictedPos), factor)
end

-- ============================================================
-- ✅ AUTO-SHOOT
-- ============================================================

RunService.Heartbeat:Connect(function()
    if AIMBOT_BLOCKED then return end
    if not CONFIG.COMBAT.ENABLED then return end
    if not CONFIG.COMBAT.AUTO_SHOOT then return end
    if not CURRENT_TARGET then return end
    
    local currentTime = tick()
    if currentTime - LAST_SHOT_TIME >= CONFIG.COMBAT.AUTO_SHOOT_DELAY then
        LAST_SHOT_TIME = currentTime
        
        mouse1press()
        task.wait(0.001)
        mouse1release()
    end
end)

-- ============================================================
-- ✅ SPEED
-- ============================================================

local function UpdateSpeedMovement()
    if not CONFIG.MOVEMENT.SPEED_ENABLED then return end
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    
    local root = LocalPlayer.Character.HumanoidRootPart
    local moveDirection = Vector3.new(0, 0, 0)
    
    if CONFIG.MOVEMENT.KEYS.W then moveDirection = moveDirection + Camera.CFrame.LookVector end
    if CONFIG.MOVEMENT.KEYS.S then moveDirection = moveDirection - Camera.CFrame.LookVector end
    if CONFIG.MOVEMENT.KEYS.A then moveDirection = moveDirection - Camera.CFrame.RightVector end
    if CONFIG.MOVEMENT.KEYS.D then moveDirection = moveDirection + Camera.CFrame.RightVector end
    
    if moveDirection.Magnitude > 0 then
        root.Velocity = moveDirection.Unit * CONFIG.MOVEMENT.SPEED_VALUE
    end
end

-- ============================================================
-- ✅ SUPER JUMP
-- ============================================================

local function UpdateSuperJump()
    if not CONFIG.MOVEMENT.SUPERJUMP_ENABLED then return end
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    
    local root = LocalPlayer.Character.HumanoidRootPart
    local currentTime = tick()
    
    if CONFIG.MOVEMENT.KEYS.SPACE and (currentTime - LAST_JUMP_TIME) > 0.5 then
        LAST_JUMP_TIME = currentTime
        root.AssemblyLinearVelocity = root.AssemblyLinearVelocity + Vector3.new(0, CONFIG.MOVEMENT.SUPERJUMP_POWER, 0)
    end
end

-- [ RENDER LOOP ]
RunService.RenderStepped:Connect(function(dt)
    if not LocalPlayer.Character then return end
    
    UpdateProximityStatus()
    
    FOV_CIRCLE.Radius = CONFIG.COMBAT.FOV
    FOV_CIRCLE.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    
    if CONFIG.MOVEMENT.NOCLIP then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
    
    UpdateSpeedMovement()
    UpdateSuperJump()
    AimbotProfissional(dt)
    
    if CONFIG.MOVEMENT.FLY_ENABLED and LocalPlayer.Character then
        local r = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if r then
            LocalPlayer.Character.Humanoid.PlatformStand = true
            local v = Vector3.new(0, 0, 0)
            if CONFIG.MOVEMENT.KEYS.W then v += Camera.CFrame.LookVector end
            if CONFIG.MOVEMENT.KEYS.S then v -= Camera.CFrame.LookVector end
            if CONFIG.MOVEMENT.KEYS.A then v -= Camera.CFrame.RightVector end
            if CONFIG.MOVEMENT.KEYS.D then v += Camera.CFrame.RightVector end
            r.Velocity = v * CONFIG.MOVEMENT.FLY_SPEED
        end
    end
    
    for p, d in pairs(DRAWINGS.ESP) do
        if CONFIG.VISUALS.ENABLED and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local root = p.Character.HumanoidRootPart
            local pos, on = Camera:WorldToViewportPoint(root.Position)
            local isWhitelisted = WHITELIST[p.Name]
            local drawColor = isWhitelisted and CONFIG.VISUALS.FRIEND_COLOR or CONFIG.VISUALS.ACCENT
            local distance = (root.Position - Camera.CFrame.Position).Magnitude
            
            if on then
                local t = Camera:WorldToViewportPoint(root.Position + Vector3.new(0, 3, 0))
                local b = Camera:WorldToViewportPoint(root.Position - Vector3.new(0, 3.5, 0))
                local h = math.abs(t.Y - b.Y)
                local w = h / 1.5
                
                if CONFIG.VISUALS.BOXES then
                    d.Box.Visible = true
                    d.Box.Size = Vector2.new(w, h)
                    d.Box.Position = Vector2.new(pos.X - w / 2, pos.Y - h / 2)
                    d.Box.Color = drawColor
                else
                    d.Box.Visible = false
                end
                
                if CONFIG.VISUALS.NAMES then
                    d.Name.Visible = true
                    d.Name.Text = p.DisplayName
                    d.Name.Position = Vector2.new(pos.X, pos.Y - h / 2 - 18)
                    d.Name.Color = drawColor
                else
                    d.Name.Visible = false
                end
                
                if CONFIG.VISUALS.TRACERS then
                    d.Line.Visible = true
                    d.Line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                    d.Line.To = Vector2.new(pos.X, pos.Y)
                    d.Line.Color = drawColor
                else
                    d.Line.Visible = false
                end
                
                if CONFIG.VISUALS.DISTANCE_TEXT then
                    d.Distance.Visible = true
                    d.Distance.Text = string.format("%.1fm", distance)
                    d.Distance.Position = Vector2.new(pos.X, pos.Y + h / 2 + 15)
                    d.Distance.Color = drawColor
                else
                    d.Distance.Visible = false
                end
            else
                d.Box.Visible = false
                d.Name.Visible = false
                d.Line.Visible = false
                d.Distance.Visible = false
            end
        else
            d.Box.Visible = false
            d.Name.Visible = false
            d.Line.Visible = false
            d.Distance.Visible = false
        end
    end
end)

-- [ WHITELIST ]
local function RefreshWL()
    for _, c in pairs(WLPg:GetChildren()) do
        if c:IsA("Frame") then c:Destroy() end
    end
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local f = Instance.new("Frame", WLPg)
            f.Size = UDim2.new(1, -15, 0, 55)
            f.BackgroundColor3 = CONFIG.MENU.TERTIARY
            f.BorderSizePixel = 0
            Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
            
            local fStroke = Instance.new("UIStroke", f)
            fStroke.Color = Color3.fromRGB(50, 50, 60)
            fStroke.Thickness = 1
            
            local l = Instance.new("TextLabel", f)
            l.Size = UDim2.new(1, -100, 1, 0)
            l.Position = UDim2.new(0, 15, 0, 0)
            l.Text = p.DisplayName
            l.TextColor3 = Color3.new(1, 1, 1)
            l.Font = "GothamBold"
            l.TextXAlignment = "Left"
            l.BackgroundTransparency = 1
            l.TextSize = 13
            
            local b = Instance.new("TextButton", f)
            b.Size = UDim2.new(0, 85, 0, 35)
            b.Position = UDim2.new(1, -95, 0.5, -17.5)
            b.BackgroundColor3 = WHITELIST[p.Name] and Color3.fromRGB(0, 200, 100) or CONFIG.MENU.SECONDARY
            b.Text = WHITELIST[p.Name] and "✓ AMIGO" or "+ ADD"
            b.TextColor3 = Color3.new(1, 1, 1)
            b.Font = "GothamBold"
            b.BorderSizePixel = 0
            b.TextSize = 12
            Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
            
            b.MouseButton1Click:Connect(function()
                WHITELIST[p.Name] = not WHITELIST[p.Name]
                b.BackgroundColor3 = WHITELIST[p.Name] and Color3.fromRGB(0, 200, 100) or CONFIG.MENU.SECONDARY
                b.Text = WHITELIST[p.Name] and "✓ AMIGO" or "+ ADD"
            end)
        end
    end
end

-- [ UI ELEMENTS - COMBAT ]
NewToggle(CombatPg, "🎯 Aimbot Rivals", CONFIG.COMBAT, "ENABLED", function(v)
    FOV_CIRCLE.Visible = v
end)
NewToggle(CombatPg, "🔫 Auto-Shot", CONFIG.COMBAT, "AUTO_SHOOT")
NewSlider(CombatPg, "⏱️ Shot Delay (ms)", 10, 500, CONFIG.COMBAT, "AUTO_SHOOT_DELAY", function(v)
    CONFIG.COMBAT.AUTO_SHOOT_DELAY = v / 1000
end)
NewSlider(CombatPg, "🎪 Smoothness", 0.1, 5, CONFIG.COMBAT, "SMOOTH", function(v)
    CONFIG.COMBAT.SMOOTH = v / 10
end)
NewSlider(CombatPg, "👁️ FOV", 50, 800, CONFIG.COMBAT, "FOV")
NewSlider(CombatPg, "📍 Snap Distance", 10, 200, CONFIG.COMBAT, "SNAP_DIST")
NewSlider(CombatPg, "🔮 Predição", 0.01, 0.5, CONFIG.COMBAT, "PRED", function(v)
    CONFIG.COMBAT.PRED = v / 100
end)
NewSlider(CombatPg, "📏 Max Distance (m)", 500, 2000, CONFIG.COMBAT, "MAX_DISTANCE")
NewToggle(CombatPg, "🚧 Wall Check", CONFIG.COMBAT, "USE_RAYCAST")

-- [ UI ELEMENTS - VISUALS ]
NewToggle(VisualPg, "👁️ ESP Master", CONFIG.VISUALS, "ENABLED")
NewToggle(VisualPg, "📦 Show Boxes", CONFIG.VISUALS, "BOXES")
NewToggle(VisualPg, "📝 Show Names", CONFIG.VISUALS, "NAMES")
NewToggle(VisualPg, "📍 Show Tracers", CONFIG.VISUALS, "TRACERS")
NewToggle(VisualPg, "📐 Show Distance", CONFIG.VISUALS, "DISTANCE_TEXT")

-- [ UI ELEMENTS - MOVEMENT ]
NewToggle(MovePg, "👻 Noclip", CONFIG.MOVEMENT, "NOCLIP")
NewToggle(MovePg, "🚀 Flight", CONFIG.MOVEMENT, "FLY_ENABLED")
NewSlider(MovePg, "⚡ Fly Speed", 50, 500, CONFIG.MOVEMENT, "FLY_SPEED")
NewToggle(MovePg, "💨 Speed", CONFIG.MOVEMENT, "SPEED_ENABLED")
NewSlider(MovePg, "💨 Speed Value", 20, 200, CONFIG.MOVEMENT, "SPEED_VALUE")
NewToggle(MovePg, "⬆️ Super Jump", CONFIG.MOVEMENT, "SUPERJUMP_ENABLED")
NewSlider(MovePg, "⬆️ Jump Power", 25, 300, CONFIG.MOVEMENT, "SUPERJUMP_POWER")

-- [ BUTTONS ]
local Z = Instance.new("TextButton", Screen)
Z.Name = "ZurionToggle"
Z.Size = UDim2.new(0, 70, 0, 70)
Z.Position = UDim2.new(0, 5, 0.5, -35)
Z.BackgroundColor3 = CONFIG.MENU.BG
Z.TextColor3 = CONFIG.MENU.ACCENT
Z.Text = "Z"
Z.Font = "GothamBold"
Z.TextSize = 35
Z.BorderSizePixel = 0
Instance.new("UICorner", Z).CornerRadius = UDim.new(1, 0)
local ZStroke = Instance.new("UIStroke", Z)
ZStroke.Color = CONFIG.MENU.ACCENT
ZStroke.Thickness = 2

Z.MouseButton1Click:Connect(function()
    Main.Visible = not Main.Visible
    PANEL_OPEN = Main.Visible
end)

-- [ INPUTS ]
UIS.InputBegan:Connect(function(i, g)
    if g then return end
    
    if i.KeyCode == Enum.KeyCode.Insert then
        Main.Visible = not Main.Visible
        PANEL_OPEN = Main.Visible
        return
    end
    
    if i.KeyCode == Enum.KeyCode.W then CONFIG.MOVEMENT.KEYS.W = true end
    if i.KeyCode == Enum.KeyCode.S then CONFIG.MOVEMENT.KEYS.S = true end
    if i.KeyCode == Enum.KeyCode.A then CONFIG.MOVEMENT.KEYS.A = true end
    if i.KeyCode == Enum.KeyCode.D then CONFIG.MOVEMENT.KEYS.D = true end
    if i.KeyCode == Enum.KeyCode.Space then CONFIG.MOVEMENT.KEYS.SPACE = true end
end)

UIS.InputEnded:Connect(function(i)
    if i.KeyCode == Enum.KeyCode.W then CONFIG.MOVEMENT.KEYS.W = false end
    if i.KeyCode == Enum.KeyCode.S then CONFIG.MOVEMENT.KEYS.S = false end
    if i.KeyCode == Enum.KeyCode.A then CONFIG.MOVEMENT.KEYS.A = false end
    if i.KeyCode == Enum.KeyCode.D then CONFIG.MOVEMENT.KEYS.D = false end
    if i.KeyCode == Enum.KeyCode.Space then CONFIG.MOVEMENT.KEYS.SPACE = false end
end)

-- [ DRAGGABLE ]
local dragging = false
local dragStart = nil
local panelStart = nil

Main.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = i.Position
        panelStart = Main.Position
    end
end)

Main.InputChanged:Connect(function(i)
    if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = i.Position - dragStart
        Main.Position = UDim2.new(panelStart.X.Scale, panelStart.X.Offset + delta.X, panelStart.Y.Scale, panelStart.Y.Offset + delta.Y)
    end
end)

UIS.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- [ INIT ]
for _, p in pairs(Players:GetPlayers()) do
    if p ~= LocalPlayer then CreateESP(p) end
end

Players.PlayerAdded:Connect(function(p)
    CreateESP(p)
    RefreshWL()
end)

Players.PlayerRemoving:Connect(RefreshWL)

RefreshWL()
CombatPg.Visible = true

print("✅ ZURION v124 - DISCORD LINK CORRIGIDO")
print("💬 Discord: https://discord.gg/fnRvBUDx9s")
