-- Definir a função de controle para o jogador
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local mouse = player:GetMouse()

-- Criar a GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Criar o painel principal
local mainPanel = Instance.new("Frame")
mainPanel.Size = UDim2.new(0, 300, 0, 500)
mainPanel.Position = UDim2.new(0.5, -150, 0.5, -250)
mainPanel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainPanel.BorderSizePixel = 0
mainPanel.Visible = false  -- Começa invisível
mainPanel.Parent = screenGui

-- Adicionar título ao painel
local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(0, 300, 0, 40)
titleLabel.Position = UDim2.new(0, 0, 0, 0)
titleLabel.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
titleLabel.Text = "Painel de Funções"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 20
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Center
titleLabel.Parent = mainPanel

-- Adicionar botão para alternar o painel
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 100, 0, 50)
toggleButton.Position = UDim2.new(0.5, -50, 0, 460)
toggleButton.Text = "Mostrar"
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
toggleButton.TextSize = 18
toggleButton.Font = Enum.Font.Gotham
toggleButton.Parent = screenGui

-- Criar categorias no painel
local categoryFrame = Instance.new("Frame")
categoryFrame.Size = UDim2.new(1, 0, 1, -40)
categoryFrame.Position = UDim2.new(0, 0, 0, 40)
categoryFrame.BackgroundTransparency = 1
categoryFrame.Parent = mainPanel

-- Função para criar botões
local function createButton(text, position, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, 0, 0, 50)
    button.Position = position
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    button.TextSize = 18
    button.Font = Enum.Font.Gotham
    button.Parent = categoryFrame
    button.MouseButton1Click:Connect(callback)
end

-- Variáveis de estado
local espEnabled = false
local aimbotEnabled = false
local fallDamageEnabled = false

-- Função para ESP (simples exemplo)
local function createESP(target)
    local box = Instance.new("BoxHandleAdornment")
    box.Adornee = target.Head
    box.Size = Vector3.new(2, 2, 2)
    box.Color3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha
    box.Transparency = 0.5
    box.Parent = workspace.CurrentCamera
end

-- Função de Aimbot (simples exemplo)
local function aimbot()
    local closestEnemy = nil
    local shortestDistance = 50  -- Defina um limite para o alcance
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj:FindFirstChild("Head") then
            local distance = (obj.Head.Position - character.HumanoidRootPart.Position).Magnitude
            if distance < shortestDistance then
                closestEnemy = obj
                shortestDistance = distance
            end
        end
    end

    if closestEnemy then
        mouse.Hit = closestEnemy.Head.CFrame
    end
end

-- Função para dano de queda
local function fallDamage()
    local humanoid = character:FindFirstChild("Humanoid")
    if humanoid then
        humanoid.FreeFalling:Connect(function(isFalling)
            if isFalling then
                local fallHeight = character.HumanoidRootPart.Position.Y
                if fallHeight > 50 then  -- Se cair de mais de 50 de altura
                    humanoid:TakeDamage(20)  -- Aplica dano
                end
            end
        end)
    end
end

-- Função para alternar todas as funções
local function toggleAllFunctions()
    espEnabled = not espEnabled
    aimbotEnabled = not aimbotEnabled
    fallDamageEnabled = not fallDamageEnabled
    
    espButton.Text = espEnabled and "Desativar ESP" or "Ativar ESP"
    aimbotButton.Text = aimbotEnabled and "Desativar Aimbot" or "Ativar Aimbot"
    fallDamageButton.Text = fallDamageEnabled and "Desativar Dano de Queda" or "Ativar Dano de Queda"
end

-- Função para alternar a visibilidade do painel
toggleButton.MouseButton1Click:Connect(function()
    mainPanel.Visible = not mainPanel.Visible
    toggleButton.Text = mainPanel.Visible and "Ocultar" or "Mostrar"
end)

-- Criar os botões no painel com categorias
createButton("Ativar/Desativar ESP", UDim2.new(0, 0, 0, 0), function()
    espEnabled = not espEnabled
end)

createButton("Ativar/Desativar Aimbot", UDim2.new(0, 0, 0, 60), function()
    aimbotEnabled = not aimbotEnabled
end)

createButton("Ativar/Desativar Dano de Queda", UDim2.new(0, 0, 0, 120), function()
    fallDamageEnabled = not fallDamageEnabled
end)

createButton("Ativar/Desativar Tudo", UDim2.new(0, 0, 0, 180), toggleAllFunctions)

-- Criar configurações (exemplo simples)
local settingsLabel = Instance.new("TextLabel")
settingsLabel.Size = UDim2.new(1, 0, 0, 30)
settingsLabel.Position = UDim2.new(0, 0, 0, 240)
settingsLabel.Text = "Configurações"
settingsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
settingsLabel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
settingsLabel.TextSize = 18
settingsLabel.Font = Enum.Font.Gotham
settingsLabel.Parent = categoryFrame

createButton("Resetar Configurações", UDim2.new(0, 0, 0, 270), function()
    espEnabled = false
    aimbotEnabled = false
    fallDamageEnabled = false
end)

-- Funcionalidades contínuas
game:GetService("RunService").RenderStepped:Connect(function()
    if espEnabled then
        -- Cria ESP para todos os inimigos
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj:FindFirstChild("Head") then
                createESP(obj)
            end
        end
    end

    if aimbotEnabled then
        aimbot()
    end

    if fallDamageEnabled then
        fallDamage()
    end
end)

-- Ocultar/Exibir GUI com Shift Direito
local shiftDown = false
mouse.KeyDown:Connect(function(key)
    if key:lower() == "rshift" then
        shiftDown = true
        mainPanel.Visible = not mainPanel.Visible
    end
end)

mouse.KeyUp:Connect(function(key)
    if key:lower() == "rshift" then
        shiftDown = false
    end
end)
