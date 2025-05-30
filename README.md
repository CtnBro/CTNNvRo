local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Main GUI
local ScreenGui = Instance.new("ScreenGui", playerGui)
ScreenGui.Name = "IngeniousScriptDupeGUI"
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local Frame = Instance.new("Frame", ScreenGui)
Frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Frame.Position = UDim2.new(0.4, 0, 0.35, 0)
Frame.Size = UDim2.new(0, 260, 0, 130)
Frame.Active = true
Frame.Draggable = true
Frame.BorderSizePixel = 0

local UICorner = Instance.new("UICorner", Frame)
UICorner.CornerRadius = UDim.new(0, 16)

-- Title Box
local TitleBox = Instance.new("TextLabel", Frame)
TitleBox.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
TitleBox.BackgroundTransparency = 0.4
TitleBox.Position = UDim2.new(0.07, 0, 0.05, 0)
TitleBox.Size = UDim2.new(0.86, 0, 0.25, 0)
TitleBox.Font = Enum.Font.Gotham
TitleBox.Text = "Ingenious Script Dupe"
TitleBox.TextColor3 = Color3.fromRGB(255, 215, 0)
TitleBox.TextScaled = true
TitleBox.TextStrokeTransparency = 0.7
TitleBox.TextTransparency = 0.05
TitleBox.BorderSizePixel = 0

local titleCorner = Instance.new("UICorner", TitleBox)
titleCorner.CornerRadius = UDim.new(0, 12)

-- Gold Texture (optional shiny effect)
local goldTexture = Instance.new("ImageLabel", TitleBox)
goldTexture.Image = "rbxassetid://11478733258"
goldTexture.Size = UDim2.new(1, 0, 1, 0)
goldTexture.BackgroundTransparency = 1
goldTexture.ZIndex = 0

-- Shiny line animation
local ShineLine = Instance.new("Frame", TitleBox)
ShineLine.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ShineLine.Size = UDim2.new(0.08, 0, 1, 0)
ShineLine.Position = UDim2.new(-0.1, 0, 0, 0)
ShineLine.BackgroundTransparency = 0.6
ShineLine.BorderSizePixel = 0

task.spawn(function()
	while true do
		ShineLine.Position = UDim2.new(-0.1, 0, 0, 0)
		ShineLine:TweenPosition(UDim2.new(1.1, 0, 0, 0), Enum.EasingDirection.Out, Enum.EasingStyle.Linear, 0.7, true)
		wait(1)
	end
end)

-- Dupe Button
local Button = Instance.new("TextButton", Frame)
Button.Position = UDim2.new(0.07, 0, 0.5, 0)
Button.Size = UDim2.new(0.86, 0, 0.35, 0)
Button.Text = "Dupe"
Button.Font = Enum.Font.Gotham
Button.TextSize = 24
Button.TextColor3 = Color3.new(1, 1, 1)
Button.BackgroundColor3 = Color3.fromRGB(100, 150, 255)
Button.BorderSizePixel = 0

local btnCorner = Instance.new("UICorner", Button)
btnCorner.CornerRadius = UDim.new(0, 12)

-- Duplication + Animation replication logic
Button.MouseButton1Click:Connect(function()
	local character = player.Character or player.CharacterAdded:Wait()
	local humanoid = character:FindFirstChildOfClass("Humanoid")
	local heldTool = character:FindFirstChildOfClass("Tool")

	if heldTool and humanoid then
		-- Clone the tool and parent to character (so it acts equipped)
		local clone = heldTool:Clone()
		clone.Parent = character
		
		-- Equip clone to activate tool and animations
		humanoid:EquipTool(clone)
		
		-- Animation replication attempt
		local function getAnimController(tool)
			return tool:FindFirstChildOfClass("AnimationController") or tool:FindFirstChildOfClass("Humanoid")
		end
		
		local origAnimCtrl = getAnimController(heldTool)
		local cloneAnimCtrl = getAnimController(clone)
		
		if origAnimCtrl and cloneAnimCtrl then
			for _, track in pairs(origAnimCtrl:GetPlayingAnimationTracks()) do
				local anim = track.Animation
				if anim then
					local newTrack = cloneAnimCtrl:LoadAnimation(anim)
					newTrack:Play()
					newTrack.TimePosition = track.TimePosition
					newTrack.Speed = track.Speed
				end
			end
		end
		
		-- UI feedback
		Button.Text = "✔️ Duped!"
		Button.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
		task.wait(1.2)
		Button.Text = "Dupe"
		Button.BackgroundColor3 = Color3.fromRGB(100, 150, 255)
	else
		Button.Text = "No Tool Held!"
		Button.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
		task.wait(1.2)
		Button.Text = "Dupe"
		Button.BackgroundColor3 = Color3.fromRGB(100, 150, 255)
	end
end)
