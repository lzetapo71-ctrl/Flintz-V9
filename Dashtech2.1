--// SERVICES (OPTIMIZADOS)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

--// PLAYER & CHARACTER
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local character = player.Character or player.CharacterAdded:Wait()
local rootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")
local inputEvent = ReplicatedStorage:WaitForChild("Events"):WaitForChild("Input")

--// VARIABLES DE ESTADO
local isActiveLoop = false 
local script1Available = false 
local isDashingS2 = false
local lastGoTimeS2 = 0
local isLocked = false
local lockedTarget = nil
local highlight = nil
local lookConn = nil

-- CONFIG
local dashForceS1 = 150
local dashDurationS1 = 0.20
local cooldownS2 = 1.1
local DISTANCIA_RODEO_S2 = 12
local DISTANCIA_ESPALDA_LOCK = 5

--// CACHE DE ANIMACIONES
local function loadAnim(id)
    local a = Instance.new("Animation")
    a.AnimationId = id
    return humanoid:LoadAnimation(a)
end
local forwardTrack = loadAnim("rbxassetid://18783040383")

--// FUNCIONES NÚCLEO
local function lanzarGolpe()
    local tool = character:FindFirstChildOfClass("Tool")
    if tool then tool:Activate() end
	inputEvent:FireServer("M1")
end

local function clearLock()
	isLocked = false; lockedTarget = nil
    humanoid.AutoRotate = true 
	if highlight then highlight:Destroy() highlight = nil end
	if lookConn then lookConn:Disconnect() lookConn = nil end
    local gui = player.PlayerGui:FindFirstChild("UnifiedScriptGui")
    if gui and gui:FindFirstChild("LockFrame") then
        gui.LockFrame.LockBtn.Text = "LOCK"; gui.LockFrame.LockBtn.TextColor3 = Color3.fromRGB(200, 60, 60)
    end
end

local function lockSpecificTarget(target)
    if not target or not target:FindFirstChild("HumanoidRootPart") then return end
    lockedTarget = target; isLocked = true
    humanoid.AutoRotate = false 
    highlight = Instance.new("Highlight", target); highlight.FillColor = Color3.fromRGB(255, 60, 60)
    highlight.OutlineTransparency = 0.5
    
    local gui = player.PlayerGui:FindFirstChild("UnifiedScriptGui")
    if gui then
        gui.LockFrame.LockBtn.Text = "UNLOCK"; gui.LockFrame.LockBtn.TextColor3 = Color3.fromRGB(60, 200, 60)
    end
    
    lookConn = RunService.RenderStepped:Connect(function()
        if not isLocked or not lockedTarget or not lockedTarget.Parent then clearLock() return end
        local tRoot = lockedTarget:FindFirstChild("HumanoidRootPart")
        if not tRoot or lockedTarget.Humanoid.Health <= 0 then clearLock() return end
        
        rootPart.CFrame = CFrame.lookAt(rootPart.Position, Vector3.new(tRoot.Position.X, rootPart.Position.Y, tRoot.Position.Z))
        
        if humanoid.MoveDirection.Magnitude < 0.05 and not isDashingS2 then
            local pEspalda = tRoot.Position - (tRoot.CFrame.LookVector * DISTANCIA_ESPALDA_LOCK)
            if (pEspalda - rootPart.Position).Magnitude > 1.5 then
                humanoid:Move((pEspalda - rootPart.Position).Unit, false)
            end
        end
    end)
end

--// DASH S1 (FÍSICO - LOOP)
local function applyDashS1()
    if forwardTrack then forwardTrack:Play() end
    lanzarGolpe()
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(50000, 0, 50000); bv.Parent = rootPart
    local dir = (humanoid.MoveDirection.Magnitude > 0.1) and humanoid.MoveDirection or rootPart.CFrame.LookVector
    
    for _, p in ipairs(character:GetChildren()) do if p:IsA("BasePart") then p.CanCollide = false end end

    task.delay(dashDurationS1, function()
        bv:Destroy()
        for _, p in ipairs(character:GetChildren()) do if p:IsA("BasePart") then p.CanCollide = true end end
    end)
    
    local start = os.clock()
    local conn
    conn = RunService.RenderStepped:Connect(function()
        if os.clock() - start < dashDurationS1 then
            bv.Velocity = dir * dashForceS1
            rootPart.CFrame = rootPart.CFrame:Lerp(CFrame.new(rootPart.Position, rootPart.Position + dir), 0.15)
        else
            conn:Disconnect()
        end
    end)
end

--// DASH S2 (SUAVE - ÓRBITA)
local function applyDashS2()
    if isDashingS2 then return end
    isDashingS2 = true
    if forwardTrack then forwardTrack:Play() end
    lanzarGolpe()
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(500000, 500000, 500000); bv.Parent = rootPart
    local force = (humanoid.FloorMaterial == Enum.Material.Air) and 115 or 140
    local start = os.clock()
    local orbitDir = (math.random(1, 2) == 1) and 1 or -1
    local currentT = lockedTarget
    
    local conn
    conn = RunService.RenderStepped:Connect(function()
        local elapsed = os.clock() - start
        if elapsed < 0.35 and humanoid.Health > 0 then
            local dir
            if isLocked and currentT and currentT.Parent then
                local tRoot = currentT:FindFirstChild("HumanoidRootPart")
                if tRoot then
                    local vec = (tRoot.Position - rootPart.Position)
                    if vec.Magnitude < DISTANCIA_RODEO_S2 then
                        dir = (Vector3.new(-vec.Unit.Z, 0, vec.Unit.X) * orbitDir + vec.Unit * 0.4).Unit
                    else dir = Vector3.new(vec.X, 0, vec.Z).Unit end
                end
            end
            if not dir then dir = (humanoid.MoveDirection.Magnitude > 0.1) and humanoid.MoveDirection or rootPart.CFrame.LookVector end
            bv.Velocity = dir * force
            rootPart.CFrame = rootPart.CFrame:Lerp(CFrame.lookAt(rootPart.Position, rootPart.Position + dir), 0.25)
        else
            bv:Destroy(); conn:Disconnect(); isDashingS2 = false
            if not isLocked then humanoid.AutoRotate = true end
        end
    end)
end

--// INTERFAZ (UI)
local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
ScreenGui.Name = "UnifiedScriptGui"; ScreenGui.ResetOnSpawn = false

local function makeDraggable(frame, padlock)
    local active = true
    padlock.MouseButton1Click:Connect(function()
        active = not active
        padlock.Text = active and "🔓" or "🔒"
        padlock.BackgroundColor3 = active and Color3.fromRGB(40,40,40) or Color3.fromRGB(150,0,0)
    end)
    local drag, start, startPos
    frame.InputBegan:Connect(function(input)
        if active and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
            drag = true; start = input.Position; startPos = frame.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if drag and active then
            local delta = input.Position - start
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function() drag = false end)
end

local GoFrame = Instance.new("Frame", ScreenGui)
GoFrame.Size = UDim2.fromOffset(110, 55)
GoFrame.Position = UDim2.new(0.83, -45, 0.12, 0)
GoFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20); Instance.new("UICorner", GoFrame)
local GoBtn = Instance.new("TextButton", GoFrame); GoBtn.Size = UDim2.new(0.65, 0, 0.8, 0); GoBtn.Position = UDim2.new(0.05, 0, 0.1, 0); GoBtn.Text = "Go"; GoBtn.BackgroundColor3 = Color3.new(0,0,0); GoBtn.TextColor3 = Color3.new(1,1,1); GoBtn.TextSize = 20; Instance.new("UICorner", GoBtn)
local GoPad = Instance.new("TextButton", GoFrame); GoPad.Size = UDim2.new(0.2, 0, 0.4, 0); GoPad.Position = UDim2.new(0.75, 0, 0.1, 0); GoPad.Text = "🔓"; GoPad.BackgroundColor3 = Color3.fromRGB(40,40,40); Instance.new("UICorner", GoPad)
local LoopT = Instance.new("TextButton", GoFrame); LoopT.Size = UDim2.new(0.2, 0, 0.4, 0); LoopT.Position = UDim2.new(0.75, 0, 0.55, 0); LoopT.Text = "OFF"; LoopT.BackgroundColor3 = Color3.fromRGB(170, 0, 0); LoopT.TextSize = 10; Instance.new("UICorner", LoopT)

local LockFrame = Instance.new("Frame", ScreenGui)
LockFrame.Size = UDim2.fromOffset(165, 50); LockFrame.Position = UDim2.new(0.5, -82, 0.7, 0); LockFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20); Instance.new("UICorner", LockFrame)
local LockBtn = Instance.new("TextButton", LockFrame); LockBtn.Size = UDim2.new(0.6, 0, 0.75, 0); LockBtn.Position = UDim2.new(0.05, 0, 0.125, 0); LockBtn.Text = "LOCK"; LockBtn.TextColor3 = Color3.fromRGB(200, 60, 60); LockBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); Instance.new("UICorner", LockBtn)
local LockPad = Instance.new("TextButton", LockFrame); LockPad.Size = UDim2.new(0.25, 0, 0.75, 0); LockPad.Position = UDim2.new(0.7, 0, 0.125, 0); LockPad.Text = "🔓"; LockPad.BackgroundColor3 = Color3.fromRGB(40, 40, 40); Instance.new("UICorner", LockPad)

makeDraggable(GoFrame, GoPad); makeDraggable(LockFrame, LockPad)

--// CLICS
GoBtn.MouseButton1Click:Connect(function()
    if humanoid.Health <= 0 then return end
    if script1Available then
        script1Available = false
        inputEvent:FireServer("Dash", false); applyDashS1()
    elseif os.clock() - lastGoTimeS2 >= cooldownS2 then
        lastGoTimeS2 = os.clock()
        inputEvent:FireServer("Dash", false); applyDashS2()
    end
end)

LockBtn.MouseButton1Click:Connect(function()
    if isLocked then clearLock() else 
        local closest, short = nil, 300
        for _, m in ipairs(workspace:GetChildren()) do
            if m:IsA("Model") and m ~= character and m:FindFirstChild("HumanoidRootPart") then
                local sPos, vis = camera:WorldToViewportPoint(m.HumanoidRootPart.Position)
                if vis then
                    local d = (Vector2.new(sPos.X, sPos.Y) - camera.ViewportSize/2).Magnitude
                    if d < short then short = d; closest = m end
                end
            end
        end
        lockSpecificTarget(closest)
    end
end)

LoopT.MouseButton1Click:Connect(function()
    isActiveLoop = not isActiveLoop
    LoopT.Text = isActiveLoop and "ON" or "OFF"
    LoopT.BackgroundColor3 = isActiveLoop and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(170, 0, 0)
end)

--// DETECCIÓN ANIMACIONES (OPTIMIZADA)
local function setupDetection(hum)
    hum.AnimationPlayed:Connect(function(track)
        local id = tostring(track.Animation.AnimationId)
        
        if id:find("14817925281") or id:find("16523111553") then
            script1Available = true
            if isActiveLoop then
                local sCF = rootPart.CFrame
                task.spawn(function()
                    for i = 1, 20 do
                        local angle = math.rad((i/20)*360)
                        rootPart.CFrame = sCF * CFrame.new(math.sin(angle)*5, 0, (math.cos(angle)*-5)+5 - (i/20)*4) * CFrame.Angles(0, angle, 0)
                        RunService.Heartbeat:Wait()
                    end
                end)
            end
        end
        
        if id:find("18790224306") then
            lanzarGolpe()
            if isLocked and lockedTarget then
                local tRestore = lockedTarget
                clearLock()
                task.delay(track.Length - 0.5, function()
                    if tRestore and tRestore.Parent and not isLocked then lockSpecificTarget(tRestore) end
                end)
            end
        end
        
        if id:find("18783040383") or id:find("18783044488") then lanzarGolpe() end
    end)
end

setupDetection(humanoid)
player.CharacterAdded:Connect(function(char)
    character = char; rootPart = char:WaitForChild("HumanoidRootPart"); humanoid = char:WaitForChild("Humanoid")
    forwardTrack = loadAnim("rbxassetid://18783040383") -- Recargar animación al morir
    setupDetection(humanoid)
end)
