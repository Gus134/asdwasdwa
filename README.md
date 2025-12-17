-- =====================================================
-- DSYNC EXTREMO (LIMITE VISUAL DA ENGINE)
-- NÃO É EXPLOIT - ILUSÃO MÁXIMA
-- =====================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================= CONFIG MÁXIMA =================

local ATRASO_MIN = 1.2
local ATRASO_MAX = 2.0

local FREEZE_CHANCE = 0.6
local FREEZE_TIME_MIN = 0.3
local FREEZE_TIME_MAX = 0.8

-- ================= ESTADO =================

local ativo = false
local proxy = nil
local buffer = {}
local conn = nil
local ultimoFreeze = 0

-- ================= UTIL =================

local function setInvisivel(char, estado)
	for _, v in ipairs(char:GetDescendants()) do
		if v:IsA("BasePart") then
			v.LocalTransparencyModifier = estado and 1 or 0
		end
	end
end

local function criarProxy(char)
	local clone = char:Clone()
	clone.Name = "DsyncProxyExtreme"

	for _, v in ipairs(clone:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Anchored = true
			v.CanCollide = false
		end
		if v:IsA("Humanoid") then
			v:ChangeState(Enum.HumanoidStateType.Physics)
		end
	end

	clone.Parent = workspace
	return clone
end

-- ================= DSYNC =================

local function iniciar()
	local char = player.Character
	if not char then return end

	local root = char:WaitForChild("HumanoidRootPart")

	proxy = criarProxy(char)
	setInvisivel(char, true)
	camera.CameraSubject = proxy:FindFirstChild("Humanoid")

	buffer = {}
	ultimoFreeze = os.clock()

	conn = RunService.RenderStepped:Connect(function()
		local agora = os.clock()

		-- grava posição real
		table.insert(buffer, {
			time = agora,
			cframe = root.CFrame
		})

		-- freeze pesado
		if agora - ultimoFreeze > 1 and math.random() < FREEZE_CHANCE then
			local t = math.random() * (FREEZE_TIME_MAX - FREEZE_TIME_MIN) + FREEZE_TIME_MIN
			task.wait(t)
			ultimoFreeze = agora
		end

		-- atraso variável extremo
		local atraso = math.random() * (ATRASO_MAX - ATRASO_MIN) + ATRASO_MIN
		local alvo = nil

		for i = 1, #buffer do
			if agora - buffer[i].time >= atraso then
				alvo = buffer[i]
			else
				break
			end
		end

		if alvo and proxy and proxy:FindFirstChild("HumanoidRootPart") then
			proxy.HumanoidRootPart.CFrame = alvo.cframe
		end

		-- limpeza agressiva
		while #buffer > 60 do
			table.remove(buffer, 1)
		end
	end)
end

local function parar()
	if conn then
		conn:Disconnect()
		conn = nil
	end

	if proxy then
		proxy:Destroy()
		proxy = nil
	end

	local char = player.Character
	if char then
		setInvisivel(char, false)
		camera.CameraSubject = char:FindFirstChild("Humanoid")
	end
end

-- ================= BOTÃO =================

local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0, 180, 0, 38)
btn.Position = UDim2.new(0, 18, 0.78, 0)
btn.Text = "DSYNC EXTREMO OFF"
btn.Font = Enum.Font.GothamBold
btn.TextSize = 13
btn.BackgroundColor3 = Color3.fromRGB(15,15,15)
btn.TextColor3 = Color3.fromRGB(230,230,230)
btn.BorderSizePixel = 0
btn.Parent = gui

btn.MouseButton1Click:Connect(function()
	ativo = not ativo

	if ativo then
		btn.Text = "DSYNC EXTREMO ON"
		btn.BackgroundColor3 = Color3.fromRGB(140,20,20)
		iniciar()
	else
		btn.Text = "DSYNC EXTREMO OFF"
		btn.BackgroundColor3 = Color3.fromRGB(15,15,15)
		parar()
	end
end)
