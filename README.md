```lua
--// CHAT SECUNDÁRIO SONECAZ
--// Coloque este LocalScript em:
--// StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local TextChatService = game:GetService("TextChatService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

--------------------------------------------------
-- CONFIGURAÇÕES
--------------------------------------------------

local OWNER_USER_ID = 10368308763

local CHAT_WIDTH = 520
local CHAT_HEIGHT = 300

local BACKGROUND_TRANSPARENCY = 0.38

--------------------------------------------------
-- GUI PRINCIPAL
--------------------------------------------------

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SonecaZSecondaryChat"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--------------------------------------------------
-- BOTÃO DO CHAT
--------------------------------------------------

local OpenButton = Instance.new("TextButton")
OpenButton.Name = "ChatButton"
OpenButton.Size = UDim2.fromOffset(48, 48)
OpenButton.Position = UDim2.new(0, 20, 1, -70)
OpenButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
OpenButton.BackgroundTransparency = 0.18
OpenButton.BorderSizePixel = 0
OpenButton.Text = "💬"
OpenButton.TextSize = 23
OpenButton.Font = Enum.Font.GothamBold
OpenButton.Parent = ScreenGui

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(1, 0)
ButtonCorner.Parent = OpenButton

--------------------------------------------------
-- CHAT
--------------------------------------------------

local Main = Instance.new("Frame")
Main.Name = "SecondaryChat"
Main.Size = UDim2.fromOffset(CHAT_WIDTH, CHAT_HEIGHT)
Main.Position = UDim2.new(0, 25, 1, -CHAT_HEIGHT - 80)
Main.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
Main.BackgroundTransparency = BACKGROUND_TRANSPARENCY
Main.BorderSizePixel = 0
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = Main

--------------------------------------------------
-- MARCA D'ÁGUA
--------------------------------------------------

local Watermark = Instance.new("TextLabel")
Watermark.Name = "Watermark"
Watermark.Size = UDim2.new(1, 0, 1, 0)
Watermark.Position = UDim2.new(0, 0, 0, 0)
Watermark.BackgroundTransparency = 1
Watermark.Text = "SonecaZ"
Watermark.Font = Enum.Font.GothamBlack
Watermark.TextSize = 70
Watermark.TextColor3 = Color3.fromRGB(0, 0, 0)
Watermark.TextTransparency = 0.72
Watermark.ZIndex = 0
Watermark.Parent = Main

--------------------------------------------------
-- ÁREA DAS MENSAGENS
--------------------------------------------------

local Messages = Instance.new("ScrollingFrame")
Messages.Name = "Messages"
Messages.Size = UDim2.new(1, -16, 1, -60)
Messages.Position = UDim2.fromOffset(8, 8)
Messages.BackgroundTransparency = 1
Messages.BorderSizePixel = 0
Messages.ScrollBarThickness = 3
Messages.AutomaticCanvasSize = Enum.AutomaticSize.Y
Messages.CanvasSize = UDim2.new(0, 0, 0, 0)
Messages.ZIndex = 2
Messages.Parent = Main

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0, 4)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Parent = Messages

--------------------------------------------------
-- CAIXA DE TEXTO
--------------------------------------------------

local Input = Instance.new("TextBox")
Input.Name = "Input"
Input.Size = UDim2.new(1, -16, 0, 38)
Input.Position = UDim2.new(0, 8, 1, -46)
Input.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Input.BackgroundTransparency = 0.35
Input.BorderSizePixel = 0
Input.ClearTextOnFocus = false
Input.Font = Enum.Font.Gotham
Input.TextSize = 15
Input.TextColor3 = Color3.fromRGB(255, 255, 255)
Input.PlaceholderColor3 = Color3.fromRGB(170, 170, 170)
Input.PlaceholderText = "Digite uma mensagem..."
Input.Text = ""
Input.TextXAlignment = Enum.TextXAlignment.Left
Input.ZIndex = 3
Input.Parent = Main

local InputPadding = Instance.new("UIPadding")
InputPadding.PaddingLeft = UDim.new(0, 12)
InputPadding.Parent = Input

local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 8)
InputCorner.Parent = Input

--------------------------------------------------
-- COR DO NOME
--------------------------------------------------

local function GetNameColor(name)

	local hash = 0

	for i = 1, #name do
		hash = (hash * 31 + string.byte(name, i)) % 360
	end

	local hue = hash / 360

	return Color3.fromHSV(hue, 0.75, 1)
end

--------------------------------------------------
-- RICH TEXT SEGURO
--------------------------------------------------

local function EscapeRichText(text)

	text = string.gsub(text, "&", "&amp;")
	text = string.gsub(text, "<", "&lt;")
	text = string.gsub(text, ">", "&gt;")
	text = string.gsub(text, '"', "&quot;")

	return text
end

--------------------------------------------------
-- RGB
--------------------------------------------------

local function GetRGBColor(time)

	local hue = (time % 5) / 5

	return Color3.fromHSV(hue, 0.9, 1)
end

local function ToHex(color)

	return string.format(
		"#%02X%02X%02X",
		math.floor(color.R * 255),
		math.floor(color.G * 255),
		math.floor(color.B * 255)
	)

end

--------------------------------------------------
-- CRIAR MENSAGEM
--------------------------------------------------

local function AddMessage(message)

	local TextSource = message.TextSource

	if not TextSource then
		return
	end

	local player = Players:GetPlayerByUserId(TextSource.UserId)

	if not player then
		return
	end

	local name = player.DisplayName
	local messageText = EscapeRichText(message.Text)

	local nameColor = GetNameColor(name)
	local nameHex = ToHex(nameColor)

	local Line = Instance.new("TextLabel")

	Line.Name = "Message"
	Line.Size = UDim2.new(1, -5, 0, 24)
	Line.AutomaticSize = Enum.AutomaticSize.Y
	Line.BackgroundTransparency = 1
	Line.TextWrapped = true
	Line.RichText = true
	Line.Font = Enum.Font.Gotham
	Line.TextSize = 15
	Line.TextXAlignment = Enum.TextXAlignment.Left
	Line.TextYAlignment = Enum.TextYAlignment.Top
	Line.TextColor3 = Color3.fromRGB(255, 255, 255)
	Line.ZIndex = 3

	--------------------------------------------------
	-- DONO
	--------------------------------------------------

	if player.UserId == OWNER_USER_ID then

		Line.Text =
			'<font color="#FFFFFF"><b>[</b></font>' ..
			'<font color="#FF0000"><b>DONO</b></font>' ..
			'<font color="#FFFFFF"><b>]</b></font> ' ..
			'<font color="' .. nameHex .. '"><b>' ..
			name ..
			'</b></font>: ' ..
			messageText

		Line.Parent = Messages

		-- RGB + piscando rápido
		task.spawn(function()

			local startTime = os.clock()

			while Line.Parent do

				local elapsed = os.clock() - startTime

				local rgb = GetRGBColor(elapsed)
				local hex = ToHex(rgb)

				local blink = math.sin(elapsed * 11) > 0

				local tagColor = hex

				if not blink then
					tagColor = "#555555"
				end

				Line.Text =
					'<font color="#FFFFFF"><b>[</b></font>' ..
					'<font color="' .. tagColor .. '"><b>DONO</b></font>' ..
					'<font color="#FFFFFF"><b>]</b></font> ' ..
					'<font color="' .. nameHex .. '"><b>' ..
					name ..
					'</b></font>: ' ..
					messageText

				task.wait(0.04)
			end

		end)

	else

		Line.Text =
			'<font color="' .. nameHex .. '"><b>' ..
			name ..
			'</b></font>: ' ..
			messageText

		Line.Parent = Messages

	end

	--------------------------------------------------
	-- LIMITE DE MENSAGENS
	--------------------------------------------------

	local labels = {}

	for _, child in ipairs(Messages:GetChildren()) do

		if child:IsA("TextLabel") then
			table.insert(labels, child)
		end

	end

	if #labels > 60 then
		labels[1]:Destroy()
	end

	task.wait()

	Messages.CanvasPosition = Vector2.new(
		0,
		Messages.AbsoluteCanvasSize.Y
	)

end

--------------------------------------------------
-- RECEBER SOMENTE O CHAT SECUNDÁRIO
--------------------------------------------------

TextChatService.MessageReceived:Connect(function(message)

	local textChannel = message.TextChannel

	if not textChannel then
		return
	end

	if textChannel.Name ~= "SonecaZChat" then
		return
	end

	AddMessage(message)

end)

--------------------------------------------------
-- ENVIAR PARA O CHAT SECUNDÁRIO
--------------------------------------------------

Input.FocusLost:Connect(function(enterPressed)

	if not enterPressed then
		return
	end

	local text = Input.Text

	if text == "" then
		return
	end

	Input.Text = ""

	local channel = TextChatService:WaitForChild("SonecaZChat")

	channel:SendAsync(text)

end)

--------------------------------------------------
-- MOSTRAR / ESCONDER
--------------------------------------------------

local chatVisible = true

OpenButton.MouseButton1Click:Connect(function()

	chatVisible = not chatVisible

	Main.Visible = chatVisible

end)

--------------------------------------------------
-- CHAT ARRASTÁVEL
--------------------------------------------------

local dragging = false
local dragStart
local startPosition

local function UpdateDrag(input)

	local delta = input.Position - dragStart

	Main.Position = UDim2.new(
		startPosition.X.Scale,
		startPosition.X.Offset + delta.X,
		startPosition.Y.Scale,
		startPosition.Y.Offset + delta.Y
	)

end

Main.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then

		dragging = true
		dragStart = input.Position
		startPosition = Main.Position

		input.Changed:Connect(function()

			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end

		end)

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		UpdateDrag(input)
	end

end)
```
