
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Settings
local chainWindow = 0.6 -- Time to chain (seconds)
local teleportDistance = 5 -- How close to teleport behind

-- Variables
local lastBlackFlash = 0
local chainCount = 0
local isChaining = false

-- Find nearest player BEHIND you
local function findNearestPlayerBehind()
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    
    local nearest = nil
    local nearestDist = math.huge
    
    for _, otherPlayer in ipairs(Players:GetPlayers()) do
        if otherPlayer ~= player then
            local otherChar = otherPlayer.Character
            if otherChar then
                local otherRoot = otherChar:FindFirstChild("HumanoidRootPart")
                if otherRoot then
                    -- Check if they're behind us
                    local toTarget = (otherRoot.Position - root.Position).Unit
                    local behindCheck = toTarget:Dot(root.CFrame.LookVector)
                    
                    if behindCheck < -0.2 then -- Behind us
                        local dist = (root.Position - otherRoot.Position).Magnitude
                        if dist < nearestDist then
                            nearestDist = dist
                            nearest = otherPlayer
                        end
                    end
                end
            end
        end
    end
    
    return nearest
end

-- Teleport behind target
local function teleportBehind(target)
    local root = character:FindFirstChild("HumanoidRootPart")
    local targetRoot = target.Character and target.Character:FindFirstChild("HumanoidRootPart")
    
    if root and targetRoot then
        -- Position behind target
        local behindPos = targetRoot.Position - (targetRoot.CFrame.LookVector * teleportDistance)
        root.CFrame = CFrame.new(behindPos, targetRoot.Position)
    end
end

-- Simulate pressing the black flash key
local function pressBlackFlash()
    -- Try different keybinds (adjust based on your settings)
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Three, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Three, false, game)
end

-- Main chain function
local function doChainBlackFlash()
    local target = findNearestPlayerBehind()
    
    if target then
        -- Teleport behind them
        teleportBehind(target)
        
        -- Small delay for teleport
        task.wait(0.1)
        
        -- Press black flash again
        pressBlackFlash()
        
        chainCount = chainCount + 1
        lastBlackFlash = tick()
        
        print("[CHAIN] Black Flash #" .. chainCount)
    end
end

-- Detect when black flash is used (look for player state changes)
local oldPosition = character:FindFirstChild("HumanoidRootPart").Position

RunService.Heartbeat:Connect(function()
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    -- Detect black flash by sudden movement/teleport
    local currentPos = root.Position
    local distanceMoved = (currentPos - oldPosition).Magnitude
    
    -- If player teleported (black flash move), trigger chain
    if distanceMoved > 15 and tick() - lastBlackFlash > 0.1 then
        if tick() - lastBlackFlash < chainWindow then
            -- Double tap! Chain it
            doChainBlackFlash()
        else
            -- First black flash
            lastBlackFlash = tick()
            chainCount = 1
        end
    end
    
    oldPosition = currentPos
end)

-- Manual trigger with keybind (optional)
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Q then -- Press Q to manually chain
        doChainBlackFlash()
    end
end)

print("Script loaded! Press Q to manually trigger chain black flash")
