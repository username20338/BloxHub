-- Carregar a biblioteca Orion
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/OrionUIs/Orion/main/source.lua"))()

-- Variáveis para controle do Auto Farm
local isAutoFarming = false
local enemyName = "Bandit" -- Nome do inimigo, você pode mudar conforme necessário
local fruit = "Flame" -- Exemplo de fruta que o jogador usa (substitua conforme a fruta que você usa)

-- Variáveis para TTK e CDDK
local lastAttackTime = 0
local lastAbilityUseTime = 0
local abilityCooldown = 5 -- Exemplo de tempo de recarga para habilidade (ajuste conforme necessário)
local ttkThreshold = 2 -- Tempo máximo para matar o inimigo (ajuste conforme necessário)

-- Função para usar habilidade de fruta (Exemplo com Flame)
local function useFruitAbility()
    local currentTime = tick() -- Marca o tempo atual
    local character = game.Players.LocalPlayer.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Verifica o tempo de recarga antes de usar a habilidade
        if currentTime - lastAbilityUseTime >= abilityCooldown then
            -- Supondo que a fruta flame tenha uma habilidade que pode ser ativada
            local flameAbility = character:FindFirstChild("FlameAbility") -- Isso depende de como a fruta é configurada
            if flameAbility then
                -- Aqui, vamos simular a ativação da habilidade (substitua com o código correto da habilidade da fruta)
                flameAbility:Activate()
                lastAbilityUseTime = currentTime
                print("Habilidade da fruta ativada!")
            end
        end
    end
end

-- Função para atacar inimigos automaticamente
local function startAutoFarm()
    while isAutoFarming do
        local target = game.Workspace:FindFirstChild(enemyName)
        
        -- Verifica se o inimigo foi encontrado
        if target then
            local currentTime = tick() -- Marca o tempo atual
            local character = game.Players.LocalPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                -- Verifica o TTK (Time To Kill)
                if currentTime - lastAttackTime >= ttkThreshold then
                    -- Atacar o inimigo
                    firetouchinterest(character.HumanoidRootPart, target, 0)
                    lastAttackTime = currentTime
                    print("Atacando o inimigo: " .. target.Name)
                    
                    -- Usar habilidade de fruta
                    useFruitAbility()
                end
            end
        end
        
        -- Delay entre as ações (ajuste conforme necessário)
        wait(0.5) 
    end
end

-- Função para parar o Auto Farm
local function stopAutoFarm()
    isAutoFarming = false
end

-- Criar a janela principal do Orion
local Window = OrionLib:MakeWindow({
    Name = "Blox Fruits Auto Farm",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "BloxFruitsConfig",
})

-- Criar uma aba para o Auto Farm
local Tab = Window:MakeTab({
    Name = "Auto Farm",
    Icon = "rbxassetid://6031092055",
    PremiumOnly = false
})

-- Adicionar um botão para iniciar o Auto Farm
Tab:AddButton({
    Name = "Iniciar Auto Farm",
    Callback = function()
        if not isAutoFarming then
            isAutoFarming = true
            startAutoFarm()
            print("Auto Farm iniciado!")
        else
            print("Auto Farm já está ativado.")
        end
    end
})

-- Adicionar um botão para parar o Auto Farm
Tab:AddButton({
    Name = "Parar Auto Farm",
    Callback = function()
        isAutoFarming = false
        print("Auto Farm parado!")
    end
})

-- Adicionar uma caixa de seleção para controlar o estado do Auto Farm
Tab:AddToggle({
    Name = "Ativar Auto Farm",
    Default = false,
    Callback = function(State)
        if State then
            isAutoFarming = true
            startAutoFarm()
            print("Auto Farm ativado!")
        else
            stopAutoFarm()
            print("Auto Farm desativado.")
        end
    end
})

-- Adicionar uma caixa de seleção para usar uma fruta específica
Tab:AddDropdown({
    Name = "Selecionar Fruta",
    Default = "Flame", -- Fruta inicial
    Options = {"Flame", "Magma", "Ice", "Gomu", "Light"}, -- Você pode adicionar mais opções aqui
    Callback = function(selectedFruit)
        fruit = selectedFruit
        print("Fruta selecionada: " .. selectedFruit)
    end
})

-- Inicializar a interface
OrionLib:Init()
