# agr
-- GUI Setup
local gui = Instance.new("ScreenGui", game.CoreGui)
local btn = Instance.new("TextButton", gui)
btn.Size = UDim2.new(0, 160, 0, 40)
btn.Position = UDim2.new(0, 10, 0, 10)
btn.Text = "Auto Coleta: OFF"
btn.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
btn.TextColor3 = Color3.new(1, 1, 1)
btn.Font = Enum.Font.SourceSansBold
btn.TextSize = 20

-- Variáveis
local ativo = false
local lp = game.Players.LocalPlayer
local frutas = {"Gomu", "Mera", "Suna", "Yami", "Hie", "Tori", "Magu", "Pika"} -- nomes reais das frutas
local rs = game:GetService("ReplicatedStorage")

-- Função para teleportar até a fruta
local function teleportar(pos)
    local hrp = lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        hrp.CFrame = CFrame.new(pos + Vector3.new(0, 5, 0)) -- sobe um pouco pra evitar colisão
    end
end

-- Função para coletar fruta
local function coletar(fruta)
    if rs:FindFirstChild("CollectFruit") then
        rs.CollectFruit:FireServer(fruta)
    else
        fruta:Destroy()
    end
end

-- Loop principal sem limite de distância
task.spawn(function()
    while true do
        task.wait(2)
        if not ativo then continue end
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") and table.find(frutas, obj.Name) then
                teleportar(obj.Position)
                task.wait(1)
                coletar(obj)
            end
        end
    end
end)

-- Botão de ativação
btn.MouseButton1Click:Connect(function()
    ativo = not ativo
    btn.Text = ativo and "Auto Coleta: ON" or "Auto Coleta: OFF"
    btn.BackgroundColor3 = ativo and Color3.fromRGB(80, 255, 80) or Color3.fromRGB(255, 80, 80)
end)
