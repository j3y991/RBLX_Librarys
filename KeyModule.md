-- KeyModule.lua

local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

local KeyModule = {}

local SERVER_URL = "https://jey-scripts.de"  -- change to your VPS

-- Helper functions
local function CreateUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "KeySystemUI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = PlayerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 400, 0, 200)
    frame.Position = UDim2.new(0.5, -200, 0.5, -100)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
    frame.BorderSizePixel = 0
    frame.Parent = screenGui

    local title = Instance.new("TextLabel")
    title.Text = "Roblox Key System"
    title.Size = UDim2.new(1,0,0,40)
    title.BackgroundTransparency = 1
    title.TextColor3 = Color3.fromRGB(255,255,255)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 24
    title.Parent = frame

    local keyBox = Instance.new("TextBox")
    keyBox.PlaceholderText = "Enter your key here"
    keyBox.Size = UDim2.new(0.8,0,0,40)
    keyBox.Position = UDim2.new(0.1,0,0,60)
    keyBox.BackgroundColor3 = Color3.fromRGB(50,50,50)
    keyBox.TextColor3 = Color3.fromRGB(255,255,255)
    keyBox.ClearTextOnFocus = false
    keyBox.Parent = frame

    local validateBtn = Instance.new("TextButton")
    validateBtn.Text = "Validate Key"
    validateBtn.Size = UDim2.new(0.4,0,0,40)
    validateBtn.Position = UDim2.new(0.1,0,0,110)
    validateBtn.BackgroundColor3 = Color3.fromRGB(70,150,70)
    validateBtn.TextColor3 = Color3.fromRGB(255,255,255)
    validateBtn.Parent = frame

    local claimBtn = Instance.new("TextButton")
    claimBtn.Text = "Claim Key"
    claimBtn.Size = UDim2.new(0.4,0,0,40)
    claimBtn.Position = UDim2.new(0.5,0,0,110)
    claimBtn.BackgroundColor3 = Color3.fromRGB(70,70,150)
    claimBtn.TextColor3 = Color3.fromRGB(255,255,255)
    claimBtn.Parent = frame

    local openSiteBtn = Instance.new("TextButton")
    openSiteBtn.Text = "Get Key From Website"
    openSiteBtn.Size = UDim2.new(0.8,0,0,40)
    openSiteBtn.Position = UDim2.new(0.1,0,0,160)
    openSiteBtn.BackgroundColor3 = Color3.fromRGB(150,70,70)
    openSiteBtn.TextColor3 = Color3.fromRGB(255,255,255)
    openSiteBtn.Parent = frame

    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(1,0,0,20)
    statusLabel.Position = UDim2.new(0,0,1,-20)
    statusLabel.BackgroundTransparency = 1
    statusLabel.TextColor3 = Color3.fromRGB(255,255,255)
    statusLabel.Font = Enum.Font.SourceSans
    statusLabel.TextSize = 18
    statusLabel.Text = ""
    statusLabel.Parent = frame

    return {
        KeyBox = keyBox,
        ValidateBtn = validateBtn,
        ClaimBtn = claimBtn,
        OpenSiteBtn = openSiteBtn,
        Status = statusLabel
    }
end

-- Validate key
function KeyModule.ValidateKey(key)
    local success, response = pcall(function()
        return HttpService:PostAsync(
            SERVER_URL.."/api/validate",
            HttpService:JSONEncode({key=key, userId=tostring(player.UserId)}),
            Enum.HttpContentType.ApplicationJson
        )
    end)

    if success then
        local data = HttpService:JSONDecode(response)
        return data.valid
    else
        warn("Failed to validate key:", response)
        return false
    end
end

-- Claim key
function KeyModule.ClaimKey(key)
    local success, response = pcall(function()
        return HttpService:PostAsync(
            SERVER_URL.."/api/claim",
            HttpService:JSONEncode({key=key, userId=tostring(player.UserId)}),
            Enum.HttpContentType.ApplicationJson
        )
    end)

    if success then
        local data = HttpService:JSONDecode(response)
        return data.ok
    else
        warn("Failed to claim key:", response)
        return false
    end
end

-- Open landing page
function KeyModule.OpenLandingPage()
    local url = SERVER_URL.."/?userId="..tostring(player.UserId)
    print("Open the website in browser: "..url)
end

-- Show UI
function KeyModule.ShowUI()
    local ui = CreateUI()

    ui.OpenSiteBtn.MouseButton1Click:Connect(function()
        KeyModule.OpenLandingPage()
        ui.Status.Text = "Website opened. Complete 3 checkpoints to get key."
    end)

    ui.ValidateBtn.MouseButton1Click:Connect(function()
        local key = ui.KeyBox.Text
        if key == "" then
            ui.Status.Text = "Enter a key first!"
            return
        end

        local valid = KeyModule.ValidateKey(key)
        if valid then
            ui.Status.Text = "Key is valid!"
        else
            ui.Status.Text = "Key is invalid or expired."
        end
    end)

    ui.ClaimBtn.MouseButton1Click:Connect(function()
        local key = ui.KeyBox.Text
        if key == "" then
            ui.Status.Text = "Enter a key first!"
            return
        end

        local claimed = KeyModule.ClaimKey(key)
        if claimed then
            ui.Status.Text = "Key claimed successfully!"
        else
            ui.Status.Text = "Failed to claim key."
        end
    end)
end

return KeyModule
