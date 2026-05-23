-- [[ Coelho Hub - Clean Template ]]
-- Desenvolvido na pura força do tédio (e do pó de café)
-- Créditos: by mr by tedio

local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = " Coelho Hub",
    SubTitle = "by mr by tedio",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.RightControl
})
local ScreenGui = Instance.new("ScreenGui")
local ToggleBtn = Instance.new("ImageButton")
local UICorner = Instance.new("UICorner")

ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false

ToggleBtn.Parent = ScreenGui
ToggleBtn.Size = UDim2.new(0, 60, 0, 60)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, 0)
ToggleBtn.BackgroundTransparency = 1
ToggleBtn.Image = "rbxassetid://108660186329467"

UICorner.CornerRadius = UDim.new(1, 0)
UICorner.Parent = ToggleBtn

-- Abre/fecha a GUI
ToggleBtn.MouseButton1Click:Connect(function()
    Window.Enabled = not Window.Enabled
end)

-- Arrastar a bolinha
local dragging, dragStart, startPos

ToggleBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = ToggleBtn.Position
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        ToggleBtn.Position = UDim2.new(
            startPos.X.Scale, 
            startPos.X.Offset + delta.X, 
            startPos.Y.Scale, 
            startPos.Y.Offset + delta.Y
        )
    end
end)

game:GetService("UserInputService").InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)
-- ========================================================
-- 1. PRIMEIRO: CRIA TODAS AS ABAS DO MENU
-- ========================================================
local Tabs = {
     Config = Window:AddTab({ Title = "config", Icon = "settings" }),
      Main = Window:AddTab({ Title = "main", Icon = "swords" }),
    Fruit = Window:AddTab({ Title = "Fruit", Icon = "cherry" }),
    Teleport = Window:AddTab({ Title = "Teleport", Icon = "compass" }),
    Creditos = Window:AddTab({ Title = "Creditos", Icon = "info" })
}

-- [[ GERENCIADORES INTERNOS ]]
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
SaveManager:SetIgnoreIndexes({})

-- ========================================================
-- ABA PRINCIPAL (MAIN)
-- ========================================================
Tabs.Main:AddParagraph({
    Title = "Bem-vindo ao Coelho Hub!",
    Content = "Modo cafeína ativado. Use as opções abaixo para automatizar o jogo."
})

Tabs.Main:AddToggle("AutoFarmLevelToggle", {
    Title = "Auto Farm Level",
    Default = false,
    Callback = function(Value)
        _G.AutoFarmMissions = Value
        if Value then
            local success, err = pcall(function()

                print("Auto Farm ativado!")
            end)
        end
    end
})
            print("Coelho Hub: Auto Farm Level LIGADO")
        
            print("Coelho Hub: Auto Farm Level DESLIGADO")

-- ========================================================
-- ABA CONFIG
-- ========================================================


-- ========================================================
-- ABA CRÉDITOS
-- (A Fluent não suporta AddImageLabel nativamente,
--  então usamos Paragraph pra dar o crédito da imagem)
-- ========================================================
Tabs.Creditos:AddParagraph({
    Title = " Musa do Café (Fiscal de Código)",
    Content = "A guardiã que salvou o Coelho Hub de 60 erros fatais.\nID da imagem: 108660186329467"
})

Tabs.Creditos:AddParagraph({
    Title = "Agradecimentos Especiais",
    Content = "Criado por: by mr."
})

-- ========================================================
-- ATALHO PARA MINIMIZAR (RightAlt)
-- ========================================================
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.RightAlt then
        Fluent:ToggleVisibility()
    end
end)
-- =====================================================
-- Auto Attack - Corrigido pra parar de dar erro no Studio
-- Funções de executor (getupvalue, setupvalue, etc)
-- funcionam normal no executor, aqui só silenciamos o Luau
-- =====================================================
 
-- Fallbacks pra o Studio não surtar com funções de executor
local getupvalue   = getupvalue   or function() return nil end
local setupvalue   = setupvalue   or function() end
local getgc        = getgc        or function() return {} end
local hookfunction = hookfunction or function() end
local iscclosure   = iscclosure   or function() return false end
local getsenv      = getsenv      or function() return {} end
 
-- =====================================================
loadstring(game:HttpGet("https://raw.githubusercontent.com/AnhDzaiScript/Setting/refs/heads/main/FastMax.lua"))()
 
local function GetBladeHits()
    local targets = {}
    local function GetDistance(v)
        return (v.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
    end
 
    for _, part in pairs({game.Workspace.Enemies, game.Workspace.Characters}) do
        for _, v in pairs(part:GetChildren()) do
            if v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Head") and v:FindFirstChild("Humanoid") then
                if GetDistance(v.HumanoidRootPart) < 60 then
                    table.insert(targets, v)
                end
            end
        end
    end
 
    return targets
end
 
local function AttackAll()
    local player = game.Players.LocalPlayer
    local character = player.Character
    if not character then return end
 
    local equippedWeapon = character:FindFirstChild("EquippedWeapon")
    if not equippedWeapon then return end
 
    local enemies = GetBladeHits()
    if #enemies > 0 then
        local netModule = game:GetService("ReplicatedStorage"):WaitForChild("Modules"):WaitForChild("Net")
        netModule:WaitForChild("RE/RegisterAttack"):FireServer(-math.huge)
 
        local args = {nil, {}}
        for i, v in pairs(enemies) do
            if not args[1] then
                args[1] = v.Head
            end
            args[2][i] = {v, v.HumanoidRootPart}
        end
 
        netModule:WaitForChild("RE/RegisterHit"):FireServer(unpack(args))
    end
end
 
spawn(function()
    while task.wait() do AttackAll() end
end)
 
-- =====================================================
 
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local VirtualInputManager = game:GetService("VirtualInputManager")
 
local Player = Players.LocalPlayer
local Modules = ReplicatedStorage:WaitForChild("Modules")
local Net = Modules:WaitForChild("Net")
local RegisterAttack = Net:WaitForChild("RE/RegisterAttack")
local RegisterHit = Net:WaitForChild("RE/RegisterHit")
local ShootGunEvent = Net:WaitForChild("RE/ShootGunEvent")
local GunValidator = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Validator2")
 
local Config = {
    AttackDistance = 90,
    AttackMobs = true,
    AttackPlayers = true,
    AttackCooldown = 0.2,
    ComboResetTime = 0.3,
    MaxCombo = 4,
    HitboxLimbs = {"RightLowerArm", "RightUpperArm", "LeftLowerArm", "LeftUpperArm", "RightHand", "LeftHand"},
    AutoClickEnabled = true
}
 
local FastAttack = {}
FastAttack.__index = FastAttack
 
function FastAttack.new()
    local self = setmetatable({
        Debounce = 0,
        ComboDebounce = 0,
        ShootDebounce = 0,
        M1Combo = 0,
        EnemyRootPart = nil,
        Connections = {},
        Overheat = {Dragonstorm = {MaxOverheat = 3, Cooldown = 0, TotalOverheat = 0, Distance = 350, Shooting = false}},
        ShootsPerTarget = {["Dual Flintlock"] = 2},
        SpecialShoots = {["Skull Guitar"] = "TAP", ["Bazooka"] = "Position", ["Cannon"] = "Position", ["Dragonstorm"] = "Overheat"}
    }, FastAttack)
 
    pcall(function()
        self.CombatFlags = require(Modules.Flags).COMBAT_REMOTE_THREAD
        self.ShootFunction = getupvalue(require(ReplicatedStorage.Controllers.CombatController).Attack, 9)
        local LocalScript = Player:WaitForChild("PlayerScripts"):FindFirstChildOfClass("LocalScript")
        if LocalScript and getsenv then
            self.HitFunction = getsenv(LocalScript)._G.SendHitsToServer
        end
    end)
 
    return self
end
 
function FastAttack:IsEntityAlive(entity)
    local humanoid = entity and entity:FindFirstChild("Humanoid")
    return humanoid and humanoid.Health > 0
end
 
function FastAttack:CheckStun(Character, Humanoid, ToolTip)
    local Stun = Character:FindFirstChild("Stun")
    local Busy = Character:FindFirstChild("Busy")
    if Humanoid.Sit and (ToolTip == "Sword" or ToolTip == "Melee" or ToolTip == "Blox Fruit") then
        return false
    elseif Stun and Stun.Value > 0 or Busy and Busy.Value then
        return false
    end
    return true
end
 
function FastAttack:GetBladeHits(Character, Distance)
    local Position = Character:GetPivot().Position
    local BladeHits = {}
    Distance = Distance or Config.AttackDistance
 
    local function ProcessTargets(Folder)
        for _, Enemy in ipairs(Folder:GetChildren()) do
            if Enemy ~= Character and self:IsEntityAlive(Enemy) then
                local BasePart = Enemy:FindFirstChild(Config.HitboxLimbs[math.random(#Config.HitboxLimbs)]) or Enemy:FindFirstChild("HumanoidRootPart")
                if BasePart and (Position - BasePart.Position).Magnitude <= Distance then
                    if not self.EnemyRootPart then
                        self.EnemyRootPart = BasePart
                    else
                        table.insert(BladeHits, {Enemy, BasePart})
                    end
                end
            end
        end
    end
 
    if Config.AttackMobs then ProcessTargets(Workspace.Enemies) end
    if Config.AttackPlayers then ProcessTargets(Workspace.Characters) end
 
    return BladeHits
end
 
function FastAttack:GetClosestEnemy(Character, Distance)
    local BladeHits = self:GetBladeHits(Character, Distance)
    local Closest, MinDistance = nil, math.huge
 
    for _, Hit in ipairs(BladeHits) do
        local Magnitude = (Character:GetPivot().Position - Hit[2].Position).Magnitude
        if Magnitude < MinDistance then
            MinDistance = Magnitude
            Closest = Hit[2]
        end
    end
    return Closest
end
 
function FastAttack:GetCombo()
    local Combo = (tick() - self.ComboDebounce) <= Config.ComboResetTime and self.M1Combo or 0
    Combo = Combo >= Config.MaxCombo and 1 or Combo + 1
    self.ComboDebounce = tick()
    self.M1Combo = Combo
    return Combo
end
 
function FastAttack:ShootInTarget(TargetPosition)
    local Character = Player.Character
    if not self:IsEntityAlive(Character) then return end
 
    local Equipped = Character:FindFirstChildOfClass("Tool")
    if not Equipped or Equipped.ToolTip ~= "Gun" then return end
 
    local Cooldown = Equipped:FindFirstChild("Cooldown") and Equipped.Cooldown.Value or 0.3
    if (tick() - self.ShootDebounce) < Cooldown then return end
 
    local ShootType = self.SpecialShoots[Equipped.Name] or "Normal"
    if ShootType == "Position" or (ShootType == "TAP" and Equipped:FindFirstChild("RemoteEvent")) then
        Equipped:SetAttribute("LocalTotalShots", (Equipped:GetAttribute("LocalTotalShots") or 0) + 1)
        GunValidator:FireServer(self:GetValidator2())
 
        if ShootType == "TAP" then
            Equipped.RemoteEvent:FireServer("TAP", TargetPosition)
        else
            ShootGunEvent:FireServer(TargetPosition)
        end
        self.ShootDebounce = tick()
    else
        VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 1)
        task.wait(0.05)
        VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 1)
        self.ShootDebounce = tick()
    end
end
 
function FastAttack:GetValidator2()
    local v1 = getupvalue(self.ShootFunction, 15)
    local v2 = getupvalue(self.ShootFunction, 13)
    local v3 = getupvalue(self.ShootFunction, 16)
    local v4 = getupvalue(self.ShootFunction, 17)
    local v5 = getupvalue(self.ShootFunction, 14)
    local v6 = getupvalue(self.ShootFunction, 12)
    local v7 = getupvalue(self.ShootFunction, 18)
 
    local v8 = v6 * v2
    local v9 = (v5 * v2 + v6 * v1) % v3
    v9 = (v9 * v3 + v8) % v4
    v5 = math.floor(v9 / v3)
    v6 = v9 - v5 * v3
    v7 = v7 + 1
 
    setupvalue(self.ShootFunction, 15, v1)
    setupvalue(self.ShootFunction, 13, v2)
    setupvalue(self.ShootFunction, 16, v3)
    setupvalue(self.ShootFunction, 17, v4)
    setupvalue(self.ShootFunction, 14, v5)
    setupvalue(self.ShootFunction, 12, v6)
    setupvalue(self.ShootFunction, 18, v7)
 
    return math.floor(v9 / v4 * 16777215), v7
end
 
function FastAttack:UseNormalClick(Character, Humanoid, Cooldown)
    self.EnemyRootPart = nil
    local BladeHits = self:GetBladeHits(Character)
 
    if self.EnemyRootPart then
        RegisterAttack:FireServer(Cooldown)
        if self.CombatFlags and self.HitFunction then
            self.HitFunction(self.EnemyRootPart, BladeHits)
        else
            RegisterHit:FireServer(self.EnemyRootPart, BladeHits)
        end
    end
end
 
function FastAttack:UseFruitM1(Character, Equipped, Combo)
    local Targets = self:GetBladeHits(Character)
    if not Targets[1] then return end
 
    local Direction = (Targets[1][2].Position - Character:GetPivot().Position).Unit
    Equipped.LeftClickRemote:FireServer(Direction, Combo)
end
 
function FastAttack:Attack()
    if not Config.AutoClickEnabled or (tick() - self.Debounce) < Config.AttackCooldown then return end
    local Character = Player.Character
    if not Character or not self:IsEntityAlive(Character) then return end
 
    local Humanoid = Character.Humanoid
    local Equipped = Character:FindFirstChildOfClass("Tool")
    if not Equipped then return end
 
    local ToolTip = Equipped.ToolTip
    if not table.find({"Melee", "Blox Fruit", "Sword", "Gun"}, ToolTip) then return end
 
    local Cooldown = Equipped:FindFirstChild("Cooldown") and Equipped.Cooldown.Value or Config.AttackCooldown
    if not self:CheckStun(Character, Humanoid, ToolTip) then return end
 
    local Combo = self:GetCombo()
    Cooldown = Cooldown + (Combo >= Config.MaxCombo and 0.05 or 0)
    self.Debounce = Combo >= Config.MaxCombo and ToolTip ~= "Gun" and (tick() + 0.05) or tick()
 
    if ToolTip == "Blox Fruit" and Equipped:FindFirstChild("LeftClickRemote") then
        self:UseFruitM1(Character, Equipped, Combo)
    elseif ToolTip == "Gun" then
        local Target = self:GetClosestEnemy(Character, 120)
        if Target then
            self:ShootInTarget(Target.Position)
        end
    else
        self:UseNormalClick(Character, Humanoid, Cooldown)
    end
end
 
local AttackInstance = FastAttack.new()
table.insert(AttackInstance.Connections, RunService.Stepped:Connect(function()
    AttackInstance:Attack()
end))
 
-- getgc só existe no executor, no Studio retorna {} então o loop não faz nada
for _, v in pairs(getgc(true)) do
    if typeof(v) == "function" and iscclosure(v) then
        local name = debug.getinfo(v).name
        if name == "Attack" or name == "attack" or name == "RegisterHit" then
            hookfunction(v, function(...)
                AttackInstance:Attack()
                return v(...)
            end)
        end
    end
end
 
-- =====================================================
-- Fast 2
-- =====================================================
local Modules2 = game.ReplicatedStorage.Modules
local Net2 = Modules2.Net
local Register_Hit = Net2:WaitForChild("RE/RegisterHit")
local Register_Attack = Net2:WaitForChild("RE/RegisterAttack")
local Funcs = {}
 
local function GetAllBladeHits()
    local bladehits = {}
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        if v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") and v.Humanoid.Health > 0
        and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
            table.insert(bladehits, v)
        end
    end
    return bladehits
end
 
local function Getplayerhit()
    local bladehits = {}
    for _, v in pairs(workspace.Characters:GetChildren()) do
        if v.Name ~= game.Players.LocalPlayer.Name and v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") and v.Humanoid.Health > 0
        and (v.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 65 then
            table.insert(bladehits, v)
        end
    end
    return bladehits
end
 
function Funcs:Attack()
    local bladehits = {}
    for _, v in pairs(GetAllBladeHits()) do
        table.insert(bladehits, v)
    end
    for _, v in pairs(Getplayerhit()) do
        table.insert(bladehits, v)
    end
    if #bladehits == 0 then return end
 
    local args = {
        [1] = nil,
        [2] = {},
        [4] = "078da341"
    }
    for r, v in pairs(bladehits) do
        Register_Attack:FireServer(0)
        if not args[1] then
            args[1] = v.Head
        end
        args[2][r] = {
            [1] = v,
            [2] = v.HumanoidRootPart
        }
    end
    Register_Hit:FireServer(unpack(args))
end

local TeamDropdown

TeamDropdown = Tabs.Config:AddDropdown("TeamDropdown", {
    Title = "Selecionar Time",
    Values = {"---", "Marines", "Pirates"},
    Default = "---",
    Callback = function(Value)
        if Value == "-- Selecionar --" then return end
        local replicated = game:GetService("ReplicatedStorage")
        local success, err = pcall(function()
            replicated.Remotes.CommF_:InvokeServer("SetTeam", Value)
        end)
        if success then
            print("Entrou no time: " .. Value)
            task.wait(0.2)
            TeamDropdown:SetValue("---")
        else
            warn("Erro ao entrar no time: " .. tostring(err))
        end
    end
})

Tabs.Fruit:AddToggle("CollectFruitsBtn", {
    Title = "Collect Fruits",
    Default = false,
    Callback = function(Value)
        _G.CollectFruits = Value
        if Value then
            task.spawn(function()
                while _G.CollectFruits do
                    local _success, _err = pcall(function()
                        local plr = game.Players.LocalPlayer
                        local char = plr.Character
                        if char then
                            local hrp = char:FindFirstChild("HumanoidRootPart")
                            local hum = char:FindFirstChildOfClass("Humanoid")
                            if hum then hum.WalkSpeed = 325 end

                            for _, K in pairs(workspace:GetChildren()) do
                                if string.find(K.Name, "Fruit") and K:FindFirstChild("Handle") then
                                    while _G.CollectFruits and K and K.Parent do
                                        local dist = (K.Handle.Position - hrp.Position).Magnitude
                                        if dist < 5 then break end

                                        -- noclip em todos os parts do char
                                        for _, part in pairs(char:GetDescendants()) do
                                            if part:IsA("BasePart") then
                                                part.CanCollide = false
                                            end
                                        end

                                        local bv = Instance.new("BodyVelocity")
                                        bv.Velocity = (K.Handle.Position - hrp.Position).Unit * 325
                                        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        bv.Parent = hrp
                                        task.wait(0.05)
                                        bv:Destroy()
                                    end
                                end
                            end

                            -- volta colisão normal depois de pegar
                            for _, part in pairs(char:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = true
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)
        else
            -- desligou o toggle, volta tudo ao normal
            local char = game.Players.LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum then hum.WalkSpeed = 16 end
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end
            end
        end
    end
})
local selectedStats = {}
local plr = game.Players.LocalPlayer
local replicated = game:GetService("ReplicatedStorage")

Tabs.Config:AddDropdown("StatDropdown", {
    Title = "Stats ",
    Values = {"Melee", "Defense", "Sword", "Gun", "Devil"},
    Default = {},
    Multi = true,
    Callback = function(Value)
        selectedStats = {}
        for stat, enabled in pairs(Value) do
            if enabled then
                table.insert(selectedStats, stat)
            end
        end
    end
})

Tabs.Config:AddToggle("AutoStatToggle", {
    Title = "Auto Distribuir Stats",
    Default = false,
    Callback = function(Value)
        _G.AutoStat = Value
        if Value then
            task.spawn(function()
                while _G.AutoStat do
                    pcall(function()
                        if plr.Data.Points.Value ~= 0 then
                            local hasMelee = table.find(selectedStats, "Melee")
                            local hasDefense = table.find(selectedStats, "Defense")

                            if hasMelee and hasDefense then
                                -- prioridade melee mas ainda da pra defense
                                replicated.Remotes.CommF_:InvokeServer("AddPoint", "Melee", 2)
                                replicated.Remotes.CommF_:InvokeServer("AddPoint", "Defense", 1)
                            else
                                for _, stat in pairs(selectedStats) do
                                    local statName = stat == "Devil" and "Demon Fruit" or stat
                                    replicated.Remotes.CommF_:InvokeServer("AddPoint", statName, 1)
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)
        end
    end
})

Tabs.Teleport:AddButton({
    Title = "Teleport to Sea 1",
    Callback = function()
        pcall(function()
            replicated.Remotes.CommF_:InvokeServer("TravelMain")
        end)
    end
})

Tabs.Teleport:AddButton({
    Title = "Teleport to Sea 2",
    Callback = function()
        pcall(function()
            replicated.Remotes.CommF_:InvokeServer("TravelDressrosa")
        end)
    end
})

Tabs.Teleport:AddButton({
    Title = "Teleport to Sea 3",
    Callback = function()
        pcall(function()
            replicated.Remotes.CommF_:InvokeServer("TravelZou")
        end)
    end
})
