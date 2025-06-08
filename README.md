-- Carrega Rayfield UI
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "🔥 KIWI Hub - V2",
    LoadingTitle = "Kiwi Hub Carregando...",
    LoadingSubtitle = "Por Kiwi",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "ScriptHub",
        FileName = "MenuData"
    },
    Discord = {
        Enabled = false,
    },
    KeySystem = false
})

local Player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local RS = game:GetService("ReplicatedStorage")

local Char = Player.Character or Player.CharacterAdded:Wait()
local Hum = Char:WaitForChild("Humanoid")
local HRP = Char:WaitForChild("HumanoidRootPart")

local noclip = false
local flying = false
local aimbotEnabled = false
local autoClick = false

-- Tabs
local main = Window:CreateTab("🎮 Principal", 4483362458)
local player = Window:CreateTab("👤 Jogador", 4483362458)
local troll = Window:CreateTab("🌀 Trolls", 4483362458)
local extra = Window:CreateTab("🛠️ Extra", 4483362458)

-- 📦 HITBOX
main:CreateSlider({
    Name = "Tamanho da Hitbox",
    Range = {2, 20},
    Increment = 1,
    CurrentValue = 5,
    Callback = function(size)
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= Player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local part = p.Character.HumanoidRootPart
                part.Size = Vector3.new(size, size, size)
                part.Transparency = 0.5
                part.Material = Enum.Material.ForceField
                part.CanCollide = false
            end
        end
    end,
})

main:CreateButton({
    Name = "🔁 Resetar Hitbox",
    Callback = function()
        for _, p in pairs(game.Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local part = p.Character.HumanoidRootPart
                part.Size = Vector3.new(2, 2, 1)
                part.Transparency = 1
                part.Material = Enum.Material.Plastic
            end
        end
    end
})

-- 👀 ESP
main:CreateButton({
    Name = "👁️ Ativar ESP Colorido",
    Callback = function()
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= Player and p.Character and p.Character:FindFirstChild("Head") then
                local esp = Instance.new("BillboardGui", p.Character.Head)
                esp.Size = UDim2.new(0, 100, 0, 40)
                esp.AlwaysOnTop = true

                local label = Instance.new("TextLabel", esp)
                label.Size = UDim2.new(1, 0, 1, 0)
                label.Text = p.Name
                label.BackgroundTransparency = 1
                label.TextColor3 = Color3.fromRGB(math.random(0,255), math.random(0,255), math.random(0,255))
            end
        end
    end
})

-- 🦘 Pulo Infinito
player:CreateToggle({
    Name = "Pulo Infinito",
    CurrentValue = false,
    Callback = function(val)
        _G.infiniteJump = val
    end
})
UIS.JumpRequest:Connect(function()
    if _G.infiniteJump then
        Hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- 🚀 Fly
player:CreateToggle({
    Name = "Fly",
    CurrentValue = false,
    Callback = function(state)
        flying = state
        local bv = Instance.new("BodyVelocity", HRP)
        bv.Velocity = Vector3.zero
        bv.MaxForce = Vector3.new(100000, 100000, 100000)
        bv.Name = "flyForce"

        RunService.RenderStepped:Connect(function()
            if flying then
                local move = Vector3.zero
                if UIS:IsKeyDown(Enum.KeyCode.W) then move = move + (workspace.CurrentCamera.CFrame.LookVector) end
                if UIS:IsKeyDown(Enum.KeyCode.S) then move = move - (workspace.CurrentCamera.CFrame.LookVector) end
                if UIS:IsKeyDown(Enum.KeyCode.A) then move = move - (workspace.CurrentCamera.CFrame.RightVector) end
                if UIS:IsKeyDown(Enum.KeyCode.D) then move = move + (workspace.CurrentCamera.CFrame.RightVector) end
                HRP:FindFirstChild("flyForce").Velocity = move * 80
            end
        end)
    end
})

-- 🚫 NoClip
player:CreateToggle({
    Name = "NoClip",
    CurrentValue = false,
    Callback = function(state)
        noclip = state
        RunService.Stepped:Connect(function()
            if noclip and Char then
                for _, v in pairs(Char:GetDescendants()) do
                    if v:IsA("BasePart") then v.CanCollide = false end
                end
            end
        end)
    end
})

-- ⚡ Velocidade e Pulo
player:CreateSlider({
    Name = "Velocidade",
    Range = {16, 150},
    Increment = 1,
    CurrentValue = 16,
    Callback = function(val)
        Hum.WalkSpeed = val
    end
})

player:CreateSlider({
    Name = "Altura do Pulo",
    Range = {50, 200},
    Increment = 5,
    CurrentValue = 50,
    Callback = function(val)
        Hum.JumpPower = val
    end
})

-- 🎯 Aimbot (simples)
extra:CreateToggle({
    Name = "Aimbot (Head)",
    CurrentValue = false,
    Callback = function(val)
        aimbotEnabled = val
    end
})

RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local closest = nil
        local shortest = math.huge
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= Player and p.Character and p.Character:FindFirstChild("Head") then
                local pos = workspace.CurrentCamera:WorldToViewportPoint(p.Character.Head.Position)
                local dist = (Vector2.new(pos.X, pos.Y) - UIS:GetMouseLocation()).Magnitude
                if dist < shortest then
                    shortest = dist
                    closest = p
                end
            end
        end
        if closest then
            workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, closest.Character.Head.Position)
        end
    end
end)

-- ⚔️ Auto Clicker
extra:CreateToggle({
    Name = "Auto Clicker (mouse1)",
    CurrentValue = false,
    Callback = function(val)
        autoClick = val
    end
})
task.spawn(function()
    while true do
        if autoClick then
            mouse1click()
        end
        wait(0.1)
    end
end)

-- 🎁 Give Item
troll:CreateInput({
    Name = "Give Item por Nome",
    PlaceholderText = "Ex: Sword",
    RemoveTextAfterFocusLost = true,
    Callback = function(name)
        local item = RS:FindFirstChild(name)
        if item then
            item:Clone().Parent = Player.Backpack
        end
    end,
})

-- 📍 Teleport
troll:CreateDropdown({
    Name = "Teleportar para...",
    Options = (function()
        local list = {}
        for _, v in pairs(game.Players:GetPlayers()) do
            if v ~= Player then table.insert(list, v.Name) end
        end
        return list
    end)(),
    Callback = function(name)
        local target = game.Players:FindFirstChild(name)
        if target and target.Character then
            Char:MoveTo(target.Character:GetPrimaryPartCFrame().Position + Vector3.new(0, 5, 0))
        end
    end
})

-- 💣 Explosão
troll:CreateButton({
    Name = "Explodir onde está",
    Callback = function()
        local boom = Instance.new("Explosion")
        boom.Position = HRP.Position
        boom.BlastRadius = 10
        boom.Parent = workspace
    end
})

-- ❌ Fechar menu
extra:CreateButton({
    Name = "❌ Destruir Menu",
    Callback = function()
        Rayfield:Destroy()
    end
})
