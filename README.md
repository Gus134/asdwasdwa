local UIS = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local event = game.ReplicatedStorage:WaitForChild("FreezeVision")

local ativo = false

UIS.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.R then
		ativo = not ativo
		event:FireServer(ativo)
	end
end)
local event = game.ReplicatedStorage:WaitForChild("FreezeVision")

local clones = {}

event.OnServerEvent:Connect(function(player, ativar)
	local char = player.Character
	if not char then return end
	local humanoid = char:FindFirstChild("Humanoid")
	local root = char:FindFirstChild("HumanoidRootPart")
	if not humanoid or not root then return end

	if ativar then
		-- cria clone congelado
		local clone = char:Clone()
		clone.Name = player.Name .. "_Clone"
		clone.Parent = workspace

		for _, v in ipairs(clone:GetDescendants()) do
			if v:IsA("BasePart") then
				v.Anchored = true
			elseif v:IsA("Humanoid") then
				v:Destroy()
			end
		end

		clone:SetPrimaryPartCFrame(root.CFrame)
		clones[player] = clone

		-- esconde personagem real dos outros
		for _, v in ipairs(char:GetDescendants()) do
			if v:IsA("BasePart") then
				v.Transparency = 1
				v.CanCollide = false
			end
		end

	else
		-- remove clone
		if clones[player] then
			clones[player]:Destroy()
			clones[player] = nil
		end

		-- volta personagem real
		for _, v in ipairs(char:GetDescendants()) do
			if v:IsA("BasePart") then
				v.Transparency = 0
				v.CanCollide = true
			end
		end
	end
end)
