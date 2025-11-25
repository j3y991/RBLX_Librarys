-- KeyModule.lua (Modern Executor UI - Purple Theme)

local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

local KeyModule = {}

local SERVER_URL = "https://jey-scripts.de"


---------------------------------------------------------------------
-- UNIVERSAL EXECUTOR CLIPBOARD
---------------------------------------------------------------------

local function CopyToClipboard(text)
if setclipboard then 
setclipboard(text)
elseif toclipboard then 
toclipboard(text)
elseif setrbxclipboard then
setrbxclipboard(text)
else
warn("Clipboard not supported on this executor")
end
end


---------------------------------------------------------------------
-- NOTIFICATION SYSTEM (FADE IN / OUT)
---------------------------------------------------------------------

local function Notify(message, color)
local screen = Instance.new("ScreenGui")
screen.IgnoreGuiInset = true
screen.Parent = PlayerGui
screen.Name = "NotificationUI"

local frame = Instance.new("Frame")
frame.AnchorPoint = Vector2.new(0.5, 0)
frame.Position = UDim2.new(0.5, 0, 0.1, 0)
frame.Size = UDim2.new(0, 320, 0, 50)
frame.BackgroundColor3 = color or Color3.fromRGB(180, 120, 255)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 1
frame.Parent = screen

local text = Instance.new("TextLabel")
text.Size = UDim2.new(1, 0, 1, 0)
text.BackgroundTransparency = 1
text.TextColor3 = Color3.fromRGB(255, 255, 255)
text.Font = Enum.Font.GothamBold
text.TextSize = 20
text.Text = message
text.Parent = frame

-- Fade-in
for i = 1,10 do
frame.BackgroundTransparency = 1 - (i * 0.1)
text.TextTransparency = 1 - (i * 0.1)
task.wait(0.02)
end

task.wait(2)

-- Fade-out
for i = 1,10 do
frame.BackgroundTransparency = i * 0.1
text.TextTransparency = i * 0.1
task.wait(0.02)
end

screen:Destroy()
end


---------------------------------------------------------------------
-- UI CREATION (EXECUTOR MODERN PURPLE STYLE)
---------------------------------------------------------------------

local function CreateUI()
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = game.Lighting

-- Blur animation
task.spawn(function()
for i=0,12 do
blur.Size = i
task.wait(0.02)
end
end)

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "KeySystemUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = PlayerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 420, 0, 260)
frame.Position = UDim2.new(0.5, -210, 0.5, -130)
frame.BackgroundColor3 = Color3.fromRGB(40, 0, 60)
frame.BackgroundTransparency = 0.25
frame.BorderSizePixel = 0
frame.Parent = screenGui

local uiCorner = Instance.new("UICorner", frame)
uiCorner.CornerRadius = UDim.new(0, 14)

local title = Instance.new("TextLabel")
title.Text = "⚡ Executor Key System"
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(230, 210, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.Parent = frame

local keyBox = Instance.new("TextBox")
keyBox.PlaceholderText = "Enter your key..."
keyBox.Size = UDim2.new(0.8, 0, 0, 40)
keyBox.Position = UDim2.new(0.1, 0, 0.28, 0)
keyBox.BackgroundColor3 = Color3.fromRGB(70, 0, 120)
keyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
keyBox.Font = Enum.Font.Gotham
keyBox.TextSize = 18
keyBox.BorderSizePixel = 0
keyBox.Parent = frame
Instance.new("UICorner", keyBox).CornerRadius = UDim.new(0, 10)

local validateBtn = Instance.new("TextButton")
validateBtn.Text = "Validate Key"
validateBtn.Size = UDim2.new(0.36, 0, 0, 40)
validateBtn.Position = UDim2.new(0.1, 0, 0.55, 0)
validateBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 200)
validateBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
validateBtn.Font = Enum.Font.GothamSemibold
validateBtn.TextSize = 18
validateBtn.BorderSizePixel = 0
validateBtn.Parent = frame
Instance.new("UICorner", validateBtn).CornerRadius = UDim.new(0, 10)

local claimBtn = Instance.new("TextButton")
claimBtn.Text = "Claim Key"
claimBtn.Size = UDim2.new(0.36, 0, 0, 40)
claimBtn.Position = UDim2.new(0.54, 0, 0.55, 0)
claimBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 200)
claimBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
claimBtn.Font = Enum.Font.GothamSemibold
claimBtn.TextSize = 18
claimBtn.BorderSizePixel = 0
claimBtn.Parent = frame
Instance.new("UICorner", claimBtn).CornerRadius = UDim.new(0, 10)

local openSiteBtn = Instance.new("TextButton")
openSiteBtn.Text = "Copy Landing Page URL"
openSiteBtn.Size = UDim2.new(0.8, 0, 0, 40)
openSiteBtn.Position = UDim2.new(0.1, 0, 0.78, 0)
openSiteBtn.BackgroundColor3 = Color3.fromRGB(160, 60, 255)
openSiteBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openSiteBtn.Font = Enum.Font.GothamBold
openSiteBtn.TextSize = 18
openSiteBtn.BorderSizePixel = 0
openSiteBtn.Parent = frame
Instance.new("UICorner", openSiteBtn).CornerRadius = UDim.new(0, 10)

return {
KeyBox = keyBox,
ValidateBtn = validateBtn,
ClaimBtn = claimBtn,
OpenSiteBtn = openSiteBtn,
Blur = blur
}
end


---------------------------------------------------------------------
-- SERVER REQUESTS
---------------------------------------------------------------------

function KeyModule.ValidateKey(key)
local success, response = pcall(function()
return HttpService:PostAsync(
SERVER_URL.."/api/validate",
HttpService:JSONEncode({key = key, userId = tostring(player.UserId)}),
Enum.HttpContentType.ApplicationJson
)
end)

if success then
return HttpService:JSONDecode(response).valid
end
return false
end

function KeyModule.ClaimKey(key)
local success, response = pcall(function()
return HttpService:PostAsync(
SERVER_URL.."/api/claim",
HttpService:JSONEncode({key = key, userId = tostring(player.UserId)}),
Enum.HttpContentType.ApplicationJson
)
end)

if success then
return HttpService:JSONDecode(response).ok
end
return false
end

---------------------------------------------------------------------
-- UI HANDLERS
---------------------------------------------------------------------

function KeyModule.ShowUI()
local ui = CreateUI()

-- Copy link
ui.OpenSiteBtn.MouseButton1Click:Connect(function()
local url = SERVER_URL.."/?userId="..tostring(player.UserId)
CopyToClipboard(url)
Notify("URL copied to clipboard!", Color3.fromRGB(170, 80, 255))
end)

-- Validate
ui.ValidateBtn.MouseButton1Click:Connect(function()
local key = ui.KeyBox.Text
if key == "" then return Notify("Enter a key first!", Color3.fromRGB(255, 80, 80)) end

if KeyModule.ValidateKey(key) then
Notify("Key is VALID!", Color3.fromRGB(80, 255, 80))
else
Notify("Invalid or expired key!", Color3.fromRGB(255, 80, 80))
end
end)

-- Claim
ui.ClaimBtn.MouseButton1Click:Connect(function()
local key = ui.KeyBox.Text
if key == "" then return Notify("Enter a key first!", Color3.fromRGB(255, 80, 80)) end

if KeyModule.ClaimKey(key) then
Notify("Key claimed successfully!", Color3.fromRGB(80, 255, 80))
else
Notify("Failed to claim key.", Color3.fromRGB(255, 80, 80))
end
end)
end

return KeyModule
