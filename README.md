local mainParent = game.CoreGui

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Mouse = game.Players.LocalPlayer:GetMouse()
function dragify(Frame, object)
	dragToggle = nil
	dragSpeed = .25
	dragInput = nil
	dragStart = nil
	dragPos = nil

	function updateInput(input)
		if Move then
			return
		end
		Delta = input.Position - dragStart
		Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + Delta.X, startPos.Y.Scale, startPos.Y.Offset + Delta.Y)
		game:GetService("TweenService"):Create(object, TweenInfo.new(dragSpeed), {Position = Position}):Play()
	end

	Frame.InputBegan:Connect(function(input)
		if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
			dragToggle = true
			dragStart = input.Position
			startPos = object.Position
			input.Changed:Connect(function()
				if (input.UserInputState == Enum.UserInputState.End) then
					dragToggle = false
				end
			end)
		end
	end)

	Frame.InputChanged:Connect(function(input)
		if (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch)then
			dragInput = input
		end
	end)

	game:GetService("UserInputService").InputChanged:Connect(function(input)
		if (input == dragInput and dragToggle) then
			updateInput(input)
		end
	end)
end

local function tablefound(ta, object)
	for i,v in pairs(ta) do
		if v == object then
			return true
		end
	end
	return false
end

local ActualTypes = {
	RoundFrame = "ImageLabel",
	Shadow = "ImageLabel",
	Circle = "ImageLabel",
	CircleButton = "ImageButton",
	Frame = "Frame",
	Label = "TextLabel",
	Button = "TextButton",
	SmoothButton = "ImageButton",
	Box = "TextBox",
	ScrollingFrame = "ScrollingFrame",
	Menu = "ImageButton",
	NavBar = "ImageButton"
}

local Properties = {
	RoundFrame = {
		BackgroundTransparency = 1,
		Image = "http://www.roblox.com/asset/?id=5554237731",
		ScaleType = Enum.ScaleType.Slice,
		SliceCenter = Rect.new(3,3,297,297)
	},
	SmoothButton = {
		AutoButtonColor = false,
		BackgroundTransparency = 1,
		Image = "http://www.roblox.com/asset/?id=5554237731",
		ScaleType = Enum.ScaleType.Slice,
		SliceCenter = Rect.new(3,3,297,297)
	},
	Shadow = {
		Name = "Shadow",
		BackgroundTransparency = 1,
		Image = "http://www.roblox.com/asset/?id=5554236805",
		ScaleType = Enum.ScaleType.Slice,
		SliceCenter = Rect.new(23,23,277,277),
		Size = UDim2.fromScale(1,1) + UDim2.fromOffset(30,30),
		Position = UDim2.fromOffset(-15,-15)
	},
	Circle = {
		BackgroundTransparency = 1,
		Image = "http://www.roblox.com/asset/?id=5554831670"
	},
	CircleButton = {
		BackgroundTransparency = 1,
		AutoButtonColor = false,
		Image = "http://www.roblox.com/asset/?id=5554831670"
	},
	Frame = {
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		Size = UDim2.fromScale(1,1)
	},
	Label = {
		BackgroundTransparency = 1,
		Position = UDim2.fromOffset(5,0),
		Size = UDim2.fromScale(1,1) - UDim2.fromOffset(5,0),
		TextSize = 14,
		TextXAlignment = Enum.TextXAlignment.Left
	},
	Button = {
		BackgroundTransparency = 1,
		Position = UDim2.fromOffset(5,0),
		Size = UDim2.fromScale(1,1) - UDim2.fromOffset(5,0),
		TextSize = 14,
		TextXAlignment = Enum.TextXAlignment.Left
	},
	Box = {
		BackgroundTransparency = 1,
		Position = UDim2.fromOffset(5,0),
		Size = UDim2.fromScale(1,1) - UDim2.fromOffset(5,0),
		TextSize = 14,
		TextXAlignment = Enum.TextXAlignment.Left
	},
	ScrollingFrame = {
		BackgroundTransparency = 1,
		ScrollBarThickness = 0,
		CanvasSize = UDim2.fromScale(0,0),
		Size = UDim2.fromScale(1,1)
	},
	Menu = {
		Name = "More",
		AutoButtonColor = false,
		BackgroundTransparency = 1,
		Image = "http://www.roblox.com/asset/?id=5555108481",
		Size = UDim2.fromOffset(20,20),
		Position = UDim2.fromScale(1,0.5) - UDim2.fromOffset(25,10)
	},
	NavBar = {
		Name = "SheetToggle",
		Image = "http://www.roblox.com/asset/?id=5576439039",
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(20,20),
		Position = UDim2.fromOffset(5,5),
		AutoButtonColor = false
	}
}

local Types = {
	"RoundFrame",
	"Shadow",
	"Circle",
	"CircleButton",
	"Frame",
	"Label",
	"Button",
	"SmoothButton",
	"Box",
	"ScrollingFrame",
	"Menu",
	"NavBar"
}

function FindType(String)
	for _, Type in next, Types do
		if Type:sub(1, #String):lower() == String:lower() then
			return Type
		end
	end
	return false
end

local Objects = {}

function Objects.new(Type)
	local TargetType = FindType(Type)
	if TargetType then
		local NewImage = Instance.new(ActualTypes[TargetType])
		if Properties[TargetType] then
			for Property, Value in next, Properties[TargetType] do
				NewImage[Property] = Value
			end
		end
		return NewImage
	else
		return Instance.new(Type)
	end
end

local function GetXY(GuiObject)
	local Max, May = GuiObject.AbsoluteSize.X, GuiObject.AbsoluteSize.Y
	local Px, Py = math.clamp(Mouse.X - GuiObject.AbsolutePosition.X, 0, Max), math.clamp(Mouse.Y - GuiObject.AbsolutePosition.Y, 0, May)
	return Px/Max, Py/May
end

local function CircleAnim(GuiObject, EndColour, StartColour)
	local PX, PY = GetXY(GuiObject)
	local Circle = Objects.new("Shadow")
	Circle.Size = UDim2.fromScale(0,0)
	Circle.Position = UDim2.fromScale(PX,PY)
	Circle.ImageColor3 = StartColour or GuiObject.ImageColor3
	Circle.ZIndex = 200
	Circle.Parent = GuiObject
	local Size = GuiObject.AbsoluteSize.X
	TweenService:Create(Circle, TweenInfo.new(0.5), {Position = UDim2.fromScale(PX,PY) - UDim2.fromOffset(Size/2,Size/2), ImageTransparency = 1, ImageColor3 = EndColour, Size = UDim2.fromOffset(Size,Size)}):Play()
	spawn(function()
		wait(0.5)
		Circle:Destroy()
	end)
end

function CircleClick(Button, X, Y)
	coroutine.resume(
		coroutine.create(
			function()
				local Circle = Instance.new("ImageLabel")
				Circle.Parent = Button
				Circle.Name = "Circle"
				Circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				Circle.BackgroundTransparency = 1.000
				Circle.ZIndex = 10
				Circle.Image = "rbxassetid://266543268"
				Circle.ImageColor3 = Color3.fromRGB(255, 255, 255)
				Circle.ImageTransparency = 0.7
				local NewX = X - Circle.AbsolutePosition.X
				local NewY = Y - Circle.AbsolutePosition.Y
				Circle.Position = UDim2.new(0, NewX, 0, NewY)

				local Time = 0.2
				Circle:TweenSizeAndPosition(
					UDim2.new(0, 30.25, 0, 30.25),
					UDim2.new(0, NewX - 15, 0, NewY - 10),
					"Out",
					"Quad",
					Time,
					false,
					nil
				)
				for i = 1, 10 do
					Circle.ImageTransparency = Circle.ImageTransparency + 0.01
					wait(Time / 10)
				end
				Circle:Destroy()
			end
		)
	)
end

if mainParent:FindFirstChild("ReachLibrary") then
	mainParent:FindFirstChild("ReachLibrary"):Destroy()
end

local ReachLibrary = Instance.new("ScreenGui")
ReachLibrary.Name = "ReachLibrary"
ReachLibrary.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

Library = {
	Taps = {
		Value = false
	},
	Page = {},
	Main = {}
}


function errorHandler(err)
	warn("An error occurred:", err)
	return err
end

_G.NowSize = _G.Size or 247
_G.SizeY = 450
_G.Color = Color3.fromRGB(255, 0, 89)
_G.Settings = {}
local Nummm = 0
function Library.AddWindown(options)
	local NameHub = {
		options["Name Hub"]
	}

	local Main = Instance.new("Frame")
	Main.Name = "Main"
	Main.Parent = ReachLibrary
	Main.AnchorPoint = Vector2.new(0.5, 0.5)
	Main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	Main.BorderColor3 = Color3.fromRGB(0, 0, 0)
	Main.BorderSizePixel = 0
	Main.ClipsDescendants = true
	Main.Position = UDim2.new(0.5, 0, 0.5, 0)
	Main.Size = UDim2.new(0, 0, 0, 0)

	TweenService:Create(
		Main,
		TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
		{Size = UDim2.new(0, 450, 0, 247)}
	):Play()

	local Backgrond = Instance.new("Frame")
	Backgrond.Name = "Backgrond"
	Backgrond.Parent = Main
	Backgrond.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
	Backgrond.BackgroundTransparency = 0.500
	Backgrond.BorderColor3 = Color3.fromRGB(0, 0, 0)
	Backgrond.BorderSizePixel = 0
	Backgrond.Position = UDim2.new(0.0222222228, 0, 0.113360323, 0)
	Backgrond.Size = UDim2.new(0, 0, 0.837889612, 0)
	Backgrond.ZIndex = 4

	local mainPage = Instance.new("Frame")
	mainPage.Name = "mainPage"
	mainPage.Parent = Main
	mainPage.AnchorPoint = Vector2.new(0.5, 0.5)
	mainPage.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	mainPage.BackgroundTransparency = 1.000
	mainPage.BorderColor3 = Color3.fromRGB(0, 0, 0)
	mainPage.BorderSizePixel = 0
	mainPage.Position = UDim2.new(0.50000006, 0, 0.551143765, 0)
	mainPage.Size = UDim2.new(0.953333378, 0, 0.800212502, 0)

	local BackgrondIcon = Instance.new("ImageLabel")
	BackgrondIcon.Name = "BackgrondIcon"
	BackgrondIcon.Parent = mainPage
	BackgrondIcon.AnchorPoint = Vector2.new(0.5, 0.5)
	BackgrondIcon.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	BackgrondIcon.BackgroundTransparency = 1.000
	BackgrondIcon.BorderColor3 = Color3.fromRGB(0, 0, 0)
	BackgrondIcon.BorderSizePixel = 0
	BackgrondIcon.Size = UDim2.new(1, 0, 1, 0)
	BackgrondIcon.Visible = false
	BackgrondIcon.Image = "rbxassetid://15513045429"

	local UICorner = Instance.new("UICorner")
	UICorner.CornerRadius = UDim.new(0, 5)
	UICorner.Parent = mainPage

	local Top = Instance.new("Frame")
	local HubName = Instance.new("TextLabel")
	local ButrtonTap = Instance.new("ImageButton")

	Top.Name = "Top"
	Top.Parent = Main
	Top.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
	Top.BorderColor3 = Color3.fromRGB(0, 0, 0)
	Top.BorderSizePixel = 0
	Top.Position = UDim2.new(-6.78168419e-08, 0, -6.17764755e-08, 0)
	Top.Size = UDim2.new(1.00000012, 0, 0.110273957, 0)

	dragify(Top, Main)
	HubName.Name = "HubName"
	HubName.Parent = Top
	HubName.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	HubName.BackgroundTransparency = 1.000
	HubName.BorderColor3 = Color3.fromRGB(0, 0, 0)
	HubName.BorderSizePixel = 0
	HubName.Position = UDim2.new(0.0242221374, 0, 0.0200000256, 0)
	HubName.Size = UDim2.new(0.975777745, 0, 1, 0)
	HubName.Font = Enum.Font.Ubuntu
	HubName.Text = NameHub[1]
	HubName.TextColor3 = Color3.fromRGB(255, 255, 255)
	HubName.TextSize = 12.000
	HubName.TextXAlignment = Enum.TextXAlignment.Left
	HubName.TextStrokeTransparency = 1
	HubName.TextStrokeColor3 = Color3.fromRGB(255, 255, 255)
	HubName.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

	ButrtonTap.Name = "ButrtonTap"
	ButrtonTap.Parent = Top
	ButrtonTap.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	ButrtonTap.BackgroundTransparency = 1.000
	ButrtonTap.BorderColor3 = Color3.fromRGB(0, 0, 0)
	ButrtonTap.BorderSizePixel = 0
	ButrtonTap.AnchorPoint = Vector2.new(.5,.5)
	ButrtonTap.Position = UDim2.new(0.95, 0, 0.5, 0)
	ButrtonTap.Size = UDim2.new(0, 20, 0.701575637, 0)
	ButrtonTap.Image = "http://www.roblox.com/asset/?id=6031280894"

	local UICorner_24 = Instance.new("UICorner")
	UICorner_24.CornerRadius = UDim.new(0, 5)
	UICorner_24.Parent = ButrtonTap

	local Tapmain = Instance.new("Frame")
	local ScrollingTap = Instance.new("ScrollingFrame")
	local UIPadding_6 = Instance.new("UIPadding")
	local UIListLayout_6 = Instance.new("UIListLayout")
	Tapmain.Name = "Tapmain"
	Tapmain.Parent = Main
	Tapmain.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	Tapmain.BorderColor3 = Color3.fromRGB(0, 0, 0)
	Tapmain.BorderSizePixel = 0
	Tapmain.ClipsDescendants = true
	Tapmain.Position = UDim2.new(-0.00221659336, 0, 0.113360323, 0)
	Tapmain.Size = UDim2.new(-0.353339106, 160, 0.890000045, 0)
	Tapmain.ZIndex = 5

	ScrollingTap.Name = "ScrollingTap"
	ScrollingTap.Parent = Tapmain
	ScrollingTap.Active = true
	ScrollingTap.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	ScrollingTap.BackgroundTransparency = 12.000
	ScrollingTap.BorderColor3 = Color3.fromRGB(0, 0, 0)
	ScrollingTap.BorderSizePixel = 0
	ScrollingTap.Position = UDim2.new(0, 5, 0, 5)
	ScrollingTap.Size = UDim2.new(0.956401765, 0, 1, 0)
	ScrollingTap.ZIndex = 5
	ScrollingTap.ScrollBarImageColor3 = Color3.fromRGB(0, 0, 0)
	ScrollingTap.ScrollBarThickness = 0

	UIPadding_6.Parent = ScrollingTap
	UIPadding_6.PaddingLeft = UDim.new(0, 5)
	UIPadding_6.PaddingTop = UDim.new(0, 5)

	UIListLayout_6.Parent = ScrollingTap
	UIListLayout_6.SortOrder = Enum.SortOrder.LayoutOrder
	UIListLayout_6.Padding = UDim.new(0, 5)

	local openTap = false
	ButrtonTap.MouseButton1Click:Connect(function()
		local targetSize,targetSize2 = UDim2.new(0, 0, 0.889726043, 0),UDim2.new(0, 0, 0.837889612, 0)
		if not openTap then
			targetSize,targetSize2 = UDim2.new(0, 170, 0.889726043, 0),UDim2.new(0.96, 0, 0.837889612, 0)
		end
		TweenService:Create(
			Tapmain,
			TweenInfo.new(.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{Size = targetSize}
		):Play()
		TweenService:Create(
			Backgrond,
			TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
			{Size = targetSize2}
		):Play()
		openTap = not openTap
	end)

	Library.Taps = {
		Value = false
	}
	function Library.Taps.AddTap(options)
		local Title = {options.Title} or {"Tap"}

		local TextButton_2 = Instance.new("TextButton")
		local UICorner_23 = Instance.new("UICorner")

		TextButton_2.Parent = ScrollingTap
		TextButton_2.Name = "Kuy"
		spawn(function() while true do task.wait() TextButton_2.BackgroundColor3 = _G.Color end end)
		TextButton_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
		TextButton_2.BorderSizePixel = 0
		TextButton_2.Size = UDim2.new(0.941066206, 0, 0, 20)
		TextButton_2.ZIndex = 5
		TextButton_2.Font = Enum.Font.Ubuntu
		TextButton_2.Text = Title[1]
		TextButton_2.TextColor3 = Color3.fromRGB(255, 255, 255)
		TextButton_2.TextSize = 11.0000
		TextButton_2.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

		UICorner_23.CornerRadius = UDim.new(0, 2)
		UICorner_23.Parent = TextButton_2

		local Page = Instance.new("ScrollingFrame")
		Page.Name = "Page"
		Page.Parent = mainPage
		Page.Active = true
		Page.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		Page.BackgroundTransparency = 1.000
		Page.BorderColor3 = Color3.fromRGB(0, 0, 0)
		Page.BorderSizePixel = 0
		Page.Position = UDim2.new( 1.5, 0, 0, 0)
		Page.Size = UDim2.new(1.00116539, 0, 0.99999994, 0)
		Page.ScrollBarThickness = 1

		local LeftMain = Instance.new("ScrollingFrame")
		local UIListLayout = Instance.new("UIListLayout")
		local UIPadding = Instance.new("UIPadding")
		LeftMain.Name = "LeftMain"
		LeftMain.Parent = Page
		LeftMain.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		LeftMain.BackgroundTransparency = 1.000
		LeftMain.BorderColor3 = Color3.fromRGB(0, 0, 0)
		LeftMain.BorderSizePixel = 0
		LeftMain.Position = UDim2.new(0.0150000127, 0, -0.00258558267, 0)
		LeftMain.Size = UDim2.new(0.473, 0, 1, 0)
		LeftMain.CanvasSize = UDim2.new(0, 0, 0, 0)
		LeftMain.ScrollBarImageColor3 = Color3.fromRGB(25, 25, 25)
		LeftMain.ScrollBarThickness = 1

		UIListLayout.Parent = LeftMain
		UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
		UIListLayout.Padding = UDim.new(0, 5)

		UIPadding.Parent = LeftMain
		local RightMain = Instance.new("ScrollingFrame")
		local UIListLayout_4 = Instance.new("UIListLayout")
		local UIPadding_4 = Instance.new("UIPadding")
		RightMain.Name = "RightMain"
		RightMain.Parent = Page
		RightMain.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		RightMain.BackgroundTransparency = 1.000
		RightMain.BorderColor3 = Color3.fromRGB(0, 0, 0)
		RightMain.BorderSizePixel = 0
		RightMain.Position = UDim2.new(0.507999957, 0, -0.00258558267, 0)
		RightMain.Size = UDim2.new(.473, 0, 1, 0)
		RightMain.CanvasSize = UDim2.new(0, 0, 0, 0)
		RightMain.ScrollBarImageColor3 = Color3.fromRGB(25, 25, 25)
		RightMain.ScrollBarThickness = 1

		UIListLayout_4.Parent = RightMain
		UIListLayout_4.SortOrder = Enum.SortOrder.LayoutOrder
		UIListLayout_4.Padding = UDim.new(0, 5)

		UIPadding_4.Parent = RightMain

		UIListLayout_4:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
			Page.CanvasSize = UDim2.new(0, 0, 3, UIListLayout_4.AbsoluteContentSize.Y + UIListLayout.AbsoluteContentSize.Y + 350)
		end)

		UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
			Page.CanvasSize = UDim2.new(0, 0, 3, UIListLayout.AbsoluteContentSize.Y + UIListLayout_4.AbsoluteContentSize.Y + 350)
		end)

		if Library.Taps.Value == false then
			Library.Taps.Value = true
			TweenService:Create(
				Page,
				TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
				{Position = UDim2.new( 0, 0, 0, 0)}
			):Play()
		end

		TextButton_2.MouseButton1Click:Connect(function()
			for i, v in pairs(mainPage:GetChildren()) do
				if v:IsA("ScrollingFrame") then
					TweenService:Create(
						v,
						TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
						{Position = UDim2.new( 1.5, 0, 0, 0)}
					):Play()
				end
			end
			delay(1,function()
				TweenService:Create(
					Page,
					TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
					{Position = UDim2.new( 0, 0, 0, 0)}
				):Play()
			end)
		end)

		local getpage = function(...)
			local a = {
				...
			}
			if a[1] == 1 or a[1] == "Left" then
				return LeftMain
			elseif a[1] == 2 or a[1] == "Right" then
				return RightMain
			end
		end

		foldername = NameHub[1];
		filename = game.Players.LocalPlayer.Name.." Config.json"
		function SaveSettings()
			local HttpService = game:GetService("HttpService")
			local json = HttpService:JSONEncode(_G.Settings)
			if writefile then
				if isfolder(foldername) then
					if isfolder(foldername) then
						writefile(foldername.."/"..filename, json)
					else
						makefolder(foldername.."/"..filename)
						writefile(foldername.."/"..filename, json)
					end
				else
					makefolder(foldername)
					makefolder(foldername.."/"..filename)
					writefile(foldername.."/"..filename, json)
				end
			end
		end

		function LoadSettings()
			local HttpService = game:GetService("HttpService")
			if isfile(foldername.."/"..filename) then
				_G.Settings = HttpService:JSONDecode(readfile(foldername.."/"..filename))
				for i, v in pairs(_G.Settings) do
					print(i,v)
				end
			end
		end;LoadSettings()

		Library.Page = {}
		function Library.Page.AddPage(options)
			local Side = options.Side or 1

			local sections = Instance.new("Frame")
			local UICorner_2 = Instance.new("UICorner")
			local UIListLayout_2 = Instance.new("UIListLayout")
			local UIPadding_2 = Instance.new("UIPadding")
			sections.Name = "sections"
			sections.Parent = getpage(Side)
			sections.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
			sections.BorderColor3 = Color3.fromRGB(0, 0, 0)
			sections.BorderSizePixel = 0
			sections.Size = UDim2.new(1.00000012, 0, 0.941745579, 0)

			UICorner_2.CornerRadius = UDim.new(0, 5)
			UICorner_2.Parent = sections

			UIListLayout_2.Parent = sections
			UIListLayout_2.SortOrder = Enum.SortOrder.LayoutOrder
			UIListLayout_2.Padding = UDim.new(0, 5)

			UIPadding_2.Parent = sections
			UIPadding_2.PaddingLeft = UDim.new(0, 5)
			UIPadding_2.PaddingTop = UDim.new(0, 15)

			UIListLayout_2:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
				sections.Size = UDim2.new(1.00000012, 0, 0, UIListLayout_2.AbsoluteContentSize.Y + 35)
			end)

			Library.Main = {}
			function Library.Main.AddButton(options)
				local Buttons = {}
				local AddButton = Instance.new("TextButton")
				local UICorner_16 = Instance.new("UICorner")

				AddButton.Name = "AddButton"
				AddButton.Parent = sections
				spawn(function() while true do task.wait() AddButton.BackgroundColor3 = _G.Color end end)
				AddButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddButton.BorderSizePixel = 0
				AddButton.Size = UDim2.new(0.941066206, 0, 0, 20)
				AddButton.Font = Enum.Font.Ubuntu
				AddButton.Text = options.Title
				AddButton.TextColor3 = Color3.fromRGB(255, 255, 255)
				AddButton.TextSize = 11.000
				AddButton.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

				UICorner_16.CornerRadius = UDim.new(0, 2)
				UICorner_16.Parent = AddButton

				function Buttons.OnChanged(Callback)
					AddButton.MouseButton1Click:Connect(function()
						Callback()
						CircleClick(AddButton, Mouse.X, Mouse.Y)
						AddButton.TextSize = 0
						TweenService:Create(
							AddButton,
							TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
							{TextSize = 11.00}
						):Play()
					end)
				end
				wait(.2)
				return Buttons
			end
			function Library.Main.AddToggle(options)
				local Toggle = {}
				local AddToggle = Instance.new("Frame")
				local TextLabel_2 = Instance.new("TextLabel")
				local ToggleButton = Instance.new("TextButton")
				local ToggleMain = Instance.new("Frame")
				local UICorner_10 = Instance.new("UICorner")
				local UIStroke = Instance.new("UIStroke")
				local ImageLabel_2 = Instance.new("ImageLabel")
				local Slider = Instance.new("Frame")
				local UICorner_11 = Instance.new("UICorner")
				local ButtonText = Instance.new("TextLabel")
				local TextBox_2 = Instance.new("TextBox")
				local UICorner_12 = Instance.new("UICorner")
				local ValueFrame2 = Instance.new("Frame")
				local UICorner_13 = Instance.new("UICorner")
				local ValueFrame = Instance.new("Frame")
				local UICorner_14 = Instance.new("UICorner")
				local Frame = Instance.new("Frame")
				local UICorner_15 = Instance.new("UICorner")

				AddToggle.Name = "AddToggle"
				AddToggle.Parent = sections
				AddToggle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				AddToggle.BackgroundTransparency = 1.000
				AddToggle.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddToggle.BorderSizePixel = 0
				AddToggle.Position = UDim2.new(0, 0, 0.119217172, 0)
				AddToggle.Size = UDim2.new(0.949999988, 0, 0, 27)

				TextLabel_2.Parent = AddToggle
				TextLabel_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				TextLabel_2.BackgroundTransparency = 1.000
				TextLabel_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
				TextLabel_2.BorderSizePixel = 0
				TextLabel_2.Position = UDim2.new(0.191709846, 0, 0, 0)
				TextLabel_2.Size = UDim2.new(0.808290184, 0, 1, 0)
				TextLabel_2.Font = Enum.Font.Ubuntu
				TextLabel_2.Text = options.Title
				TextLabel_2.TextColor3 = Color3.fromRGB(255, 255, 255)
				TextLabel_2.TextSize = 10.000
				TextLabel_2.TextXAlignment = Enum.TextXAlignment.Left
				TextLabel_2.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

				ToggleButton.Name = "ToggleButton"
				ToggleButton.Parent = AddToggle
				ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ToggleButton.BackgroundTransparency = 1.000
				ToggleButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ToggleButton.BorderSizePixel = 0
				ToggleButton.Size = UDim2.new(1, 0, 1, 0)
				ToggleButton.Font = Enum.Font.SourceSans
				ToggleButton.Text = ""
				ToggleButton.TextColor3 = Color3.fromRGB(0, 0, 0)
				ToggleButton.TextSize = 14.000

				ToggleMain.Name = "ToggleMain"
				ToggleMain.Parent = AddToggle
				ToggleMain.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
				ToggleMain.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ToggleMain.BorderSizePixel = 0
				ToggleMain.Position = UDim2.new(0.0310880821, 0, 0.142857149, 0)
				ToggleMain.Size = UDim2.new(0, 20, 0, 20)

				UICorner_10.CornerRadius = UDim.new(0, 3)
				UICorner_10.Parent = ToggleMain

				spawn(function() while true do task.wait() UIStroke.Color = _G.Color end end)
				UIStroke.Transparency = 0.20000000298023224
				UIStroke.Parent = ToggleMain

				ImageLabel_2.Parent = ToggleMain
				ImageLabel_2.AnchorPoint = Vector2.new(0.5, 0.5)
				ImageLabel_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ImageLabel_2.BackgroundTransparency = 1.000
				ImageLabel_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ImageLabel_2.BorderSizePixel = 0
				ImageLabel_2.Position = UDim2.new(0.5, 0, 0.5, 0)
				ImageLabel_2.Size = UDim2.new(0.800000012, 0, 0.800000012, 0)
				ImageLabel_2.Image = "http://www.roblox.com/asset/?id=6031068421"
				ImageLabel_2.Visible = false
				spawn(function() while true do task.wait() ImageLabel_2.ImageColor3 = _G.Color end end)

				function Toggle.OnChanged(callback)
					local Num = Nummm
					if _G.Settings[options.Title .. Num] == true then
						options.Default = true
					else
						options.Default = false
					end
					if options.Default == true then
						ImageLabel_2.Visible = options.Default
					end
					ToggleButton.MouseButton1Click:Connect(function()
						ImageLabel_2.Visible = not options.Default
						_G.Settings[options.Title .. Num] = not options.Default
						pcall(callback, not options.Default)
						options.Default = not options.Default
						CircleClick(AddToggle, Mouse.X, Mouse.Y)
						SaveSettings()
					end)
					pcall(callback, options.Default)
				end
				wait(.2)
				Nummm = Nummm + 1
				return Toggle
			end

			function Library.Main.AddDropdown(options)
				local AddDropdow = Instance.new("Frame")
				local UICorner_3 = Instance.new("UICorner")
				local Main_2 = Instance.new("Frame")
				local UICorner_4 = Instance.new("UICorner")
				local AddDropdowTitel = Instance.new("TextLabel")
				local IconDow = Instance.new("Frame")
				local UICorner_5 = Instance.new("UICorner")
				local ImageLabel = Instance.new("ImageLabel")
				local DropButton = Instance.new("TextButton")
				local ScrollingItems = Instance.new("ScrollingFrame")
				local Drop = false
				local Dropdown = {}
				local DropTable = {}
				local Num = Nummm
				
				if type(options.Default) == "table" then
					DropTable = options.Default 
				end

				AddDropdow.Name = "AddDropdow"
				AddDropdow.Parent = sections
				AddDropdow.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
				AddDropdow.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddDropdow.BorderSizePixel = 0
				AddDropdow.ClipsDescendants = true
				AddDropdow.Position = UDim2.new(0, 0, 0.457219809, 0)
				AddDropdow.Size = UDim2.new(0.949999988, 0, 0, 27)

				UICorner_3.CornerRadius = UDim.new(0, 2)
				UICorner_3.Parent = AddDropdow

				Main_2.Name = "Main"
				Main_2.Parent = AddDropdow
				Main_2.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
				Main_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
				Main_2.BorderSizePixel = 0
				Main_2.Size = UDim2.new(1, 0, 0, 27)
				Main_2.ZIndex = 3

				UICorner_4.CornerRadius = UDim.new(0, 2)
				UICorner_4.Parent = Main_2

				AddDropdowTitel.Name = "AddDropdowTitel"
				AddDropdowTitel.Parent = Main_2
				AddDropdowTitel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				AddDropdowTitel.BackgroundTransparency = 1.000
				AddDropdowTitel.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddDropdowTitel.BorderSizePixel = 0
				AddDropdowTitel.Position = UDim2.new(0.0294061638, 0, 0, 0)
				AddDropdowTitel.Size = UDim2.new(0.808290184, 0, 1, 0)
				AddDropdowTitel.Font = Enum.Font.Ubuntu
				
				if options.Multi then
					_G.Settings[options.Title .. Num] = _G.Settings[options.Title .. Num] or DropTable
					AddDropdowTitel.Text = options.Title .. " : ".. table.concat(DropTable, ", ")
				else
					_G.Settings[options.Title .. Num] = _G.Settings[options.Title .. Num] or options.Default
					AddDropdowTitel.Text = options.Title .. " : ".. options.Default
				end
				AddDropdowTitel.TextColor3 = Color3.fromRGB(255, 255, 255)
				AddDropdowTitel.TextSize = 10.000
				AddDropdowTitel.TextXAlignment = Enum.TextXAlignment.Left
				AddDropdowTitel.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)
				AddDropdowTitel.ZIndex = 3

				IconDow.Name = "IconDow"
				IconDow.Parent = Main_2
				IconDow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
				IconDow.BackgroundTransparency = 1.000
				IconDow.BorderColor3 = Color3.fromRGB(0, 0, 0)
				IconDow.BorderSizePixel = 0
				IconDow.Position = UDim2.new(0.848167539, 0, 0.197037309, 0)
				IconDow.Size = UDim2.new(0, 15, 0, 15)
				IconDow.ZIndex = 3

				UICorner_5.CornerRadius = UDim.new(0, 2)
				UICorner_5.Parent = IconDow

				ImageLabel.Parent = IconDow
				ImageLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ImageLabel.BackgroundTransparency = 1.000
				ImageLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ImageLabel.BorderSizePixel = 0
				ImageLabel.Rotation = 90.000
				ImageLabel.Size = UDim2.new(1, 0, 1, 0)
				ImageLabel.Image = "http://www.roblox.com/asset/?id=6031094680"
				ImageLabel.ZIndex = 3

				DropButton.Name = "DropButton"
				DropButton.Parent = Main_2
				DropButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				DropButton.BackgroundTransparency = 1.000
				DropButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
				DropButton.BorderSizePixel = 0
				DropButton.Size = UDim2.new(1, 0, 1, 0)
				DropButton.Font = Enum.Font.SourceSans
				DropButton.Text = ""
				DropButton.TextColor3 = Color3.fromRGB(0, 0, 0)
				DropButton.TextSize = 14.000
				DropButton.ZIndex = 3

				ScrollingItems.Name = "ScrollingItems"
				ScrollingItems.Parent = AddDropdow
				ScrollingItems.Active = true
				ScrollingItems.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ScrollingItems.BackgroundTransparency = 1.000
				ScrollingItems.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ScrollingItems.BorderSizePixel = 0
				ScrollingItems.Position = UDim2.new(0, 0, 0.305555701, 0)
				ScrollingItems.Size = UDim2.new(1, 0, 0, 75)
				ScrollingItems.ScrollBarImageColor3 = Color3.fromRGB(0, 0, 0)
				ScrollingItems.ScrollBarThickness = 1

				DropButton.MouseButton1Click:Connect(function()
					TweenService:Create(
						AddDropdow,
						TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
						{
							Size = Drop and UDim2.new(0.949999988, 0, 0, 27) or UDim2.new(0.949999988, 0, 0, 135)
						}
					):Play()
					Drop = not Drop
				end)

				function Dropdown.resetValue()
					for i, v in next, ScrollingItems:GetChildren() do
						if v:IsA("TextButton") then 
							v:Destroy()
						end
					end
				end

				function Dropdown.addValue(Text)
					local TextButton = Instance.new("TextButton")
					local UICorner_6 = Instance.new("UICorner")
					local UIListLayout_3 = Instance.new("UIListLayout")
					local UIPadding_3 = Instance.new("UIPadding")
					TextButton.Parent = ScrollingItems
					spawn(function() while true do task.wait() TextButton.BackgroundColor3 = _G.Color end end)
					TextButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
					TextButton.BorderSizePixel = 0
					TextButton.Size = UDim2.new(0.941066206, 0, 0, 20)
					TextButton.Text = Text
					TextButton.Font = Enum.Font.Ubuntu
					TextButton.TextColor3 = Color3.fromRGB(255, 255, 255)
					TextButton.TextSize = 11.000
					TextButton.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

					UICorner_6.CornerRadius = UDim.new(0, 2)
					UICorner_6.Parent = TextButton

					UIListLayout_3.Parent = ScrollingItems
					UIListLayout_3.SortOrder = Enum.SortOrder.LayoutOrder
					UIListLayout_3.Padding = UDim.new(0, 5)

					UIPadding_3.Parent = ScrollingItems
					UIPadding_3.PaddingLeft = UDim.new(0, 5)
					UIPadding_3.PaddingTop = UDim.new(0, 5)

					if not options.Multi then
						AddDropdowTitel.Text = options.Title .. " : ".. _G.Settings[options.Title .. Num]
						TextButton.MouseButton1Click:Connect(function()
							AddDropdowTitel.Text = options.Title .. " : "..Text
							options.Callback(Text)
							_G.Settings[options.Title .. Num] = Text
							SaveSettings()
						end)
					else
						AddDropdowTitel.Text = options.Title .. " : ".. table.concat(_G.Settings[options.Title .. Num], ", ")
						TextButton.MouseButton1Click:Connect(function()
							if not table.find(DropTable, Text) then
								table.insert(DropTable, Text)
								options.Callback(DropTable, Text)
								AddDropdowTitel.Text = options.Title ..": "..table.concat(DropTable, ", ")..""
								_G.Settings[options.Title .. Num] = DropTable
								SaveSettings()
							else
								for i2, v2 in pairs(DropTable) do
									if v2 == Text then
										table.remove(DropTable, i2)
										AddDropdowTitel.Text = options.Title ..": "..table.concat(DropTable, ", ")
									end
								end
								options.Callback(DropTable, Text)
								_G.Settings[options.Title .. Num] = DropTable
								SaveSettings()
							end
						end)
					end
					wait(.2)
				end

				for _,_v in next, options.Lists do
					Dropdown.addValue(_v)
				end

				if options.Multi then
					options.Callback(DropTable)
				else
					options.Callback(options.Default)
				end
				Nummm = Nummm + 1
				wait(.2)
				return Dropdown
			end
			function Library.Main.AddLabel(options)
				local AddLabel = Instance.new("Frame")
				local TextLabel = Instance.new("TextLabel")
				local LabelTable = {}

				AddLabel.Name = "AddLabel"
				AddLabel.Parent = sections
				AddLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				AddLabel.BackgroundTransparency = 1.000
				AddLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddLabel.BorderSizePixel = 0
				AddLabel.Size = UDim2.new(0.949999988, 0, 0, 27)

				TextLabel.Parent = AddLabel
				TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				TextLabel.BackgroundTransparency = 1.000
				TextLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
				TextLabel.BorderSizePixel = 0
				TextLabel.Size = UDim2.new(1, 0, 1, 0)
				TextLabel.Font = Enum.Font.Ubuntu
				TextLabel.Text = options.Title
				TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
				TextLabel.TextSize = 10.000
				TextLabel.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

				LabelTable.resetValue,LabelTable.setColor = function(Title)
					TextLabel.Text = Title
				end,function(Color)
					TextLabel.TextColor3 = Color
				end

				wait(.2)
				return LabelTable
			end
			function Library.Main.AddInput(options)
				local AddInput = Instance.new("Frame")
				local UICorner_7 = Instance.new("UICorner")
				local Main_3 = Instance.new("Frame")
				local UICorner_8 = Instance.new("UICorner")
				local TextBox = Instance.new("TextBox")
				local UICorner_9 = Instance.new("UICorner")
				local inPutT = {}

				AddInput.Name = "AddInput"
				AddInput.Parent = sections
				AddInput.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
				AddInput.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddInput.BorderSizePixel = 0
				AddInput.ClipsDescendants = true
				AddInput.Position = UDim2.new(0, 0, 0.749452055, 0)
				AddInput.Size = UDim2.new(0.949999988, 0, 0, 35)

				UICorner_7.CornerRadius = UDim.new(0, 2)
				UICorner_7.Parent = AddInput

				Main_3.Name = "Main"
				Main_3.Parent = AddInput
				Main_3.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
				Main_3.BorderColor3 = Color3.fromRGB(0, 0, 0)
				Main_3.BorderSizePixel = 0
				Main_3.ClipsDescendants = true
				Main_3.Size = UDim2.new(1, 0, 1, 0)

				UICorner_8.CornerRadius = UDim.new(0, 2)
				UICorner_8.Parent = Main_3

				TextBox.Parent = Main_3
				TextBox.AnchorPoint = Vector2.new(0.5, 0.5)
				TextBox.BackgroundColor3 = Color3.fromRGB(23, 23, 23)
				TextBox.BorderColor3 = Color3.fromRGB(0, 0, 0)
				TextBox.BorderSizePixel = 0
				TextBox.Position = UDim2.new(0.510624409, 0, 0.499999732, 0)
				TextBox.Size = UDim2.new(0.800000012, 0, 0.459695935, 0)
				TextBox.Font = Enum.Font.SourceSansBold
				TextBox.PlaceholderColor3 = Color3.fromRGB(178, 178, 178)
				TextBox.PlaceholderText = options.Placeholder
				TextBox.Text = options.Default
				TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
				TextBox.TextSize = 12.000

				UICorner_9.CornerRadius = UDim.new(0, 5)
				UICorner_9.Parent = TextBox

				function inPutT.OnChanged(Callback)
					TextBox.FocusLost:Connect(function()
						Callback(TextBox.Text)
					end)
				end
				return inPutT
			end
function Library.Main.AddSlider(options)
    local Slider = Instance.new("Frame")
    local UICorner_11 = Instance.new("UICorner")
    local ButtonText = Instance.new("TextLabel")
    local TextBox_2 = Instance.new("TextBox")
    local UICorner_12 = Instance.new("UICorner")
    local ValueFrame2 = Instance.new("Frame")
    local UICorner_13 = Instance.new("UICorner")
    local ValueFrame = Instance.new("Frame")
    local UICorner_14 = Instance.new("UICorner")
    local Frame = Instance.new("Frame")
    local UICorner_15 = Instance.new("UICorner")

    Slider.Name = "Slider"
    Slider.Parent = sections
    Slider.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    Slider.BorderColor3 = Color3.fromRGB(0, 0, 0)
    Slider.BorderSizePixel = 0
    Slider.Position = UDim2.new(0, 0, 0.718812883, 0)
    Slider.Size = UDim2.new(0.949999988, 0, 0, 31)

    UICorner_11.CornerRadius = UDim.new(0, 2)
    UICorner_11.Parent = Slider

    ButtonText.Name = "ButtonText"
    ButtonText.Parent = Slider
    ButtonText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    ButtonText.BackgroundTransparency = 1.000
    ButtonText.BorderColor3 = Color3.fromRGB(0, 0, 0)
    ButtonText.BorderSizePixel = 0
    ButtonText.Position = UDim2.new(0.0462909453, 0, 0.191856131, 0)
    ButtonText.Size = UDim2.new(0, 148, 0, 9)
    ButtonText.Font = Enum.Font.FredokaOne
    ButtonText.Text = options.Title
    ButtonText.TextColor3 = Color3.fromRGB(255, 255, 255)
    ButtonText.TextSize = 10.000
    ButtonText.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)
    ButtonText.TextXAlignment = Enum.TextXAlignment.Left

    TextBox_2.Parent = Slider
    TextBox_2.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    TextBox_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
    TextBox_2.BorderSizePixel = 0
    TextBox_2.Position = UDim2.new(0.723164678, 0, 0.175377637, 0)
    TextBox_2.Size = UDim2.new(0, 45, 0, 10)
    TextBox_2.Font = Enum.Font.Ubuntu
    TextBox_2.Text = options.Default
    TextBox_2.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextBox_2.TextSize = 9.000

    UICorner_12.CornerRadius = UDim.new(0, 2)
    UICorner_12.Parent = TextBox_2

    ValueFrame2.Name = "ValueFrame2"
    ValueFrame2.Parent = Slider
    ValueFrame2.AnchorPoint = Vector2.new(0.5, 0.5)
    ValueFrame2.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    ValueFrame2.BorderColor3 = Color3.fromRGB(0, 0, 0)
    ValueFrame2.BorderSizePixel = 0
    ValueFrame2.Position = UDim2.new(0.5, 0, 0.800000012, 0)
    ValueFrame2.Size = UDim2.new(0, 175, 0, 2)

    UICorner_13.CornerRadius = UDim.new(0, 5)
    UICorner_13.Parent = ValueFrame2

    ValueFrame.Name = "ValueFrame"
    ValueFrame.Parent = ValueFrame2
    spawn(function() while true do task.wait() ValueFrame.BackgroundColor3 = _G.Color end end)
    ValueFrame.BorderColor3 = Color3.fromRGB(0, 0, 0)
    ValueFrame.BorderSizePixel = 0
    --ValueFrame.Position = UDim2.new(0, 0, 0, 0)
    ValueFrame.Size = UDim2.new(1, 0, 1, 0)

    UICorner_14.CornerRadius = UDim.new(0, 5)
    UICorner_14.Parent = ValueFrame

    Frame.Parent = ValueFrame
    Frame.AnchorPoint = Vector2.new(0.5, 0.5)
    spawn(function() while true do task.wait() Frame.BackgroundColor3 = _G.Color end end)
    Frame.BorderColor3 = Color3.fromRGB(0, 0, 0)
    Frame.BorderSizePixel = 0
    Frame.Position = UDim2.new(1, 0, 0, 0)
    Frame.Size = UDim2.new(0, 10, 0, 10)

    UICorner_15.CornerRadius = UDim.new(1, 8)
    UICorner_15.Parent = Frame

    local SliderTable = {}

    function SliderTable.OnChanged(callback)
        local Num = Nummm
        ValueFrame:TweenSize(UDim2.new((_G.Settings[options.Title .. Num] or options.Default or 0) / options.Max, 0, 1, 0), "Out", "Back", 0.2, true)
        callback(_G.Settings[options.Title .. Num] or options.Default)
        TextBox_2.Text = _G.Settings[options.Title .. Num] or options.Default
        TextBox_2.FocusLost:Connect(function()
            if TextBox_2.Text == "" then
                TextBox_2.Text = options.Default
            end
            if tonumber(TextBox_2.Text) > options.Max then
                TextBox_2.Text = options.Max
            end
            if tonumber(TextBox_2.Text) <= options.Min then
                TextBox_2.Text = options.Min
            end

            ValueFrame:TweenSize(UDim2.new((TextBox_2.Text or 0) / options.Max, 0, 1, 0), "Out", "Back", 0.2, true)
            TextBox_2.Text = tostring(TextBox_2.Text)
            pcall(callback, TextBox_2.Text)
            _G.Settings[options.Title .. Num] = tonumber(TextBox_2.Text)
            SaveSettings()
        end)

        local function move(input)
            local pos = UDim2.new(
                math.clamp((input.Position.X - ValueFrame.AbsolutePosition.X) / ValueFrame.AbsoluteSize.X, 0, 1),
                0,
                0,
                0
            )
            local pos1 = UDim2.new(
                math.clamp((input.Position.X - ValueFrame.AbsolutePosition.X) / ValueFrame.AbsoluteSize.X, 0, 1),
                0,
                1,
                0
            )

            ValueFrame:TweenSize(pos1, "Out", "Sine", 0.2, true)
            local value = math.floor(((pos.X.Scale * options.Max) / options.Max) * (options.Max - options.Min) + options.Min)
            TextBox_2.Text = tostring(value)
            callback(value)
            _G.Settings[options.Title .. Num] = value
            SaveSettings()
        end

local isDragging = false  -- Flag to track dragging state
local initialTouchPosition  -- Store initial touch position

Frame.InputBegan:Connect(function(input)
  if input.UserInputType == Enum.UserInputType.Touch then
    isDragging = true
    initialTouchPosition = input.Position
  end
end)

Frame.InputEnded:Connect(function(input)
  if input.UserInputType == Enum.UserInputType.Touch then
    isDragging = false
  end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
  if isDragging and input.UserInputType == Enum.UserInputType.Touch then
    local deltaX = input.Position.X - initialTouchPosition.X
    local relativeX = deltaX / ValueFrame.AbsoluteSize.X  -- Normalize to 0-1 range

    -- Calculate new value based on relative position
    local newValue = math.clamp(
      ((relativeX * options.Max) / options.Max) * (options.Max - options.Min) + options.Min,
      options.Min,
      options.Max
    )

    -- Update UI elements and settings
    ValueFrame:TweenSize(UDim2.new(relativeX, 0, 1, 0), "Out", "Sine", 0.2, true)
    TextBox_2.Text = tostring(newValue)
    callback(newValue)
    _G.Settings[options.Title .. Num] = newValue
    SaveSettings()
  end
end)

        Nummm = Nummm + 1
    end

    return SliderTable
end



			function Library.Main.AddColorpicker(options)
				local AddColorpicker = Instance.new("Frame")
				local ColorpickerT = {}
				local UICorner_17 = Instance.new("UICorner")
				local ColorpickerTitel_2 = Instance.new("TextLabel")
				local NowColor = Instance.new("Frame")
				local UICorner_18 = Instance.new("UICorner")
				local ColorSelection = Instance.new("Frame")
				local Color = Instance.new("ImageLabel")
				local UIStroke_2 = Instance.new("UIStroke")
				local ColorSelection_2 = Instance.new("ImageLabel")
				local Hue = Instance.new("Frame")
				local UIGradient = Instance.new("UIGradient")
				local UICorner_19 = Instance.new("UICorner")
				local HueSelection = Instance.new("ImageLabel")
				local UICorner_20 = Instance.new("UICorner")
				local ColorpickerButton = Instance.new("TextButton")


				AddColorpicker.Name = "AddColorpicker"
				AddColorpicker.Parent = sections
				AddColorpicker.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
				AddColorpicker.BorderColor3 = Color3.fromRGB(0, 0, 0)
				AddColorpicker.BorderSizePixel = 0
				AddColorpicker.Position = UDim2.new(0, 0, 0.297402054, 0)
				AddColorpicker.Size = UDim2.new(0.949999988, 0, 0, 27)

				UICorner_17.CornerRadius = UDim.new(0, 2)
				UICorner_17.Parent = AddColorpicker

				ColorpickerTitel_2.Name = "ColorpickerTitel"
				ColorpickerTitel_2.Parent = AddColorpicker
				ColorpickerTitel_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ColorpickerTitel_2.BackgroundTransparency = 1.000
				ColorpickerTitel_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ColorpickerTitel_2.BorderSizePixel = 0
				ColorpickerTitel_2.Position = UDim2.new(0.0294061638, 0, 0, 0)
				ColorpickerTitel_2.Size = UDim2.new(0.808290184, 0, 1, 0)
				ColorpickerTitel_2.Font = Enum.Font.Ubuntu
				ColorpickerTitel_2.Text = options.Title
				ColorpickerTitel_2.TextColor3 = Color3.fromRGB(255, 255, 255)
				ColorpickerTitel_2.TextSize = 10.000
				ColorpickerTitel_2.TextXAlignment = Enum.TextXAlignment.Left
				ColorpickerTitel_2.FontFace = Font.new("rbxasset://fonts/families/Ubuntu.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

				NowColor.Name = "NowColor"
				NowColor.Parent = AddColorpicker
				NowColor.BackgroundColor3 = Color3.fromRGB(255, 0, 89)
				NowColor.BorderColor3 = Color3.fromRGB(0, 0, 0)
				NowColor.BorderSizePixel = 0
				NowColor.Position = UDim2.new(0.806282699, 0, 0.159999996, 0)
				NowColor.Size = UDim2.new(0, 31, 0, 16)

				UICorner_18.CornerRadius = UDim.new(0, 2)
				UICorner_18.Parent = NowColor

				ColorSelection.Name = "ColorSelection"
				ColorSelection.Parent = AddColorpicker
				ColorSelection.AnchorPoint = Vector2.new(0.5, 0.5)
				ColorSelection.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
				ColorSelection.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ColorSelection.BorderSizePixel = 0
				ColorSelection.Visible = false
				ColorSelection.Position = UDim2.new(0.50000006, 0, 1.67629671, 0)
				ColorSelection.Size = UDim2.new(0, 137, 0, 106)
				ColorSelection.ZIndex = 2

				Color.Name = "Color"
				Color.Parent = ColorSelection
				Color.BackgroundColor3 = Color3.fromRGB(255, 0, 89)
				Color.BorderColor3 = Color3.fromRGB(0, 0, 0)
				Color.BorderSizePixel = 0
				Color.Position = UDim2.new(0.0656934306, 0, 0.148202568, 0)
				Color.Size = UDim2.new(0, 77, 0, 79)
				Color.ZIndex = 3
				Color.Image = "rbxassetid://4155801252"

				UIStroke_2.Color = Color3.fromRGB(255, 0, 89)
				UIStroke_2.Parent = Color

				ColorSelection_2.Name = "ColorSelection"
				ColorSelection_2.Parent = Color
				ColorSelection_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ColorSelection_2.BackgroundTransparency = 1.000
				ColorSelection_2.AnchorPoint = Vector2.new(0.5, 0.5)
				ColorSelection_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ColorSelection_2.BorderSizePixel = 0
				ColorSelection_2.Position = UDim2.new(1,0,0,0)
				ColorSelection_2.Size = UDim2.new(0, 10, 0, 10)
				ColorSelection_2.ZIndex = 3
				ColorSelection_2.Image = "http://www.roblox.com/asset/?id=4805639000"

				Hue.Name = "Hue"
				Hue.Parent = ColorSelection
				Hue.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				Hue.BorderColor3 = Color3.fromRGB(0, 0, 0)
				Hue.BorderSizePixel = 0
				Hue.Position = UDim2.new(0.686131358, 0, 0.142243013, 0)
				Hue.Size = UDim2.new(0, 15, 0, 80)
				Hue.ZIndex = 2

				UIGradient.Color = ColorSequence.new{ColorSequenceKeypoint.new(0.00, Color3.fromRGB(255, 0, 0)), ColorSequenceKeypoint.new(0.19, Color3.fromRGB(255, 247, 0)), ColorSequenceKeypoint.new(0.38, Color3.fromRGB(34, 255, 0)), ColorSequenceKeypoint.new(0.56, Color3.fromRGB(0, 255, 247)), ColorSequenceKeypoint.new(0.74, Color3.fromRGB(0, 17, 255)), ColorSequenceKeypoint.new(0.89, Color3.fromRGB(238, 0, 255)), ColorSequenceKeypoint.new(1.00, Color3.fromRGB(255, 0, 4))}
				UIGradient.Rotation = 270
				UIGradient.Parent = Hue

				UICorner_19.CornerRadius = UDim.new(0, 2)
				UICorner_19.Parent = Hue

				HueSelection.Name = "HueSelection"
				HueSelection.Parent = Hue
				HueSelection.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				HueSelection.BackgroundTransparency = 1.000
				HueSelection.BorderColor3 = Color3.fromRGB(0, 0, 0)
				HueSelection.BorderSizePixel = 0
				HueSelection.AnchorPoint = Vector2.new(0.5, 0.5)
				HueSelection.Position = UDim2.new(0.5, 0, 0.0, 0)
				HueSelection.Size = UDim2.new(0, 10, 0, 10)
				HueSelection.ZIndex = 3
				HueSelection.Image = "http://www.roblox.com/asset/?id=4805639000"

				UICorner_20.CornerRadius = UDim.new(0, 5)
				UICorner_20.Parent = ColorSelection

				ColorpickerButton.Name = "ColorpickerButton"
				ColorpickerButton.Parent = AddColorpicker
				ColorpickerButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
				ColorpickerButton.BackgroundTransparency = 1.000
				ColorpickerButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
				ColorpickerButton.BorderSizePixel = 0
				ColorpickerButton.Size = UDim2.new(1, 0, 1, 0)
				ColorpickerButton.Font = Enum.Font.SourceSans
				ColorpickerButton.Text = ""
				ColorpickerButton.TextColor3 = Color3.fromRGB(0, 0, 0)
				ColorpickerButton.TextSize = 14.000

				local HHHHH = false
				local Num = Nummm
				function ColorpickerT.OnChanged(callback)
					local OldToggleColor = Color3.fromRGB(0, 0, 0)
					local OldColor = Color3.fromRGB(0, 0, 0)
					local OldColorSelectionPosition = nil
					local OldHueSelectionPosition = nil
					local ColorH, ColorS, ColorV = 1, 1, 1
					local RainbowColorPicker = false
					local ColorPickerInput = nil
					local ColorInput = nil
					local HueInput = nil
					local RunService = game:GetService("RunService")

					local function UpdateColorPicker(nope)
						NowColor.BackgroundColor3 = Color3.fromHSV(ColorH, ColorS, ColorV)
						Color.BackgroundColor3 = Color3.fromHSV(ColorH, 1, 1)
						_G.Settings[options.Title .. Num] = Color.BackgroundColor3
						SaveSettings()
						pcall(callback, NowColor.BackgroundColor3)
					end

					ColorH = 1 - (math.clamp(HueSelection.AbsolutePosition.Y - Hue.AbsolutePosition.Y, 0, Hue.AbsoluteSize.Y) / Hue.AbsoluteSize.Y)
					ColorS = (math.clamp(ColorSelection_2.AbsolutePosition.X - Color.AbsolutePosition.X, 0, Color.AbsoluteSize.X) / Color.AbsoluteSize.X)
					ColorV = 1 - (math.clamp(ColorSelection_2.AbsolutePosition.Y - Color.AbsolutePosition.Y, 0, Color.AbsoluteSize.Y) / Color.AbsoluteSize.Y)

					NowColor.BackgroundColor3 = _G.Settings[options.Title .. Num] or options.Preset
					Color.BackgroundColor3 = _G.Settings[options.Title .. Num] or options.Preset
					pcall(callback, NowColor.BackgroundColor3)

					ColorpickerButton.MouseButton1Click:Connect(function()
						ColorSelection.Visible = not HHHHH
						HHHHH = not HHHHH
						Page.ScrollingEnabled = not HHHHH
					end)

					Color.InputBegan:Connect(function(input)
						if input.UserInputType == Enum.UserInputType.MouseButton1 then
							if ColorInput then
								ColorInput:Disconnect()
							end

							ColorInput = RunService.RenderStepped:Connect(function()
								local ColorX = (math.clamp(Mouse.X - Color.AbsolutePosition.X, 0, Color.AbsoluteSize.X) / Color.AbsoluteSize.X)
								local ColorY = (math.clamp(Mouse.Y - Color.AbsolutePosition.Y, 0, Color.AbsoluteSize.Y) / Color.AbsoluteSize.Y)
								ColorSelection_2.Position = UDim2.new(ColorX, 0, ColorY, 0)
								ColorS = ColorX
								ColorV = 1 - ColorY

								UpdateColorPicker(true)
							end)
						end
					end)

					Color.InputEnded:Connect(function(input)
						if input.UserInputType == Enum.UserInputType.MouseButton1 then
							if ColorInput then
								ColorInput:Disconnect()
							end
						end
					end)

					Hue.InputBegan:Connect(function(input)
						if input.UserInputType == Enum.UserInputType.MouseButton1 then
							if HueInput then
								HueInput:Disconnect()
							end

							HueInput = RunService.RenderStepped:Connect(function()
								local HueY = (math.clamp(Mouse.Y - Hue.AbsolutePosition.Y, 0, Hue.AbsoluteSize.Y) / Hue.AbsoluteSize.Y)
								HueSelection.Position = UDim2.new(0.48, 0, HueY, 0)
								ColorH = 1 - HueY

								UpdateColorPicker(true)
							end)
						end
					end)

					Hue.InputEnded:Connect(function(input)
						if input.UserInputType == Enum.UserInputType.MouseButton1 then
							if HueInput then
								HueInput:Disconnect()
							end
						end
					end)
				end
				Nummm = Nummm + 1
				wait(.2)
				return ColorpickerT
			end
			spawn(function()
				while task.wait(1) do
					xpcall(
						function()
							if (_G.NowSize ~= _G.Size or _G.NowSize ~= 300) then
								_G.NowSize = _G.Size or 247
								TweenService:Create(
									Main,
									TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
									{Size = UDim2.new(0, _G.SizeY, 0, _G.NowSize)}
								):Play()
								TweenService:Create(
									ButrtonTap,
									TweenInfo.new(0.2,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
									{Size = UDim2.new(0, _G.SizeIcon, 0.701575637, 0)}
								):Play()
							end
						end,
						errorHandler
					)
				end
			end)
			return Library.Main
		end
		return Library.Page
	end
	return Library.Taps
end



local Win = Library.AddWindown({
	["Name Hub"] = "Reach Hub - GG"
})
local Tap = Win.AddTap({
	Title = "General"
})

local Page = Tap.AddPage({ Side = "Left", })

local Label = Page.AddLabel({
	Title = " // General \\ "
})

Label.resetValue("// 123 \\")
Label.setColor(Color3.fromRGB(255, 0, 111))

local Button = Page.AddButton({ Title = "123", })
Button.OnChanged(function()
	print(123)
end)

local Toggle = Page.AddToggle({Title = "Test", Default = false})
Toggle.OnChanged(function(v)
	print(v)
end)

local Dropdow = Page.AddDropdown({ Title = "Dropdow", Multi = false, Default = "Melee", Lists = {"Melee", "Sword"}, Callback = function(v)
	print(v)
end})

local ResetVelua = Page.AddButton({ Title = "123", })
ResetVelua.OnChanged(function()
	Dropdow.resetValue()
	for _,_v in next, {"Melee2","Sword2"} do
		Dropdow.addValue(_v)
	end
end)

local Dropdow = Page.AddDropdown({ Title = "Dropdow", Multi = true, Default = {"Melee"}, Lists = {"Melee", "Sword"}, Callback = function(v)
	print(v)
end})

local Input = Page.AddInput({ Default = "AddInput", Placeholder = "Your Hee"})
Input.OnChanged(function(v)
	print(v)
end)

local Slider = Page.AddSlider({ Title = "AddSlider", Default = 10, Min = -100, Max = 100})
Slider.OnChanged(function(v)
	print(v)
end)

local Colorpicker = Page.AddColorpicker({ Title = "Colorpicker", Preset = Color3.fromRGB(255, 0, 4)})
Colorpicker.OnChanged(function(v)
	print(v)
end)


local Page2 = Tap.AddPage({ Side = "Right", })

local Label = Page2.AddLabel({
	Title = "// General \\"
})

local Button = Page2.AddButton({ Title = "123", })
Button.OnChanged(function()
	print(123)
end)

local Toggle = Page2.AddToggle({Title = "Test", Default = false})
Toggle.OnChanged(function(v)
	print(v)
end)

local Dropdow = Page2.AddDropdown({ Title = "Dropdow", Multi = false, Default = "Melee", Lists = {"Melee", "Sword"}, Callback = function(v)
	print(v)
end})

local ResetVelua = Page2.AddButton({ Title = "เก", })
ResetVelua.OnChanged(function()
	Dropdow.resetValue()
	for _,_v in next, {"Melee2","Sword2"} do
		Dropdow.addValue(_v)
	end
end)

local Dropdow = Page2.AddDropdown({ Title = "Dropdow", Multi = true, Default = {"Melee"}, Lists = {"Melee", "Sword"}, Callback = function(v)
	print(v)
end})

local Input = Page2.AddInput({ Default = "AddInput", Placeholder = "Your Hee"})
Input.OnChanged(function(v)
	print(v)
end)

local Slider = Page2.AddSlider({ Title = "AddSlider", Default = 10, Min = -100, Max = 100})
Slider.OnChanged(function(v)
	print(v)
end)

local Colorpicker = Page2.AddColorpicker({ Title = "Colorpicker", Preset = Color3.fromRGB(255, 0, 4)})
Colorpicker.OnChanged(function(v)
	print(v)
end)

local Setting = Win.AddTap({ Title = "Setting"})
local PageSetting = Setting.AddPage({ Side = "Left", })

PageSetting.AddLabel({
	Title = "// Setting UI \\"
})

local SizeUi = PageSetting.AddSlider({ Title = "Size Ui X", Default = 247, Min = 1, Max = 600})
SizeUi.OnChanged(function(v)
	_G.Size = v
end)
local SizeUi = PageSetting.AddSlider({ Title = "Size Ui Y", Default = 450, Min = 1, Max = 750})
SizeUi.OnChanged(function(v)
	_G.SizeY = v
end)
local SizeUi = PageSetting.AddSlider({ Title = "Size Ui Icon", Default = 20, Min = 1, Max = 750})
SizeUi.OnChanged(function(v)
	_G.SizeIcon = v
end)

local UIColor = PageSetting.AddColorpicker({ Title = "UI Color", Preset = Color3.fromRGB(255, 0, 89)})
UIColor.OnChanged(function(v)
	_G.Color = v
end)
