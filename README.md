local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- GUI principal
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "ModernToggleGUI"
gui.ResetOnSpawn = false

-- Frame principal com estilo futurista
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 240, 0, 240)
frame.Position = UDim2.new(0.5, -120, 0.5, -120)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
frame.BackgroundTransparency = 0.1
frame.BorderSizePixel = 0
frame.Parent = gui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

local frameStroke = Instance.new("UIStroke", frame)
frameStroke.Color = Color3.fromRGB(0, 255, 255)
frameStroke.Thickness = 2
frameStroke.Transparency = 0.4

-- Texto "Pls Hub" dentro do frame
local plsHub = Instance.new("TextLabel", frame)
plsHub.Size = UDim2.new(1, 0, 0, 40)
plsHub.Position = UDim2.new(0, 0, 0, 0)
plsHub.BackgroundTransparency = 1
plsHub.Text = "Pls Hub"
plsHub.Font = Enum.Font.GothamBlack
plsHub.TextSize = 28
plsHub.TextColor3 = Color3.fromRGB(0, 255, 255)
plsHub.TextStrokeTransparency = 0.4
plsHub.TextStrokeColor3 = Color3.fromRGB(0, 150, 150)
plsHub.TextXAlignment = Enum.TextXAlignment.Center

-- Função para criar botões com estilo futurista
local function createFuturisticButton(text, yPos, parent)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(0, 220, 0, 40)
	button.Position = UDim2.new(0, 10, 0, yPos)
	button.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
	button.BorderSizePixel = 0
	button.TextColor3 = Color3.fromRGB(100, 255, 255)
	button.Font = Enum.Font.GothamBold
	button.TextSize = 14
	button.Text = text
	button.AutoButtonColor = false

	local corner = Instance.new("UICorner", button)
	corner.CornerRadius = UDim.new(0, 6)

	local stroke = Instance.new("UIStroke", button)
	stroke.Color = Color3.fromRGB(0, 255, 255)
	stroke.Thickness = 2
	stroke.Transparency = 0.4

	button.Parent = parent
	return button
end

-- Botões dentro do frame (Instant Steal removido)
local btnSpeed = createFuturisticButton("Bypass Walkspeed 43", 50, frame)
local btnESPOn = createFuturisticButton("Player ESP", 100, frame)

-- Função para criar botões flutuantes fora do frame
local function createFloatingButton(text, pos, color)
	local btn = Instance.new("TextButton", gui)
	btn.Size = UDim2.new(0, 120, 0, 35)
	btn.Position = pos
	btn.BackgroundColor3 = color
	btn.BorderSizePixel = 0
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 14
	btn.Text = text
	btn.AutoButtonColor = false

	local corner = Instance.new("UICorner", btn)
	corner.CornerRadius = UDim.new(0, 8)

	local stroke = Instance.new("UIStroke", btn)
	stroke.Color = Color3.fromRGB(0, 255, 255)
	stroke.Thickness = 2
	stroke.Transparency = 0.4

	return btn
end

local toggleBtn = createFloatingButton("Hide GUI", UDim2.new(0, 20, 0, 20), Color3.fromRGB(35, 35, 45))
local destroyBtn = createFloatingButton("Destroy GUI", UDim2.new(0, 160, 0, 20), Color3.fromRGB(180, 20, 20))

-- Drag do frame (PC e celular)
local dragging, dragInput, dragStart, startPos
frame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = frame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then dragging = false end
		end)
	end
end)
frame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-- WalkSpeed com loop infinito (bypass)
btnSpeed.MouseButton1Click:Connect(function()
	local player = game.Players.LocalPlayer
	local character = player.Character or player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")

	task.spawn(function()
		while true do
			if humanoid and humanoid.Parent then
				humanoid.WalkSpeed = 43
			end
			task.wait()
		end
	end)
end)

-- ESP
btnESPOn.MouseButton1Click:Connect(function()
	local function getDistance(pos1, pos2)
		return math.floor((pos1 - pos2).Magnitude) .. "m"
	end

	local function createNameTag(character, player)
		local head = character:FindFirstChild("Head")
		if head and not head:FindFirstChild("NameTag") then
			local billboard = Instance.new("BillboardGui")
			billboard.Name = "NameTag"
			billboard.Size = UDim2.new(0, 200, 0, 50)
			billboard.StudsOffset = Vector3.new(0, 2.5, 0)
			billboard.AlwaysOnTop = true
			billboard.Parent = head

			local textLabel = Instance.new("TextLabel", billboard)
			textLabel.Size = UDim2.new(1, 0, 1, 0)
			textLabel.BackgroundTransparency = 1
			textLabel.TextColor3 = Color3.new(1, 1, 1)
			textLabel.TextStrokeTransparency = 0
			textLabel.TextScaled = true
			textLabel.Font = Enum.Font.SourceSansBold
			textLabel.Text = ""

			RunService.RenderStepped:Connect(function()
				if character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					local dist = getDistance(character.HumanoidRootPart.Position, LocalPlayer.Character.HumanoidRootPart.Position)
					textLabel.Text = player.Name .. " | " .. dist
				end
			end)
		end
	end

	local function setupPlayer(player)
		if player == LocalPlayer then return end
		player.CharacterAdded:Connect(function(character)
			character:WaitForChild("Head")
			character:WaitForChild("HumanoidRootPart")
			local highlight = Instance.new("Highlight")
			highlight.Name = "PlayerHighlight"
			highlight.FillColor = Color3.new(1, 1, 1)
			highlight.FillTransparency = 0.5
			highlight.OutlineColor = Color3.new(1, 1, 1)
			highlight.OutlineTransparency = 0
			highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
			highlight.Parent = character
			createNameTag(character, player)
		end)
		if player.Character then
			local character = player.Character
			character:WaitForChild("Head")
			character:WaitForChild("HumanoidRootPart")
			if not character:FindFirstChild("PlayerHighlight") then
				local highlight = Instance.new("Highlight")
				highlight.Name = "PlayerHighlight"
				highlight.FillColor = Color3.new(1, 1, 1)
				highlight.FillTransparency = 0.5
				highlight.OutlineColor = Color3.new(1, 1, 1)
				highlight.OutlineTransparency = 0
				highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
				highlight.Parent = character
			end
			createNameTag(character, player)
		end
	end

	for _, p in pairs(Players:GetPlayers()) do
		setupPlayer(p)
	end
	Players.PlayerAdded:Connect(setupPlayer)
end)

-- Toggle GUI
local guiVisible = true
toggleBtn.MouseButton1Click:Connect(function()
	guiVisible = not guiVisible
	frame.Visible = guiVisible
	toggleBtn.Text = guiVisible and "Hide GUI" or "Show GUI"
end)

-- Destroy GUI
destroyBtn.MouseButton1Click:Connect(function()
	gui:Destroy()
end)
