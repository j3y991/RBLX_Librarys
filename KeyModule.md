local HttpService = game:GetService("HttpService")

local Players = game:GetService("Players")

local player = Players.LocalPlayer



local KeyModule = {}

local SERVER\_URL = "http://193.34.69.102:3000"



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



-- Claim key (mark as used)

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

&nbsp;       print("Failed to claim key:", response)

&nbsp;       return false

&nbsp;   end

end



-- Open landing page

function KeyModule.OpenLandingPage()

&nbsp;   local url = SERVER\_URL .. "/?userId=" .. tostring(player.UserId)

&nbsp;   -- Opens the URL in default browser

&nbsp;   -- For Roblox Studio, this may require a compatible exploit or dev environment

&nbsp;   syn.request({Url = url, Method = "GET"})  -- if using Synapse X

end



return KeyModule







