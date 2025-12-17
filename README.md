-- =====================================================
-- DSYNC REALISTA (ESTILO ROUBE UM BRAINROT)
-- CONTROLE POR BOTÃO - CLIENTE
-- =====================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================= ESTADO =================

local dsyncAtivo = false
local loopConexao = nil

-- ================= UI =====================

local gui = Instance.new("ScreenGui")
gui.Name = "DsyncBrainrotGUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 160, 0, 36)
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

local function getRoot()
	local char = player.Character
	if not char then return nil end
	return char:FindFirstChild("HumanoidRootPart")
end

local function cameraSnap()
	local offset = Vector3.new(
		math.random(-6, 6) * 0.03,
		math.random(-6, 6) * 0.03,
		0
	)
	camera.CFrame = camera.CFrame * CFrame.new(offset)
end

local function personagemSnap()
	local root = getRoot()
	if not root then return end

	local offset = Vector3.new(
		math.random(-4, 4),
		0,
		math.random(-4, 4)
	)

	root.CFrame = root.CFrame * CFrame.new(offset)
end

local function microFreeze()
	RunService.RenderStepped:Wait()
	RunService.RenderStepped:Wait()
end

-- ================= LOOP DSYNC =================

local function iniciarDsync()
	if loopConexao then return end

	loopConexao = RunService.RenderStepped:Connect(function()
		if not dsyncAtivo then return end

		-- snaps irregulares
		if math.random() < 0.25 then
			cameraSnap()
		end

		-- atraso falso
		if math.random() < 0.12 then
			microFreeze()
		end

		-- correção brusca de posição
		if math.random() < 0.08 then
			personagemSnap()
		end
	end)
end

local function pararDsync()
	if loopConexao then
		loopConexao:Disconnect()
		loopConexao = nil
	end
end

-- ================= BOTÃO ====================

botao.MouseButton1Click:Connect(function()
	dsyncAtivo = not dsyncAtivo

	if dsyncAtivo then
		botao.Text = "DSYNC ON"
		botao.BackgroundColor3 = Color3.fromRGB(110, 30, 30)
		iniciarDsync()
	else
		botao.Text = "DSYNC OFF"
		botao.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
		pararDsync()
	end
end)
