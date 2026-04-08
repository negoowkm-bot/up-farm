local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

local npcList = {"Flora","Octidal","Morfo","Vulcan","Brain","Cursed King"}
local farming = {}
local index = {}

for _, v in ipairs(npcList) do
    farming[v] = false
    index[v] = 1
end

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "PRO HUB"

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 320, 0, 360)
main.Position = UDim2.new(0.3,0,0.3,0)
main.BackgroundColor3 = Color3.fromRGB(18,18,18)
main.Active = true
main.Draggable = true

-- borda
local stroke = Instance.new("UIStroke", main)
stroke.Color = Color3.fromRGB(60,60,60)
stroke.Thickness = 2

-- TOP BAR
local top = Instance.new("Frame", main)
top.Size = UDim2.new(1,0,0,45)
top.BackgroundColor3 = Color3.fromRGB(25,25,25)

local title = Instance.new("TextLabel", top)
title.Size = UDim2.new(1,0,1,0)
title.Text = "⚡ PRO FARM HUB"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 16

-- botão minimizar
local minimize = Instance.new("TextButton", top)
minimize.Size = UDim2.new(0,40,1,0)
minimize.Position = UDim2.new(1,-40,0,0)
minimize.Text = "-"
minimize.BackgroundColor3 = Color3.fromRGB(40,40,40)
minimize.TextColor3 = Color3.new(1,1,1)

local open = true

minimize.MouseButton1Click:Connect(function()
    open = not open

    if open then
        main:TweenSize(UDim2.new(0, 320, 0, 360), "Out", "Quad", 0.25, true)
        scroll.Visible = true
    else
        main:TweenSize(UDim2.new(0, 320, 0, 45), "Out", "Quad", 0.25, true)
        scroll.Visible = false
    end
end)

-- SCROLL
local scroll = Instance.new("ScrollingFrame", main)
scroll.Size = UDim2.new(1,0,1,-45)
scroll.Position = UDim2.new(0,0,0,45)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 4

local layout = Instance.new("UIListLayout", scroll)
layout.Padding = UDim.new(0,8)

-- BOTÕES
for _, name in ipairs(npcList) do
    local btn = Instance.new("TextButton", scroll)
    btn.Size = UDim2.new(1,-20,0,40)
    btn.Text = name.." [OFF]"
    btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14

    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0,6)

    btn.MouseButton1Click:Connect(function()
        farming[name] = not farming[name]

        if farming[name] then
            btn.Text = name.." [ON]"
            btn.BackgroundColor3 = Color3.fromRGB(0,170,0)
        else
            btn.Text = name.." [OFF]"
            btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
        end
    end)
end

-- ajustar scroll automaticamente
layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    scroll.CanvasSize = UDim2.new(0,0,0,layout.AbsoluteContentSize.Y + 10)
end)

-- PEGAR NPC
function getNPCs(name)
    local list = {}
    local enemies = workspace:FindFirstChild("_Enemies")

    if enemies then
        for _, npc in ipairs(enemies:GetChildren()) do
            if npc.Name == name and npc:FindFirstChild("HumanoidRootPart") then
                table.insert(list, npc)
            end
        end
    end

    return list
end

-- LOOP FARM
task.spawn(function()
    while task.wait(0.3) do
        for _, name in ipairs(npcList) do
            if farming[name] then
                local list = getNPCs(name)

                if #list > 0 then
                    if index[name] > #list then
                        index[name] = 1
                    end

                    local npc = list[index[name]]

                    if npc and npc:FindFirstChild("HumanoidRootPart") then
                        hrp.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0,0,-2)
                        hrp.Velocity = Vector3.new(0,0,0)
                    end

                    index[name] = index[name] + 1
                    task.wait(0.2)
                end
            end
        end
    end
end)
