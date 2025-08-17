local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local hologramaAtivo = false

-- Função para aplicar holograma (outline) nos personagens
local function aplicarHolograma()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            for _, part in pairs(player.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    if not part:FindFirstChild("Holograma") then
                        local outline = Instance.new("SelectionBox")
                        outline.Name = "Holograma"
                        outline.Adornee = part
                        outline.Color3 = Color3.fromHSV(math.random(), 1, 1) -- Cor aleatória
                        outline.Parent = part
                    end
                end
            end
        end
    end
end

local function removerHolograma()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character then
            for _, part in pairs(player.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    local outline = part:FindFirstChild("Holograma")
                    if outline then
                        outline:Destroy()
                    end
                end
            end
        end
    end
end

-- Função para olhar pros outros
local function olharParaPersonagens()
    RunService.RenderStepped:Connect(function()
        if hologramaAtivo and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local meuHRP = LocalPlayer.Character.HumanoidRootPart
            local playersProximos = {}
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    table.insert(playersProximos, player.Character.HumanoidRootPart.Position)
                end
            end
            if #playersProximos > 0 then
                -- Olha para o primeiro personagem encontrado
                local alvoPos = playersProximos[1]
                local lookVector = (alvoPos - meuHRP.Position).Unit
                local newCFrame = CFrame.new(meuHRP.Position, alvoPos)
                meuHRP.CFrame = CFrame.new(meuHRP.Position) * CFrame.Angles(0, math.atan2(lookVector.X, lookVector.Z), 0)
            end
        end
    end)
end

UIS.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.KeyCode == Enum.KeyCode.H then
        hologramaAtivo = not hologramaAtivo
        if hologramaAtivo then
            aplicarHolograma()
            olharParaPersonagens()
        else
            removerHolograma()
        end
    end
end)
