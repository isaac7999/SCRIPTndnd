local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 220, 0, 400)
Frame.Position = UDim2.new(0, 20, 0.5, -200)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true

-- Scroll
local Scroll = Instance.new("ScrollingFrame", Frame)
Scroll.Size = UDim2.new(1, 0, 1, 0)
Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
Scroll.ScrollBarThickness = 6
Scroll.BackgroundTransparency = 1

local UIListLayout = Instance.new("UIListLayout", Scroll)
UIListLayout.Padding = UDim.new(0, 4)

-- Função para criar botão de Tool
local function criarBotaoTool(toolName, toolRef)
	local Botao = Instance.new("TextButton", Scroll)
	Botao.Size = UDim2.new(1, -8, 0, 36)
	Botao.Position = UDim2.new(0, 4, 0, 0)
	Botao.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	Botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	Botao.Text = toolName
	Botao.Font = Enum.Font.SourceSansBold
	Botao.TextSize = 16
	Botao.BorderSizePixel = 0

	Botao.MouseButton1Click:Connect(function()
		local success, err = pcall(function()
			local clone = toolRef:Clone()
			clone.Parent = LocalPlayer:FindFirstChildOfClass("Backpack")
			task.wait(0.2)
			if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
				LocalPlayer.Character.Humanoid:EquipTool(clone)
			end
		end)
		if not success then
			warn("Erro ao equipar tool: ", err)
		end
	end)
end

-- Função para buscar todas as Tools
local function buscarTodasAsTools()
	local toolsEncontradas = {}

	for _, lugar in ipairs({workspace, game:GetService("ReplicatedStorage"), LocalPlayer}) do
		for _, obj in ipairs(lugar:GetDescendants()) do
			if obj:IsA("Tool") and not toolsEncontradas[obj.Name] then
				toolsEncontradas[obj.Name] = obj
			end
		end
	end

	for nome, tool in pairs(toolsEncontradas) do
		criarBotaoTool(nome, tool)
	end

	-- Atualiza CanvasSize baseado na quantidade de botões
	task.wait(0.1)
	Scroll.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
end

buscarTodasAsTools()
