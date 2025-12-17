-- =====================================================
-- SERVIÇOS
-- =====================================================
local Players = game:GetService("Players")

local localPlayer = Players.LocalPlayer

-- =====================================================
-- FUNÇÃO PRINCIPAL DO ESP
-- =====================================================
local function criarESPParaJogador(player)
	-- não cria ESP em si mesmo
	if player == localPlayer then
		return
	end

	-- =================================================
	-- FUNÇÃO PARA APLICAR NO PERSONAGEM
	-- =================================================
	local function aplicarESP(character)
		if character == nil then
			return
		end

		local head = character:WaitForChild("Head", 5)
		if head == nil then
			return
		end

		-- evita duplicação
		if head:FindFirstChild("ESP") then
			return
		end

		-- BillboardGui
		local billboardGui = Instance.new("BillboardGui")
		billboardGui.Name = "ESP"
		billboardGui.Adornee = head
		billboardGui.Size = UDim2.new(0, 200, 0, 40)
		billboardGui.StudsOffset = Vector3.new(0, 2.5, 0)
		billboardGui.AlwaysOnTop = true
		billboardGui.Parent = head

		-- Texto
		local textLabel = Instance.new("TextLabel")
		textLabel.Size = UDim2.new(1, 0, 1, 0)
		textLabel.BackgroundTransparency = 1
		textLabel.TextScaled = true
		textLabel.TextStrokeTransparency = 0
		textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
		textLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
		textLabel.Font = Enum.Font.GothamBold
		textLabel.Text = player.Name
		textLabel.Parent = billboardGui
	end

	-- se já tiver personagem
	if player.Character then
		aplicarESP(player.Character)
	end

	-- reaplica ao respawn
	player.CharacterAdded:Connect(function(character)
		aplicarESP(character)
	end)
end

-- =====================================================
-- APLICA PARA JOGADORES ATUAIS
-- =====================================================
for _, player in ipairs(Players:GetPlayers()) do
	criarESPParaJogador(player)
end

-- =====================================================
-- APLICA PARA NOVOS JOGADORES
-- =====================================================
Players.PlayerAdded:Connect(function(player)
	criarESPParaJogador(player)
end)


-- ==========================================
-- BOTÃO DSYNC (CLIENTE)
-- ==========================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

local dsyncAtivo = false
local conexao = nil

-- ================= UI =====================

local gui = Instance.new("ScreenGui")
gui.Name = "DsyncGUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 180, 0, 40)
botao.Position = UDim2.new(0, 20, 0.8, 0)
botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
botao.BorderSizePixel = 0
botao.Text = "DSYNC: DESLIGADO"
botao.TextColor3 = Color3.fromRGB(255, 255, 255)
botao.TextScaled = true
botao.Font = Enum.Font.Gotham
botao.Parent = gui

local canto = Instance.new("UICorner")
canto.CornerRadius = UDim.new(0, 8)
canto.Parent = botao

-- ================= EFEITOS =================

local function efeitoCamera()
	local offset = Vector3.new(
		math.random(-2,2) * 0.1,
		math.random(-2,2) * 0.1,
		0
	)
	camera.CFrame = camera.CFrame * CFrame.new(offset)
end

local function efeitoPersonagem()
	local char = player.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	local deslocamento = Vector3.new(
		math.random(-2,2),
		0,
		math.random(-2,2)
	)

	root.CFrame = root.CFrame * CFrame.new(deslocamento)
end

-- ================= CONTROLE =================

local function ligarDsync()
	if conexao then return end

	conexao = RunService.RenderStepped:Connect(function()
		if not dsyncAtivo then return end

		if math.random() < 0.2 then
			efeitoCamera()
		end

		if math.random() < 0.06 then
			efeitoPersonagem()
		end
	end)
end

local function desligarDsync()
	if conexao then
		conexao:Disconnect()
		conexao = nil
	end
end

-- ================= BOTÃO ===================

botao.MouseButton1Click:Connect(function()
	dsyncAtivo = not dsyncAtivo

	if dsyncAtivo then
		botao.Text = "DSYNC: ATIVADO"
		botao.BackgroundColor3 = Color3.fromRGB(120, 40, 40)
		ligarDsync()
	else
		botao.Text = "DSYNC: DESLIGADO"
		botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		desligarDsync()
	end
end)

