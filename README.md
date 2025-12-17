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
