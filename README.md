local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local espBoxes = {}

local function createBox(player)
    local box = Drawing.new("Square")
    box.Thickness = 2
    box.Color = Color3.fromRGB(0, 255, 0)
    box.Filled = false
    box.Visible = false
    return box
end

local function updateBox(player, box)
    if not player or not player.Parent then box.Visible = false return end
    local char = player.Character
    if not char then box.Visible = false return end
    local root = char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Head")
    if not root then box.Visible = false return end
    local pos, onScreen = Camera:WorldToViewportPoint(root.Position)
    if not onScreen then box.Visible = false return end
    local scale = 3
    box.Position = Vector2.new(pos.X - scale, pos.Y - scale)
    box.Size = Vector2.new(scale*2, scale*2)
    box.Visible = true
end

for _, p in ipairs(Players:GetPlayers()) do
    if p ~= LocalPlayer then
        local box = createBox(p)
        espBoxes[p] = box
        RunService.RenderStepped:Connect(function()
            updateBox(p, box)
        end)
    end
end

Players.PlayerAdded:Connect(function(p)
    if p == LocalPlayer then return end
    local box = createBox(p)
    espBoxes[p] = box
    RunService.RenderStepped:Connect(function()
        updateBox(p, box)
    end)
end)
