-- Proteção Anti-Ban, Anti-Kick, Anti-Exploit (avançado)
local mt = getrawmetatable(game)
setreadonly(mt, false)
local oldNamecall = mt.__namecall
mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    if tostring(self) == "Kick" or method == "Kick" then
        return warn("Tentativa de kick bloqueada.")
    end
    return oldNamecall(self, ...)
end)
setreadonly(mt, true)

-- GUI Rayfield
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
local Window = Rayfield:CreateWindow({
    Name = "MTD V1",
    LoadingTitle = "MTD V1",
    LoadingSubtitle = "Vyctor_21",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "MTDConfigs",
        FileName = "MTD_Settings"
    },
    KeySystem = false,
})

-- Aba Início
local AbaInicio = Window:CreateTab("Início", 4483362458)
AbaInicio:CreateParagraph({Title = "Bem-vindo!", Content = "MTD V1 by Vyctor_21"})
AbaInicio:CreateButton({
    Name = "Mensagem de Boas-Vindas",
    Callback = function()
        Rayfield:Notify({Title = "MTD V1", Content = "Seja bem-vindo!", Duration = 3})
    end,
})

-- Aba Auto Farm
local AbaAutoFarm = Window:CreateTab("Auto Farm", 4483362458)
getgenv().AutoFarm = false
AbaAutoFarm:CreateToggle({
    Name = "Auto Farm (placeholder)",
    CurrentValue = false,
    Callback = function(Value)
        getgenv().AutoFarm = Value
        Rayfield:Notify({Title = "Auto Farm", Content = Value and "Auto Farm ativado!" or "Desativado", Duration = 2})
    end,
})

-- Salvar/Teleportar local
local LocalSalvo = nil
AbaAutoFarm:CreateButton({
    Name = "Salvar Local Atual",
    Callback = function()
        local char = game.Players.LocalPlayer.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            LocalSalvo = char.HumanoidRootPart.Position
            Rayfield:Notify({Title = "Local Salvo", Content = "Posição salva com sucesso!", Duration = 2})
        end
    end,
})
AbaAutoFarm:CreateButton({
    Name = "Teleportar para Local Salvo",
    Callback = function()
        local char = game.Players.LocalPlayer.Character
        if char and char:FindFirstChild("HumanoidRootPart") and LocalSalvo then
            char:MoveTo(LocalSalvo)
            Rayfield:Notify({Title = "Teleporte", Content = "Teleportado para o local salvo!", Duration = 2})
        else
            Rayfield:Notify({Title = "Erro", Content = "Nenhum local salvo ou personagem não carregado!", Duration = 2})
        end
    end,
})

-- Aba Aimbot
local AbaAimbot = Window:CreateTab("Aimbot", 4483362458)
AbaAimbot:CreateButton({
    Name = "Ativar Aimbot (Mobile)",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Aimbot-fov-mobile-7607"))()
    end,
})

-- Aba Visual
local AbaVisual = Window:CreateTab("Visual", 4483362458)

-- ESP
getgenv().ESP_Ativo = false
AbaVisual:CreateButton({
    Name = "Ativar ESP + Itens",
    Callback = function()
        if getgenv().ESP_Ativo then return end
        getgenv().ESP_Ativo = true
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local RunService = game:GetService("RunService")

        local function ApplyESP(plr)
            if plr == LocalPlayer then return end
            local function AddHighlight(char)
                if not char:FindFirstChild("ESP_HIGHLIGHT") then
                    local h = Instance.new("Highlight", char)
                    h.Name = "ESP_HIGHLIGHT"
                    h.FillColor = Color3.fromRGB(255, 255, 0)
                    h.OutlineColor = Color3.fromRGB(255, 255, 255)
                    h.FillTransparency = 0.2
                    h.OutlineTransparency = 0
                    h.Adornee = char
                end
            end

            local function AddToolName()
                local bb = Instance.new("BillboardGui")
                bb.Name = "ESP_ITEM"
                bb.Size = UDim2.new(0, 150, 0, 25)
                bb.StudsOffset = Vector3.new(0, 3, 0)
                bb.AlwaysOnTop = true
                local txt = Instance.new("TextLabel", bb)
                txt.Size = UDim2.new(1, 0, 1, 0)
                txt.BackgroundTransparency = 1
                txt.TextColor3 = Color3.new(1, 1, 1)
                txt.TextStrokeTransparency = 0
                txt.TextScaled = true
                txt.Font = Enum.Font.SourceSansBold
                txt.Name = "TextLabel"
                return bb, txt
            end

            local gui, label = AddToolName()

            RunService.RenderStepped:Connect(function()
                if not getgenv().ESP_Ativo then return end
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and plr.Character:FindFirstChild("Head") then
                    AddHighlight(plr.Character)
                    local tool = plr.Character:FindFirstChildOfClass("Tool") or (plr:FindFirstChild("Backpack") and plr.Backpack:FindFirstChildOfClass("Tool"))
                    local name = tool and tool.Name or ""
                    if name ~= "" then
                        label.Text = "[" .. name .. "]"
                        if not plr.Character.Head:FindFirstChild("ESP_ITEM") then
                            local c = gui:Clone()
                            c.TextLabel.Text = "[" .. name .. "]"
                            c.Parent = plr.Character.Head
                        else
                            plr.Character.Head.ESP_ITEM.TextLabel.Text = "[" .. name .. "]"
                        end
                    elseif plr.Character.Head:FindFirstChild("ESP_ITEM") then
                        plr.Character.Head.ESP_ITEM.TextLabel.Text = ""
                    end
                end
            end)
        end

        for _, p in pairs(Players:GetPlayers()) do ApplyESP(p) end
        Players.PlayerAdded:Connect(function(p)
            p.CharacterAdded:Connect(function() wait(1) ApplyESP(p) end)
        end)
    end,
})

AbaVisual:CreateButton({
    Name = "Desativar ESP",
    Callback = function()
        getgenv().ESP_Ativo = false
        for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
            if plr.Character then
                local h = plr.Character:FindFirstChild("ESP_HIGHLIGHT")
                if h then h:Destroy() end
                if plr.Character:FindFirstChild("Head") then
                    local b = plr.Character.Head:FindFirstChild("ESP_ITEM")
                    if b then b:Destroy() end
                end
            end
        end
    end,
})

-- Tracers
getgenv().TracersAtivos = false
local TracerCon = nil
local Tracers = {}

AbaVisual:CreateButton({
    Name = "Ativar Tracers (Aliado/Inimigo)",
    Callback = function()
        if getgenv().TracersAtivos then return end
        getgenv().TracersAtivos = true
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local Camera = workspace.CurrentCamera

        local function CriarLinha(plr)
            if plr == LocalPlayer then return end
            local l = Drawing.new("Line")
            l.Thickness = 2
            l.Transparency = 1
            l.Visible = true
            Tracers[plr] = l
        end

        local function RemoverLinha(plr)
            if Tracers[plr] then
                Tracers[plr]:Remove()
                Tracers[plr] = nil
            end
        end

        for _, p in ipairs(Players:GetPlayers()) do CriarLinha(p) end
        Players.PlayerAdded:Connect(CriarLinha)
        Players.PlayerRemoving:Connect(RemoverLinha)

        TracerCon = game:GetService("RunService").RenderStepped:Connect(function()
            for plr, linha in pairs(Tracers) do
                if not getgenv().TracersAtivos then return end
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and plr.Team and LocalPlayer.Team then
                    local hrp = plr.Character.HumanoidRootPart
                    local pos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
                    if onScreen then
                        linha.Visible = true
                        linha.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                        linha.To = Vector2.new(pos.X, pos.Y)
                        linha.Color = (plr.Team == LocalPlayer.Team) and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
                    else
                        linha.Visible = false
                    end
                else
                    linha.Visible = false
                end
            end
        end)
    end,
})

AbaVisual:CreateButton({
    Name = "Desativar Tracers",
    Callback = function()
        getgenv().TracersAtivos = false
        if TracerCon then TracerCon:Disconnect() TracerCon = nil end
        for _, l in pairs(Tracers) do if l then l:Remove() end end
        Tracers = {}
    end,
})

-- No Clip (corrigido com respawn)
getgenv().NoClipAtivo = false
local NoClipCon = nil
local function AtivarNoClip()
    local plr = game:GetService("Players").LocalPlayer
    local function SetupNoClip(char)
        if NoClipCon then NoClipCon:Disconnect() end
        NoClipCon = game:GetService("RunService").Stepped:Connect(function()
            if getgenv().NoClipAtivo then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    end
    if plr.Character then
        SetupNoClip(plr.Character)
    end
    plr.CharacterAdded:Connect(function(char)
        char:WaitForChild("Humanoid")
        if getgenv().NoClipAtivo then
            SetupNoClip(char)
        end
    end)
end

AbaVisual:CreateToggle({
    Name = "Ativar No Clip",
    CurrentValue = false,
    Callback = function(state)
        getgenv().NoClipAtivo = state
        if state then
            Rayfield:Notify({Title = "No Clip", Content = "Ativado", Duration = 2})
            AtivarNoClip()
        else
            Rayfield:Notify({Title = "No Clip", Content = "Desativado", Duration = 2})
            if NoClipCon then NoClipCon:Disconnect() NoClipCon = nil end
            local char = game.Players.LocalPlayer.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = true end
                end
            end
        end
    end,
})

-- /revistar morto
AbaVisual:CreateButton({
    Name = "Mandar /revistar morto",
    Callback = function()
        game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer("/revistar morto", "All")
    end,
})

-- Garante carregamento
game:GetService("Players").LocalPlayer.CharacterAdded:Connect(function(character)
    character:WaitForChild("Humanoid")
end)
