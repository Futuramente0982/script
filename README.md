-- Criar uma GUI simples
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local TextBox = Instance.new("TextBox")
local TeleportButton = Instance.new("TextButton")
local SpectateButton = Instance.new("TextButton")
local WaypointButton = Instance.new("TextButton")
local TPButton = Instance.new("TextButton")
local FooterLabel = Instance.new("TextLabel")

-- Variável para armazenar a posição do waypoint
local WaypointPosition = nil

-- Variável para armazenar a câmera original
local OriginalCameraSubject = workspace.CurrentCamera.CameraSubject

-- Configurar a GUI
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Frame.Position = UDim2.new(0.5, -150, 0.3, -75) -- Ajustado para ficar mais centralizado
Frame.Size = UDim2.new(0, 300, 0, 250) -- Ajustado o tamanho para caber os elementos

TextBox.Parent = Frame
TextBox.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
TextBox.Position = UDim2.new(0.1, 0, 0.05, 0)
TextBox.Size = UDim2.new(0.8, 0, 0.15, 0)
TextBox.PlaceholderText = "Nome do Jogador"

TeleportButton.Parent = Frame
TeleportButton.BackgroundColor3 = Color3.fromRGB(100, 255, 100)
TeleportButton.Position = UDim2.new(0.1, 0, 0.25, 0)
TeleportButton.Size = UDim2.new(0.35, 0, 0.15, 0) -- Tamanho ajustado
TeleportButton.Text = "Teleportar"

SpectateButton.Parent = Frame
SpectateButton.BackgroundColor3 = Color3.fromRGB(100, 100, 255)
SpectateButton.Position = UDim2.new(0.55, 0, 0.25, 0) -- Posição ajustada
SpectateButton.Size = UDim2.new(0.35, 0, 0.15, 0) -- Tamanho ajustado
SpectateButton.Text = "Espectar"

WaypointButton.Parent = Frame
WaypointButton.BackgroundColor3 = Color3.fromRGB(255, 255, 100)
WaypointButton.Position = UDim2.new(0.1, 0, 0.45, 0) -- Posição ajustada
WaypointButton.Size = UDim2.new(0.35, 0, 0.15, 0) -- Tamanho ajustado
WaypointButton.Text = "Waypoint"

TPButton.Parent = Frame
TPButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
TPButton.Position = UDim2.new(0.55, 0, 0.45, 0) -- Posição ajustada
TPButton.Size = UDim2.new(0.35, 0, 0.15, 0) -- Tamanho ajustado
TPButton.Text = "TP"

FooterLabel.Parent = Frame
FooterLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
FooterLabel.Position = UDim2.new(0.1, 0, 0.75, 0) -- Ajustado para a parte inferior
FooterLabel.Size = UDim2.new(0.8, 0, 0.1, 0) -- Ajustado para estender até o final, com altura correta
FooterLabel.Text = "Feito Por Toddy"
FooterLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
FooterLabel.TextScaled = true

-- Função de teleporte
local function TeleportPlayer()
    local playerName = TextBox.Text
    local targetPlayer = game.Players:FindFirstChild(playerName)
    
    if targetPlayer then
        local character = targetPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local targetPosition = character.HumanoidRootPart.Position
            local localPlayer = game.Players.LocalPlayer
            
            if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
                localPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(targetPosition)
            end
        end
    else
        print("Jogador não encontrado")
    end
end

-- Variável para alternar entre modos de câmera
local isSpectating = false

-- Função de espectar em terceira pessoa
local function SpectatePlayer()
    if isSpectating then
        -- Voltar para a câmera original
        workspace.CurrentCamera.CameraSubject = OriginalCameraSubject
    else
        -- Espectar o jogador alvo
        local playerName = TextBox.Text
        local targetPlayer = game.Players:FindFirstChild(playerName)
        
        if targetPlayer then
            local character = targetPlayer.Character
            if character and character:FindFirstChild("Humanoid") then
                -- Ajustar a câmera para a terceira pessoa do personagem alvo
                OriginalCameraSubject = workspace.CurrentCamera.CameraSubject
                workspace.CurrentCamera.CameraSubject = targetPlayer.Character.Humanoid
                workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
            end
        else
            print("Jogador não encontrado")
        end
    end
    isSpectating = not isSpectating
end

-- Função para marcar a posição do waypoint
local function SetWaypoint()
    local localPlayer = game.Players.LocalPlayer
    if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
        WaypointPosition = localPlayer.Character.HumanoidRootPart.Position
        print("Waypoint marcado em: " .. tostring(WaypointPosition))
    end
end

-- Função para teleportar para o waypoint
local function TeleportToWaypoint()
    local localPlayer = game.Players.LocalPlayer
    if WaypointPosition and localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
        localPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(WaypointPosition)
        print("Teleportado para o waypoint: " .. tostring(WaypointPosition))
    else
        print("Waypoint não definido ou personagem não encontrado")
    end
end

-- Conectar os botões às funções
TeleportButton.MouseButton1Click:Connect(TeleportPlayer)
SpectateButton.MouseButton1Click:Connect(SpectatePlayer)
WaypointButton.MouseButton1Click:Connect(SetWaypoint)
TPButton.MouseButton1Click:Connect(TeleportToWaypoint)

-- Mostrar GUI ao apertar a tecla "E"
game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.E then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)
