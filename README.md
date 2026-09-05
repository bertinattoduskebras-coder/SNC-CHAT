```lua
-- CHAT SECUNDARIO SONECAZ
-- Coloque em StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Event = ReplicatedStorage:WaitForChild("SonecaZChatEvent")

local OWNER_USER_ID = 10368308763

--------------------------------------------------
-- GUI
--------------------------------------------------

local Gui = Instance.new("ScreenGui")
Gui.Name = "SonecaZChat"
Gui.ResetOnSpawn = false
Gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--------------------------------------------------
-- BOTÃO
--------------------------------------------------

local Button = Instance.new("TextButton")
Button.Size = UDim2.fromOffset(50, 50)
Button.Position = UDim2.new(0, 20, 1, -75)
Button.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Button.BackgroundTransparency = 0.15
Button.Text = "💬"
Button.TextSize = 23
Button.Font = Enum.Font.GothamBold
Button.TextColor3 = Color3.new(1,1,1)
Button.Parent = Gui

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(1,0)
ButtonCorner.Parent = Button

--------------------------------------------------
-- JANELA
--------------------------------------------------

local Chat = Instance.new("Frame")
Chat.Size = UDim2.fromOffset(520, 300)
Chat.Position = UDim2.new(0, 25, 1, -390)
Chat.BackgroundColor3 = Color3.fromRGB(15,15,15)
Chat.BackgroundTransparency = 0.38
Chat.BorderSizePixel = 0
Chat.Parent = Gui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 12)
Corner.Parent = Chat

--------------------------------------------------
-- SONECAZ NO FUNDO
--------------------------------------------------

local Watermark = Instance.new("TextLabel")
Watermark.Size = UDim2.new(1,0,1,0)
Watermark.BackgroundTransparency = 1
Watermark.Text = "SonecaZ"
Watermark.Font = Enum.Font.GothamBlack
Watermark.TextSize = 65
Watermark.TextColor3 = Color3.fromRGB(0,0,0)
Watermark.TextTransparency = 0.65
Watermark.ZIndex = 0
Watermark.Parent = Chat

--------------------------------------------------
-- MENSAGENS
--------------------------------------------------

local Messages = Instance.new("ScrollingFrame")
Messages.Size = UDim2.new(1,-16,1,-58)
Messages.Position = UDim2.fromOffset(8,8)
Messages.BackgroundTransparency = 1
Messages.BorderSizePixel = 0
Messages.ScrollBarThickness = 3
Messages.AutomaticCanvasSize = Enum.AutomaticSize.Y
Messages.CanvasSize = UDim2.new(0,0,0,0)
Messages.ZIndex = 2
Messages.Parent = Chat

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0,3)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Parent = Messages

--------------------------------------------------
-- CAIXA DE TEXTO
--------------------------------------------------

local Input = Instance.new("TextBox")
Input.Size = UDim2.new(1,-16,0,38)
Input.Position = UDim2.new(0,8,1,-46)
Input.BackgroundColor3 = Color3.fromRGB(0,0,0)
Input.BackgroundTransparency = 0.3
Input.BorderSizePixel = 0
Input.Text = ""
Input.PlaceholderText = "Digite uma mensagem..."
Input.PlaceholderColor3 = Color3.fromRGB(170,170,170)
Input.TextColor3 = Color3.new(1,1,1)
Input.TextSize = 15
Input.Font = Enum.Font.Gotham
Input.TextXAlignment = Enum.TextXAlignment.Left
Input.ClearTextOnFocus = false
Input.ZIndex = 3
Input.Parent = Chat

local Padding = Instance.new("UIPadding")
Padding.PaddingLeft = UDim.new(0,12)
Padding.Parent = Input

local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0,8)
InputCorner.Parent = Input

--------------------------------------------------
-- CORES DOS NOMES
--------------------------------------------------

local function GetNameColor(name)

	local hash = 0

	for i = 1,#name do
		hash = (hash * 31 + string.byte(name,i)) % 360
	end

	return Color3.fromHSV(hash / 360,0.75,1)
end

local function Hex(color)

	return string.format(
		"#%02X%02X%02X",
		math.floor(color.R * 255),
		math.floor(color.G * 255),
		math.floor(color.B * 255)
	)

end

--------------------------------------------------
-- MENSAGEM RECEBIDA
--------------------------------------------------

Event.OnClientEvent:Connect(function(userId, name, text)

	local Line = Instance.new("TextLabel")
	Line.Size = UDim2.new(1,-5,0,24)
	Line.AutomaticSize = Enum.AutomaticSize.Y
	Line.BackgroundTransparency = 1
	Line.TextWrapped = true
	Line.RichText = true
	Line.Font = Enum.Font.Gotham
	Line.TextSize = 15
	Line.TextXAlignment = Enum.TextXAlignment.Left
	Line.TextYAlignment = Enum.TextYAlignment.Top
	Line.TextColor3 = Color3.new(1,1,1)
	Line.ZIndex = 3
	Line.Parent = Messages

	local nameColor = Hex(GetNameColor(name))

	if userId == OWNER_USER_ID then

		Line.Text =
			'<font color="#FFFFFF"><b>[</b></font>' ..
			'<font color="#FF0000"><b>DONO</b></font>' ..
			'<font color="#FFFFFF"><b>]</b></font> ' ..
			'<font color="' .. nameColor .. '"><b>' ..
			name ..
			'</b></font>: ' ..
			text

		task.spawn(function()

			local hue = 0
			local blink = false

			while Line.Parent do

				hue = (hue + 0.025) % 1
				blink = not blink

				local rgb = Color3.fromHSV(hue,1,1)
				local rgbHex = Hex(rgb)

				local donoColor = rgbHex

				if not blink then
					donoColor = "#555555"
				end

				Line.Text =
					'<font color="#FFFFFF"><b>[</b></font>' ..
					'<font color="' .. donoColor .. '"><b>DONO</b></font>' ..
					'<font color="#FFFFFF"><b>]</b></font> ' ..
					'<font color="' .. nameColor .. '"><b>' ..
					name ..
					'</b></font>: ' ..
					text

				task.wait(0.05)
			end

		end)

	else

		Line.Text =
			'<font color="' .. nameColor .. '"><b>' ..
			name ..
			'</b></font>: ' ..
			text

	end

	task.wait()

	Messages.CanvasPosition = Vector2.new(
		0,
		Messages.AbsoluteCanvasSize.Y
	)

end)

--------------------------------------------------
-- ENVIAR MENSAGEM
--------------------------------------------------

Input.FocusLost:Connect(function(enter)

	if not enter then
		return
	end

	if Input.Text == "" then
		return
	end

	Event:FireServer(Input.Text)

	Input.Text = ""

end)

--------------------------------------------------
-- MOSTRAR / ESCONDER
--------------------------------------------------

Button.MouseButton1Click:Connect(function()

	Chat.Visible = not Chat.Visible

end)

--------------------------------------------------
-- ARRASTAR CHAT
--------------------------------------------------

local dragging = false
local dragStart
local startPosition

Chat.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then

		dragging = true
		dragStart = input.Position
		startPosition = Chat.Position

	end

end)

UserInputService.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end

end)

UserInputService.InputChanged:Connect(function(input)

	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then

		local delta = input.Position - dragStart

		Chat.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)

	end

end)
```
