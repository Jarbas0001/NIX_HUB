local gui = Instance.new("ScreenGui", game.Players.LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "ShiftLockMobile"
gui.ResetOnSpawn = false

local btn = Instance.new("ImageButton", gui)
btn.Size = UDim2.new(0, 60, 0, 60)
btn.Position = UDim2.new(0.7, 0, 0.78, 0) -- Mais à esquerda e acima do pulo
btn.BackgroundTransparency = 1
btn.Image = "rbxasset://textures/ui/mouseLock_off@2x.png"
btn.Visible = game:GetService("UserInputService").TouchEnabled

local dragging = false
local dragStart
local startPos

btn.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = btn.Position
	end
end)

btn.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.Touch then
		local delta = input.Position - dragStart
		btn.Position = UDim2.new(
			0, startPos.X.Offset + delta.X,
			0, startPos.Y.Offset + delta.Y
		)
	end
end)

game:GetService("UserInputService").InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

-- Shift Lock funcional
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local runService = game:GetService("RunService")
local active = false
local conn

local function toggleShiftLock()
	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:WaitForChild("HumanoidRootPart")
	local humanoid = char:WaitForChild("Humanoid")

	if active then
		if conn then conn:Disconnect() end
		humanoid.AutoRotate = true
		btn.Image = "rbxasset://textures/ui/mouseLock_off@2x.png"
		active = false
	else
		humanoid.AutoRotate = false
		conn = runService.RenderStepped:Connect(function()
			local dir = camera.CFrame.LookVector
			root.CFrame = CFrame.new(root.Position, root.Position + Vector3.new(dir.X, 0, dir.Z))
		end)
		btn.Image = "rbxasset://textures/ui/mouseLock_on@2x.png"
		active = true
	end
end

btn.MouseButton1Click:Connect(toggleShiftLock)
