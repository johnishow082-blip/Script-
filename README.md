local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoid = char:WaitForChild("Humanoid")
local root = char:WaitForChild("HumanoidRootPart")
local camera = workspace.CurrentCamera

-------------------------------------------------
-- 📱 UI
-------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "LEGEND_MODE_UI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,300,0,420)
frame.Position = UDim2.new(0,80,0.5,-210)
frame.BackgroundColor3 = Color3.fromRGB(12,12,12)
frame.Parent = gui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,14)

-------------------------------------------------
-- 🎯 TARGET SYSTEM
-------------------------------------------------
local function getTarget()
	local closest, dist = nil, 9999
	for _,v in pairs(game.Players:GetPlayers()) do
		if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
			local d = (v.Character.HumanoidRootPart.Position - root.Position).Magnitude
			if d < dist then
				dist = d
				closest = v.Character
			end
		end
	end
	return closest
end

-------------------------------------------------
-- 🎥 CAMERA SHAKE
-------------------------------------------------
local function shake(power, time)
	local start = tick()
	local conn
	conn = game:GetService("RunService").RenderStepped:Connect(function()
		if tick() - start > time then conn:Disconnect() return end
		camera.CFrame = camera.CFrame * CFrame.new(
			math.random(-power,power)/100,
			math.random(-power,power)/100,
			0
		)
	end)
end

-------------------------------------------------
-- ⚡ DASH
-------------------------------------------------
local function dash()
	local bv = Instance.new("BodyVelocity")
	bv.Velocity = root.CFrame.LookVector * 200
	bv.MaxForce = Vector3.new(1e5,0,1e5)
	bv.Parent = root
	game.Debris:AddItem(bv,0.12)
	shake(4,0.15)
end

-------------------------------------------------
-- 🔥 FIRE SKILL
-------------------------------------------------
local function fire()
	local target = getTarget()
	if not target or not target:FindFirstChild("HumanoidRootPart") then return end

	local hrp = target.HumanoidRootPart
	hrp.Velocity = Vector3.new(0,80,0) + root.CFrame.LookVector * 80
	hrp.BrickColor = BrickColor.new("Bright orange")
	shake(5,0.2)
end

-------------------------------------------------
-- ❄️ ICE SKILL
-------------------------------------------------
local function ice()
	local target = getTarget()
	if not target or not target:FindFirstChild("HumanoidRootPart") then return end

	local hrp = target.HumanoidRootPart
	hrp.Anchored = true

	task.delay(1.2,function()
		if hrp then hrp.Anchored = false end
	end)

	shake(3,0.2)
end

-------------------------------------------------
-- ⚡ LIGHTNING SKILL
-------------------------------------------------
local function lightning()
	local target = getTarget()
	if not target or not target:FindFirstChild("HumanoidRootPart") then return end

	local hrp = target.HumanoidRootPart
	hrp.Velocity = Vector3.new(0,120,0)
	shake(6,0.25)
end

-------------------------------------------------
-- 💀 KILL FX
-------------------------------------------------
local function killEffect(target)
	if not target then return end
	for _,p in pairs(target:GetDescendants()) do
		if p:IsA("BasePart") then
			p.Color = Color3.fromRGB(0,0,0)
			p.Material = Enum.Material.Neon
		end
	end
end

-------------------------------------------------
-- 🧠 SIMPLE AI (NPC STYLE)
-------------------------------------------------
task.spawn(function()
	while true do
		for _,v in pairs(game.Players:GetPlayers()) do
			if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = v.Character.HumanoidRootPart
				local dist = (hrp.Position - root.Position).Magnitude

				if dist < 15 then
					root.CFrame = CFrame.lookAt(root.Position, hrp.Position)
					hrp.Velocity = (hrp.Position - root.Position).Unit * 60
				end
			end
		end
		task.wait(0.5)
	end
end)

-------------------------------------------------
-- 📱 BUTTON SYSTEM
-------------------------------------------------
local function makeButton(text, y, color, func)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(0.9,0,0,75)
	b.Position = UDim2.new(0.05,0,y,0)
	b.Text = text
	b.TextScaled = true
	b.BackgroundColor3 = color
	b.Parent = frame

	b.MouseButton1Click:Connect(func)
end

makeButton("⚡ DASH", 0.02, Color3.fromRGB(0,170,255), dash)
makeButton("🔥 FIRE", 0.25, Color3.fromRGB(255,120,0), fire)
makeButton("❄️ ICE", 0.48, Color3.fromRGB(120,200,255), ice)
makeButton("⚡ LIGHTNING", 0.71, Color3.fromRGB(255,255,0), lightning)

-------------------------------------------------
-- 🔄 RESPAWN FIX
-------------------------------------------------
player.CharacterAdded:Connect(function(c)
	char = c
	humanoid = char:WaitForChild("Humanoid")
	root = char:WaitForChild("HumanoidRootPart")
end)
