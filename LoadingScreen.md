
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")

local Intro = {}

local CONFIG = {
	titleText = "jey scripts ",
	quoteText = "Made by the best for the best",
	bgColor = Color3.fromRGB(12, 12, 20),
	accentColor = Color3.fromRGB(170, 90, 255),
	durationTotal = 6.5,
	typingSpeed = 0.04,
	sparkleCount = 18,
}

local function new(inst, props)
	local obj = Instance.new(inst)
	for k, v in pairs(props or {}) do
		obj[k] = v
	end
	return obj
end

function Intro.Play(callback)
	local player = Players.LocalPlayer
	local playerGui = player:WaitForChild("PlayerGui")
	
	if playerGui:FindFirstChild("FlashyIntroGui") then
		playerGui:FindFirstChild("FlashyIntroGui"):Destroy()
	end

	local screenGui = new("ScreenGui", {
		Name = "FlashyIntroGui",
		Parent = playerGui,
		ResetOnSpawn = false,
	})

	local bg = new("Frame", {
		Parent = screenGui,
		BackgroundColor3 = CONFIG.bgColor,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, 0),
		Size = UDim2.new(1.2, 0, 1.2, 0),
		BackgroundTransparency = 1,
	})

	new("UIGradient", {
		Parent = bg,
		Color = ColorSequence.new {
			ColorSequenceKeypoint.new(0, CONFIG.bgColor),
			ColorSequenceKeypoint.new(0.5, CONFIG.bgColor:Lerp(CONFIG.accentColor, 0.06)),
			ColorSequenceKeypoint.new(1, CONFIG.bgColor),
		},
		Rotation = 45
	})

	local panel = new("Frame", {
		Parent = bg,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.42, 0),
		Size = UDim2.new(0.75, 0, 0.36, 0),
		BackgroundTransparency = 0.18,
		BackgroundColor3 = Color3.new(0, 0, 0),
		ClipsDescendants = true
	})

	new("UIStroke", { Parent = panel, Color = CONFIG.accentColor, Thickness = 2, Transparency = 0.6 })
	new("UICorner", { Parent = panel, CornerRadius = UDim.new(0, 18) })

	local title = new("TextLabel", {
		Parent = panel,
		AnchorPoint = Vector2.new(0.5, 0),
		Position = UDim2.new(0.5, 0, 0.06, 0),
		Size = UDim2.new(0.9, 0, 0.18, 0),
		BackgroundTransparency = 1,
		Text = CONFIG.titleText,
		TextColor3 = CONFIG.accentColor,
		TextStrokeTransparency = 0.6,
		Font = Enum.Font.GothamBold,
		TextSize = 28,
	})

	local titleGlow = new("TextLabel", {
		Parent = panel,
		AnchorPoint = Vector2.new(0.5, 0),
		Position = UDim2.new(0.5, 0, 0.05, 6),
		Size = UDim2.new(0.9, 0, 0.18, 0),
		BackgroundTransparency = 1,
		Text = CONFIG.titleText,
		TextColor3 = CONFIG.accentColor,
		TextTransparency = 0.8,
		Font = Enum.Font.GothamBlack,
		TextSize = 40,
	})

	local quote = new("TextLabel", {
		Parent = panel,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.53, 0),
		Size = UDim2.new(0.9, 0, 0.45, 0),
		BackgroundTransparency = 1,
		Text = "",
		TextColor3 = Color3.fromRGB(235, 235, 235),
		Font = Enum.Font.GothamBold,
		TextSize = 28,
		TextWrapped = true,
		TextXAlignment = Enum.TextXAlignment.Center,
		TextYAlignment = Enum.TextYAlignment.Center,
	})

	new("UIStroke", { Parent = quote, Color = Color3.new(0, 0, 0), Thickness = 1, Transparency = 0.6 })

	local accentBar = new("Frame", {
		Parent = panel,
		AnchorPoint = Vector2.new(0.5, 0),
		Position = UDim2.new(0.5, 0, 0.86, 0),
		Size = UDim2.new(0.6, 0, 0.03, 0),
		BackgroundColor3 = CONFIG.accentColor,
		BackgroundTransparency = 1,
	})
	new("UICorner", { Parent = accentBar, CornerRadius = UDim.new(1, 0) })

	for i = 1, CONFIG.sparkleCount do
		local s = new("Frame", {
			Parent = panel,
			Size = UDim2.new(0, 6, 0, 6),
			BackgroundColor3 = CONFIG.accentColor,
			AnchorPoint = Vector2.new(0.5, 0.5),
			Position = UDim2.new(math.random(), 0, math.random(), 0),
			BackgroundTransparency = 0.9,
			ZIndex = 5,
		})
		new("UICorner", { Parent = s, CornerRadius = UDim.new(1, 0) })
		task.spawn(function()
			while s and s.Parent do
				local showT = TweenService:Create(s, TweenInfo.new(0.4), { BackgroundTransparency = 0 })
				local hideT = TweenService:Create(s, TweenInfo.new(0.7), { BackgroundTransparency = 0.9 })
				showT:Play()
				showT.Completed:Wait()
				hideT:Play()
				hideT.Completed:Wait()
				task.wait(math.random() * 0.6)
			end
		end)
	end

	local function play()
		TweenService:Create(bg, TweenInfo.new(0.5), { BackgroundTransparency = 0 }):Play()
		panel.Position = UDim2.new(0.5, 0, 0.42, 0)
		panel.Rotation = -6
		TweenService:Create(panel, TweenInfo.new(0.8, Enum.EasingStyle.Back), { Rotation = 0 }):Play()
		local text = CONFIG.quoteText
		for i = 1, #text do
			quote.Text = string.sub(text, 1, i)
			task.wait(CONFIG.typingSpeed)
		end
		task.wait(CONFIG.durationTotal - 2)
		TweenService:Create(panel, TweenInfo.new(0.5), { BackgroundTransparency = 1 }):Play()
		TweenService:Create(bg, TweenInfo.new(0.6), { BackgroundTransparency = 1 }):Play()
		task.wait(0.7)
		screenGui:Destroy()
		if callback then
			callback()
		end
	end

	task.spawn(play)
end

return Intro
