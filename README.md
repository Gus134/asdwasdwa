-- =====================================================
-- SERVIÇOS
-- =====================================================
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local PLACE_ID = game.PlaceId
local REMOTE_NAME = "EntrarServidorPrivado"

-- =====================================================
-- REMOTEEVENT (NOME FIXO, SEM ACENTOS)
-- =====================================================
local remote = ReplicatedStorage:FindFirstChild(REMOTE_NAME)

if remote == nil then
	remote = Instance.new("RemoteEvent")
	remote.Name = REMOTE_NAME
	remote.Parent = ReplicatedStorage
end

-- =====================================================
-- TELEPORTE (SERVIDOR)
-- =====================================================
remote.OnServerEvent:Connect(function(player, codigoPrivado)
	if typeof(player) ~= "Instance" then
		return
	end

	if typeof(codigoPrivado) ~= "string" then
		return
	end

	-- remove espaços, quebras de linha e caracteres invisíveis
	codigoPrivado = string.gsub(codigoPrivado, "%s+", "")

	-- códigos reais têm tamanho mínimo
	if #codigoPrivado < 10 then
		return
	end

	pcall(function()
		TeleportService:TeleportToPrivateServer(
			PLACE_ID,
			codigoPrivado,
			{ player }
		)
	end)
end)

-- =====================================================
-- FUNÇÃO DE CRIAÇÃO DA GUI
-- =====================================================
local function criarInterface(player)
	if player == nil then
		return
	end

	local playerGui = player:WaitForChild("PlayerGui", 10)
	if playerGui == nil then
		return
	end

	if playerGui:FindFirstChild("EntrarServidorPrivadoGUI") then
		return
	end

	-- ScreenGui
	local screenGui = Instance.new("ScreenGui")
	screenGui.Name = "EntrarServidorPrivadoGUI"
	screenGui.ResetOnSpawn = false
	screenGui.Enabled = true
	screenGui.Parent = playerGui

	-- Frame principal
	local frame = Instance.new("Frame")
	frame.Name = "Container"
	frame.Size = UDim2.new(0, 320, 0, 140)
	frame.Position = UDim2.new(0.5, -160, 0.5, -70)
	frame.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
	frame.BorderSizePixel = 0
	frame.Parent = screenGui

	local frameCorner = Instance.new("UICorner")
	frameCorner.CornerRadius = UDim.new(0, 12)
	frameCorner.Parent = frame

	-- Título
	local titulo = Instance.new("TextLabel")
	titulo.Name = "Titulo"
	titulo.Size = UDim2.new(1, -20, 0, 28)
	titulo.Position = UDim2.new(0, 10, 0, 6)
	titulo.BackgroundTransparency = 1
	titulo.Text = "Entrar em servidor privado"
	titulo.TextColor3 = Color3.fromRGB(235, 235, 235)
	titulo.Font = Enum.Font.GothamMedium
	titulo.TextScaled = true
	titulo.Parent = frame

	-- Caixa de texto
	local caixaTexto = Instance.new("TextBox")
	caixaTexto.Name = "TextBox"
	caixaTexto.Size = UDim2.new(1, -20, 0, 36)
	caixaTexto.Position = UDim2.new(0, 10, 0, 44)
	caixaTexto.PlaceholderText = "Link do seu servidor"
	caixaTexto.Text = ""
	caixaTexto.ClearTextOnFocus = false
	caixaTexto.TextScaled = true
	caixaTexto.Font = Enum.Font.Gotham
	caixaTexto.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	caixaText
