--[[
    WAGURI ADMIN HUB v3.6
    8tfu3
    Key System + Home | Visual | Aimbot | Ghost | Auto Shoot | Fling | Animations | Tools | Settings | Credits
    Feito pra Delta Executor (mobile/PC)
]]

----------------------------------------------------------------
-- KEY SYSTEM
----------------------------------------------------------------
local KEY       = "adm123"
local KEY_FILE  = "waguri_key.txt"
local SAVE_KEY  = true   -- lembra a key depois de acertar (pra não pedir toda vez)

local Players      = game:GetService("Players")
local RunService   = game:GetService("RunService")
local UIS          = game:GetService("UserInputService")
local Workspace    = game:GetService("Workspace")
local CoreGui      = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")

local lp = Players.LocalPlayer

local function guiParent()
    local ok, h = pcall(function() return gethui and gethui() end)
    if ok and h then return h end
    local ok2 = pcall(function() return CoreGui.Name end)
    if ok2 then return CoreGui end
    return lp:WaitForChild("PlayerGui")
end

local LoadHub -- definido abaixo

local function askKey()
    local parent = guiParent()
    local old = parent:FindFirstChild("WaguriKeyGui")
    if old then old:Destroy() end

    local kgui = Instance.new("ScreenGui")
    kgui.Name = "WaguriKeyGui"
    kgui.ResetOnSpawn = false
    kgui.IgnoreGuiInset = true
    kgui.DisplayOrder = 50
    kgui.Parent = parent

    local frame = Instance.new("Frame")
    frame.Size = UDim2.fromOffset(310, 200)
    frame.AnchorPoint = Vector2.new(0.5, 0.5)
    frame.Position = UDim2.new(0.5, 0, 0.5, 0)
    frame.BackgroundColor3 = Color3.fromRGB(26, 15, 16)
    frame.BorderSizePixel = 0
    frame.Parent = kgui
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 14)
    local st = Instance.new("UIStroke", frame)
    st.Color = Color3.fromRGB(235, 60, 70)
    st.Thickness = 1.5

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 30)
    title.Position = UDim2.fromOffset(0, 12)
    title.BackgroundTransparency = 1
    title.Text = "🌸 Waguri Admin"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.Font = Enum.Font.GothamBold
    title.TextSize = 20
    title.Parent = frame

    local sub = Instance.new("TextLabel")
    sub.Size = UDim2.new(1, 0, 0, 18)
    sub.Position = UDim2.fromOffset(0, 42)
    sub.BackgroundTransparency = 1
    sub.Text = "Digite a key para continuar"
    sub.TextColor3 = Color3.fromRGB(165, 145, 145)
    sub.Font = Enum.Font.Gotham
    sub.TextSize = 13
    sub.Parent = frame

    local box = Instance.new("TextBox")
    box.Size = UDim2.new(1, -40, 0, 36)
    box.Position = UDim2.fromOffset(20, 72)
    box.BackgroundColor3 = Color3.fromRGB(58, 38, 39)
    box.PlaceholderText = "Key"
    box.PlaceholderColor3 = Color3.fromRGB(165, 145, 145)
    box.Text = ""
    box.TextColor3 = Color3.new(1, 1, 1)
    box.Font = Enum.Font.GothamMedium
    box.TextSize = 15
    box.ClearTextOnFocus = false
    box.Parent = frame
    Instance.new("UICorner", box).CornerRadius = UDim.new(0, 9)

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -40, 0, 36)
    btn.Position = UDim2.fromOffset(20, 118)
    btn.BackgroundColor3 = Color3.fromRGB(235, 60, 70)
    btn.Text = "Verificar"
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    btn.Parent = frame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 9)

    local status = Instance.new("TextLabel")
    status.Size = UDim2.new(1, -40, 0, 16)
    status.Position = UDim2.fromOffset(20, 160)
    status.BackgroundTransparency = 1
    status.Text = ""
    status.TextColor3 = Color3.fromRGB(255, 90, 90)
    status.Font = Enum.Font.GothamMedium
    status.TextSize = 12
    status.Parent = frame

    local credit = Instance.new("TextLabel")
    credit.Size = UDim2.new(1, 0, 0, 16)
    credit.Position = UDim2.new(0, 0, 1, -20)
    credit.BackgroundTransparency = 1
    credit.Text = "8tfu3"
    credit.Font = Enum.Font.GothamBold
    credit.TextSize = 13
    credit.Parent = frame

    local rgb = RunService.RenderStepped:Connect(function()
        credit.TextColor3 = Color3.fromHSV((tick() * 0.3) % 1, 1, 1)
    end)

    local busy = false
    local function check()
        if busy then return end
        local typed = (box.Text or ""):gsub("^%s+", ""):gsub("%s+$", "")
        if typed == KEY then
            busy = true
            status.TextColor3 = Color3.fromRGB(80, 230, 130)
            status.Text = "Key correta! Carregando..."
            if SAVE_KEY and writefile then pcall(writefile, KEY_FILE, KEY) end
            task.wait(0.6)
            rgb:Disconnect()
            kgui:Destroy()
            LoadHub()
        else
            status.TextColor3 = Color3.fromRGB(255, 90, 90)
            status.Text = "Key inválida. Tente de novo."
            task.spawn(function()
                for i = 1, 4 do
                    frame.Position = UDim2.new(0.5, (i % 2 == 0) and 8 or -8, 0.5, 0)
                    task.wait(0.04)
                end
                frame.Position = UDim2.new(0.5, 0, 0.5, 0)
            end)
        end
    end
    btn.MouseButton1Click:Connect(check)
    box.FocusLost:Connect(function(enter) if enter then check() end end)
end

----------------------------------------------------------------
-- HUB (só carrega depois da key correta)
----------------------------------------------------------------
LoadHub = function()

local CONFIG = {
    HubName        = "Waguri Admin",
    Version        = "v3.6",
    Credit         = "8tfu3",
    ImageAssetId   = "94176053651962",
    ImageUrl       = "",
    Accent         = Color3.fromRGB(235, 60, 70),    -- vermelho da interface
    EspColor       = Color3.fromRGB(160, 100, 255),  -- roxo do ESP / linhas / FOV
    BgImageTransp  = 0.78,
}

local cam = Workspace.CurrentCamera

----------------------------------------------------------------
-- Estado
----------------------------------------------------------------
local State = {
    fly = false, flySpeed = 60, vert = 0,
    speedOn = false, walkSpeed = 50,
    jumpOn = false, jumpPower = 100, infJump = false,
    noclip = false,
    esp = false, lines = false, roleEsp = false,
    aim = false, aimFov = 200, aimSmooth = 0.25,
    showFov = false, teamCheck = true, wallCheck = false,
    aimPart = "Head",
    flingOn = false, flingBusy = false, flingTime = 2,
    ghost = false, ghostTransp = 0.6, ghostSpeed = 24,
    shoot = false, shootFov = 80, shootDelay = 0.15,
    shootLock = true, shootOnlyMurder = false, autoEquip = false,
    fireMethod = "Tool",
    animSpeed = 1, animLoop = true,
    -- ferramentas
    wantNoclipTool = false, wantInvisTool = false,
    noclipTool = false, invisTool = false,
}

local connections = {}
local function bind(c) table.insert(connections, c) return c end

local function getChar() return lp.Character end
local function getHum() local c = getChar() return c and c:FindFirstChildOfClass("Humanoid") end
local function getHRP() local c = getChar() return c and c:FindFirstChild("HumanoidRootPart") end

----------------------------------------------------------------
-- Imagem de fundo
----------------------------------------------------------------
local function loadImage()
    if CONFIG.ImageAssetId ~= "" then
        local ok, obj = pcall(function()
            return game:GetObjects("rbxassetid://" .. CONFIG.ImageAssetId)[1]
        end)
        if ok and obj then
            if obj:IsA("Decal") or obj:IsA("Texture") then return obj.Texture end
            if obj:IsA("ImageLabel") or obj:IsA("ImageButton") then return obj.Image end
        end
        return "rbxassetid://" .. CONFIG.ImageAssetId
    end
    if CONFIG.ImageUrl ~= "" and writefile and getcustomasset then
        local ok, res = pcall(function()
            writefile("waguri_bg.png", game:HttpGet(CONFIG.ImageUrl))
            return getcustomasset("waguri_bg.png")
        end)
        if ok and res then return res end
    end
    return ""
end

----------------------------------------------------------------
-- FLY
----------------------------------------------------------------
local bv, bg
local function stopFly()
    if bv then bv:Destroy() bv = nil end
    if bg then bg:Destroy() bg = nil end
    local hum = getHum()
    if hum then hum.PlatformStand = false end
end
local function startFly()
    stopFly()
    local hrp, hum = getHRP(), getHum()
    if not (hrp and hum) then return end
    bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    bv.Velocity = Vector3.zero
    bv.Parent = hrp
    bg = Instance.new("BodyGyro")
    bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.P = 9e4
    bg.CFrame = hrp.CFrame
    bg.Parent = hrp
    hum.PlatformStand = true
end

bind(RunService.Heartbeat:Connect(function()
    if State.fly and bv and bg then
        local hum = getHum()
        if hum then
            local md = hum.MoveDirection
            local v = md * State.flySpeed
            if md.Magnitude > 0 then
                v = v + Vector3.new(0, cam.CFrame.LookVector.Y * State.flySpeed, 0)
            end
            v = v + Vector3.new(0, State.vert * State.flySpeed, 0)
            bv.Velocity = v
            bg.CFrame = cam.CFrame
        end
    end
    local hum = getHum()
    if hum then
        if State.speedOn then
            hum.WalkSpeed = State.walkSpeed
        elseif State.ghost then
            hum.WalkSpeed = State.ghostSpeed
        end
        if State.jumpOn then
            if hum.UseJumpPower then hum.JumpPower = State.jumpPower
            else hum.JumpHeight = State.jumpPower / 7 end
        end
    end
end))

----------------------------------------------------------------
-- INFINITE JUMP
----------------------------------------------------------------
bind(UIS.JumpRequest:Connect(function()
    if State.infJump then
        local h = getHum()
        if h then h:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end))

----------------------------------------------------------------
-- NOCLIP (também usado pelo Ghost e pela Noclip Tool)
----------------------------------------------------------------
local origCollide = {}
bind(RunService.Stepped:Connect(function()
    if State.noclip or State.ghost or State.noclipTool then
        local c = getChar()
        if c then
            for _, part in ipairs(c:GetDescendants()) do
                if part:IsA("BasePart") then
                    if origCollide[part] == nil then origCollide[part] = part.CanCollide end
                    part.CanCollide = false
                end
            end
        end
    end
end))
local function restoreCollide()
    for part, v in pairs(origCollide) do
        if part and part.Parent then part.CanCollide = v end
    end
    origCollide = {}
end

----------------------------------------------------------------
-- GHOST (visual local: personagem translúcido + atravessa paredes)
----------------------------------------------------------------
local ghostOrig = {}
local function setGhost(on)
    if on then
        local c = getChar()
        if not c then return end
        for _, d in ipairs(c:GetDescendants()) do
            if (d:IsA("BasePart") or d:IsA("Decal")) and d.Name ~= "HumanoidRootPart" then
                if ghostOrig[d] == nil then ghostOrig[d] = d.Transparency end
                d.Transparency = math.max(ghostOrig[d], State.ghostTransp)
            end
        end
    else
        for d, v in pairs(ghostOrig) do
            if d and d.Parent then d.Transparency = v end
        end
        ghostOrig = {}
    end
end

task.spawn(function()
    while task.wait(0.3) do
        if State.ghost and not State.invisTool then pcall(setGhost, true) end
    end
end)

----------------------------------------------------------------
-- FERRAMENTAS (Tools que aparecem no inventário)
----------------------------------------------------------------
local activeTools = {}
local invisOrig = {}

-- Invisível (visual local: só você fica sem ver o seu personagem)
local function setInvisible(on)
    if on then
        local c = getChar()
        if not c then return end
        for _, d in ipairs(c:GetDescendants()) do
            if (d:IsA("BasePart") or d:IsA("Decal")) and d.Name ~= "HumanoidRootPart" then
                if invisOrig[d] == nil then invisOrig[d] = ghostOrig[d] or d.Transparency end
                d.Transparency = 1
            end
        end
    else
        for d, v in pairs(invisOrig) do
            if d and d.Parent then d.Transparency = v end
        end
        invisOrig = {}
    end
end

task.spawn(function()
    while task.wait(0.3) do
        if State.invisTool then pcall(setInvisible, true) end
    end
end)

local function removeTool(key)
    local t = activeTools[key]
    if t then pcall(function() t:Destroy() end) end
    activeTools[key] = nil
end

local function makeTool(key, label, onActivate)
    removeTool(key)
    local bp = lp:WaitForChild("Backpack", 5)
    if not bp then return end
    local t = Instance.new("Tool")
    t.Name = label
    t.ToolTip = label
    t.RequiresHandle = false
    t.CanBeDropped = false
    t.Activated:Connect(function() onActivate(t) end)
    t.Parent = bp
    activeTools[key] = t
end

local function noclipToolLabel()
    return "Noclip Tool [" .. (State.noclipTool and "ON" or "OFF") .. "]"
end
local function invisToolLabel()
    return "Invisible Tool [" .. (State.invisTool and "ON" or "OFF") .. "]"
end

local function giveNoclipTool()
    makeTool("noclip", noclipToolLabel(), function(t)
        State.noclipTool = not State.noclipTool
        t.Name = noclipToolLabel()
        if not State.noclipTool and not State.noclip and not State.ghost then
            restoreCollide()
        end
    end)
end

local function giveInvisTool()
    makeTool("invis", invisToolLabel(), function(t)
        State.invisTool = not State.invisTool
        t.Name = invisToolLabel()
        if State.invisTool then
            pcall(setInvisible, true)
        else
            setInvisible(false)
        end
    end)
end

bind(lp.CharacterAdded:Connect(function()
    origCollide = {}
    ghostOrig = {}
    invisOrig = {}
    State.noclipTool = false
    State.invisTool = false
    task.wait(0.8)
    cam = Workspace.CurrentCamera
    if State.fly then startFly() end
    task.wait(0.4)
    if State.wantNoclipTool then giveNoclipTool() end
    if State.wantInvisTool then giveInvisTool() end
end))

----------------------------------------------------------------
-- ESP (highlight roxo + nome/distância)
----------------------------------------------------------------
local espObjects = {}
local function clearESP(p)
    local o = espObjects[p]
    if o then
        for _, i in pairs(o) do pcall(function() i:Destroy() end) end
        espObjects[p] = nil
    end
end
local function applyESP(p)
    if p == lp then return end
    clearESP(p)
    local char = p.Character
    local head = char and char:FindFirstChild("Head")
    if not head then return end
    local hl = Instance.new("Highlight")
    hl.FillColor = CONFIG.EspColor
    hl.OutlineColor = Color3.fromRGB(220, 190, 255)
    hl.FillTransparency = 0.6
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Parent = char
    local bb = Instance.new("BillboardGui")
    bb.Size = UDim2.new(0, 120, 0, 34)
    bb.StudsOffset = Vector3.new(0, 2.5, 0)
    bb.AlwaysOnTop = true
    bb.Adornee = head
    bb.Parent = head
    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, 0, 1, 0)
    t.BackgroundTransparency = 1
    t.TextColor3 = Color3.new(1, 1, 1)
    t.TextStrokeTransparency = 0.3
    t.Font = Enum.Font.GothamBold
    t.TextSize = 13
    t.Text = p.DisplayName
    t.Parent = bb
    espObjects[p] = { hl, bb, label = t }
end
local function refreshESP()
    for _, p in ipairs(Players:GetPlayers()) do
        if State.esp then applyESP(p) else clearESP(p) end
    end
end

local function watchPlayer(p)
    bind(p.CharacterAdded:Connect(function()
        task.wait(1)
        if State.esp then applyESP(p) end
    end))
end
for _, p in ipairs(Players:GetPlayers()) do if p ~= lp then watchPlayer(p) end end
bind(Players.PlayerAdded:Connect(watchPlayer))

task.spawn(function()
    while task.wait(0.3) do
        if State.esp then
            local hrp = getHRP()
            for p, o in pairs(espObjects) do
                local ph = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
                if o.label and ph and hrp then
                    local d = math.floor((ph.Position - hrp.Position).Magnitude)
                    o.label.Text = p.DisplayName .. "\n[" .. d .. "m]"
                end
            end
        end
    end
end)

----------------------------------------------------------------
-- ROLE ESP (Murder Mystery 2): Murder / Sheriff / Innocent
----------------------------------------------------------------
local COLOR_MURDER   = Color3.fromRGB(255, 60, 60)
local COLOR_SHERIFF  = Color3.fromRGB(70, 140, 255)
local COLOR_INNOCENT = Color3.fromRGB(70, 230, 120)

local roleObjs = {}

local function getRole(p)
    local c = p.Character
    local bp = p:FindFirstChild("Backpack")
    local function has(n)
        return (c and c:FindFirstChild(n)) or (bp and bp:FindFirstChild(n))
    end
    if has("Knife") then return "Murder", COLOR_MURDER end
    if has("Gun") then return "Sheriff", COLOR_SHERIFF end
    return "Innocent", COLOR_INNOCENT
end

local function clearRole(p)
    local o = roleObjs[p]
    if o then
        pcall(function() o.hl:Destroy() end)
        pcall(function() o.bb:Destroy() end)
        roleObjs[p] = nil
    end
end

local function updateRole(p)
    local c = p.Character
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChildOfClass("Humanoid")
    if not head or not hum or hum.Health <= 0 then
        clearRole(p)
        return
    end
    local o = roleObjs[p]
    if not o or o.char ~= c or not o.hl.Parent then
        clearRole(p)
        local hl = Instance.new("Highlight")
        hl.FillTransparency = 0.45
        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        hl.Parent = c
        local bb = Instance.new("BillboardGui")
        bb.Size = UDim2.new(0, 130, 0, 38)
        bb.StudsOffset = Vector3.new(0, 3, 0)
        bb.AlwaysOnTop = true
        bb.Adornee = head
        bb.Parent = head
        local t = Instance.new("TextLabel")
        t.Size = UDim2.new(1, 0, 1, 0)
        t.BackgroundTransparency = 1
        t.TextStrokeTransparency = 0.2
        t.Font = Enum.Font.GothamBold
        t.TextSize = 13
        t.Parent = bb
        o = { char = c, hl = hl, bb = bb, label = t }
        roleObjs[p] = o
    end
    local role, color = getRole(p)
    local hrp, phrp = getHRP(), c:FindFirstChild("HumanoidRootPart")
    local dist = (hrp and phrp) and math.floor((phrp.Position - hrp.Position).Magnitude) or 0
    o.hl.FillColor = color
    o.hl.OutlineColor = color
    o.label.TextColor3 = color
    o.label.Text = p.DisplayName .. "\n[" .. role .. " • " .. dist .. "m]"
end

task.spawn(function()
    while task.wait(0.25) do
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= lp then
                if State.roleEsp then
                    pcall(updateRole, p)
                else
                    clearRole(p)
                end
            end
        end
    end
end)

----------------------------------------------------------------
-- AIMBOT (invisível, FOV opcional)
----------------------------------------------------------------
local rayParams = RaycastParams.new()
rayParams.FilterType = Enum.RaycastFilterType.Exclude

local function visible(part, char)
    rayParams.FilterDescendantsInstances = { getChar(), cam }
    local origin = cam.CFrame.Position
    local res = Workspace:Raycast(origin, part.Position - origin, rayParams)
    return (not res) or res.Instance:IsDescendantOf(char)
end

local function getTarget()
    local best, bd = nil, State.aimFov
    local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp and p.Character then
            local sameTeam = State.teamCheck and p.Team ~= nil and p.Team == lp.Team
            local part = p.Character:FindFirstChild(State.aimPart) or p.Character:FindFirstChild("HumanoidRootPart")
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if part and hum and hum.Health > 0 and not sameTeam then
                local pos, on = cam:WorldToViewportPoint(part.Position)
                if on then
                    local d = (Vector2.new(pos.X, pos.Y) - center).Magnitude
                    if d < bd and (not State.wallCheck or visible(part, p.Character)) then
                        best, bd = part, d
                    end
                end
            end
        end
    end
    return best
end

----------------------------------------------------------------
-- AUTO SHOOT
----------------------------------------------------------------
local function getShootTarget()
    local best, bd = nil, State.shootFov
    local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp and p.Character then
            local sameTeam = State.teamCheck and p.Team ~= nil and p.Team == lp.Team
            local part = p.Character:FindFirstChild(State.aimPart) or p.Character:FindFirstChild("HumanoidRootPart")
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if part and hum and hum.Health > 0 and not sameTeam then
                local okRole = true
                if State.shootOnlyMurder then
                    local role = getRole(p)
                    okRole = (role == "Murder")
                end
                if okRole then
                    local pos, on = cam:WorldToViewportPoint(part.Position)
                    if on then
                        local d = (Vector2.new(pos.X, pos.Y) - center).Magnitude
                        if d < bd and visible(part, p.Character) then
                            best, bd = part, d
                        end
                    end
                end
            end
        end
    end
    return best
end

local function clickCenter()
    pcall(function()
        local vim = game:GetService("VirtualInputManager")
        local vs = cam.ViewportSize
        vim:SendMouseButtonEvent(vs.X / 2, vs.Y / 2, 0, true, game, 0)
        vim:SendMouseButtonEvent(vs.X / 2, vs.Y / 2, 0, false, game, 0)
    end)
end

local lastShot = 0
local function doAutoShoot()
    if tick() - lastShot < State.shootDelay then return end
    local hum, char = getHum(), getChar()
    if not hum or not char or hum.Health <= 0 then return end

    local tool = char:FindFirstChildOfClass("Tool")
    if not tool and State.autoEquip then
        local bp = lp:FindFirstChild("Backpack")
        local t = bp and (bp:FindFirstChild("Gun") or bp:FindFirstChildOfClass("Tool"))
        if t then pcall(function() hum:EquipTool(t) end) end
        return
    end
    if not tool then return end

    local target = getShootTarget()
    if not target then return end

    if State.shootLock then
        cam.CFrame = CFrame.new(cam.CFrame.Position, target.Position)
    end
    lastShot = tick()
    if State.fireMethod == "Click" then
        clickCenter()
    else
        pcall(function() tool:Activate() end)
    end
end

----------------------------------------------------------------
-- FLING
----------------------------------------------------------------
local selected = nil

local function doFling(p)
    local hrp, hum = getHRP(), getHum()
    local tc = p and p.Character
    local thrp = tc and tc:FindFirstChild("HumanoidRootPart")
    if not (hrp and hum and thrp) then return end

    local origin = hrp.CFrame
    local wasFly = State.fly
    if wasFly then State.fly = false stopFly() end

    local t0 = tick()
    while tick() - t0 < State.flingTime and State.flingOn and hrp.Parent and thrp.Parent do
        RunService.Heartbeat:Wait()
        if thrp.AssemblyLinearVelocity.Magnitude > 600 then break end
        hrp.CFrame = CFrame.new(thrp.Position + thrp.AssemblyLinearVelocity * 0.08)
        hrp.AssemblyLinearVelocity = Vector3.new(9e7, 9e7, 9e7)
        hrp.AssemblyAngularVelocity = Vector3.new(9e8, 9e8, 9e8)
    end

    for _ = 1, 12 do
        if not hrp.Parent then break end
        hrp.AssemblyLinearVelocity = Vector3.zero
        hrp.AssemblyAngularVelocity = Vector3.zero
        hrp.CFrame = origin
        RunService.Heartbeat:Wait()
    end

    if wasFly then State.fly = true startFly() end
end

local function flingSelected()
    if State.flingBusy or not selected then return end
    State.flingBusy, State.flingOn = true, true
    pcall(doFling, selected)
    State.flingBusy, State.flingOn = false, false
end

local function flingAll()
    if State.flingBusy then return end
    State.flingBusy, State.flingOn = true, true
    for _, p in ipairs(Players:GetPlayers()) do
        if not State.flingOn then break end
        if p ~= lp then pcall(doFling, p) end
    end
    State.flingBusy, State.flingOn = false, false
end

----------------------------------------------------------------
-- ANIMAÇÕES
----------------------------------------------------------------
local animTrack
local function stopAnim()
    if animTrack then pcall(function() animTrack:Stop() end) animTrack = nil end
end
local function playAnim(id)
    id = tostring(id):gsub("%D", "")
    if id == "" then return end
    local hum = getHum()
    if not hum then return end
    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = hum
    end
    stopAnim()
    local a = Instance.new("Animation")
    a.AnimationId = "rbxassetid://" .. id
    local ok, track = pcall(function() return animator:LoadAnimation(a) end)
    if ok and track then
        track.Priority = Enum.AnimationPriority.Action4
        track.Looped = State.animLoop
        track:Play()
        track:AdjustSpeed(State.animSpeed)
        animTrack = track
    end
end

----------------------------------------------------------------
-- GUI base
----------------------------------------------------------------
local parent = guiParent()
for _, n in ipairs({ "WaguriAdminGui", "WaguriAdminOverlay" }) do
    local o = parent:FindFirstChild(n)
    if o then o:Destroy() end
end

local overlay = Instance.new("ScreenGui")
overlay.Name = "WaguriAdminOverlay"
overlay.ResetOnSpawn = false
overlay.IgnoreGuiInset = true
overlay.DisplayOrder = 5
overlay.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
overlay.Parent = parent

local gui = Instance.new("ScreenGui")
gui.Name = "WaguriAdminGui"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.DisplayOrder = 10
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = parent

local function corner(o, r)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, r or 10)
    c.Parent = o
    return c
end

local C = {
    win   = Color3.fromRGB(26, 15, 16),
    card  = Color3.fromRGB(42, 25, 26),
    side  = Color3.fromRGB(20, 12, 13),
    sel   = Color3.fromRGB(62, 36, 37),
    field = Color3.fromRGB(58, 38, 39),
    off   = Color3.fromRGB(78, 66, 68),
    mute  = Color3.fromRGB(165, 145, 145),
    white = Color3.new(1, 1, 1),
}

-- Crédito RGB (canto superior esquerdo)
local credit = Instance.new("TextLabel")
credit.Size = UDim2.new(0, 180, 0, 22)
credit.Position = UDim2.new(0, 10, 0, 46)
credit.BackgroundTransparency = 1
credit.Text = CONFIG.Credit
credit.TextColor3 = Color3.new(1, 1, 1)
credit.TextStrokeTransparency = 0.4
credit.Font = Enum.Font.GothamBold
credit.TextSize = 16
credit.TextXAlignment = Enum.TextXAlignment.Left
credit.Parent = overlay

-- Círculo do FOV
local fovCircle = Instance.new("Frame")
fovCircle.AnchorPoint = Vector2.new(0.5, 0.5)
fovCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
fovCircle.BackgroundTransparency = 1
fovCircle.Visible = false
fovCircle.Parent = overlay
corner(fovCircle, 999)
local fovStroke = Instance.new("UIStroke")
fovStroke.Color = CONFIG.EspColor
fovStroke.Thickness = 1.5
fovStroke.Parent = fovCircle

-- Linhas do ESP
local lineFrames = {}
local function getLine(p)
    local f = lineFrames[p]
    if not f then
        f = Instance.new("Frame")
        f.AnchorPoint = Vector2.new(0.5, 0.5)
        f.BackgroundColor3 = CONFIG.EspColor
        f.BorderSizePixel = 0
        f.Visible = false
        f.Parent = overlay
        lineFrames[p] = f
    end
    return f
end
local function removeLine(p)
    if lineFrames[p] then lineFrames[p]:Destroy() lineFrames[p] = nil end
end
bind(Players.PlayerRemoving:Connect(function(p)
    clearESP(p)
    clearRole(p)
    removeLine(p)
end))

local function setLine(f, a, b)
    local d = b - a
    f.Size = UDim2.fromOffset(d.Magnitude, 2)
    f.Position = UDim2.fromOffset((a.X + b.X) / 2, (a.Y + b.Y) / 2)
    f.Rotation = math.deg(math.atan2(d.Y, d.X))
    f.Visible = true
end

bind(RunService.RenderStepped:Connect(function()
    cam = Workspace.CurrentCamera

    fovCircle.Visible = State.showFov
    if State.showFov then
        fovCircle.Size = UDim2.fromOffset(State.aimFov * 2, State.aimFov * 2)
    end

    if State.aim then
        local target = getTarget()
        if target then
            local goal = CFrame.new(cam.CFrame.Position, target.Position)
            cam.CFrame = cam.CFrame:Lerp(goal, State.aimSmooth)
        end
    end

    if State.shoot then
        pcall(doAutoShoot)
    end

    if State.lines then
        local vs = cam.ViewportSize
        local origin = Vector2.new(vs.X / 2, vs.Y)
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= lp then
                local f = getLine(p)
                local hrp = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
                local hum = p.Character and p.Character:FindFirstChildOfClass("Humanoid")
                if hrp and hum and hum.Health > 0 then
                    local pos = cam:WorldToViewportPoint(hrp.Position)
                    if pos.Z > 0 then
                        setLine(f, origin, Vector2.new(pos.X, pos.Y))
                    else
                        f.Visible = false
                    end
                else
                    f.Visible = false
                end
            end
        end
    else
        for _, f in pairs(lineFrames) do f.Visible = false end
    end
end))

----------------------------------------------------------------
-- Janela principal (interface estilo Mystrix)
----------------------------------------------------------------
local W, H = 560, 350

local main = Instance.new("Frame")
main.Name = "Main"
main.Size = UDim2.fromOffset(W, H)
main.Position = UDim2.new(0.5, -W / 2, 0.5, -H / 2)
main.BackgroundColor3 = C.win
main.BackgroundTransparency = 0.08
main.BorderSizePixel = 0
main.ClipsDescendants = true
main.Parent = gui
corner(main, 14)

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(80, 44, 46)
mainStroke.Thickness = 1.5
mainStroke.Parent = main

local bgImg = Instance.new("ImageLabel")
bgImg.Name = "WaguriBG"
bgImg.Size = UDim2.new(1, 0, 1, 0)
bgImg.BackgroundTransparency = 1
bgImg.Image = loadImage()
bgImg.ImageTransparency = CONFIG.BgImageTransp
bgImg.ScaleType = Enum.ScaleType.Crop
bgImg.ZIndex = 1
bgImg.Parent = main
corner(bgImg, 14)

-- Cabeçalho
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 46)
header.BackgroundTransparency = 1
header.ZIndex = 3
header.Parent = main

local logo = Instance.new("TextLabel")
logo.Size = UDim2.fromOffset(28, 28)
logo.Position = UDim2.fromOffset(14, 9)
logo.BackgroundTransparency = 1
logo.Text = "🌸"
logo.TextSize = 22
logo.Parent = header

local hubName = Instance.new("TextLabel")
hubName.Size = UDim2.fromOffset(120, 18)
hubName.Position = UDim2.fromOffset(48, 6)
hubName.BackgroundTransparency = 1
hubName.Text = CONFIG.HubName
hubName.TextColor3 = C.white
hubName.Font = Enum.Font.GothamBold
hubName.TextSize = 15
hubName.TextXAlignment = Enum.TextXAlignment.Left
hubName.Parent = header

local hubSub = Instance.new("TextLabel")
hubSub.Size = UDim2.fromOffset(120, 14)
hubSub.Position = UDim2.fromOffset(48, 24)
hubSub.BackgroundTransparency = 1
hubSub.Text = "by " .. CONFIG.Credit
hubSub.TextColor3 = C.white
hubSub.Font = Enum.Font.GothamBold
hubSub.TextSize = 11
hubSub.TextXAlignment = Enum.TextXAlignment.Left
hubSub.Parent = header

local badge = Instance.new("TextLabel")
badge.Size = UDim2.fromOffset(44, 20)
badge.Position = UDim2.fromOffset(150, 13)
badge.BackgroundColor3 = CONFIG.Accent
badge.Text = CONFIG.Version
badge.TextColor3 = C.white
badge.Font = Enum.Font.GothamBold
badge.TextSize = 11
badge.Parent = header
corner(badge, 10)

-- RGB animado (8tfu3 + nomes da aba Credits)
local rgbLabels = {}
bind(RunService.RenderStepped:Connect(function()
    local col = Color3.fromHSV((tick() * 0.3) % 1, 1, 1)
    credit.TextColor3 = col
    hubSub.TextColor3 = col
    for _, l in ipairs(rgbLabels) do
        l.TextColor3 = col
    end
end))

local function topBtn(text, xOff, color)
    local b = Instance.new("TextButton")
    b.Size = UDim2.fromOffset(28, 28)
    b.Position = UDim2.new(1, xOff, 0, 9)
    b.BackgroundTransparency = 1
    b.Text = text
    b.TextColor3 = color or Color3.fromRGB(215, 195, 195)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 17
    b.Parent = header
    return b
end
local minBtn   = topBtn("–", -102)
local maxBtn   = topBtn("▢", -70)
local closeBtn = topBtn("✕", -38, CONFIG.Accent)

do
    local dragging, dragStart, startPos
    header.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging, dragStart, startPos = true, i.Position, main.Position
        end
    end)
    bind(UIS.InputChanged:Connect(function(i)
        if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            local d = i.Position - dragStart
            main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
        end
    end))
    bind(UIS.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end))
end

-- Sidebar
local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, 160, 1, -58)
sidebar.Position = UDim2.fromOffset(8, 50)
sidebar.BackgroundTransparency = 1
sidebar.ZIndex = 2
sidebar.Parent = main

-- Busca
local searchBox = Instance.new("Frame")
searchBox.Size = UDim2.new(1, 0, 0, 34)
searchBox.BackgroundColor3 = C.side
searchBox.BackgroundTransparency = 0.2
searchBox.Parent = sidebar
corner(searchBox, 9)
local searchStroke = Instance.new("UIStroke")
searchStroke.Color = Color3.fromRGB(95, 52, 55)
searchStroke.Thickness = 1
searchStroke.Parent = searchBox

local searchIcon = Instance.new("TextLabel")
searchIcon.Size = UDim2.fromOffset(26, 34)
searchIcon.Position = UDim2.fromOffset(6, 0)
searchIcon.BackgroundTransparency = 1
searchIcon.Text = "🔍"
searchIcon.TextSize = 14
searchIcon.TextColor3 = CONFIG.Accent
searchIcon.Parent = searchBox

local search = Instance.new("TextBox")
search.Size = UDim2.new(1, -38, 1, 0)
search.Position = UDim2.fromOffset(32, 0)
search.BackgroundTransparency = 1
search.PlaceholderText = "Search"
search.PlaceholderColor3 = C.mute
search.Text = ""
search.TextColor3 = C.white
search.Font = Enum.Font.Gotham
search.TextSize = 14
search.TextXAlignment = Enum.TextXAlignment.Left
search.ClearTextOnFocus = false
search.Parent = searchBox

-- Lista de abas
local tabScroll = Instance.new("ScrollingFrame")
tabScroll.Size = UDim2.new(1, 0, 1, -94)
tabScroll.Position = UDim2.fromOffset(0, 42)
tabScroll.BackgroundTransparency = 1
tabScroll.BorderSizePixel = 0
tabScroll.ScrollBarThickness = 0
tabScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
tabScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
tabScroll.Parent = sidebar
local tabLayout = Instance.new("UIListLayout")
tabLayout.Padding = UDim.new(0, 4)
tabLayout.SortOrder = Enum.SortOrder.LayoutOrder
tabLayout.Parent = tabScroll

-- Card do usuário
local userCard = Instance.new("Frame")
userCard.Size = UDim2.new(1, 0, 0, 44)
userCard.Position = UDim2.new(0, 0, 1, -44)
userCard.BackgroundTransparency = 1
userCard.Parent = sidebar

local avatar = Instance.new("ImageLabel")
avatar.Size = UDim2.fromOffset(34, 34)
avatar.Position = UDim2.fromOffset(2, 5)
avatar.BackgroundColor3 = C.field
avatar.Image = ""
avatar.Parent = userCard
corner(avatar, 999)
task.spawn(function()
    local ok, img = pcall(function()
        return Players:GetUserThumbnailAsync(lp.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
    end)
    if ok and img then avatar.Image = img end
end)

local uName = Instance.new("TextLabel")
uName.Size = UDim2.new(1, -46, 0, 18)
uName.Position = UDim2.fromOffset(44, 5)
uName.BackgroundTransparency = 1
uName.Text = lp.DisplayName
uName.TextColor3 = C.white
uName.Font = Enum.Font.GothamBold
uName.TextSize = 13
uName.TextXAlignment = Enum.TextXAlignment.Left
uName.TextTruncate = Enum.TextTruncate.AtEnd
uName.Parent = userCard

local uSub = Instance.new("TextLabel")
uSub.Size = UDim2.new(1, -46, 0, 14)
uSub.Position = UDim2.fromOffset(44, 23)
uSub.BackgroundTransparency = 1
uSub.Text = lp.Name
uSub.TextColor3 = C.mute
uSub.Font = Enum.Font.Gotham
uSub.TextSize = 11
uSub.TextXAlignment = Enum.TextXAlignment.Left
uSub.TextTruncate = Enum.TextTruncate.AtEnd
uSub.Parent = userCard

-- Área de conteúdo
local content = Instance.new("Frame")
content.Size = UDim2.new(1, -186, 1, -58)
content.Position = UDim2.fromOffset(178, 50)
content.BackgroundTransparency = 1
content.ZIndex = 2
content.Parent = main

----------------------------------------------------------------
-- Páginas / seções / componentes
----------------------------------------------------------------
local pages, tabBtns, allSections = {}, {}, {}
local currentTab = nil
local order = 0
local function nextOrder() order += 1 return order end

local function newPage(name)
    local sf = Instance.new("ScrollingFrame")
    sf.Size = UDim2.new(1, 0, 1, 0)
    sf.BackgroundTransparency = 1
    sf.BorderSizePixel = 0
    sf.ScrollBarThickness = 3
    sf.ScrollBarImageColor3 = CONFIG.Accent
    sf.CanvasSize = UDim2.new(0, 0, 0, 0)
    sf.AutomaticCanvasSize = Enum.AutomaticSize.Y
    sf.Visible = false
    sf.Parent = content
    local l = Instance.new("UIListLayout")
    l.Padding = UDim.new(0, 8)
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Parent = sf
    pages[name] = sf
    return sf
end

local function applyFilter()
    local q = string.lower(search.Text or "")
    for _, S in ipairs(allSections) do
        if S.page == currentTab then
            local any = false
            for _, r in ipairs(S.rows) do
                local show = (q == "") or (string.find(string.lower(r.name), q, 1, true) ~= nil)
                r.frame.Visible = show
                if show then any = true end
            end
            S.frame.Visible = any
            if q ~= "" and any then
                S.body.Visible = true
                S.chevron.Text = "▲"
            end
        end
    end
end

local function selectTab(name)
    currentTab = name
    for n, pg in pairs(pages) do pg.Visible = (n == name) end
    for n, b in pairs(tabBtns) do
        b.BackgroundTransparency = (n == name) and 0.1 or 1
    end
    applyFilter()
end
search:GetPropertyChangedSignal("Text"):Connect(applyFilter)

local function addTab(name, icon)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, 36)
    b.BackgroundColor3 = C.sel
    b.BackgroundTransparency = 1
    b.Text = ""
    b.AutoButtonColor = false
    b.LayoutOrder = nextOrder()
    b.Parent = tabScroll
    corner(b, 9)

    local ic = Instance.new("TextLabel")
    ic.Size = UDim2.fromOffset(24, 36)
    ic.Position = UDim2.fromOffset(10, 0)
    ic.BackgroundTransparency = 1
    ic.Text = icon
    ic.TextSize = 15
    ic.TextColor3 = CONFIG.Accent
    ic.Parent = b

    local tx = Instance.new("TextLabel")
    tx.Size = UDim2.new(1, -44, 1, 0)
    tx.Position = UDim2.fromOffset(40, 0)
    tx.BackgroundTransparency = 1
    tx.Text = name
    tx.TextColor3 = C.white
    tx.Font = Enum.Font.GothamMedium
    tx.TextSize = 14
    tx.TextXAlignment = Enum.TextXAlignment.Left
    tx.Parent = b

    b.MouseButton1Click:Connect(function() selectTab(name) end)
    tabBtns[name] = b
end

-- Seção recolhível
local function newSection(pageName, icon, title)
    local page = pages[pageName]
    local sec = Instance.new("Frame")
    sec.Size = UDim2.new(1, -8, 0, 0)
    sec.AutomaticSize = Enum.AutomaticSize.Y
    sec.BackgroundColor3 = C.card
    sec.BackgroundTransparency = 0.15
    sec.BorderSizePixel = 0
    sec.LayoutOrder = nextOrder()
    sec.Parent = page
    corner(sec, 12)
    local sl = Instance.new("UIListLayout")
    sl.SortOrder = Enum.SortOrder.LayoutOrder
    sl.Parent = sec

    local head = Instance.new("TextButton")
    head.Size = UDim2.new(1, 0, 0, 38)
    head.BackgroundTransparency = 1
    head.Text = ""
    head.AutoButtonColor = false
    head.LayoutOrder = 0
    head.Parent = sec

    local ic = Instance.new("TextLabel")
    ic.Size = UDim2.fromOffset(22, 38)
    ic.Position = UDim2.fromOffset(14, 0)
    ic.BackgroundTransparency = 1
    ic.Text = icon
    ic.TextSize = 14
    ic.TextColor3 = CONFIG.Accent
    ic.Parent = head

    local tt = Instance.new("TextLabel")
    tt.Size = UDim2.new(1, -80, 1, 0)
    tt.Position = UDim2.fromOffset(40, 0)
    tt.BackgroundTransparency = 1
    tt.Text = title
    tt.TextColor3 = C.white
    tt.Font = Enum.Font.GothamBold
    tt.TextSize = 14
    tt.TextXAlignment = Enum.TextXAlignment.Left
    tt.Parent = head

    local chev = Instance.new("TextLabel")
    chev.Size = UDim2.fromOffset(20, 38)
    chev.AnchorPoint = Vector2.new(1, 0)
    chev.Position = UDim2.new(1, -12, 0, 0)
    chev.BackgroundTransparency = 1
    chev.Text = "▲"
    chev.TextSize = 10
    chev.TextColor3 = CONFIG.Accent
    chev.Parent = head

    local body = Instance.new("Frame")
    body.Size = UDim2.new(1, 0, 0, 0)
    body.AutomaticSize = Enum.AutomaticSize.Y
    body.BackgroundTransparency = 1
    body.LayoutOrder = 1
    body.Parent = sec
    local bl = Instance.new("UIListLayout")
    bl.Padding = UDim.new(0, 2)
    bl.SortOrder = Enum.SortOrder.LayoutOrder
    bl.Parent = body
    local bp = Instance.new("UIPadding")
    bp.PaddingLeft = UDim.new(0, 6)
    bp.PaddingRight = UDim.new(0, 6)
    bp.PaddingBottom = UDim.new(0, 8)
    bp.Parent = body

    local S = { frame = sec, body = body, chevron = chev, page = pageName, rows = {} }
    head.MouseButton1Click:Connect(function()
        body.Visible = not body.Visible
        chev.Text = body.Visible and "▲" or "▼"
    end)
    table.insert(allSections, S)
    return S
end

local function newRow(S, title, height)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1, 0, 0, height or 40)
    row.BackgroundTransparency = 1
    row.LayoutOrder = nextOrder()
    row.Parent = S.body

    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(0.5, -8, 1, 0)
    t.Position = UDim2.fromOffset(8, 0)
    t.BackgroundTransparency = 1
    t.Text = title
    t.TextColor3 = C.white
    t.Font = Enum.Font.GothamMedium
    t.TextSize = 14
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.TextTruncate = Enum.TextTruncate.AtEnd
    t.Parent = row

    table.insert(S.rows, { frame = row, name = title })
    return row
end

-- Switch (pílula)
local function toggleRow(S, title, default, cb)
    local row = newRow(S, title)
    local sw = Instance.new("TextButton")
    sw.Size = UDim2.fromOffset(46, 24)
    sw.AnchorPoint = Vector2.new(1, 0.5)
    sw.Position = UDim2.new(1, -8, 0.5, 0)
    sw.Text = ""
    sw.AutoButtonColor = false
    sw.Parent = row
    corner(sw, 12)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.fromOffset(18, 18)
    knob.AnchorPoint = Vector2.new(0, 0.5)
    knob.BackgroundColor3 = C.white
    knob.BorderSizePixel = 0
    knob.Parent = sw
    corner(knob, 9)

    local on = default
    local function render(animate)
        sw.BackgroundColor3 = on and CONFIG.Accent or C.off
        local goal = on and UDim2.new(1, -21, 0.5, 0) or UDim2.new(0, 3, 0.5, 0)
        if animate then
            TweenService:Create(knob, TweenInfo.new(0.12), { Position = goal }):Play()
        else
            knob.Position = goal
        end
    end
    render(false)
    sw.MouseButton1Click:Connect(function()
        on = not on
        render(true)
        cb(on)
    end)
end

-- Slider
local function sliderRow(S, title, min, max, default, cb)
    local row = newRow(S, title)

    local val = Instance.new("TextLabel")
    val.Size = UDim2.fromOffset(44, 20)
    val.AnchorPoint = Vector2.new(1, 0.5)
    val.Position = UDim2.new(1, -128, 0.5, 0)
    val.BackgroundTransparency = 1
    val.TextColor3 = C.white
    val.Font = Enum.Font.Gotham
    val.TextSize = 13
    val.TextXAlignment = Enum.TextXAlignment.Right
    val.Parent = row

    local hit = Instance.new("Frame")
    hit.Size = UDim2.fromOffset(110, 28)
    hit.AnchorPoint = Vector2.new(1, 0.5)
    hit.Position = UDim2.new(1, -8, 0.5, 0)
    hit.BackgroundTransparency = 1
    hit.Parent = row

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1, 0, 0, 4)
    bar.Position = UDim2.new(0, 0, 0.5, -2)
    bar.BackgroundColor3 = C.off
    bar.BorderSizePixel = 0
    bar.Parent = hit
    corner(bar, 2)

    local fill = Instance.new("Frame")
    fill.BackgroundColor3 = CONFIG.Accent
    fill.BorderSizePixel = 0
    fill.Size = UDim2.new(0, 0, 1, 0)
    fill.Parent = bar
    corner(fill, 2)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.fromOffset(13, 13)
    knob.AnchorPoint = Vector2.new(0.5, 0.5)
    knob.BackgroundColor3 = C.white
    knob.BorderSizePixel = 0
    knob.Parent = bar
    corner(knob, 7)

    local function set(v)
        v = math.clamp(v, min, max)
        local rel = (v - min) / (max - min)
        fill.Size = UDim2.new(rel, 0, 1, 0)
        knob.Position = UDim2.new(rel, 0, 0.5, 0)
        val.Text = (max <= 1) and string.format("%.2f", v) or tostring(math.floor(v))
        cb(v)
    end
    set(default)

    local dragging = false
    local function update(x)
        local rel = math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
        local v = min + (max - min) * rel
        if max > 1 then v = math.floor(v + 0.5) end
        set(v)
    end
    hit.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            update(i.Position.X)
        end
    end)
    bind(UIS.InputChanged:Connect(function(i)
        if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            update(i.Position.X)
        end
    end))
    bind(UIS.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end))
end

-- Dropdown (toca pra alternar)
local function dropdownRow(S, title, options, default, cb)
    local row = newRow(S, title)
    local b = Instance.new("TextButton")
    b.Size = UDim2.fromOffset(120, 30)
    b.AnchorPoint = Vector2.new(1, 0.5)
    b.Position = UDim2.new(1, -8, 0.5, 0)
    b.BackgroundColor3 = C.field
    b.TextColor3 = C.white
    b.Font = Enum.Font.GothamMedium
    b.TextSize = 13
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.AutoButtonColor = false
    b.Parent = row
    corner(b, 8)
    local pad = Instance.new("UIPadding")
    pad.PaddingLeft = UDim.new(0, 12)
    pad.Parent = b
    local arrows = Instance.new("TextLabel")
    arrows.Size = UDim2.fromOffset(20, 30)
    arrows.AnchorPoint = Vector2.new(1, 0)
    arrows.Position = UDim2.new(1, -4, 0, 0)
    arrows.BackgroundTransparency = 1
    arrows.Text = "⇅"
    arrows.TextColor3 = CONFIG.Accent
    arrows.TextSize = 14
    arrows.Parent = b

    local idx = table.find(options, default) or 1
    local function render() b.Text = options[idx] end
    render()
    b.MouseButton1Click:Connect(function()
        idx = idx % #options + 1
        render()
        cb(options[idx])
    end)
end

-- Botão de ação
local function buttonRow(S, title, btnText, cb, danger)
    local row = newRow(S, title)
    local b = Instance.new("TextButton")
    b.Size = UDim2.fromOffset(92, 28)
    b.AnchorPoint = Vector2.new(1, 0.5)
    b.Position = UDim2.new(1, -8, 0.5, 0)
    b.BackgroundColor3 = danger and Color3.fromRGB(120, 40, 50) or CONFIG.Accent
    b.TextColor3 = C.white
    b.Font = Enum.Font.GothamBold
    b.TextSize = 13
    b.Text = btnText
    b.AutoButtonColor = true
    b.Parent = row
    corner(b, 8)
    b.MouseButton1Click:Connect(cb)
    return b
end

-- Campo de texto
local function inputRow(S, title, placeholder)
    local row = newRow(S, title)
    local box = Instance.new("TextBox")
    box.Size = UDim2.fromOffset(140, 28)
    box.AnchorPoint = Vector2.new(1, 0.5)
    box.Position = UDim2.new(1, -8, 0.5, 0)
    box.BackgroundColor3 = C.field
    box.PlaceholderText = placeholder
    box.PlaceholderColor3 = C.mute
    box.Text = ""
    box.TextColor3 = C.white
    box.Font = Enum.Font.Gotham
    box.TextSize = 12
    box.ClearTextOnFocus = false
    box.Parent = row
    corner(box, 8)
    return box
end

-- Linha de texto simples
local function textRow(S, title, text, color)
    local row = newRow(S, title)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(0.5, -8, 1, 0)
    l.AnchorPoint = Vector2.new(1, 0)
    l.Position = UDim2.new(1, -8, 0, 0)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = color or C.mute
    l.Font = Enum.Font.GothamBold
    l.TextSize = 12
    l.TextXAlignment = Enum.TextXAlignment.Right
    l.TextTruncate = Enum.TextTruncate.AtEnd
    l.Parent = row
    return l
end

----------------------------------------------------------------
-- Botões ▲ ▼ do fly
----------------------------------------------------------------
local flyPad = Instance.new("Frame")
flyPad.Size = UDim2.fromOffset(60, 130)
flyPad.Position = UDim2.new(1, -80, 0.5, -65)
flyPad.BackgroundTransparency = 1
flyPad.Visible = false
flyPad.Parent = gui

local function padBtn(text, y, dir)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, 60)
    b.Position = UDim2.fromOffset(0, y)
    b.BackgroundColor3 = CONFIG.Accent
    b.BackgroundTransparency = 0.25
    b.Text = text
    b.TextColor3 = C.white
    b.Font = Enum.Font.GothamBold
    b.TextSize = 28
    b.Parent = flyPad
    corner(b, 12)
    b.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            State.vert = dir
        end
    end)
    b.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            State.vert = 0
        end
    end)
end
padBtn("▲", 0, 1)
padBtn("▼", 70, -1)

----------------------------------------------------------------
-- ABAS
----------------------------------------------------------------
addTab("Home", "🏠")
addTab("Visual", "👁")
addTab("Aimbot", "🎯")
addTab("Ghost", "👻")
addTab("Auto Shoot", "🔫")
addTab("Fling", "💥")
addTab("Animations", "🎭")
addTab("Tools", "🛠")
addTab("Settings", "⚙")
addTab("Credits", "⭐")

newPage("Home")
newPage("Visual")
newPage("Aimbot")
newPage("Ghost")
newPage("Auto Shoot")
newPage("Fling")
newPage("Animations")
newPage("Tools")
newPage("Settings")
newPage("Credits")

-- HOME ---------------------------------------------------------
local sMove = newSection("Home", "🏃", "Movement")
toggleRow(sMove, "Fly", false, function(v)
    State.fly = v
    flyPad.Visible = v
    State.vert = 0
    if v then startFly() else stopFly() end
end)
sliderRow(sMove, "Fly Speed", 10, 300, State.flySpeed, function(v) State.flySpeed = v end)
toggleRow(sMove, "Speed", false, function(v)
    State.speedOn = v
    if not v then local h = getHum() if h then h.WalkSpeed = 16 end end
end)
sliderRow(sMove, "Walk Speed", 16, 300, State.walkSpeed, function(v) State.walkSpeed = v end)
toggleRow(sMove, "Noclip", false, function(v)
    State.noclip = v
    if not v and not State.ghost and not State.noclipTool then restoreCollide() end
end)

local sJump = newSection("Home", "⬆", "Jump")
toggleRow(sJump, "Infinite Jump", false, function(v) State.infJump = v end)
toggleRow(sJump, "Jump Boost", false, function(v)
    State.jumpOn = v
    if not v then
        local h = getHum()
        if h then
            if h.UseJumpPower then h.JumpPower = 50 else h.JumpHeight = 7.2 end
        end
    end
end)
sliderRow(sJump, "Jump Power", 50, 400, State.jumpPower, function(v) State.jumpPower = v end)

-- VISUAL -------------------------------------------------------
local sEsp = newSection("Visual", "👁", "ESP")
toggleRow(sEsp, "ESP", false, function(v)
    State.esp = v
    refreshESP()
end)
toggleRow(sEsp, "ESP Lines", false, function(v) State.lines = v end)

local sRole = newSection("Visual", "🔪", "Role ESP (MM2)")
toggleRow(sRole, "Role ESP", false, function(v)
    State.roleEsp = v
    if not v then
        for _, p in ipairs(Players:GetPlayers()) do clearRole(p) end
    end
end)
textRow(sRole, "Legenda", "Murder 🔴  Sheriff 🔵  Innocent 🟢", C.mute)

-- AIMBOT -------------------------------------------------------
local sAim = newSection("Aimbot", "🎯", "Aimbot")
toggleRow(sAim, "Aimbot", false, function(v) State.aim = v end)
toggleRow(sAim, "Show FOV", false, function(v) State.showFov = v end)
dropdownRow(sAim, "Aim Part", { "Head", "Torso" }, "Head", function(v)
    State.aimPart = (v == "Torso") and "HumanoidRootPart" or "Head"
end)
sliderRow(sAim, "FOV Size", 50, 600, State.aimFov, function(v) State.aimFov = v end)
sliderRow(sAim, "Smoothness", 0.05, 1, State.aimSmooth, function(v) State.aimSmooth = v end)

local sChecks = newSection("Aimbot", "🛡", "Checks")
toggleRow(sChecks, "Team Check", true, function(v) State.teamCheck = v end)
toggleRow(sChecks, "Wall Check", false, function(v) State.wallCheck = v end)

-- GHOST --------------------------------------------------------
local sGhost = newSection("Ghost", "👻", "Ghost Mode")
toggleRow(sGhost, "Ghost Mode", false, function(v)
    State.ghost = v
    if v then
        pcall(setGhost, true)
    else
        setGhost(false)
        if not State.noclip and not State.noclipTool then restoreCollide() end
        if not State.speedOn then
            local h = getHum()
            if h then h.WalkSpeed = 16 end
        end
    end
end)
sliderRow(sGhost, "Transparência (%)", 10, 95, math.floor(State.ghostTransp * 100), function(v)
    State.ghostTransp = v / 100
end)
sliderRow(sGhost, "Ghost Speed", 16, 100, State.ghostSpeed, function(v) State.ghostSpeed = v end)
textRow(sGhost, "Info", "Visual só pra você + atravessa paredes", C.mute)

-- AUTO SHOOT ---------------------------------------------------
local sShoot = newSection("Auto Shoot", "🔫", "Auto Shoot")
toggleRow(sShoot, "Auto Shoot", false, function(v) State.shoot = v end)
toggleRow(sShoot, "Lock Aim", true, function(v) State.shootLock = v end)
toggleRow(sShoot, "Only Murderer (MM2)", false, function(v) State.shootOnlyMurder = v end)
toggleRow(sShoot, "Auto Equip", false, function(v) State.autoEquip = v end)
dropdownRow(sShoot, "Fire Method", { "Tool", "Click" }, "Tool", function(v) State.fireMethod = v end)
sliderRow(sShoot, "Shoot FOV", 30, 400, State.shootFov, function(v) State.shootFov = v end)
sliderRow(sShoot, "Fire Delay (ms)", 50, 1000, math.floor(State.shootDelay * 1000), function(v)
    State.shootDelay = v / 1000
end)

-- FLING --------------------------------------------------------
local sTarget = newSection("Fling", "🎯", "Target")
local selLabel = textRow(sTarget, "Alvo", "nenhum", CONFIG.Accent)

local listWrap = Instance.new("Frame")
listWrap.Size = UDim2.new(1, 0, 0, 120)
listWrap.BackgroundColor3 = C.side
listWrap.BackgroundTransparency = 0.3
listWrap.LayoutOrder = nextOrder()
listWrap.Parent = sTarget.body
corner(listWrap, 9)
table.insert(sTarget.rows, { frame = listWrap, name = "lista de jogadores alvo" })

local listFrame = Instance.new("ScrollingFrame")
listFrame.Size = UDim2.new(1, -10, 1, -10)
listFrame.Position = UDim2.fromOffset(5, 5)
listFrame.BackgroundTransparency = 1
listFrame.BorderSizePixel = 0
listFrame.ScrollBarThickness = 3
listFrame.ScrollBarImageColor3 = CONFIG.Accent
listFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
listFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
listFrame.Parent = listWrap
local ll = Instance.new("UIListLayout")
ll.Padding = UDim.new(0, 4)
ll.Parent = listFrame

local function rebuildList()
    for _, c in ipairs(listFrame:GetChildren()) do
        if c:IsA("TextButton") then c:Destroy() end
    end
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp then
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, -6, 0, 28)
            b.BackgroundColor3 = C.field
            b.TextColor3 = C.white
            b.Font = Enum.Font.Gotham
            b.TextSize = 12
            b.Text = p.DisplayName .. " (@" .. p.Name .. ")"
            b.Parent = listFrame
            corner(b, 7)
            b.MouseButton1Click:Connect(function()
                selected = p
                selLabel.Text = p.DisplayName
            end)
        end
    end
end
rebuildList()
bind(Players.PlayerAdded:Connect(function() task.wait(0.5) rebuildList() end))
bind(Players.PlayerRemoving:Connect(function(p)
    if selected == p then selected = nil selLabel.Text = "nenhum" end
    task.wait(0.2)
    rebuildList()
end))

local sFling = newSection("Fling", "💥", "Fling")
sliderRow(sFling, "Tempo por alvo", 1, 6, State.flingTime, function(v) State.flingTime = v end)
buttonRow(sFling, "Fling Alvo", "Executar", function() task.spawn(flingSelected) end)
buttonRow(sFling, "Fling Todos", "Executar", function() task.spawn(flingAll) end)
buttonRow(sFling, "Parar Fling", "Parar", function() State.flingOn = false end, true)

-- ANIMATIONS ---------------------------------------------------
local sPre = newSection("Animations", "🎭", "Presets")
local presets = {
    { "Wave",    "507770239" },
    { "Point",   "507770453" },
    { "Cheer",   "507770677" },
    { "Laugh",   "507770818" },
    { "Dance 1", "507771019" },
    { "Dance 2", "507776043" },
    { "Dance 3", "507777268" },
}
for _, a in ipairs(presets) do
    buttonRow(sPre, a[1], "Play", function() playAnim(a[2]) end)
end

local sCustom = newSection("Animations", "🎬", "Custom")
local animBox = inputRow(sCustom, "Animation ID", "ID da animação")
buttonRow(sCustom, "Tocar ID", "Play", function() playAnim(animBox.Text) end)

local sCtl = newSection("Animations", "⚙", "Controle")
sliderRow(sCtl, "Anim Speed (%)", 10, 300, 100, function(v)
    State.animSpeed = v / 100
    if animTrack then pcall(function() animTrack:AdjustSpeed(State.animSpeed) end) end
end)
toggleRow(sCtl, "Loop", true, function(v)
    State.animLoop = v
    if animTrack then pcall(function() animTrack.Looped = v end) end
end)
buttonRow(sCtl, "Parar animação", "Stop", stopAnim, true)

-- TOOLS --------------------------------------------------------
local sTools = newSection("Tools", "🛠", "Ferramentas")
toggleRow(sTools, "Noclip Tool", false, function(v)
    State.wantNoclipTool = v
    if v then
        giveNoclipTool()
    else
        removeTool("noclip")
        State.noclipTool = false
        if not State.noclip and not State.ghost then restoreCollide() end
    end
end)
toggleRow(sTools, "Invisible Tool", false, function(v)
    State.wantInvisTool = v
    if v then
        giveInvisTool()
    else
        removeTool("invis")
        State.invisTool = false
        setInvisible(false)
    end
end)
textRow(sTools, "Como usar", "Equipe a tool e toque pra ligar/desligar", C.mute)
textRow(sTools, "Invisible", "Visual só pra você (outros te veem)", C.mute)

-- SETTINGS -----------------------------------------------------
local sUI = newSection("Settings", "🖼", "Interface")
sliderRow(sUI, "Background", 0, 100, math.floor(CONFIG.BgImageTransp * 100), function(v)
    bgImg.ImageTransparency = v / 100
end)

local sKey = newSection("Settings", "🔑", "Key")
buttonRow(sKey, "Esquecer key salva", "Apagar", function()
    if delfile and isfile then pcall(function()
        if isfile(KEY_FILE) then delfile(KEY_FILE) end
    end) end
end, true)

-- CREDITS ------------------------------------------------------
local sCred = newSection("Credits", "⭐", "Créditos")
local credHub = textRow(sCred, "Hub", CONFIG.HubName .. " " .. CONFIG.Version, C.white)
local cr1 = textRow(sCred, "Criador", "8tfu3", C.white)
local cr2 = textRow(sCred, "Créditos", "davirio", C.white)
table.insert(rgbLabels, cr1)
table.insert(rgbLabels, cr2)

selectTab("Home")

----------------------------------------------------------------
-- Minimizar / maximizar / fechar
----------------------------------------------------------------
local bubble = Instance.new("TextButton")
bubble.Size = UDim2.fromOffset(44, 44)
bubble.Position = UDim2.new(0, 10, 0.5, -22)
bubble.BackgroundColor3 = CONFIG.Accent
bubble.Text = "🌸"
bubble.TextSize = 22
bubble.Visible = false
bubble.Parent = gui
corner(bubble, 22)

minBtn.MouseButton1Click:Connect(function()
    main.Visible = false
    bubble.Visible = true
end)
bubble.MouseButton1Click:Connect(function()
    main.Visible = true
    bubble.Visible = false
end)

local maximized = false
maxBtn.MouseButton1Click:Connect(function()
    maximized = not maximized
    local vs = cam.ViewportSize
    if maximized then
        main.Size = UDim2.fromOffset(math.min(vs.X * 0.9, 780), math.min(vs.Y * 0.88, 440))
    else
        main.Size = UDim2.fromOffset(W, H)
    end
    main.Position = UDim2.new(0.5, -main.Size.X.Offset / 2, 0.5, -main.Size.Y.Offset / 2)
end)

closeBtn.MouseButton1Click:Connect(function()
    State.fly, State.esp, State.aim, State.lines, State.roleEsp = false, false, false, false, false
    State.speedOn, State.jumpOn, State.noclip, State.infJump = false, false, false, false
    State.flingOn, State.ghost, State.shoot = false, false, false
    State.noclipTool, State.invisTool = false, false
    State.wantNoclipTool, State.wantInvisTool = false, false
    stopFly()
    refreshESP()
    for _, p in ipairs(Players:GetPlayers()) do clearRole(p) end
    setGhost(false)
    setInvisible(false)
    removeTool("noclip")
    removeTool("invis")
    restoreCollide()
    stopAnim()
    local h = getHum()
    if h then
        h.WalkSpeed = 16
        if h.UseJumpPower then h.JumpPower = 50 else h.JumpHeight = 7.2 end
    end
    for _, c in ipairs(connections) do pcall(function() c:Disconnect() end) end
    gui:Destroy()
    overlay:Destroy()
end)

end -- fim do LoadHub

----------------------------------------------------------------
-- INÍCIO: checa key salva, senão pede
----------------------------------------------------------------
local saved
if SAVE_KEY and isfile and readfile then
    pcall(function()
        if isfile(KEY_FILE) then saved = readfile(KEY_FILE) end
    end)
end

if saved == KEY then
    LoadHub()
else
    askKey()
end
