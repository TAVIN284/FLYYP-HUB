--[[ ESP com Team Check e Interface de Ativação (Script de Teste Autorizado) ]]--

-- CONFIGURAÇÕES INICIAIS
local ESP_Ativado = false
local Mostrar_Aliados = false -- Se true, mostra todos. Se false, oculta jogadores da mesma equipe
local Tecla_Toggle = Enum.KeyCode.F6 -- Tecla para ativar/desativar ESP

-- Criar a interface simples de status
local StarterGui = game:GetService("StarterGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ESP_GUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(0, 200, 0, 40)
statusLabel.Position = UDim2.new(0, 10, 0, 10)
statusLabel.BackgroundTransparency = 0.4
statusLabel.BackgroundColor3 = Color3.new(0, 0, 0)
statusLabel.TextColor3 = Color3.new(0, 1, 0)
statusLabel.Font = Enum.Font.SourceSansBold
statusLabel.TextSize = 18
statusLabel.Text = "ESP: DESATIVADO"
statusLabel.Parent = screenGui

-- Tabela para guardar os desenhos
local espObjetos = {}

-- Função para criar o ESP
local function CriarESP(jogador)
    if jogador == game.Players.LocalPlayer then return end
    if not Mostrar_Aliados and jogador.Team == game.Players.LocalPlayer.Team then return end

    local caixa = Drawing.new("Text")
    caixa.Visible = false
    caixa.Center = true
    caixa.Outline = true
    caixa.Font = 2
    caixa.Size = 13
    caixa.Color = Color3.new(1, 0, 0)
    caixa.Text = jogador.Name

    espObjetos[jogador] = caixa
end

-- Remover ESP ao sair
game.Players.PlayerRemoving:Connect(function(plr)
    if espObjetos[plr] then
        espObjetos[plr]:Remove()
        espObjetos[plr] = nil
    end
end)

-- Atualizar posições
game:GetService("RunService").RenderStepped:Connect(function()
    if not ESP_Ativado then
        for _, obj in pairs(espObjetos) do
            obj.Visible = false
        end
        return
    end

    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= game.Players.LocalPlayer then
            if not Mostrar_Aliados and plr.Team == game.Players.LocalPlayer.Team then
                if espObjetos[plr] then
                    espObjetos[plr].Visible = false
                end
            else
                local char = plr.Character
                local head = char and char:FindFirstChild("Head")
                if head then
                    local pos, naTela = workspace.CurrentCamera:WorldToViewportPoint(head.Position)
                    if naTela then
                        if not espObjetos[plr] then
                            CriarESP(plr)
                        end
                        local esp = espObjetos[plr]
                        esp.Position = Vector2.new(pos.X, pos.Y)
                        esp.Text = plr.Name
                        esp.Color = (plr.Team == game.Players.LocalPlayer.Team and Color3.new(0, 1, 0) or Color3.new(1, 0, 0))
                        esp.Visible = true
                    else
                        if espObjetos[carregado] then
