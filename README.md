

if not game:IsLoaded() then
    game.Loaded:Wait()
end

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

local Window = OrionLib:MakeWindow({Name = "Anime Evolutions Simulator|Lcopium#1131", HidePremium = false, SaveConfig = true, ConfigFolder = "OrionTest"})


local Tab1 = Window:MakeTab({
	Name = "Auto Farm",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = true
})


local Section1a = Tab1:AddSection({
	Name = "Basic"
})

function collectfirstdrops()
    for _,v in pairs(game.Workspace["__DROPS"]:GetChildren()) do
        v.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
    end
end
collectfirstdrops()

local collectdrops = false
Section1a:AddToggle({
	Name = "Collect Drops",
	Default = true,
	Callback = function(Value)
		collectdrops = Value
        if Value then
            collectfirstdrops()
        end
	end
})



game.Workspace["__DROPS"].ChildAdded:Connect(function(child)
    if collectdrops then
        child.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
    end
end)


local autoclick = false
local autoclicker = Section1a:AddToggle({
	Name = "Autoclick",
	Default = false,
	Callback = function(Value)
		autoclick = Value
	end
})

spawn(function()
    while wait(0.15) do
        if autoclick then
            firesignal(game.Players.LocalPlayer.PlayerGui.UI.BottomFrame.Click.TextButton.MouseButton1Click)
        end
    end
end)

local Section1b = Tab1:AddSection({
	Name = "Autofarm"
})




local mobs = {}
local mobarealist = {}



local mobtofarm
local mobarea

local mobareadropdown = Section1b:AddDropdown({
	Name = "Mob Area List",
	Default = "yes",
	Options = mobarealist,
	Callback = function(Value)
        mobarea = Value
        getmobs()
	end
})


function getmobarea()
    mobareadropdown:Refresh({},true)
    for _,v in pairs(game.Workspace["__WORKSPACE"].Mobs:GetChildren()) do
        if not table.find(mobarealist,v.Name) then
            table.insert(mobarealist,v.Name)
        end
    end
    mobareadropdown:Refresh(mobarealist,false)
end
pcall(getmobarea)

local mobdropdown = Section1b:AddDropdown({
	Name = "Mob List",
	Default = "yes",
	Options = mobs,
	Callback = function(Value)
		mobtofarm = Value
	end
})

Section1b:AddButton({
	Name = "Refresh",
	Callback = function()
        pcall(getmobarea)
        wait()
        pcall(getmobs)
  	end
})


function getmobs()
    mobdropdown:Refresh({},true)
    for _,mob in pairs(game.Workspace["__WORKSPACE"].Mobs[mobarea]:GetChildren()) do
        if not table.find(mobs,mob.Name) then
            table.insert(mobs,mob.name)
        end
    end
    mobdropdown:Refresh(mobs,false)
end
pcall(getmobs)


local autofarm = false
Section1b:AddToggle({
	Name = "Auto Farm Selected Mob",
	Default = false,
	Callback = function(Value)
		autofarm = Value
	end
})


local defaultx = game:GetService("Workspace").Point.size.X
local defaulty = game:GetService("Workspace").Point.size.Y
local defaultz = game:GetService("Workspace").Point.size.Z


function gettarget()
    local targetdistance = math.huge
    local target
    for _,mob in pairs(game.Workspace["__WORKSPACE"].Mobs[mobarea]:GetChildren()) do
        if mob:IsA("Model") and mob.Name == mobtofarm then
            local distance = (game.Players.LocalPlayer.Character.HumanoidRootPart.Position - mob.HumanoidRootPart.Position).magnitude
            if distance < targetdistance and distance < 500 then
                targetdistance = distance
                target = mob
            end
        end
    end
    return target
end


spawn(function()
    while wait(0.1) do
        if autofarm then
            pcall(function()
                local target = gettarget()
                local hp = target.Settings.HP.Value
                if hp > 0 then
                    local oldhp
                    local failsafe = 0
                    local killfailsafe = false
                    autoclicker:Set(true)
                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0)
                    spawn(function()
                        while wait() do
                            if failsafe > 3 then
                                autoclicker:Set(true)
                                if target.Settings.HP.Value > 0 then
                                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0)
                                end
                                failsafe = 0
                            elseif killfailsafe then
                                return
                            end
                        end
                    end)
                    while hp > 0 and autofarm do
                        if autofarm then
                            oldhp = target.Settings.HP.Value
                            if hp == oldhp then
                                failsafe += 1
                            end
                            hp = target.Settings.HP.Value
                            if hp > 0 then
                                wait(0.5)
                            end
                        end
                    end
                    killfailsafe = true
                    return
                end
            end)
        end
    end
end)










local Tab2 = Window:MakeTab({
	Name = "Auto open",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})


local Section2a = Tab2:AddSection({
	Name = "Opener"
})



local eggs ={}

for _,v in pairs(game.Workspace["__WORKSPACE"].FightersPoint:GetChildren()) do
    table.insert(eggs,v.Name)
end

local eggtoopen = "iagree"
Section2a:AddDropdown({
	Name = "Star to open",
	Default = "",
	Options = eggs,
	Callback = function(Value)
		eggtoopen = Value
        getfighterinstars()
	end
})


local openegg
Section2a:AddToggle({
	Name = "Open Stars",
	Default = false,
	Callback = function(Value)
        openegg = Value
        if Value then
            spawn(function()
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Workspace["__WORKSPACE"].FightersPoint[eggtoopen].Star.Part.CFrame + Vector3.new(0, 4, 0)
            end)
        end
	end
})






spawn(function()
    while wait(0.5) do
        if openegg then
            local data = {
                [1]="BuyTier",
                [2]=game.Workspace["__WORKSPACE"].FightersPoint[eggtoopen],
                [3]="E",
                [4]={}
            }
            game.ReplicatedStorage.Remotes.Client:FireServer(data)
        end
    end
end)




local Section2b = Tab2:AddSection({
	Name = "Auto Delete"
})


local fighters = require(game:GetService("ReplicatedStorage").Modules.Fighters)
local fighterinstar
local dontkillthisone1
local dontedelete1 = Section2b:AddDropdown({
	Name = "Fighter to not autodelete",
	Default = "",
	Options = fighterinstar,
	Callback = function(Value)
		dontkillthisone1 = Value
	end
})

function getfighterinstars()
    for _,v in pairs(game:GetService("Workspace")["__WORKSPACE"].FightersPoint:GetChildren()) do
        if v.Name == eggtoopen then
            local lol = {}
            dontedelete1:Refresh({},true)
            for _,v1 in pairs(v.Fighters:GetChildren()) do
                table.insert(lol,v1.Name)
            end
            dontedelete1:Refresh(lol,false)
            break
        end
    end
    
end





local delestar1
Section2b:AddToggle({
	Name = "Delete 1 Stars",
	Default = false,
	Callback = function(Value)
        delestar1 = Value
	end
})


function deletestar(unit, star, pain)
    if unit ~= dontkillthisone1 then
        if fighters[unit].Stars == star then
            local data = {
                [1] = "EquipFighter",
                [2] = "Delete",
                [3] = {[pain] = true}
            }
            game.ReplicatedStorage.Remotes.Client:FireServer(data)
        end
    end
end




local delestar2
Section2b:AddToggle({
	Name = "Delete 2 Stars",
	Default = false,
	Callback = function(Value)
        delestar2 = Value
	end
})


local delestar3
Section2b:AddToggle({
	Name = "Delete 3 Stars",
	Default = false,
	Callback = function(Value)
        delestar3 = Value
	end
})


local delestar4
Section2b:AddToggle({
	Name = "Delete 4 Stars",
	Default = false,
	Callback = function(Value)
        delestar4 = Value
	end
})
local delestar5
Section2b:AddToggle({
	Name = "Delete 5 Stars",
	Default = false,
	Callback = function(Value)
        delestar5 = Value
	end
})


game.Players.LocalPlayer.PlayerGui.UI.CenterFrame.Backpack.Frame.ChildAdded:Connect(function(unit)
    if unit:IsA("ImageLabel") and unit.Frame.ViewportFrame:FindFirstChildWhichIsA("Model") then
        local unitname = unit.Frame.ViewportFrame:FindFirstChildWhichIsA("Model").Name
        if delestar1 then
            deletestar(unitname, 1, unit.name)
        end
        if delestar2 then
            deletestar(unitname, 2, unit.name)
        end
        if delestar3 then
            deletestar(unitname, 3, unit.name)
        end
        if delestar4 then
            deletestar(unitname, 4, unit.name)
        end
        if delestar5 then
            deletestar(unitname, 5, unit.name)
        end
    end
end)

pcall(function()
    game.Players.LocalPlayer.PlayerGui.UI.Client.Modules.AnimationSettings:Destroy()
    game.Players.LocalPlayer.PlayerGui.Animation:Destroy()
end)








local Tab3 = Window:MakeTab({
	Name = "Teleports",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})


local Section3a = Tab3:AddSection({
	Name = "Teleports"
})


for _,v in pairs(game:GetService("Workspace")["__WORKSPACE"].Areas:GetChildren()) do
    Section3a:AddButton({
        Name = v.Name,
        Callback = function()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.Point.CFrame
        end
    })
end

local services = require(game.Players.LocalPlayer.PlayerGui.UI.Client.Services)

local Tab4 = Window:MakeTab({
	Name = "Misc",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

local Section4a = Tab4:AddSection({
	Name = "Weird Shit"
})

local autogift
Section4a:AddToggle({
	Name = "Auto Gifts",
	Default = true,
	Callback = function(Value)
        autogift = Value
	end
})

Section4a:AddButton({
	Name = "Fuse Menu",
	Callback = function()
        services.CallEvent:Fire({"OpenFrame", "Fuse"})
  	end
})



spawn(function()
    while wait(10) do
        if autogift then
            for _,v in pairs(game.Players.LocalPlayer.PlayerGui.UI.CenterFrame.Gifts.Frame:GetChildren()) do
                if v:IsA("ImageLabel") and v.Frame.TextLabel.Text == "Claim" then
                    game.ReplicatedStorage.Remotes.Client:FireServer({[1] = "Gift",[2] = v.Name})                    
                end
            end
        end
        pcall(function()
            game.Players.LocalPlayer.PlayerGui.UI.Client.Modules.AnimationSettings:Destroy()
            game.Players.LocalPlayer.PlayerGui.Animation:Destroy()
        end)
    end
end)




local Section4b = Tab4:AddSection({
	Name = "Auto Buy"
})

local autoaura
local autorank
local autoweapon
Section4b:AddToggle({
	Name = "Auto Aura",
	Default = false,
	Callback = function(Value)
        autoaura = Value
	end
})
Section4b:AddToggle({
	Name = "Auto Rank",
	Default = false,
	Callback = function(Value)
        autorank = Value
	end
})
Section4b:AddToggle({
	Name = "Auto Weapon",
	Default = false,
	Callback = function(Value)
        autoweapon = Value
	end
})




local auras = require(game.ReplicatedStorage.Modules.Auras)
local ranks = require(game.ReplicatedStorage.Modules.Ranks)
local weapons = require(game.ReplicatedStorage.Modules.Weapons)



spawn(function()
    while wait(2) do
        local savepoint = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
        if services.PlayerData.Coins > auras[services.PlayerData.CurrentAura+1].CoinsCost and autoaura then
            game.ReplicatedStorage.Remotes.Client:FireServer({[1] = "Aura"})
            wait()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = savepoint
        end
        if services.PlayerData.Coins > ranks[services.PlayerData.CurrentRank+1].CoinsCost and services.PlayerData.Power > ranks[services.PlayerData.CurrentRank+1].PowerCost and autorank then
            game.ReplicatedStorage.Remotes.Client:FireServer({[1] = "RankUp"})
            wait()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = savepoint
        end
        if services.PlayerData.Power > weapons[services.PlayerData.CurrentWeapon[1]+1].PowerRequires and autoweapon then
            game.ReplicatedStorage.Remotes.Client:FireServer({[1] = "Weapon"})
            wait()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = savepoint
        end
    end
end)




local Section4c = Tab4:AddSection({
	Name = "Movement"
})

local walkspeed
Section4c:AddSlider({
	Name = "Walk Speed",
	Min = 0,
	Max = 500,
	Default = 20,
	Color = Color3.fromRGB(255,255,255),
	Increment = 1,
	ValueName = "",
	Callback = function(Value)
        walkspeed = Value
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
	end
})


function destroyrunning()
    for _,v in pairs(game.Workspace:GetDescendants()) do
        if v.Name == "Running" then
            v:Destroy()
        end
    end
end

destroyrunning()


spawn(function() -- sorry for dumb code but im honestly clueless because of the way the character model works
    while wait(1) do
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = walkspeed
        pcall(destroyrunning)
    end
end)



local Tab4 = Window:MakeTab({
	Name = "Credits",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = true
})

Tab4:AddLabel("GUIlib by shlexware, search up orionlib on google")
Tab4:AddLabel("Rest of the stuff by Đs#0013")


--i know my codes ass, dont harass me
