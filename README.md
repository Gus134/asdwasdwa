-- =====================================================
-- DSYNC PESADO (PROXY + BUFFER)
-- ESTILO USADO EM JOGOS TIPO BRAINROT
-- =====================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ================= CONFIG =================

local ATRASO = 0.35 -- segundos de "lag" visual
local dsyncAtivo = false

-- ================= ESTADO =================

local proxy = nil
local buffer = {}
local conexao = nil

-- ================= FUNÇÕES =================

local function invisivel(char, estado)
	for _, v in ipairs(char:GetDescendants()) do
		if v:IsA("BasePart") then
			v.LocalTransparencyModifier = estado and 1 or 0
		end
	end
end

local function criarProxy(char)
	local clone = char:Clone()
	clone.Name = "DsyncProxy"

	for _, v in ipairs(clone:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Anchored = true
			v.CanCollide = false
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
	invisivel(char, true)

	buffer = {}

	camera.CameraSubject = proxy:FindFirstChild("Humanoid")

	conexao = RunService.RenderStepped:Connect(function(dt)
		-- grava posição real
		table.insert(buffer, {
			time = os.clock(),
			cframe = root.CFrame
		})

		-- aplica atraso
		local alvo
		for i = 1, #buffer do
			if os.clock() - buffer[i].time >= ATRASO then
				alvo = buffer[i]
			else
				break
			end
		end

		if alvo then
			proxy.HumanoidRootPart.CFrame = alvo.cframe

			-- limpa buffer antigo
			for i = 1, #buffer do
				if buffer[i] == alvo then
					for j = 1, i do
						buffer[j] = nil
					end
					break
				end
			end
		end
	end)
end

local function parar()
	if conexao then
		conexao:Disconnect()
		conexao = nil
	end

	if proxy then
		proxy:Destroy()
		proxy = nil
	end

	local char = player.Character
	if char then
		invisivel(char, false)
		camera.CameraSubject = char:FindFirstChild("Humanoid")
	end
end

-- ================= BOTÃO =================

local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0, 160, 0, 36)
btn.Position = UDim2.new(0, 20, 0.8, 0)
btn.Text = "DSYNC OFF"
btn.Font = Enum.Font.GothamBold
btn.TextSize = 14
btn.BackgroundColor3 = Color3.fromRGB(20,20,20)
btn.TextColor3 = Color3.fromRGB(230,230,230)
btn.BorderSizePixel = 0
btn.Parent = gui

btn.MouseButton1Click:Connect(function()
	dsyncAtivo = not dsyncAtivo

	if dsyncAtivo then
		btn.Text = "DSYNC ON"
		btn.BackgroundColor3 = Color3.fromRGB(120,30,30)
		iniciar()
	else
		btn.Text = "DSYNC OFF"
		btn.BackgroundColor3 = Color3.fromRGB(20,20,20)
		parar()
	end
end)
