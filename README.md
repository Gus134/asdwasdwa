-- =====================================================
-- SERVIÇOS
-- =====================================================
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local PLACE_ID = game.PlaceId

-- =====================================================
-- REMOTEEVENT (garantia de existência)
-- =====================================================
local REMOTE_NAME = "EntrarServidorPrivado"
local remote = ReplicatedStorage:FindFirstChild(REMOTE_NAME)

if not remote then
	remote = Instance.new("RemoteEvent")
	remote.Name = REMOTE_NAME
	remote.Parent = ReplicatedStorage
end

-- =====================================================
-- TELEPORTE NO SERVIDOR (seguro)
-- =====================================================
remote.OnServerEvent:Connect(function(player, codigoPrivado)
	-- validações
	if typeof(player) ~= "Instance" then return end
	if typeof(codigoPrivado) ~= "string" then return end

	codigoPrivado = string.gsub(codigoPrivado, "%s+", "")
	if #codigoPrivado < 10 then return end

	-- tentativa protegida contra erro de teleporte
	local sucesso, erro = pcall(function()
		TeleportService:TeleportToPrivateServer(
			PLACE_ID,
			codigoPrivado,
			{ player }
		)
	end)

	if not sucesso then
		warn("Erro ao teleportar:", erro)
	end
end)

-- =====================================================
-- GUI + LOCALSCRIPT (criados automaticamente)
-- =====================================================
Players.PlayerAdded:Connect(function(player)
