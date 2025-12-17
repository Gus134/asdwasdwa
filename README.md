local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function criarESP(player)
	if player == LocalPlayer then return end

    local function adicionar(character)
		local head = character:WaitForChild("Head", 5)
		if not head then return end

		-- evita duplicar
		if head:FindFirstChild("ESP") then return end

		local billboard = Instance.new("BillboardGui")
		billboard.Name = "ESP"
		billboard.Adornee = head
		billboard.Size = UDim2.new(0, 200, 0, 40)
		billboard.StudsOffset = Vector3.new(0, 2.5, 0)
		billboard.AlwaysOnTop = true
		billboard.Parent = head

		local text = Instance.new("TextLabel")
		text.Size = UDim2.new(1, 0, 1, 0)
		text.BackgroundTransparency = 1
		text.TextScaled = true
		text.TextStrokeTransparency = 0
		text.TextColor3 = Color3.fromRGB(255, 80, 80)
		text.Font = Enum.Font.GothamBold
		text.Text = player.Name
		text.Parent = billboard
	end

	if player.Character then
		adicionar(player.Character)
	end

	player.CharacterAdded:Connect(adicionar)
end

-- aplica nos jogadores atuais
for _, player in ipairs(Players:GetPlayers()) do
	criarESP(player)
end

-- aplica nos que entrarem
Players.PlayerAdded:Connect(criarESP)
