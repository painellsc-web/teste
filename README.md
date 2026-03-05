-- BiqueiraMenu REVISED (Red Border, Clean UI, All Functions + FOV Circle + Skeleton ESP + View Player)
-- Developer: BalaTensa!HS Pecinha33

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local LP = Players.LocalPlayer
local Cam = workspace.CurrentCamera

-- Key System Configuration
local ValidKeys = {"BIQUEIRA-JKFNHCD", "BIQUEIRA-Mndgf", "PECINHA-33-TOP", "admin123", "1"}
_G.LoggedIn = false

-- Configuration
local Config = {
    MenuKey = Enum.KeyCode.Insert,
    Fly = {On = false, Key = Enum.KeyCode.F, Speed = 50},
    NoClip = {On = false, Key = Enum.KeyCode.N},
    Invisible = {On = false, Key = Enum.KeyCode.V},
    ClickTP = false,
    AntiAFK = false,
    SpeedEnabled = false,
    Speed = 16,
    Jump = 50,
    SpeedKey = Enum.KeyCode.LeftShift,
    Aimbot = {On = false, Key = Enum.KeyCode.E, FOV = 100, Smooth = 5, NoRecoil = false, Hitbox = {On = false, Size = 5}, ShowFOV = true},
    ESP = {Box = false, Name = false, Dist = false, DistMax = 500, Line = false, HP = false, Skeleton = false, Color = Color3.fromRGB(255, 0, 0)},
    Exploit = {God = false, Fling = false}
}

local Flying = false
local FlySpeed = 50
local BodyVel, BodyGyro
local ESPs = {}
local Skeletons = {}
local TargetPlayer = nil
local ViewingPlayer = nil

-- FOV Circle Drawing
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.NumSides = 60
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.Color = Color3.fromRGB(255, 0, 0)
FOVCircle.Visible = false

-- Particle System Function
local function CreateParticles(parent)
    local pf = Instance.new("Frame", parent)
    pf.Size = UDim2.new(1, 0, 1, 0); pf.BackgroundTransparency = 1; pf.ZIndex = parent.ZIndex - 1; pf.ClipsDescendants = true
    spawn(function()
        while parent and parent.Parent do
            local p = Instance.new("Frame", pf)
            p.Size = UDim2.new(0, 2, 0, 2); p.BackgroundColor3 = Color3.fromRGB(255, 0, 0); p.BorderSizePixel = 0; p.Position = UDim2.new(math.random(), 0, 1.1, 0)
            local s = math.random(2, 5); local d = (math.random() - 0.5) * 0.2
            spawn(function()
                for i = 1, 150 do if not p or not p.Parent then break end p.Position = p.Position + UDim2.new(d, 0, -0.01 * s, 0); p.BackgroundTransparency = i / 150; RS.Heartbeat:Wait() end
                p:Destroy()
            end)
            wait(0.2)
        end
    end)
end

local Gui = Instance.new("ScreenGui")
Gui.Name = "BiqueiraMenu"
Gui.ResetOnSpawn = false
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Global
Gui.Parent = LP:WaitForChild("PlayerGui")

-- LOGIN GUI
local LoginFrame = Instance.new("Frame", Gui)
LoginFrame.Size = UDim2.new(0, 300, 0, 220); LoginFrame.Position = UDim2.new(0.5, -150, 0.5, -110); LoginFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15); LoginFrame.BorderSizePixel = 0; LoginFrame.ZIndex = 100; Instance.new("UICorner", LoginFrame)
local LS = Instance.new("UIStroke", LoginFrame); LS.Color = Color3.fromRGB(255, 0, 0); LS.Thickness = 2
CreateParticles(LoginFrame)

local LTitle = Instance.new("TextLabel", LoginFrame); LTitle.Size = UDim2.new(1, 0, 0, 50); LTitle.BackgroundTransparency = 1; LTitle.Text = "BIQUEIRA LOGIN"; LTitle.TextColor3 = Color3.fromRGB(255, 255, 255); LTitle.TextSize = 18; LTitle.Font = Enum.Font.GothamBold; LTitle.ZIndex = 101
local KeyInput = Instance.new("TextBox", LoginFrame); KeyInput.Size = UDim2.new(0, 240, 0, 40); KeyInput.Position = UDim2.new(0.5, -120, 0, 70); KeyInput.BackgroundColor3 = Color3.fromRGB(25, 25, 25); KeyInput.Text = ""; KeyInput.PlaceholderText = "Enter Key"; KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255); KeyInput.Font = Enum.Font.Gotham; KeyInput.TextSize = 14; KeyInput.ZIndex = 101; Instance.new("UICorner", KeyInput)
local LoginBtn = Instance.new("TextButton", LoginFrame); LoginBtn.Size = UDim2.new(0, 240, 0, 40); LoginBtn.Position = UDim2.new(0.5, -120, 0, 130); LoginBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0); LoginBtn.Text = "LOGIN"; LoginBtn.TextColor3 = Color3.fromRGB(255, 255, 255); LoginBtn.Font = Enum.Font.GothamBold; LoginBtn.TextSize = 16; LoginBtn.ZIndex = 101; Instance.new("UICorner", LoginBtn)
local Status = Instance.new("TextLabel", LoginFrame); Status.Size = UDim2.new(1, 0, 0, 30); Status.Position = UDim2.new(0, 0, 1, -30); Status.BackgroundTransparency = 1; Status.Text = "Status: Waiting"; Status.TextColor3 = Color3.fromRGB(150, 150, 150); Status.TextSize = 12; Status.Font = Enum.Font.Gotham; Status.ZIndex = 101

-- MAIN MENU
local Main = Instance.new("Frame", Gui)
Main.Size = UDim2.new(0, 550, 0, 400); Main.Position = UDim2.new(0.5, -275, 0.5, -200); Main.BackgroundColor3 = Color3.fromRGB(5, 5, 5); Main.BorderSizePixel = 0; Main.Active = true; Main.Draggable = true; Main.Visible = false; Main.ZIndex = 10; Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 12)
local MS = Instance.new("UIStroke", Main); MS.Color = Color3.fromRGB(255, 0, 0); MS.Thickness = 3
CreateParticles(Main)

local Top = Instance.new("Frame", Main); Top.Size = UDim2.new(1, 0, 0, 40); Top.BackgroundColor3 = Color3.fromRGB(10, 10, 10); Top.BorderSizePixel = 0; Top.ZIndex = 11; Instance.new("UICorner", Top)
local Title = Instance.new("TextLabel", Top); Title.Size = UDim2.new(1, -40, 1, 0); Title.Position = UDim2.new(0, 15, 0, 0); Title.BackgroundTransparency = 1; Title.Text = "BIQUEIRA MENU"; Title.TextColor3 = Color3.fromRGB(255, 255, 255); Title.TextSize = 16; Title.Font = Enum.Font.GothamBold; Title.TextXAlignment = Enum.TextXAlignment.Left; Title.ZIndex = 12
local Close = Instance.new("TextButton", Top); Close.Size = UDim2.new(0, 25, 0, 25); Close.Position = UDim2.new(1, -30, 0, 7); Close.BackgroundTransparency = 1; Close.Text = "X"; Close.TextColor3 = Color3.fromRGB(255, 255, 255); Close.TextSize = 16; Close.Font = Enum.Font.GothamBold; Close.ZIndex = 12; Close.MouseButton1Click:Connect(function() Main.Visible = false end)

local TabBar = Instance.new("Frame", Main); TabBar.Size = UDim2.new(1, -20, 0, 35); TabBar.Position = UDim2.new(0, 10, 0, 50); TabBar.BackgroundTransparency = 1; TabBar.ZIndex = 11
local Content = Instance.new("Frame", Main); Content.Size = UDim2.new(1, -20, 1, -100); Content.Position = UDim2.new(0, 10, 0, 90); Content.BackgroundColor3 = Color3.fromRGB(10, 10, 10); Content.BorderSizePixel = 0; Content.ZIndex = 11; Instance.new("UICorner", Content)

local Tabs = {}
local function MakeTab(name, pos)
    local btn = Instance.new("TextButton", TabBar)
    btn.Size = UDim2.new(0, 100, 1, 0); btn.Position = UDim2.new(0, pos, 0, 0); btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20); btn.Text = name; btn.TextColor3 = Color3.fromRGB(150, 150, 150); btn.TextSize = 11; btn.Font = Enum.Font.GothamBold; btn.ZIndex = 12; Instance.new("UICorner", btn)
    local page = Instance.new("ScrollingFrame", Content)
    page.Size = UDim2.new(1, -20, 1, -20); page.Position = UDim2.new(0, 10, 0, 10); page.BackgroundTransparency = 1; page.BorderSizePixel = 0; page.ScrollBarThickness = 2; page.ScrollBarImageColor3 = Color3.fromRGB(255, 0, 0); page.Visible = false; page.ZIndex = 12; page.CanvasSize = UDim2.new(0, 0, 0, 0)
    local layout = Instance.new("UIListLayout", page); layout.Padding = UDim.new(0, 8)
    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function() page.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10) end)
    Tabs[name] = {Btn = btn, Page = page}
    btn.MouseButton1Click:Connect(function()
        for _, t in pairs(Tabs) do t.Btn.TextColor3 = Color3.fromRGB(150, 150, 150); t.Btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20); t.Page.Visible = false end
        btn.TextColor3 = Color3.fromRGB(255, 255, 255); btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30); page.Visible = true
    end)
    return page
end

local Tab1 = MakeTab("MOVEMENT", 0)
local Tab2 = MakeTab("AIMBOT", 105)
local Tab3 = MakeTab("ESP", 210)
local Tab4 = MakeTab("EXPLOIT", 315)
local Tab5 = MakeTab("PLAYER", 420)

-- UI HELPERS
local function Toggle(parent, txt, def, cb)
    local f = Instance.new("Frame", parent); f.Size = UDim2.new(1, 0, 0, 35); f.BackgroundTransparency = 1; f.ZIndex = 13
    local l = Instance.new("TextLabel", f); l.Size = UDim2.new(1, -60, 1, 0); l.BackgroundTransparency = 1; l.Text = txt; l.TextColor3 = Color3.fromRGB(200, 200, 200); l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left; l.ZIndex = 14
    local b = Instance.new("TextButton", f); b.Size = UDim2.new(0, 40, 0, 20); b.Position = UDim2.new(1, -45, 0.5, -10); b.BackgroundColor3 = Color3.fromRGB(30, 30, 30); b.Text = def and "ON" or "OFF"; b.TextColor3 = def and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(100, 100, 100); b.TextSize = 11; b.Font = Enum.Font.Gotham; b.ZIndex = 14; Instance.new("UICorner", b)
    local on = def; b.MouseButton1Click:Connect(function() on = not on; b.TextColor3 = on and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(100, 100, 100); b.Text = on and "ON" or "OFF"; cb(on) end)
end

local function Slider(parent, txt, min, max, def, cb)
    local f = Instance.new("Frame", parent); f.Size = UDim2.new(1, 0, 0, 45); f.BackgroundTransparency = 1; f.ZIndex = 13
    local l = Instance.new("TextLabel", f); l.Size = UDim2.new(1, 0, 0, 20); l.BackgroundTransparency = 1; l.Text = txt .. ": " .. def; l.TextColor3 = Color3.fromRGB(200, 200, 200); l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left; l.ZIndex = 14
    local bar = Instance.new("Frame", f); bar.Size = UDim2.new(1, 0, 0, 3); bar.Position = UDim2.new(0, 0, 1, -8); bar.BackgroundColor3 = Color3.fromRGB(30, 30, 30); bar.ZIndex = 14
    local fill = Instance.new("Frame", bar); fill.Size = UDim2.new((def - min) / (max - min), 0, 1, 0); fill.BackgroundColor3 = Color3.fromRGB(255, 0, 0); fill.ZIndex = 15
    local drag = false; bar.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then drag = true end end)
    UIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then drag = false end end)
    UIS.InputChanged:Connect(function(i) if drag and i.UserInputType == Enum.UserInputType.MouseMovement then local p = math.clamp((i.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1); local val = math.floor(min + (max - min) * p); fill.Size = UDim2.new(p, 0, 1, 0); l.Text = txt .. ": " .. val; cb(val) end end)
end

local function Bind(parent, txt, def, cb)
    local f = Instance.new("Frame", parent); f.Size = UDim2.new(1, 0, 0, 35); f.BackgroundTransparency = 1; f.ZIndex = 13
    local l = Instance.new("TextLabel", f); l.Size = UDim2.new(1, -60, 1, 0); l.BackgroundTransparency = 1; l.Text = txt; l.TextColor3 = Color3.fromRGB(200, 200, 200); l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left; l.ZIndex = 14
    local b = Instance.new("TextButton", f); b.Size = UDim2.new(0, 60, 0, 20); b.Position = UDim2.new(1, -65, 0.5, -10); b.BackgroundColor3 = Color3.fromRGB(30, 30, 30); b.Text = def.Name; b.TextColor3 = Color3.fromRGB(255, 255, 255); b.TextSize = 11; b.Font = Enum.Font.Gotham; b.ZIndex = 14; Instance.new("UICorner", b)
    local binding = false; b.MouseButton1Click:Connect(function() binding = true; b.Text = "..." end)
    UIS.InputBegan:Connect(function(i) if binding and i.UserInputType == Enum.UserInputType.Keyboard then binding = false; b.Text = i.KeyCode.Name; cb(i.KeyCode) end end)
end

local function Button(parent, txt, cb)
    local b = Instance.new("TextButton", parent); b.Size = UDim2.new(1, 0, 0, 35); b.BackgroundColor3 = Color3.fromRGB(30, 30, 30); b.Text = txt; b.TextColor3 = Color3.fromRGB(255, 255, 255); b.TextSize = 13; b.Font = Enum.Font.GothamBold; b.ZIndex = 13; Instance.new("UICorner", b); b.MouseButton1Click:Connect(cb)
end

local function TextBox(parent, txt, placeholder, cb)
    local f = Instance.new("Frame", parent); f.Size = UDim2.new(1, 0, 0, 55); f.BackgroundTransparency = 1; f.ZIndex = 13
    local l = Instance.new("TextLabel", f); l.Size = UDim2.new(1, 0, 0, 20); l.BackgroundTransparency = 1; l.Text = txt; l.TextColor3 = Color3.fromRGB(200, 200, 200); l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left; l.ZIndex = 14
    local t = Instance.new("TextBox", f); t.Size = UDim2.new(1, 0, 0, 30); t.Position = UDim2.new(0, 0, 0, 25); t.BackgroundColor3 = Color3.fromRGB(30, 30, 30); t.Text = ""; t.PlaceholderText = placeholder; t.TextColor3 = Color3.fromRGB(255, 255, 255); t.Font = Enum.Font.Gotham; t.TextSize = 12; t.ZIndex = 14; Instance.new("UICorner", t)
    t.FocusLost:Connect(function(enter) if enter then cb(t.Text) end end)
end

-- TABS SETUP
Toggle(Tab1, "Fly", Config.Fly.On, function(v) Config.Fly.On = v end)
Slider(Tab1, "Fly Speed", 10, 200, Config.Fly.Speed, function(v) FlySpeed = v end)
Bind(Tab1, "Fly Key", Config.Fly.Key, function(v) Config.Fly.Key = v end)
Toggle(Tab1, "NoClip", Config.NoClip.On, function(v) Config.NoClip.On = v end)
Bind(Tab1, "NoClip Key", Config.NoClip.Key, function(v) Config.NoClip.Key = v end)
Toggle(Tab1, "Speed Enabled", Config.SpeedEnabled, function(v) Config.SpeedEnabled = v end)
Slider(Tab1, "Speed", 16, 200, Config.Speed, function(v) Config.Speed = v end)
Bind(Tab1, "Speed Key", Config.SpeedKey, function(v) Config.SpeedKey = v end)
Slider(Tab1, "Jump Power", 50, 500, Config.Jump, function(v) Config.Jump = v end)

Toggle(Tab2, "Aimbot", Config.Aimbot.On, function(v) Config.Aimbot.On = v end)
Bind(Tab2, "Aimbot Key", Config.Aimbot.Key, function(v) Config.Aimbot.Key = v end)
Slider(Tab2, "FOV Size", 10, 800, Config.Aimbot.FOV, function(v) Config.Aimbot.FOV = v end)
Toggle(Tab2, "Show FOV Circle", Config.Aimbot.ShowFOV, function(v) Config.Aimbot.ShowFOV = v end)
Slider(Tab2, "Smoothness", 1, 20, Config.Aimbot.Smooth, function(v) Config.Aimbot.Smooth = v end)
Toggle(Tab2, "No Recoil", Config.Aimbot.NoRecoil, function(v) Config.Aimbot.NoRecoil = v end)
Toggle(Tab2, "Hitbox Expander", Config.Aimbot.Hitbox.On, function(v) Config.Aimbot.Hitbox.On = v end)
Slider(Tab2, "Hitbox Size", 2, 50, Config.Aimbot.Hitbox.Size, function(v) Config.Aimbot.Hitbox.Size = v end)

Toggle(Tab3, "Box ESP", Config.ESP.Box, function(v) Config.ESP.Box = v end)
Toggle(Tab3, "Name ESP", Config.ESP.Name, function(v) Config.ESP.Name = v end)
Toggle(Tab3, "Skeleton ESP", Config.ESP.Skeleton, function(v) Config.ESP.Skeleton = v end)
Toggle(Tab3, "Tracers", Config.ESP.Line, function(v) Config.ESP.Line = v end)
Toggle(Tab3, "Distance ESP", Config.ESP.Dist, function(v) Config.ESP.Dist = v end)
Slider(Tab3, "Max Distance", 100, 5000, Config.ESP.DistMax, function(v) Config.ESP.DistMax = v end)

Toggle(Tab4, "God Mode", Config.Exploit.God, function(v) Config.Exploit.God = v end)
Toggle(Tab4, "Fling", Config.Exploit.Fling, function(v) Config.Exploit.Fling = v end)
Toggle(Tab4, "Anti AFK", Config.AntiAFK, function(v) Config.AntiAFK = v end)

TextBox(Tab5, "Search Player", "Username", function(txt) for _, p in pairs(Players:GetPlayers()) do if p.Name:lower():find(txt:lower()) then TargetPlayer = p; break end end end)
Button(Tab5, "View Player", function()
    if TargetPlayer and TargetPlayer.Character then
        ViewingPlayer = TargetPlayer
        Cam.CameraSubject = TargetPlayer.Character.Humanoid
    end
end)
Button(Tab5, "Stop Viewing", function()
    ViewingPlayer = nil
    Cam.CameraSubject = LP.Character.Humanoid
end)
Button(Tab5, "Copy Skin", function()
    if TargetPlayer and TargetPlayer.Character and LP.Character then
        for _, v in pairs(LP.Character:GetChildren()) do if v:IsA("Shirt") or v:IsA("Pants") then v:Destroy() end end
        for _, v in pairs(TargetPlayer.Character:GetChildren()) do if v:IsA("Shirt") or v:IsA("Pants") then v:Clone().Parent = LP.Character end end
    end
end)
Button(Tab5, "Teleport to Player", function() if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then LP.Character.HumanoidRootPart.CFrame = TargetPlayer.Character.HumanoidRootPart.CFrame end end)

-- ESP SYSTEM (CORRECTED)
local function CreateESP(p)
    if ESPs[p] then return end
    ESPs[p] = {
        Box = Drawing.new("Square"),
        Name = Drawing.new("Text"),
        Dist = Drawing.new("Text"),
        Line = Drawing.new("Line")
    }
    local e = ESPs[p]
    e.Box.Thickness = 2; e.Box.Filled = false; e.Box.Color = Config.ESP.Color; e.Box.Visible = false
    e.Name.Size = 13; e.Name.Center = true; e.Name.Outline = true; e.Name.Color = Config.ESP.Color; e.Name.Visible = false
    e.Dist.Size = 12; e.Dist.Center = true; e.Dist.Outline = true; e.Dist.Color = Config.ESP.Color; e.Dist.Visible = false
    e.Line.Thickness = 1; e.Line.Color = Config.ESP.Color; e.Line.Visible = false
    
    Skeletons[p] = {H2T = Drawing.new("Line"), T2LA = Drawing.new("Line"), T2RA = Drawing.new("Line"), T2LL = Drawing.new("Line"), T2RL = Drawing.new("Line")}
    for _, l in pairs(Skeletons[p]) do l.Thickness = 1; l.Color = Config.ESP.Color; l.Visible = false end
end

local function UpdateESP()
    for p, e in pairs(ESPs) do
        if p and p.Parent and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = p.Character.HumanoidRootPart
            local pos, onScreen = Cam:WorldToViewportPoint(hrp.Position)
            local dist = (Cam.CFrame.Position - hrp.Position).Magnitude
            
            if onScreen and dist <= Config.ESP.DistMax then
                local w = 2000 / dist; local h = 3000 / dist
                e.Box.Visible = Config.ESP.Box; e.Box.Size = Vector2.new(w, h); e.Box.Position = Vector2.new(pos.X - w/2, pos.Y - h/2)
                e.Name.Visible = Config.ESP.Name; e.Name.Text = p.Name; e.Name.Position = Vector2.new(pos.X, pos.Y - h/2 - 15)
                e.Dist.Visible = Config.ESP.Dist; e.Dist.Text = math.floor(dist) .. "m"; e.Dist.Position = Vector2.new(pos.X, pos.Y + h/2 + 5)
                e.Line.Visible = Config.ESP.Line; e.Line.From = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y); e.Line.To = Vector2.new(pos.X, pos.Y + h/2)
                
                -- Skeleton
                local sk = Skeletons[p]
                if Config.ESP.Skeleton then
                    local function G(n) local pt = p.Character:FindFirstChild(n); if pt then local ps, vs = Cam:WorldToViewportPoint(pt.Position); return Vector2.new(ps.X, ps.Y), vs end return nil, false end
                    local hP, hV = G("Head"); local tP, tV = G("UpperTorso") or G("Torso"); local laP, laV = G("LeftUpperArm") or G("Left Arm"); local raP, raV = G("RightUpperArm") or G("Right Arm"); local llP, llV = G("LeftUpperLeg") or G("Left Leg"); local rlP, rlV = G("RightUpperLeg") or G("Right Leg")
                    local function S(l, p1, p2, v1, v2) if p1 and p2 and v1 and v2 then l.From = p1; l.To = p2; l.Visible = true else l.Visible = false end end
                    S(sk.H2T, hP, tP, hV, tV); S(sk.T2LA, tP, laP, tV, laV); S(sk.T2RA, tP, raP, tV, raV); S(sk.T2LL, tP, llP, tV, llV); S(sk.T2RL, tP, rlP, tV, rlV)
                else for _, l in pairs(sk) do l.Visible = false end end
            else for _, v in pairs(e) do v.Visible = false end for _, l in pairs(Skeletons[p]) do l.Visible = false end end
        else for _, v in pairs(e) do v.Visible = false end if Skeletons[p] then for _, l in pairs(Skeletons[p]) do l.Visible = false end end end
    end
end

-- FLY SYSTEM
local function StartFly()
    if Flying then return end Flying = true
    local hrp = LP.Character:FindFirstChild("HumanoidRootPart")
    BodyVel = Instance.new("BodyVelocity", hrp); BodyVel.Velocity = Vector3.new(0, 0, 0); BodyVel.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    BodyGyro = Instance.new("BodyGyro", hrp); BodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9); BodyGyro.P = 9e4; BodyGyro.CFrame = hrp.CFrame
    spawn(function()
        while Flying do
            local cam = Cam.CFrame; local dir = Vector3.new(0, 0, 0)
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir = dir + (cam.LookVector * FlySpeed) end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir = dir - (cam.LookVector * FlySpeed) end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir = dir - (cam.RightVector * FlySpeed) end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir = dir + (cam.RightVector * FlySpeed) end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then dir = dir + (Vector3.new(0, 1, 0) * FlySpeed) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then dir = dir - (Vector3.new(0, 1, 0) * FlySpeed) end
            BodyVel.Velocity = dir; BodyGyro.CFrame = cam; RS.RenderStepped:Wait()
        end
    end)
end
local function StopFly() Flying = false; if BodyVel then BodyVel:Destroy() end if BodyGyro then BodyGyro:Destroy() end end

-- MAIN LOOPS
RS.Heartbeat:Connect(function()
    if not _G.LoggedIn then return end
    if LP.Character and LP.Character:FindFirstChild("Humanoid") then
        local h = LP.Character.Humanoid
        if Config.SpeedEnabled and UIS:IsKeyDown(Config.SpeedKey) then h.WalkSpeed = Config.Speed else h.WalkSpeed = 16 end
        h.JumpPower = Config.Jump
        if Config.Exploit.God and h.Health < h.MaxHealth then h.Health = h.MaxHealth end
    end
    if Config.Aimbot.Hitbox.On then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LP and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                p.Character.HumanoidRootPart.Size = Vector3.new(Config.Aimbot.Hitbox.Size, Config.Aimbot.Hitbox.Size, Config.Aimbot.Hitbox.Size)
                p.Character.HumanoidRootPart.Transparency = 0.8
            end
        end
    end
end)

RS.RenderStepped:Connect(function()
    if not _G.LoggedIn then return end
    UpdateESP()
    FOVCircle.Position = UIS:GetMouseLocation(); FOVCircle.Radius = Config.Aimbot.FOV; FOVCircle.Visible = Config.Aimbot.ShowFOV and Config.Aimbot.On
    if Config.Aimbot.On and UIS:IsKeyDown(Config.Aimbot.Key) then
        local t, d = nil, Config.Aimbot.FOV
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LP and p.Character and p.Character:FindFirstChild("Head") then
                local s, o = Cam:WorldToViewportPoint(p.Character.Head.Position)
                if o then local dist = (Vector2.new(s.X, s.Y) - UIS:GetMouseLocation()).Magnitude; if dist < d then t, d = p, dist end end
            end
        end
        if t and t.Character and t.Character:FindFirstChild("Head") then
            local tp = Cam:WorldToViewportPoint(t.Character.Head.Position); local m = UIS:GetMouseLocation()
            mousemoverel((tp.X - m.X) / Config.Aimbot.Smooth, (tp.Y - m.Y) / Config.Aimbot.Smooth)
        end
    end
end)

-- LOGIN ACTION
LoginBtn.MouseButton1Click:Connect(function()
    for _, k in pairs(ValidKeys) do
        if KeyInput.Text == k then
            _G.LoggedIn = true; LoginFrame:Destroy(); Main.Visible = true
            Tabs.MOVEMENT.Btn.BackgroundColor3 = Color3.fromRGB(255, 0, 0); Tabs.MOVEMENT.Btn.TextColor3 = Color3.fromRGB(255, 255, 255); Tabs.MOVEMENT.Page.Visible = true
            return
        end
    end
    Status.Text = "Invalid Key!"; Status.TextColor3 = Color3.fromRGB(255, 0, 0)
end)

UIS.InputBegan:Connect(function(i, g)
    if not g and _G.LoggedIn then
        if i.KeyCode == Config.MenuKey then Main.Visible = not Main.Visible end
        if i.KeyCode == Config.Fly.Key and Config.Fly.On then if Flying then StopFly() else StartFly() end end
    end
end)

for _, p in pairs(Players:GetPlayers()) do if p ~= LP then CreateESP(p) end end
Players.PlayerAdded:Connect(function(p) if p ~= LP then CreateESP(p) end end)
print("BiqueiraMenu REVISED LOADED")
