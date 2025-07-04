local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")

local espAtivo = true
local marcadores = {}

-- Criar botão na tela
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.ResetOnSpawn = false

local Botao = Instance.new("TextButton", ScreenGui)
Botao.Size = UDim2.new(0, 100, 0, 40)
Botao.Position = UDim2.new(0, 20, 0, 60)
Botao.Text = "ESP ON"
Botao.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
Botao.TextColor3 = Color3.new(1, 1, 1)
Botao.TextScaled = true
Botao.BorderSizePixel = 2

Botao.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    Botao.Text = espAtivo and "ESP ON" or "ESP OFF"
    Botao.BackgroundColor3 = espAtivo and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)

    for _, info in pairs(marcadores) do
        if info.Text then
            info.Text.Visible = espAtivo
        end
    end
end)

-- Cor do papel
local function corPapel(player)
    if player.Character then
        if player.Character:FindFirstChild("Knife") then
            return Color3.fromRGB(255, 0, 0) -- assassino
        elseif player.Backpack:FindFirstChild("Gun") or player.Character:FindFirstChild("Gun") then
            return Color3.fromRGB(0, 0, 255) -- xerife
        end
    end
    return Color3.fromRGB(255, 255, 0) -- inocente
end

-- Criar marcador
local function criarESP(player)
    if player == LocalPlayer then return end
    if marcadores[player] then return end

    local texto = Drawing.new("Text")
    texto.Size = 14
    texto.Center = true
    texto.Outline = true
    texto.Text = "?"
    texto.Visible = espAtivo

    marcadores[player] = {Text = texto}

    RunService.RenderStepped:Connect(function()
        pcall(function()
            if not player.Character or not player.Character:FindFirstChild("Head") then
                texto.Visible = false
                return
            end

            if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                texto.Visible = false
                return
            end

            local head = player.Character.Head
            local dist = (head.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude

            if dist > 150 or not espAtivo then
                texto.Visible = false
                return
            end

            local pos, vis = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
            if vis then
                texto.Position = Vector2.new(pos.X, pos.Y)
                texto.Color = corPapel(player)
                texto.Visible = true
            else
                texto.Visible = false
            end
        end)
    end)
end

-- Ativar para todos
for _, p in pairs(Players:GetPlayers()) do
    criarESP(p)
end

Players.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function()
        wait(1)
        criarESP(p)
    end)
end)
