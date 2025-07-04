local Plrs = game:GetService("Players")
local Cam = workspace.CurrentCamera
local LP = Plrs.LocalPlayer
local RS = game:GetService("RunService")

function marcar(alvo)
    if alvo == LP then return end

    local t = Drawing.new("Text")
    t.Size = 13
    t.Center = true
    t.Outline = true
    t.Color = Color3.fromRGB(255, 0, 0)
    t.Text = "?"

    RS.RenderStepped:Connect(function()
        pcall(function()
            if alvo.Character and alvo.Character:FindFirstChild("Head") and alvo.Character:FindFirstChild("Humanoid") then
                local head = alvo.Character.Head
                if alvo.Character.Humanoid.Health > 0 then
                    local dist = (LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") and (head.Position - LP.Character.HumanoidRootPart.Position).Magnitude) or 999
                    if dist < 150 then
                        local pos, vis = Cam:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
                        if vis then
                            t.Position = Vector2.new(pos.X, pos.Y)
                            t.Visible = true
                            return
                        end
                    end
                end
            end
            t.Visible = false
        end)
    end)
end

-- Criar marcador para todos
for _, p in pairs(Plrs:GetPlayers()) do
    marcar(p)
end

Plrs.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function()
        wait(1)
        marcar(p)
    end)
end)
