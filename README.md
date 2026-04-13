--// SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

--// PLAYER & CHARACTER
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local character = player.Character or player.CharacterAdded:Wait()
local inputEvent = ReplicatedStorage:WaitForChild("Events"):WaitForChild("Input")

--// CONFIGURACIÓN DE GOLPE
local TARGET_ANIMS = {
	["rbxassetid://18790224306"] = true,
	["rbxassetid://18783040383"] = true
}

--// STATE
local isLocked = false
local lockedTarget = nil
local highlight = nil
local lookConn = nil
local DISTANCIA_ESPALDA = 2

--// NOTIFICACIÓN (SIN "FIXED")
local function showNotification()
	local notifGui = Instance.new("ScreenGui", player.PlayerGui)
	notifGui.Name = "NotifAleexis"
	
	local notifFrame = Instance.new("TextLabel", notifGui)
	notifFrame.Size = UDim2.fromScale(0.25, 0.05)
	notifFrame.Position = UDim2.fromScale(0.375, -0.1)
	notifFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
	notifFrame.TextColor3 = Color3.fromRGB(255, 255, 255)
	notifFrame.Text = "Hecho por Flintz" -- Texto corregido
	notifFrame.Font = Enum.Font.GothamBold
	notifFrame.TextSize = 14
	notifFrame.BorderSizePixel = 0
	
	Instance.new("UICorner", notifFrame).CornerRadius = UDim.new(0, 8)

	notifFrame:TweenPosition(UDim2.fromScale(0.375, 0.05), "Out", "Back", 0.5)
	task.wait(3)
	notifFrame:TweenPosition(UDim2.fromScale(0.375, -0.1), "In", "Quad", 0.5)
	task.delay(0.6, function() notifGui:Destroy() end)
end

--// LANZAR GOLPE
local function lanzarGolpe()
    local tool = character:FindFirstChildOfClass("Tool")
    if tool then tool:Activate() end
	inputEvent:FireServer("M1")
end

local clearLock, lockTarget

--// LÓGICA DE BLOQUEO
clearLock = function()
	isLocked = false
	lockedTarget = nil
	if highlight then highlight:Destroy() highlight = nil end
	if lookConn then lookConn:Disconnect() lookConn = nil end
	
    local gui = player.PlayerGui:FindFirstChild("ModernLockGui")
    if gui then
        local btn = gui.MainFrame.TextButton
        btn.Text = "LOCK"
        btn.TextColor3 = Color3.fromRGB(200, 60, 60)
        gui.MainFrame.UIStroke.Color = Color3.fromRGB(80, 80, 80)
    end
end

local function getClosestToCamera()
	local closest, shortest = nil, 300
	local container = workspace:FindFirstChild("Live") or workspace
	for _, model in ipairs(container:GetChildren()) do
		if model:IsA("Model") and model ~= player.Character then
			local root = model:FindFirstChild("HumanoidRootPart")
			local hum = model:FindFirstChild("Humanoid")
			if root and hum and hum.Health > 0 then
				local screenPos, visible = camera:WorldToViewportPoint(root.Position)
				if visible then
					local dist = (Vector2.new(screenPos.X, screenPos.Y) - camera.ViewportSize / 2).Magnitude
					if dist < shortest then shortest = dist closest = model end
				end
			end
		end
	end
	return closest
end

lockTarget = function(specificTarget)
	local char = player.Character
	local myRoot = char and char:FindFirstChild("HumanoidRootPart")
	local myHum = char and char:FindFirstChild("Humanoid")
	if not myRoot or not myHum then return end

	local target = specificTarget or getClosestToCamera()
	if not target then return end

	lockedTarget = target
	isLocked = true
	
	highlight = Instance.new("Highlight", target)
	highlight.FillColor = Color3.fromRGB(255, 60, 60)
	
    local gui = player.PlayerGui:FindFirstChild("ModernLockGui")
    if gui then
        local btn = gui.MainFrame.TextButton
        btn.Text = "UNLOCK"
        btn.TextColor3 = Color3.fromRGB(60, 200, 60)
        gui.MainFrame.UIStroke.Color = Color3.fromRGB(60, 200, 60)
    end

	lookConn = RunService.RenderStepped:Connect(function()
		if not isLocked or not lockedTarget then return end
		local tRoot = lockedTarget:FindFirstChild("HumanoidRootPart")
		local tHum = lockedTarget:FindFirstChild("Humanoid")

		if not tRoot or not tHum or tHum.Health <= 0 then clearLock() return end

		myRoot.CFrame = myRoot.CFrame:Lerp(CFrame.lookAt(myRoot.Position, Vector3.new(tRoot.Position.X, myRoot.Position.Y, tRoot.Position.Z)), 0.4)

		if myHum.MoveDirection.Magnitude < 0.1 then
			local pEspalda = tRoot.Position - (tRoot.CFrame.LookVector * DISTANCIA_ESPALDA)
			if (pEspalda - myRoot.Position).Magnitude > 2.5 then
				myHum:Move((pEspalda - myRoot.Position).Unit, false)
			end
		end
	end)
end

--// DETECCIÓN ANIMACIONES
local function setupAnimationDetection(humanoid)
	humanoid.AnimationPlayed:Connect(function(track)
		local id = track.Animation.AnimationId
		
        if id == "rbxassetid://18790224306" then
            if isLocked and lockedTarget then
                local savedTarget = lockedTarget
                clearLock()
                task.spawn(function()
                    track.Stopped:Wait()
                    if savedTarget and savedTarget.Parent and savedTarget:FindFirstChild("Humanoid") and savedTarget.Humanoid.Health > 0 then
                        lockTarget(savedTarget)
                    end
                end)
            end
        end

		if TARGET_ANIMS[id] then
			lanzarGolpe()
		end
	end)
end

--// CREACIÓN DE INTERFAZ
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "ModernLockGui"
gui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", gui)
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.fromOffset(120, 45)
mainFrame.Position = UDim2.new(0.5, -60, 0.8, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.Active = true

Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)
local stroke = Instance.new("UIStroke", mainFrame)
stroke.Color = Color3.fromRGB(80, 80, 80)
stroke.Thickness = 2

local lockBtn = Instance.new("TextButton", mainFrame)
lockBtn.Size = UDim2.fromScale(1, 1) -- Ocupa todo el frame para facilitar el toque
lockBtn.BackgroundTransparency = 1
lockBtn.Text = "LOCK"
lockBtn.Font = Enum.Font.GothamBold
lockBtn.TextColor3 = Color3.fromRGB(200, 60, 60)
lockBtn.TextSize = 14

--// LÓGICA DE ARRASTRE SÚPER SENSIBLE (SIN TWEEN)
local dragging = false
local dragInput, dragStart, startPos

mainFrame.InputBegan:Connect(function(input)
	if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
		dragging = true
		dragInput = input
		dragStart = input.Position
		startPos = mainFrame.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input == dragInput then
		local delta = input.Position - dragStart
		mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input == dragInput then
		dragging = false
		dragInput = nil
	end
end)

lockBtn.MouseButton1Click:Connect(function()
	if isLocked then clearLock() else lockTarget() end
end)

player.CharacterAdded:Connect(function(char)
    character = char
	clearLock()
	setupAnimationDetection(char:WaitForChild("Humanoid"))
end)

if player.Character then setupAnimationDetection(player.Character:WaitForChild("Humanoid")) end
showNotification()# Flintz-V9
