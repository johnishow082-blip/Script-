local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "DashUI"

-- 🔘 زر
local button = Instance.new("TextButton", gui)
button.Size = UDim2.new(0, 220, 0, 60)
button.Position = UDim2.new(0.5, -110, 0.8, 0)
button.Text = "⚡ Dash"
button.BackgroundColor3 = Color3.fromRGB(25,25,25)
button.TextColor3 = Color3.fromRGB(0,255,255)
button.TextScaled = true

-- ⏱️ كولداون
local cooldown = false

-- ⚙️ دالة الداش
local function dash()
	local char = player.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	-- اتجاه اللاعب
	local direction = root.CFrame.LookVector

	-- قوة الداش
	local velocity = Instance.new("BodyVelocity")
	velocity.Velocity = direction * 90
	velocity.MaxForce = Vector3.new(100000, 0, 100000)
	velocity.Parent = root

	-- إزالة التأثير بسرعة
	game.Debris:AddItem(velocity, 0.15)
end

-- 🔘 عند الضغط
button.MouseButton1Click:Connect(function()
	if cooldown then return end
	cooldown = true

	dash()

	button.Text = "⏳ Cooldown"
	task.wait(1.2)

	button.Text = "⚡ Dash"
	cooldown = false
end)
