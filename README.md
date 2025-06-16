local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local userInput = game:GetService("UserInputService")
local runService = game:GetService("RunService")
local replicatedStorage = game:GetService("ReplicatedStorage")

-- Som de clique
local clickSound = replicatedStorage:WaitForChild("ClickSound")

-- Criar GUI
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "YZHUB"
gui.ResetOnSpawn = false

-- Painel principal
local panel = Instance.new("Frame", gui)
panel.Position = UDim2.new(0, 20, 0.3, 0)
panel.Size = UDim2.new(0, 220, 0, 250)
panel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
panel.BackgroundTransparency = 0.25
panel.BorderSizePixel = 0
panel.Visible = true

local stroke = Instance.new("UIStroke", panel)
stroke.Thickness = 3
stroke.Color = Color3.fromRGB(0, 255, 255)

-- Animação de abertura
local tweenService = game:GetService("TweenService")

-- Título
local title = Instance.new("TextLabel", panel)
title.Text = "YZHUB"
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SciFi
title.TextColor3 = Color3.fromRGB(255, 0, 0)
title.TextScaled = true

-- Rodapé
local footer = Instance.new("TextLabel", panel)
footer.Text = "© yzdz_n"
footer.Position = UDim2.new(0, 5, 1, -25)
footer.Size = UDim2.new(1, -10, 0, 20)
footer.BackgroundTransparency = 1
footer.TextColor3 = Color3.fromRGB(0, 255, 255)
footer.Font = Enum.Font.Code
footer.TextScaled = true

-- Botão de mostrar/esconder
local toggle = Instance.new("TextButton", gui)
toggle.Size = UDim2.new(0, 220, 0, 35)
toggle.Position = UDim2.new(0, 20, 0.25, 0)
toggle.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toggle.TextColor3 = Color3.fromRGB(0, 255, 255)
toggle.Font = Enum.Font.GothamBlack
toggle.Text = "Mostrar / Esconder"
toggle.TextScaled = true

toggle.MouseButton1Click:Connect(function()
	clickSound:Clone().Parent = player:WaitForChild("PlayerGui"); player.PlayerGui.ClickSound:Play()
	local goal = {Size = panel.Visible and UDim2.new(0, 220, 0, 0) or UDim2.new(0, 220, 0, 250)}
	local tween = tweenService:Create(panel, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), goal)
	tween:Play()
	panel.Visible = not panel.Visible
end)

-- Botão criador
local function createButton(text, iconID, positionY, callback)
	local btn = Instance.new("TextButton", panel)
	btn.Size = UDim2.new(1, -20, 0, 50)
	btn.Position = UDim2.new(0, 10, 0, positionY)
	btn.BackgroundColor3 = Color3.fromRGB(20, 0, 0)
	btn.Text = "   " .. text
	btn.Font = Enum.Font.FredokaOne
	btn.TextColor3 = Color3.fromRGB(255, 0, 0)
	btn.TextScaled = true
	btn.TextXAlignment = Enum.TextXAlignment.Left

	local img = Instance.new("ImageLabel", btn)
	img.Image = iconID
	img.Size = UDim2.new(0, 30, 0, 30)
	img.Position = UDim2.new(0, 10, 0.5, -15)
	img.BackgroundTransparency = 1

	local stroke = Instance.new("UIStroke", btn)
	stroke.Color = Color3.fromRGB(255, 0, 0)
	stroke.Thickness = 2

	btn.MouseButton1Click:Connect(function()
		clickSound:Clone().Parent = player:WaitForChild("PlayerGui"); player.PlayerGui.ClickSound:Play()
		callback()
	end)
end

-- WallHack
local wallhack = false
createButton("WallHack", "rbxassetid://6031075930", 40, function()
	character = player.Character or player.CharacterAdded:Wait()
	for _, p in pairs(character:GetDescendants()) do
		if p:IsA("BasePart") then
			p.CanCollide = wallhack
		end
	end
	wallhack = not wallhack
end)

-- Fly
local flying = false
local flyBody

createButton("Fly", "rbxassetid://6031075934", 100, function()
	character = player.Character or player.CharacterAdded:Wait()
	local root = character:FindFirstChild("HumanoidRootPart")
	if not root then return end

	flying = not flying

	if flying then
		flyBody = Instance.new("BodyVelocity", root)
		flyBody.MaxForce = Vector3.new(1e5, 1e5, 1e5)
		flyBody.Velocity = Vector3.zero

		runService:BindToRenderStep("YZFly", Enum.RenderPriority.Character.Value + 1, function()
			if not flying then return end
			local move = Vector3.zero
			local cam = workspace.CurrentCamera
			if userInput:IsKeyDown(Enum.KeyCode.W) then move += cam.CFrame.LookVector end
			if userInput:IsKeyDown(Enum.KeyCode.S) then move -= cam.CFrame.LookVector end
			if userInput:IsKeyDown(Enum.KeyCode.A) then move -= cam.CFrame.RightVector end
			if userInput:IsKeyDown(Enum.KeyCode.D) then move += cam.CFrame.RightVector end
			flyBody.Velocity = move.Unit * 50
		end)
	else
		runService:UnbindFromRenderStep("YZFly")
		if flyBody then flyBody:Destroy() end
	end
end)

-- Invisibilidade
local invis = false
createButton("Invisível", "rbxassetid://6031090990", 160, function()
	character = player.Character or player.CharacterAdded:Wait()
	for _, p in pairs(character:GetDescendants()) do
		if p:IsA("BasePart") or p:IsA("Decal") then
			p.Transparency = invis and 0 or 1
		end
	end
	invis = not invis
end)
