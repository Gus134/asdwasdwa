-- =====================================================
-- DSYNC REAL (PERSONAGEM VISUALMENTE LAGADO)
-- ESTILO ROUBE UM BRAINROT
-- =====================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================= ESTADO =================

local dsyncAtivo = false
local clonePersonagem = nil
local conexaoCamera = nil

-- ================= UI =====================

local gui = Instance.new("ScreenGui")
gui.Name = "DsyncLagGUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 170, 0, 36)
botao.Position = UDim2.new(0, 18, 0.78, 0)
botao.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
botao.BorderSizePixel = 0
botao.Text = "DSYNC OFF"
botao.Font = Enum.Font.GothamBold
botao.TextSize = 14
botao.TextColor3 = Color3.fromRGB(220, 220, 220)
botao.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 6)
corner.Parent = botao

-- ================= FUNÇÕES =================

local function tornarInvisivel(char)
	for _, obj in ipairs(char:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = 1
		end
	end
end

local function tornarVisivel(char)
	for _, obj in ipairs(char:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = 0
		end
	end
end

local function criarClone(char)
	local clone = char:Clone()
	clone.Name = "DsyncClone"

	for _, obj in ipairs(clone:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.Anchored = true
			obj.CanCollide = false
		end
		if obj:IsA("Humanoid") then
			obj.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
		end
	end

	clone.Parent = workspace
	return clone
end

local function atualizarClone()
	if not clonePersonagem then return end

	local char = player.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	local cloneRoot = clonePersonagem:FindFirstChild("HumanoidRootPart")

	if root and cloneRoot then
		-- atraso proposital
		if math.random() < 0.15 then
			return
		end

		cloneRoot.CFrame = root.CFrame
	end
end

-- ================= CONTROLE DSYNC =================

local function ligarDsync()
	local char = player.Character
	if not char then return end

	clonePersonagem = criarClone(char)
	tornarInvisivel(char)

	conexaoCamera = RunService.RenderStepped:Connect(function()
		atualizarClone()
	end)

	camera.CameraSubject = clonePersonagem:FindFirstChild("Humanoid")
end

local function desligarDsync()
	if conexaoCamera then
		conexaoCamera:Disconnect()
		conexaoCamera = nil
	end

	if clonePersonagem then
		clonePersonagem:Destroy()
		clonePersonagem = nil
	end

	local char = player.Character
	if char then
		tornarVisivel(char)
		camera.CameraSubject = char:FindFirstChild("Humanoid")
	end
end

-- ================= BOTÃO ====================

botao.MouseButton1Click:Connect(function()
	dsyncAtivo = not dsyncAtivo

	if dsyncAtivo then
		botao.Text = "DSYNC ON"
		botao.BackgroundColor3 = Color3.fromRGB(110, 30, 30)
		ligarDsync()
	else
		botao.Text = "DSYNC OFF"
		botao.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
		desligarDsync()
	end
end)
