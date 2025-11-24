-- KeyModule.lua

local HttpService = game:GetService("HttpService")

local Players = game:GetService("Players")

local player = Players.LocalPlayer

local PlayerGui = player:WaitForChild("PlayerGui")



local KeyModule = {}

local SERVER\_URL = "https://jey-scripts.de"  -- change to your VPS



-- Helper functions

local function CreateUI()

&nbsp;   local screenGui = Instance.new("ScreenGui")

&nbsp;   screenGui.Name = "KeySystemUI"

&nbsp;   screenGui.ResetOnSpawn = false

&nbsp;   screenGui.Parent = PlayerGui



&nbsp;   local frame = Instance.new("Frame")

&nbsp;   frame.Size = UDim2.new(0, 400, 0, 200)

&nbsp;   frame.Position = UDim2.new(0.5, -200, 0.5, -100)

&nbsp;   frame.BackgroundColor3 = Color3.fromRGB(30,30,30)

&nbsp;   frame.BorderSizePixel = 0

&nbsp;   frame.Parent = screenGui



&nbsp;   local title = Instance.new("TextLabel")

&nbsp;   title.Text = "Roblox Key System"

&nbsp;   title.Size = UDim2.new(1,0,0,40)

&nbsp;   title.BackgroundTransparency = 1

&nbsp;   title.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   title.Font = Enum.Font.SourceSansBold

&nbsp;   title.TextSize = 24

&nbsp;   title.Parent = frame



&nbsp;   local keyBox = Instance.new("TextBox")

&nbsp;   keyBox.PlaceholderText = "Enter your key here"

&nbsp;   keyBox.Size = UDim2.new(0.8,0,0,40)

&nbsp;   keyBox.Position = UDim2.new(0.1,0,0,60)

&nbsp;   keyBox.BackgroundColor3 = Color3.fromRGB(50,50,50)

&nbsp;   keyBox.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   keyBox.ClearTextOnFocus = false

&nbsp;   keyBox.Parent = frame



&nbsp;   local validateBtn = Instance.new("TextButton")

&nbsp;   validateBtn.Text = "Validate Key"

&nbsp;   validateBtn.Size = UDim2.new(0.4,0,0,40)

&nbsp;   validateBtn.Position = UDim2.new(0.1,0,0,110)

&nbsp;   validateBtn.BackgroundColor3 = Color3.fromRGB(70,150,70)

&nbsp;   validateBtn.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   validateBtn.Parent = frame



&nbsp;   local claimBtn = Instance.new("TextButton")

&nbsp;   claimBtn.Text = "Claim Key"

&nbsp;   claimBtn.Size = UDim2.new(0.4,0,0,40)

&nbsp;   claimBtn.Position = UDim2.new(0.5,0,0,110)

&nbsp;   claimBtn.BackgroundColor3 = Color3.fromRGB(70,70,150)

&nbsp;   claimBtn.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   claimBtn.Parent = frame



&nbsp;   local openSiteBtn = Instance.new("TextButton")

&nbsp;   openSiteBtn.Text = "Get Key From Website"

&nbsp;   openSiteBtn.Size = UDim2.new(0.8,0,0,40)

&nbsp;   openSiteBtn.Position = UDim2.new(0.1,0,0,160)

&nbsp;   openSiteBtn.BackgroundColor3 = Color3.fromRGB(150,70,70)

&nbsp;   openSiteBtn.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   openSiteBtn.Parent = frame



&nbsp;   local statusLabel = Instance.new("TextLabel")

&nbsp;   statusLabel.Size = UDim2.new(1,0,0,20)

&nbsp;   statusLabel.Position = UDim2.new(0,0,1,-20)

&nbsp;   statusLabel.BackgroundTransparency = 1

&nbsp;   statusLabel.TextColor3 = Color3.fromRGB(255,255,255)

&nbsp;   statusLabel.Font = Enum.Font.SourceSans

&nbsp;   statusLabel.TextSize = 18

&nbsp;   statusLabel.Text = ""

&nbsp;   statusLabel.Parent = frame



&nbsp;   return {

&nbsp;       KeyBox = keyBox,

&nbsp;       ValidateBtn = validateBtn,

&nbsp;       ClaimBtn = claimBtn,

&nbsp;       OpenSiteBtn = openSiteBtn,

&nbsp;       Status = statusLabel

&nbsp;   }

end



-- Validate key

function KeyModule.ValidateKey(key)

&nbsp;   local success, response = pcall(function()

&nbsp;       return HttpService:PostAsync(SERVER\_URL.."/api/validate",

&nbsp;           HttpService:JSONEncode({key=key, userId=tostring(player.UserId)}),

&nbsp;           Enum.HttpContentType.ApplicationJson)

&nbsp;   end)

&nbsp;   if success then

&nbsp;       local data = HttpService:JSONDecode(response)

&nbsp;       return data.valid

&nbsp;   else

&nbsp;       warn("Failed to validate key:", response)

&nbsp;       return false

&nbsp;   end

end



-- Claim key

function KeyModule.ClaimKey(key)

&nbsp;   local success, response = pcall(function()

&nbsp;       return HttpService:PostAsync(SERVER\_URL.."/api/claim",

&nbsp;           HttpService:JSONEncode({key=key, userId=tostring(player.UserId)}),

&nbsp;           Enum.HttpContentType.ApplicationJson)

&nbsp;   end)

&nbsp;   if success then

&nbsp;       local data = HttpService:JSONDecode(response)

&nbsp;       return data.ok

&nbsp;   else

&nbsp;       warn("Failed to claim key:", response)

&nbsp;       return false

&nbsp;   end

end



-- Open landing page

function KeyModule.OpenLandingPage()

&nbsp;   local url = SERVER\_URL.."/?userId="..tostring(player.UserId)

&nbsp;   -- Use Roblox's default browser if possible

&nbsp;   -- In exploits: syn.request({Url = url, Method = "GET"})

&nbsp;   print("Open the website in browser: "..url)

end



-- Show UI

function KeyModule.ShowUI()

&nbsp;   local ui = CreateUI()



&nbsp;   -- Buttons

&nbsp;   ui.OpenSiteBtn.MouseButton1Click:Connect(function()

&nbsp;       KeyModule.OpenLandingPage()

&nbsp;       ui.Status.Text = "Website opened. Complete 3 checkpoints to get key."

&nbsp;   end)



&nbsp;   ui.ValidateBtn.MouseButton1Click:Connect(function()

&nbsp;       local key = ui.KeyBox.Text

&nbsp;       if key == "" then

&nbsp;           ui.Status.Text = "Enter a key first!"

&nbsp;           return

&nbsp;       end

&nbsp;       local valid = KeyModule.ValidateKey(key)

&nbsp;       if valid then

&nbsp;           ui.Status.Text = "Key is valid!"

&nbsp;       else

&nbsp;           ui.Status.Text = "Key is invalid or expired."

&nbsp;       end

&nbsp;   end)



&nbsp;   ui.ClaimBtn.MouseButton1Click:Connect(function()

&nbsp;       local key = ui.KeyBox.Text

&nbsp;       if key == "" then

&nbsp;           ui.Status.Text = "Enter a key first!"

&nbsp;           return

&nbsp;       end

&nbsp;       local claimed = KeyModule.ClaimKey(key)

&nbsp;       if claimed then

&nbsp;           ui.Status.Text = "Key claimed successfully!"

&nbsp;       else

&nbsp;           ui.Status.Text = "Failed to claim key."

&nbsp;       end

&nbsp;   end)

end



return KeyModule



