-- =====================================================
-- SERVIÇOS
-- =====================================================
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local PLACE_ID = game.PlaceId
local REMOTE_NAME = "EntrarServidorPrivado"

-- =====================================================
-- REMOTEEVENT (garantia)
-- =====================================================
local remote = ReplicatedStorage:FindFirstChild(REMOTE_NAME)
if not remote then
	remote = Instance.new("RemoteEvent")
	remote.Name = REMOTE_NAME
	remote.Parent = ReplicatedStorage
end

-- =====================================================
-- TELEPORTE (servidor)
-- =====================================================
remote.OnServerEvent:Connect(function(player, codigoPrivado)
	if typeof(codigoPrivado) ~= "string" then return end

	codigoPrivado = codigoPrivado:gsub("%s+", "")
	if #codigoPrivado < 10 then return end

	pcall(function()
		TeleportService:TeleportToPrivateServer(
			PLACE_ID,
			codigoPrivado,
			{ player }
		)
	end)
end)

-- =====================================================
-- FUNÇÃO QUE CRIA A GUI (ROBUSTA)
-- =====================================================
local function criarGui(player)
	local playerGui = player:WaitForChild("PlayerGui", 10)
	if not playerGui then return end

	-- evita duplicar
	if playerGui:FindFirstChild("EntrarServidorPrivadoGUI") then
		return
	end

	local gui = Instance.new("ScreenGui")
	gui.Name = "EntrarServidorPrivadoGUI"
	gui.ResetOnSpawn = false
	gui.Enabled = true
	gui.Parent = playerGui

	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(0, 420, 0, 190)
	frame.Position = UDim2.new(0.5, -210, 0.5, -95)
	frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	frame.BorderSizePixel = 0
	frame.Parent = gui

	local box = Instance.new("TextBox")
	box.Name = "TextBox"
	box.Size = UDim2.new(1, -20, 0, 50)
	box.Position = UDim2.new(0, 10, 0, 25)
	box.PlaceholderText = "Cole o link ou código do servidor privado"
	box.Text = ""
	box.TextScaled = true
	box.ClearTextOnFocus = false
	box.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
	box.TextColor3 = Color3.fromRGB(255, 255, 255)
	box.Parent = frame

	local button = Instance.new("TextButton")
	button.Name = "TextButton"
	button.Size = UDim2.new(1, -20, 0, 50)
	button.Position = UDim2.new(0, 10, 0, 110)
	button.Text = "ENTRAR NO SERVIDOR"
	button.TextScaled = true
	button.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.Parent = frame

	local localScript = Instance.new("LocalScript")
	localScript.Source = [[
		local ReplicatedStorage = game:GetService("ReplicatedStorage")
		local remote = ReplicatedStorage:WaitForChild("EntrarServidorPrivado")

		local frame = script.Parent
		local box = frame:WaitForChild("TextBox")
		local button = frame:WaitForChild("TextButton")

		local function extrairCodigo(texto)
			local codigo = string.match(texto, "privateServerLinkCode=([%w%-]+)")
			if not codigo then
				codigo = texto
			end
			return codigo
		end

		button.MouseButton1Click:Connect(function()
			if box.Text == "" then return end
			remote:FireServer(extrairCodigo(box.Text))
		end)
	]]
	localScript.Parent = frame
end

-- =====================================================
-- GARANTIA ABSOLUTA DE EXECUÇÃO
-- =====================================================
Players.PlayerAdded:Connect(function(player)
	-- cria quando entrar
	criarGui(player)

	-- cria novamente se o personagem respawnar
	player.CharacterAdded:Connect(function()
		task.wait(1)
		criarGui(player)
	end)
end)
