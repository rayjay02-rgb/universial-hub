--===================================================
-- FSU ULTRA // NEXUS EDITION v4
-- CLEAN GUI EDITION
--===================================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

--==================================================
-- CLEANUP
--==================================================

local Old = PlayerGui:FindFirstChild("FSU_ULTRA")
if Old then
	Old:Destroy()
end

--==================================================
-- STATE
--==================================================

local State = {
	Theme = "Ultra Purple",
	Minimized = false,
	Hidden = false,

	Main = false,
	Combat = false,
	ESP = false,
	Names = false,

	Speed = 16,
	Multiplier = 1,
	Range = 15,

	Aimbot = false,
	TeamCheck = true,
	AimPart = "Head",
	AimbotSmoothness = 0.3,
	AimbotFOV = 60,

	CurrentEmote = nil
}

--==================================================
-- THEMES
--==================================================

local Themes = {

	["Ultra Purple"] = {
		Accent = Color3.fromRGB(170,70,255),
		Accent2 = Color3.fromRGB(90,30,170),
		Background = Color3.fromRGB(4,5,14),
		Panel = Color3.fromRGB(9,11,25),
		Panel2 = Color3.fromRGB(13,15,34),
		Button = Color3.fromRGB(17,19,42),
		Text = Color3.fromRGB(245,242,255),
		SubText = Color3.fromRGB(165,163,190)
	},

	Blue = {
		Accent = Color3.fromRGB(45,145,255),
		Accent2 = Color3.fromRGB(20,70,180),
		Background = Color3.fromRGB(3,7,17),
		Panel = Color3.fromRGB(7,15,32),
		Panel2 = Color3.fromRGB(10,21,43),
		Button = Color3.fromRGB(12,28,54),
		Text = Color3.fromRGB(240,248,255),
		SubText = Color3.fromRGB(155,185,220)
	},

	Green = {
		Accent = Color3.fromRGB(40,225,115),
		Accent2 = Color3.fromRGB(15,125,65),
		Background = Color3.fromRGB(3,12,8),
		Panel = Color3.fromRGB(6,23,15),
		Panel2 = Color3.fromRGB(9,32,21),
		Button = Color3.fromRGB(11,40,25),
		Text = Color3.fromRGB(235,255,242),
		SubText = Color3.fromRGB(150,205,175)
	},

	Red = {
		Accent = Color3.fromRGB(255,55,70),
		Accent2 = Color3.fromRGB(160,20,35),
		Background = Color3.fromRGB(15,3,6),
		Panel = Color3.fromRGB(29,7,12),
		Panel2 = Color3.fromRGB(39,9,15),
		Button = Color3.fromRGB(52,13,20),
		Text = Color3.fromRGB(255,240,242),
		SubText = Color3.fromRGB(215,160,170)
	},

	Yellow = {
		Accent = Color3.fromRGB(255,205,45),
		Accent2 = Color3.fromRGB(170,125,15),
		Background = Color3.fromRGB(15,13,3),
		Panel = Color3.fromRGB(28,25,7),
		Panel2 = Color3.fromRGB(38,34,9),
		Button = Color3.fromRGB(49,44,11),
		Text = Color3.fromRGB(255,252,225),
		SubText = Color3.fromRGB(210,195,145)
	},

	Gray = {
		Accent = Color3.fromRGB(160,170,190),
		Accent2 = Color3.fromRGB(90,95,110),
		Background = Color3.fromRGB(9,10,14),
		Panel = Color3.fromRGB(20,22,28),
		Panel2 = Color3.fromRGB(27,30,37),
		Button = Color3.fromRGB(35,39,48),
		Text = Color3.fromRGB(242,244,248),
		SubText = Color3.fromRGB(165,170,180)
	},

	Black = {
		Accent = Color3.fromRGB(255,255,255),
		Accent2 = Color3.fromRGB(100,100,100),
		Background = Color3.fromRGB(0,0,0),
		Panel = Color3.fromRGB(7,7,8),
		Panel2 = Color3.fromRGB(12,12,13),
		Button = Color3.fromRGB(19,19,20),
		Text = Color3.fromRGB(255,255,255),
		SubText = Color3.fromRGB(165,165,165)
	},

	White = {
		Accent = Color3.fromRGB(45,45,55),
		Accent2 = Color3.fromRGB(120,120,130),
		Background = Color3.fromRGB(230,232,238),
		Panel = Color3.fromRGB(247,247,250),
		Panel2 = Color3.fromRGB(255,255,255),
		Button = Color3.fromRGB(218,220,227),
		Text = Color3.fromRGB(18,18,23),
		SubText = Color3.fromRGB(90,90,100)
	}
}

local Theme = Themes[State.Theme]

--==================================================
-- HELPERS
--==================================================

local function New(class, properties, parent)
	local Object = Instance.new(class)

	for Property, Value in pairs(properties) do
		Object[Property] = Value
	end

	Object.Parent = parent
	return Object
end

local function Corner(parent, radius)
	return New("UICorner", {
		CornerRadius = UDim.new(0,radius)
	}, parent)
end

local function Stroke(parent, color, thickness)
	return New("UIStroke", {
		Color = color,
		Thickness = thickness or 1
	}, parent)
end

local function Tween(object, time, properties)
	return TweenService:Create(
		object,
		TweenInfo.new(
			time,
			Enum.EasingStyle.Quint,
			Enum.EasingDirection.Out
		),
		properties
	)
end

--==================================================
-- SPEED
--==================================================

local function ApplySpeed(speed)

	speed = math.clamp(speed,1,100)
	State.Speed = speed

	local Character = Player.Character
	if not Character then
		return
	end

	local Humanoid = Character:FindFirstChildOfClass("Humanoid")

	if Humanoid then
		Humanoid.WalkSpeed = speed
	end
end

Player.CharacterAdded:Connect(function(Character)

	local Humanoid = Character:WaitForChild("Humanoid",10)

	if Humanoid then
		Humanoid.WalkSpeed = State.Speed
	end
end)

--==================================================
-- GUI
--==================================================

local GUI = New("ScreenGui", {
	Name = "FSU_ULTRA",
	ResetOnSpawn = false,
	IgnoreGuiInset = true,
	ZIndexBehavior = Enum.ZIndexBehavior.Sibling
}, PlayerGui)

--==================================================
-- SCALE
--==================================================

local UIScale = New("UIScale", {}, GUI)

local function UpdateScale()

	local Camera = Workspace.CurrentCamera

	if not Camera then
		return
	end

	local Viewport = Camera.ViewportSize

	local ScaleX = Viewport.X / 1100
	local ScaleY = Viewport.Y / 760

	UIScale.Scale = math.clamp(
		math.min(ScaleX,ScaleY),
		0.55,
		1
	)
end

UpdateScale()

Workspace.CurrentCamera:GetPropertyChangedSignal(
	"ViewportSize"
):Connect(UpdateScale)

--==================================================
-- WINDOW
--==================================================

local NormalSize = UDim2.fromOffset(950,650)
local MiniSize = UDim2.fromOffset(190,65)

local NormalPosition = UDim2.fromScale(0.5,0.5)
local MiniPosition = UDim2.new(0,25,1,-25)

local Main = New("Frame", {
	Name = "Main",
	Size = NormalSize,
	Position = NormalPosition,
	AnchorPoint = Vector2.new(0.5,0.5),
	BackgroundColor3 = Theme.Background,
	BorderSizePixel = 0,
	ClipsDescendants = true,
	ZIndex = 2
}, GUI)

Corner(Main,18)

local MainStroke = Stroke(
	Main,
	Theme.Accent,
	2
)

--==================================================
-- GLOW
--==================================================

local Glow = New("ImageLabel", {
	Name = "Glow",
	Position = UDim2.fromScale(0.5,0.5),
	AnchorPoint = Vector2.new(0.5,0.5),
	Size = UDim2.new(1,55,1,55),
	BackgroundTransparency = 1,
	Image = "rbxassetid://5028857084",
	ImageColor3 = Theme.Accent,
	ImageTransparency = 0.88,
	ZIndex = 2
}, Main)

--==================================================
-- TOP BAR
--==================================================

local Top = New("Frame", {
	Name = "Top",
	Size = UDim2.new(1,0,0,82),
	BackgroundColor3 = Theme.Panel,
	BorderSizePixel = 0,
	ZIndex = 5
}, Main)

Corner(Top,18)

New("Frame", {
	Position = UDim2.new(0,0,1,-18),
	Size = UDim2.new(1,0,0,18),
	BackgroundColor3 = Theme.Panel,
	BorderSizePixel = 0,
	ZIndex = 5
}, Top)

New("Frame", {
	Position = UDim2.new(0,0,1,-2),
	Size = UDim2.new(1,0,0,2),
	BackgroundColor3 = Theme.Accent,
	BorderSizePixel = 0,
	ZIndex = 7
}, Top)

--==================================================
-- TITLE
--==================================================

local Title = New("TextLabel", {
	Position = UDim2.fromOffset(27,13),
	Size = UDim2.fromOffset(400,35),
	BackgroundTransparency = 1,
	Text = "FSU // ULTRA",
	Font = Enum.Font.GothamBlack,
	TextSize = 29,
	TextColor3 = Theme.Text,
	TextXAlignment = Enum.TextXAlignment.Left,
	ZIndex = 8
}, Top)

local Subtitle = New("TextLabel", {
	Position = UDim2.fromOffset(29,48),
	Size = UDim2.fromOffset(400,20),
	BackgroundTransparency = 1,
	Text = "N E X U S   C O N T R O L   C E N T E R",
	Font = Enum.Font.GothamBold,
	TextSize = 9,
	TextColor3 = Theme.Accent,
	TextXAlignment = Enum.TextXAlignment.Left,
	ZIndex = 8
}, Top)

--==================================================
-- STATUS AREA
--==================================================

local StatusArea = New("Frame", {
	Position = UDim2.new(1,-335,0,0),
	Size = UDim2.fromOffset(230,82),
	BackgroundTransparency = 1,
	ZIndex = 7
}, Top)

local Status = New("TextLabel", {
	Position = UDim2.fromOffset(0,27),
	Size = UDim2.fromOffset(110,25),
	BackgroundTransparency = 1,
	Text = "● ONLINE",
	Font = Enum.Font.GothamBold,
	TextSize = 12,
	TextColor3 = Color3.fromRGB(65,255,135),
	TextXAlignment = Enum.TextXAlignment.Center,
	ZIndex = 8
}, StatusArea)

local AimbotStatus = New("TextLabel", {
	Position = UDim2.fromOffset(110,27),
	Size = UDim2.fromOffset(110,25),
	BackgroundTransparency = 1,
	Text = "AIM OFF",
	Font = Enum.Font.GothamBold,
	TextSize = 11,
	TextColor3 = Color3.fromRGB(255,80,80),
	TextXAlignment = Enum.TextXAlignment.Center,
	ZIndex = 8
}, StatusArea)

--==================================================
-- WINDOW BUTTONS
--==================================================

local WindowButtons = New("Frame", {
	Position = UDim2.new(1,-100,0,0),
	Size = UDim2.fromOffset(100,82),
	BackgroundTransparency = 1,
	ZIndex = 20
}, Top)

local Minimize = New("TextButton", {
	Position = UDim2.fromOffset(5,25),
	Size = UDim2.fromOffset(28,28),
	Text = "—",
	Font = Enum.Font.GothamBlack,
	TextSize = 17,
	TextColor3 = Theme.Text,
	BackgroundColor3 = Theme.Button,
	BorderSizePixel = 0,
	AutoButtonColor = false,
	Active = true,
	ZIndex = 21
}, WindowButtons)

Corner(Minimize,8)

local Close = New("TextButton", {
	Position = UDim2.fromOffset(43,25),
	Size = UDim2.fromOffset(28,28),
	Text = "×",
	Font = Enum.Font.GothamBlack,
	TextSize = 19,
	TextColor3 = Theme.Text,
	BackgroundColor3 = Theme.Button,
	BorderSizePixel = 0,
	AutoButtonColor = false,
	Active = true,
	ZIndex = 21
}, WindowButtons)

Corner(Close,8)

local function WindowHover(Button, Normal, HoverColor)

	Button.MouseEnter:Connect(function()

		Tween(Button,0.12,{
			BackgroundColor3 = HoverColor,
			TextColor3 = Color3.new(1,1,1)
		}):Play()

	end)

	Button.MouseLeave:Connect(function()

		Tween(Button,0.12,{
			BackgroundColor3 = Normal,
			TextColor3 = Theme.Text
		}):Play()

	end)
end

WindowHover(
	Minimize,
	Theme.Button,
	Theme.Accent
)

WindowHover(
	Close,
	Theme.Button,
	Color3.fromRGB(220,45,65)
)

--==================================================
-- SIDEBAR
--==================================================

local Sidebar = New("Frame", {
	Name = "Sidebar",
	Position = UDim2.fromOffset(0,82),
	Size = UDim2.new(0,190,1,-82),
	BackgroundColor3 = Theme.Panel,
	BorderSizePixel = 0,
	ZIndex = 5
}, Main)

local SidebarTitle = New("TextLabel", {
	Position = UDim2.fromOffset(20,17),
	Size = UDim2.fromOffset(150,20),
	BackgroundTransparency = 1,
	Text = "NAVIGATION",
	Font = Enum.Font.GothamBold,
	TextSize = 9,
	TextColor3 = Theme.SubText,
	TextXAlignment = Enum.TextXAlignment.Left,
	ZIndex = 6
}, Sidebar)

New("Frame", {
	Position = UDim2.fromOffset(20,40),
	Size = UDim2.fromOffset(150,1),
	BackgroundColor3 = Theme.Button,
	BorderSizePixel = 0,
	ZIndex = 6
}, Sidebar)

local TabContainer = New("Frame", {
	Position = UDim2.fromOffset(12,55),
	Size = UDim2.new(1,-24,1,-65),
	BackgroundTransparency = 1,
	ZIndex = 6
}, Sidebar)

local TabLayout = New("UIListLayout", {
	Padding = UDim.new(0,7),
	HorizontalAlignment = Enum.HorizontalAlignment.Center,
	SortOrder = Enum.SortOrder.LayoutOrder
}, TabContainer)

--==================================================
-- CONTENT
--==================================================

local Content = New("Frame", {
	Name = "Content",
	Position = UDim2.fromOffset(190,82),
	Size = UDim2.new(1,-190,1,-82),
	BackgroundTransparency = 1,
	ZIndex = 6
}, Main)

local Pages = {}
local Tabs = {}

local TabNames = {
	"MAIN",
	"COMBAT",
	"AIMBOT",
	"MOVEMENT",
	"PLAYER",
	"VISUALS",
	"EMOTES",
	"CONFIG"
}

local Icons = {
	MAIN = "◆",
	COMBAT = "⚔",
	AIMBOT = "🎯",
	MOVEMENT = "↯",
	PLAYER = "●",
	VISUALS = "◉",
	EMOTES = "♫",
	CONFIG = "⚙"
}

--==================================================
-- TAB CREATION
--==================================================

local function CreateTab(Name,Order)

	local Button = New("TextButton", {
		Name = Name .. "Tab",
		Size = UDim2.new(1,0,0,43),
		Text = "",
		BackgroundColor3 = Theme.Button,
		BorderSizePixel = 0,
		AutoButtonColor = false,
		LayoutOrder = Order,
		ZIndex = 7
	}, TabContainer)

	Corner(Button,9)

	local Indicator = New("Frame", {
		Position = UDim2.fromOffset(0,7),
		Size = UDim2.fromOffset(3,29),
		BackgroundColor3 = Theme.Accent,
		BorderSizePixel = 0,
		BackgroundTransparency = 1,
		ZIndex = 8
	}, Button)

	Corner(Indicator,2)

	local Icon = New("TextLabel", {
		Position = UDim2.fromOffset(15,0),
		Size = UDim2.fromOffset(28,43),
		BackgroundTransparency = 1,
		Text = Icons[Name],
		Font = Enum.Font.GothamBold,
		TextSize = 14,
		TextColor3 = Theme.SubText,
		ZIndex = 8
	}, Button)

	local Label = New("TextLabel", {
		Position = UDim2.fromOffset(48,0),
		Size = UDim2.new(1,-55,1,0),
		BackgroundTransparency = 1,
		Text = Name,
		Font = Enum.Font.GothamBold,
		TextSize = 11,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		ZIndex = 8
	}, Button)

	return Button,Indicator,Icon,Label
end

for Index,Name in ipairs(TabNames) do

	local Page = New("ScrollingFrame", {
		Name = Name,
		Size = UDim2.fromScale(1,1),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		ScrollBarThickness = 3,
		ScrollBarImageColor3 = Theme.Accent,
		CanvasSize = UDim2.fromOffset(0,0),
		Visible = false,
		ZIndex = 7
	}, Content)

	Pages[Name] = Page

	local Tab,Indicator,Icon,LabelObject =
		CreateTab(Name,Index)

	Tabs[Name] = {
		Button = Tab,
		Indicator = Indicator,
		Icon = Icon,
		Label = LabelObject
	}

	Tab.MouseEnter:Connect(function()

		if not Page.Visible then
			Tween(Tab,0.12,{
				BackgroundColor3 = Theme.Panel2
			}):Play()
		end

	end)

	Tab.MouseLeave:Connect(function()

		Tween(Tab,0.12,{
			BackgroundColor3 =
				Page.Visible
				and Theme.Accent
				or Theme.Button
		}):Play()

	end)

	Tab.MouseButton1Click:Connect(function()

		for TabName,PageObject in pairs(Pages) do
			PageObject.Visible = TabName == Name
		end

		for TabName,Data in pairs(Tabs) do

			local Selected = TabName == Name

			Tween(Data.Button,0.15,{
				BackgroundColor3 =
					Selected
					and Theme.Accent
					or Theme.Button
			}):Play()

			Data.Indicator.BackgroundTransparency =
				Selected and 0 or 1

			Data.Icon.TextColor3 =
				Selected
				and Color3.new(1,1,1)
				or Theme.SubText
		end

	end)
end

--==================================================
-- COMMON UI
--==================================================

local Registered = {
	Labels = {},
	Panels = {},
	Buttons = {},
	Strokes = {},
	Toggles = {}
}

local function Label(parent,text,position,size,textSize)

	local Object = New("TextLabel", {
		Position = position,
		Size = size,
		BackgroundTransparency = 1,
		Text = text,
		Font = Enum.Font.GothamBold,
		TextSize = textSize or 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		ZIndex = 8
	}, parent)

	table.insert(Registered.Labels,Object)

	return Object
end

local function Panel(parent,position,size)

	local Object = New("Frame", {
		Position = position,
		Size = size,
		BackgroundColor3 = Theme.Panel,
		BorderSizePixel = 0,
		ZIndex = 8
	}, parent)

	Corner(Object,12)

	local ObjectStroke =
		Stroke(Object,Theme.Button,1)

	table.insert(Registered.Panels,Object)
	table.insert(Registered.Strokes,ObjectStroke)

	return Object
end

local function Button(parent,text,position,size)

	local Object = New("TextButton", {
		Position = position,
		Size = size,
		Text = text,
		Font = Enum.Font.GothamBold,
		TextSize = 11,
		TextColor3 = Theme.Text,
		BackgroundColor3 = Theme.Button,
		BorderSizePixel = 0,
		AutoButtonColor = false,
		ZIndex = 9
	}, parent)

	Corner(Object,8)

	table.insert(Registered.Buttons,Object)

	Object.MouseEnter:Connect(function()

		Tween(Object,0.12,{
			BackgroundColor3 = Theme.Accent
		}):Play()

	end)

	Object.MouseLeave:Connect(function()

		Tween(Object,0.12,{
			BackgroundColor3 = Theme.Button
		}):Play()

	end)

	return Object
end

--==================================================
-- TOGGLE
--==================================================

local function Toggle(
	parent,
	text,
	position,
	initialState,
	callback
)

	local Holder = Panel(
		parent,
		position,
		UDim2.fromOffset(370,55)
	)

	Label(
		Holder,
		text,
		UDim2.fromOffset(17,0),
		UDim2.new(1,-90,1,0),
		12
	)

	local Switch = New("TextButton", {
		Position = UDim2.new(1,-65,0.5,-13),
		Size = UDim2.fromOffset(50,26),
		Text = "",
		BackgroundColor3 =
			initialState
			and Theme.Accent
			or Theme.Background,
		BorderSizePixel = 0,
		AutoButtonColor = false,
		ZIndex = 10
	}, Holder)

	Corner(Switch,20)

	local Knob = New("Frame", {
		Position =
			initialState
			and UDim2.new(1,-22,0,4)
			or UDim2.fromOffset(4,4),
		Size = UDim2.fromOffset(18,18),
		BackgroundColor3 =
			initialState
			and Color3.new(1,1,1)
			or Theme.SubText,
		BorderSizePixel = 0,
		ZIndex = 11
	}, Switch)

	Corner(Knob,20)

	local Enabled = initialState

	local function Set(Value)

		Enabled = Value

		Tween(Switch,0.16,{
			BackgroundColor3 =
				Enabled
				and Theme.Accent
				or Theme.Background
		}):Play()

		Tween(Knob,0.16,{
			Position =
				Enabled
				and UDim2.new(1,-22,0,4)
				or UDim2.fromOffset(4,4),

			BackgroundColor3 =
				Enabled
				and Color3.new(1,1,1)
				or Theme.SubText
		}):Play()

		if callback then
			callback(Enabled)
		end
	end

	Switch.MouseButton1Click:Connect(function()
		Set(not Enabled)
	end)

	table.insert(Registered.Toggles,{
		Set = Set,
		Default = initialState
	})

	return Set
end

--==================================================
-- MAIN PAGE
--==================================================

local MainPage = Pages.MAIN

Label(
	MainPage,
	"CONTROL CENTER",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

local MainSub = Label(
	MainPage,
	"Welcome back, " .. Player.DisplayName .. ".",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,22),
	11
)

MainSub.TextColor3 = Theme.Accent

local StatusPanel = Panel(
	MainPage,
	UDim2.fromOffset(30,100),
	UDim2.new(1,-60,0,70)
)

Label(
	StatusPanel,
	"SYSTEM STATUS",
	UDim2.fromOffset(18,10),
	UDim2.fromOffset(150,18),
	9
).TextColor3 = Theme.SubText

local SystemStatus = Label(
	StatusPanel,
	"● SYSTEM READY",
	UDim2.fromOffset(18,30),
	UDim2.fromOffset(300,25),
	15
)

SystemStatus.TextColor3 =
	Color3.fromRGB(65,255,135)

local function StatCard(parent,title,value,x)

	local Card = Panel(
		parent,
		UDim2.fromOffset(x,190),
		UDim2.fromOffset(175,105)
	)

	Label(
		Card,
		title,
		UDim2.fromOffset(15,13),
		UDim2.fromOffset(140,18),
		9
	).TextColor3 = Theme.SubText

	local Value = Label(
		Card,
		value,
		UDim2.fromOffset(15,38),
		UDim2.fromOffset(145,40),
		25
	)

	Value.TextColor3 = Theme.Accent

	return Value
end

local PlayerCount = StatCard(
	MainPage,
	"PLAYERS ONLINE",
	tostring(#Players:GetPlayers()),
	30
)

local SpeedValue = StatCard(
	MainPage,
	"MOVEMENT",
	"16",
	220
)

local MultValue = StatCard(
	MainPage,
	"MULTIPLIER",
	"1x",
	410
)

local RangeValueMain = StatCard(
	MainPage,
	"RANGE",
	"15",
	600
)

Toggle(
	MainPage,
	"MAIN SYSTEM",
	UDim2.fromOffset(30,320),
	false,
	function(On)

		State.Main = On

		SystemStatus.Text =
			On
			and "● SYSTEM ACTIVE"
			or "● SYSTEM READY"

		SystemStatus.TextColor3 =
			On
			and Theme.Accent
			or Color3.fromRGB(65,255,135)
	end
)

--==================================================
-- COMBAT PAGE
--==================================================

local CombatPage = Pages.COMBAT

Label(
	CombatPage,
	"COMBAT CONTROL",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

Label(
	CombatPage,
	"Configure combat interface settings",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	11
).TextColor3 = Theme.SubText

Toggle(
	CombatPage,
	"COMBAT SYSTEM",
	UDim2.fromOffset(30,100),
	false,
	function(On)
		State.Combat = On
	end
)

local MultiPanel = Panel(
	CombatPage,
	UDim2.fromOffset(30,175),
	UDim2.fromOffset(370,130)
)

Label(
	MultiPanel,
	"MULTIPLIER",
	UDim2.fromOffset(17,13),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

for i,Value in ipairs({1,2,3,5,10}) do

	local X = 15 + ((i-1)*68)

	local B = Button(
		MultiPanel,
		Value.."x",
		UDim2.fromOffset(X,48),
		UDim2.fromOffset(58,40)
	)

	B.MouseButton1Click:Connect(function()

		State.Multiplier = Value
		MultValue.Text = Value.."x"

	end)
end

local RangePanel = Panel(
	CombatPage,
	UDim2.fromOffset(420,100),
	UDim2.fromOffset(360,205)
)

Label(
	RangePanel,
	"RANGE",
	UDim2.fromOffset(18,16),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

local RangeDisplay = Label(
	RangePanel,
	"15",
	UDim2.fromOffset(18,48),
	UDim2.fromOffset(150,45),
	28
)

RangeDisplay.TextColor3 = Theme.Accent

local Minus = Button(
	RangePanel,
	"−",
	UDim2.fromOffset(190,50),
	UDim2.fromOffset(60,40)
)

local Plus = Button(
	RangePanel,
	"+",
	UDim2.fromOffset(260,50),
	UDim2.fromOffset(60,40)
)

Minus.MouseButton1Click:Connect(function()

	State.Range = math.max(5,State.Range-5)

	RangeDisplay.Text = tostring(State.Range)
	RangeValueMain.Text = tostring(State.Range)

end)

Plus.MouseButton1Click:Connect(function()

	State.Range = math.min(50,State.Range+5)

	RangeDisplay.Text = tostring(State.Range)
	RangeValueMain.Text = tostring(State.Range)

end)

--==================================================
-- AIMBOT PAGE
--==================================================

local AimbotPage = Pages.AIMBOT

Label(
	AimbotPage,
	"AIMBOT CONTROL",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

Label(
	AimbotPage,
	"Targeting interface settings",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	11
).TextColor3 = Theme.SubText

Toggle(
	AimbotPage,
	"AIMBOT SYSTEM",
	UDim2.fromOffset(30,100),
	false,
	function(On)

		State.Aimbot = On

		AimbotStatus.Text =
			On
			and "AIM ON"
			or "AIM OFF"

		AimbotStatus.TextColor3 =
			On
			and Color3.fromRGB(65,255,135)
			or Color3.fromRGB(255,80,80)

	end
)

Toggle(
	AimbotPage,
	"TEAM CHECK",
	UDim2.fromOffset(30,165),
	true,
	function(On)
		State.TeamCheck = On
	end
)

local AimPartPanel = Panel(
	AimbotPage,
	UDim2.fromOffset(30,230),
	UDim2.fromOffset(750,100)
)

Label(
	AimPartPanel,
	"AIM PART",
	UDim2.fromOffset(18,12),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

for i,Part in ipairs({
	"Head",
	"Torso",
	"HumanoidRootPart",
	"RightFoot",
	"LeftFoot"
}) do

	local X = 15 + ((i-1)*145)

	local B = Button(
		AimPartPanel,
		Part,
		UDim2.fromOffset(X,45),
		UDim2.fromOffset(135,40)
	)

	B.MouseButton1Click:Connect(function()
		State.AimPart = Part
	end)
end

local SmoothPanel = Panel(
	AimbotPage,
	UDim2.fromOffset(30,345),
	UDim2.fromOffset(370,120)
)

Label(
	SmoothPanel,
	"SMOOTHNESS",
	UDim2.fromOffset(18,12),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

local SmoothDisplay = Label(
	SmoothPanel,
	"0.3",
	UDim2.fromOffset(18,42),
	UDim2.fromOffset(100,35),
	24
)

SmoothDisplay.TextColor3 = Theme.Accent

local SmoothMinus = Button(
	SmoothPanel,
	"−",
	UDim2.fromOffset(130,45),
	UDim2.fromOffset(50,35)
)

local SmoothPlus = Button(
	SmoothPanel,
	"+",
	UDim2.fromOffset(190,45),
	UDim2.fromOffset(50,35)
)

SmoothMinus.MouseButton1Click:Connect(function()

	State.AimbotSmoothness =
		math.max(
			0.1,
			State.AimbotSmoothness-0.1
		)

	SmoothDisplay.Text =
		string.format(
			"%.1f",
			State.AimbotSmoothness
		)

end)

SmoothPlus.MouseButton1Click:Connect(function()

	State.AimbotSmoothness =
		math.min(
			1,
			State.AimbotSmoothness+0.1
		)

	SmoothDisplay.Text =
		string.format(
			"%.1f",
			State.AimbotSmoothness
		)

end)

local FOVPanel = Panel(
	AimbotPage,
	UDim2.fromOffset(420,345),
	UDim2.fromOffset(360,120)
)

Label(
	FOVPanel,
	"FOV",
	UDim2.fromOffset(18,12),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

local FOVDisplay = Label(
	FOVPanel,
	"60",
	UDim2.fromOffset(18,42),
	UDim2.fromOffset(100,35),
	24
)

FOVDisplay.TextColor3 = Theme.Accent

local FOVMinus = Button(
	FOVPanel,
	"−",
	UDim2.fromOffset(130,45),
	UDim2.fromOffset(50,35)
)

local FOVPlus = Button(
	FOVPanel,
	"+",
	UDim2.fromOffset(190,45),
	UDim2.fromOffset(50,35)
)

FOVMinus.MouseButton1Click:Connect(function()

	State.AimbotFOV =
		math.max(
			10,
			State.AimbotFOV-5
		)

	FOVDisplay.Text =
		tostring(State.AimbotFOV)

end)

FOVPlus.MouseButton1Click:Connect(function()

	State.AimbotFOV =
		math.min(
			200,
			State.AimbotFOV+5
		)

	FOVDisplay.Text =
		tostring(State.AimbotFOV)

end)

--==================================================
-- MOVEMENT PAGE
--==================================================

local MovementPage = Pages.MOVEMENT

Label(
	MovementPage,
	"MOVEMENT",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

Label(
	MovementPage,
	"Movement configuration interface",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	11
).TextColor3 = Theme.SubText

local SpeedPanel = Panel(
	MovementPage,
	UDim2.fromOffset(30,105),
	UDim2.fromOffset(750,155)
)

Label(
	SpeedPanel,
	"WALK SPEED",
	UDim2.fromOffset(20,17),
	UDim2.fromOffset(200,20),
	10
).TextColor3 = Theme.SubText

local SpeedBox = New("TextBox", {
	Position = UDim2.fromOffset(20,52),
	Size = UDim2.fromOffset(210,48),
	Text = "16",
	ClearTextOnFocus = false,
	Font = Enum.Font.GothamBold,
	TextSize = 15,
	TextColor3 = Theme.Text,
	BackgroundColor3 = Theme.Button,
	BorderSizePixel = 0,
	ZIndex = 10
}, SpeedPanel)

Corner(SpeedBox,9)

local SpeedSet = Button(
	SpeedPanel,
	"APPLY SPEED",
	UDim2.fromOffset(245,52),
	UDim2.fromOffset(170,48)
)

SpeedSet.MouseButton1Click:Connect(function()

	local Value = tonumber(SpeedBox.Text)

	if Value then

		Value = math.clamp(Value,1,100)

		ApplySpeed(Value)

		SpeedBox.Text = tostring(State.Speed)
		SpeedValue.Text = tostring(State.Speed)

	else

		SpeedBox.Text = tostring(State.Speed)

	end

end)

Label(
	SpeedPanel,
	"Range: 1 — 100",
	UDim2.fromOffset(20,112),
	UDim2.fromOffset(300,18),
	9
).TextColor3 = Theme.SubText

--==================================================
-- PLAYER PAGE
--==================================================

local PlayerPage = Pages.PLAYER

Label(
	PlayerPage,
	"PLAYER PROFILE",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

local Profile = Panel(
	PlayerPage,
	UDim2.fromOffset(30,100),
	UDim2.fromOffset(750,220)
)

local Avatar = New("ImageLabel", {
	Position = UDim2.fromOffset(22,25),
	Size = UDim2.fromOffset(120,120),
	BackgroundColor3 = Theme.Button,
	BorderSizePixel = 0,
	Image =
		"rbxthumb://type=AvatarHeadShot&id="
		..Player.UserId..
		"&w=420&h=420",
	ZIndex = 10
}, Profile)

Corner(Avatar,60)

local AvatarStroke =
	Stroke(Avatar,Theme.Accent,2)

Label(
	Profile,
	"DISPLAY NAME",
	UDim2.fromOffset(170,25),
	UDim2.fromOffset(180,18),
	9
).TextColor3 = Theme.SubText

local DisplayName = Label(
	Profile,
	Player.DisplayName,
	UDim2.fromOffset(170,47),
	UDim2.fromOffset(400,30),
	20
)

DisplayName.TextColor3 = Theme.Accent

Label(
	Profile,
	"USERNAME",
	UDim2.fromOffset(170,87),
	UDim2.fromOffset(180,18),
	9
).TextColor3 = Theme.SubText

Label(
	Profile,
	"@"..Player.Name,
	UDim2.fromOffset(170,109),
	UDim2.fromOffset(400,25),
	14
)

Label(
	Profile,
	"USER ID",
	UDim2.fromOffset(170,145),
	UDim2.fromOffset(180,18),
	9
).TextColor3 = Theme.SubText

local UID = Label(
	Profile,
	tostring(Player.UserId),
	UDim2.fromOffset(170,167),
	UDim2.fromOffset(400,25),
	14
)

UID.TextColor3 = Theme.Accent

--==================================================
-- VISUALS PAGE
--==================================================

local VisualPage = Pages.VISUALS

Label(
	VisualPage,
	"VISUAL CENTER",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

Label(
	VisualPage,
	"Visual interface controls",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	11
).TextColor3 = Theme.SubText

Toggle(
	VisualPage,
	"PLAYER BOXES",
	UDim2.fromOffset(30,105),
	false,
	function(On)
		State.ESP = On
	end
)

Toggle(
	VisualPage,
	"PLAYER NAMES",
	UDim2.fromOffset(30,170),
	false,
	function(On)
		State.Names = On
	end
)

local VisualInfo = Panel(
	VisualPage,
	UDim2.fromOffset(420,105),
	UDim2.fromOffset(360,180)
)

Label(
	VisualInfo,
	"VISUAL STATUS",
	UDim2.fromOffset(18,18),
	UDim2.fromOffset(250,20),
	10
).TextColor3 = Theme.SubText

local VisualStatus = Label(
	VisualInfo,
	"READY",
	UDim2.fromOffset(18,52),
	UDim2.fromOffset(300,35),
	22
)

VisualStatus.TextColor3 = Theme.Accent

Label(
	VisualInfo,
	"Toggle the available visual options",
	UDim2.fromOffset(18,100),
	UDim2.fromOffset(320,40),
	11
).TextColor3 = Theme.SubText

--==================================================
-- EMOTES PAGE
--==================================================

local EmotePage = Pages.EMOTES

Label(
	EmotePage,
	"EMOTE CENTER",
	UDim2.fromOffset(30,25),
	UDim2.fromOffset(500,32),
	23
)

local EmoteStatus = Label(
	EmotePage,
	"SELECT ANIMATION",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	10
)

EmoteStatus.TextColor3 = Theme.Accent

local EmotePanel = Panel(
	EmotePage,
	UDim2.fromOffset(30,100),
	UDim2.fromOffset(750,320)
)

local Emotes = {
	"CRAB RAVE",
	"NLE",
	"ZESTY STURDY",
	"BLUE SHIRT",
	"STURDY",
	"SCUBA",
	"LAG",
	"HOMER",
	"KING NASIR",
	"KICKING FEET",
	"BADDIE HIP",
	"IN A CIRCLE",
	"TORNADO",
	"NLE 2",
	"NERVY",
	"TRIPLE T",
	"POOPING",
	"BILLY BOUNCE",
	"EDGYPT",
	"DECAULT"
}

for Index,Name in ipairs(Emotes) do

	local Column = (Index-1)%4
	local Row = math.floor((Index-1)/4)

	local B = Button(
		EmotePanel,
		Name,
		UDim2.fromOffset(
			15+Column*180,
			15+Row*48
		),
		UDim2.fromOffset(165,38)
	)

	B.MouseButton1Click:Connect(function()

		State.CurrentEmote = Name
		EmoteStatus.Text =
			"SELECTED: "..Name

	end)
end

local StopEmoteBtn = Button(
	EmotePage,
	"■ STOP EMOTE",
	UDim2.fromOffset(30,440),
	UDim2.fromOffset(190,45)
)

StopEmoteBtn.MouseButton1Click:Connect(function()

	State.CurrentEmote = nil
	EmoteStatus.Text = "EMOTE STOPPED"

end)

--==================================================
-- CONFIG PAGE
--==================================================

local ConfigPage = Pages.CONFIG

Label(
	ConfigPage,
	"CONFIGURATION",
	UDim2.fromOffset(30,28),
	UDim2.fromOffset(500,32),
	23
)

local ThemeStatus = Label(
	ConfigPage,
	"CURRENT: ULTRA PURPLE",
	UDim2.fromOffset(32,59),
	UDim2.fromOffset(500,20),
	10
)

ThemeStatus.TextColor3 = Theme.Accent

local ThemePanel = Panel(
	ConfigPage,
	UDim2.fromOffset(30,100),
	UDim2.fromOffset(750,285)
)

Label(
	ThemePanel,
	"INTERFACE THEMES",
	UDim2.fromOffset(18,15),
	UDim2.fromOffset(300,20),
	10
).TextColor3 = Theme.SubText

local ThemeNames = {
	"Ultra Purple",
	"Blue",
	"Green",
	"Red",
	"Yellow",
	"Gray",
	"Black",
	"White"
}

local ThemeButtons = {}

for Index,Name in ipairs(ThemeNames) do

	local Column = (Index-1)%2
	local Row = math.floor((Index-1)/2)

	local B = Button(
		ThemePanel,
		Name,
		UDim2.fromOffset(
			18+Column*270,
			48+Row*52
		),
		UDim2.fromOffset(250,40)
	)

	ThemeButtons[Name] = B
end

--==================================================
-- THEME
--==================================================

local function ApplyTheme(Name)

	local NewTheme = Themes[Name]

	if not NewTheme then
		return
	end

	State.Theme = Name
	Theme = NewTheme

	Main.BackgroundColor3 = Theme.Background
	MainStroke.Color = Theme.Accent
	Glow.ImageColor3 = Theme.Accent

	Top.BackgroundColor3 = Theme.Panel
	Sidebar.BackgroundColor3 = Theme.Panel

	Title.TextColor3 = Theme.Text
	Subtitle.TextColor3 = Theme.Accent
	ThemeStatus.TextColor3 = Theme.Accent

	ThemeStatus.Text =
		"CURRENT: "..string.upper(Name)

	SpeedBox.BackgroundColor3 = Theme.Button
	SpeedBox.TextColor3 = Theme.Text

	for _,Object in ipairs(Registered.Labels) do

		if Object.Parent then
			Object.TextColor3 = Theme.Text
		end

	end

	for _,Object in ipairs(Registered.Panels) do

		if Object.Parent then
			Object.BackgroundColor3 = Theme.Panel
		end

	end

	for _,Object in ipairs(Registered.Buttons) do

		if Object.Parent then
			Object.BackgroundColor3 = Theme.Button
			Object.TextColor3 = Theme.Text
		end

	end

	for _,Object in ipairs(Registered.Strokes) do

		if Object.Parent then
			Object.Color = Theme.Button
		end

	end

	for Name2,Data in pairs(Tabs) do

		local Selected = Pages[Name2].Visible

		Data.Button.BackgroundColor3 =
			Selected
			and Theme.Accent
			or Theme.Button

		Data.Indicator.BackgroundColor3 =
			Theme.Accent

		Data.Icon.TextColor3 =
			Selected
			and Color3.new(1,1,1)
			or Theme.SubText
	end

	for ThemeName,B in pairs(ThemeButtons) do

		if ThemeName == Name then

			B.BackgroundColor3 = Theme.Accent
			B.TextColor3 = Color3.new(1,1,1)

		else

			B.BackgroundColor3 = Theme.Button
			B.TextColor3 = Theme.Text

		end
	end

	AvatarStroke.Color = Theme.Accent
end

for Name,B in pairs(ThemeButtons) do

	B.MouseButton1Click:Connect(function()
		ApplyTheme(Name)
	end)

end

--==================================================
-- RESET
--==================================================

local Reset = Button(
	ConfigPage,
	"↻ RESET INTERFACE",
	UDim2.fromOffset(30,410),
	UDim2.fromOffset(210,45)
)

Reset.MouseButton1Click:Connect(function()

	State.Speed = 16
	State.Multiplier = 1
	State.Range = 15

	State.Aimbot = false
	State.TeamCheck = true
	State.AimPart = "Head"
	State.AimbotSmoothness = 0.3
	State.AimbotFOV = 60

	State.ESP = false
	State.Names = false
	State.Combat = false
	State.Main = false

	ApplySpeed(16)

	SpeedBox.Text = "16"
	SpeedValue.Text = "16"

	MultValue.Text = "1x"

	RangeValueMain.Text = "15"
	RangeDisplay.Text = "15"

	SmoothDisplay.Text = "0.3"
	FOVDisplay.Text = "60"

	for _,Data in ipairs(Registered.Toggles) do
		Data.Set(Data.Default)
	end

	State.CurrentEmote = nil
	EmoteStatus.Text = "SELECT ANIMATION"

	AimbotStatus.Text = "AIM OFF"
	AimbotStatus.TextColor3 =
		Color3.fromRGB(255,80,80)

	SystemStatus.Text = "● SYSTEM READY"
	SystemStatus.TextColor3 =
		Color3.fromRGB(65,255,135)

	ApplyTheme("Ultra Purple")

end)

--==================================================
-- PLAYER COUNT
--==================================================

local function UpdatePlayerCount()
	PlayerCount.Text =
		tostring(#Players:GetPlayers())
end

Players.PlayerAdded:Connect(UpdatePlayerCount)
Players.PlayerRemoving:Connect(UpdatePlayerCount)

--==================================================
-- MINIMIZED BOX
--==================================================

local MinimizedBox = New("TextButton", {
	Name = "MinimizedBox",
	Size = MiniSize,
	Position = MiniPosition,
	AnchorPoint = Vector2.new(0,1),
	BackgroundColor3 = Theme.Panel,
	BorderSizePixel = 0,
	Text = "",
	AutoButtonColor = false,
	Visible = false,
	Active = true,
	ZIndex = 100
}, GUI)

Corner(MinimizedBox,14)

local MiniStroke =
	Stroke(
		MinimizedBox,
		Theme.Accent,
		2
	)

local MiniTitle = New("TextLabel", {
	Position = UDim2.fromOffset(13,8),
	Size = UDim2.new(1,-26,0,23),
	BackgroundTransparency = 1,
	Text = "FSU // ULTRA",
	Font = Enum.Font.GothamBlack,
	TextSize = 14,
	TextColor3 = Theme.Text,
	TextXAlignment = Enum.TextXAlignment.Left,
	ZIndex = 101
}, MinimizedBox)

local MiniStatus = New("TextLabel", {
	Position = UDim2.fromOffset(13,34),
	Size = UDim2.new(1,-26,0,18),
	BackgroundTransparency = 1,
	Text = "CLICK TO OPEN",
	Font = Enum.Font.GothamBold,
	TextSize = 8,
	TextColor3 = Theme.Accent,
	TextXAlignment = Enum.TextXAlignment.Left,
	ZIndex = 101
}, MinimizedBox)

MinimizedBox.MouseEnter:Connect(function()

	Tween(
		MinimizedBox,
		0.15,
		{
			BackgroundColor3 = Theme.Panel2,
			Size = UDim2.fromOffset(200,70)
		}
	):Play()

end)

MinimizedBox.MouseLeave:Connect(function()

	Tween(
		MinimizedBox,
		0.15,
		{
			BackgroundColor3 = Theme.Panel,
			Size = MiniSize
		}
	):Play()

end)

--==================================================
-- MINIMIZE
--==================================================

Minimize.MouseButton1Click:Connect(function()

	if State.Minimized then
		return
	end

	State.Minimized = true

	Sidebar.Visible = false
	Content.Visible = false

	MinimizedBox.Visible = true

	Tween(
		Main,
		0.25,
		{
			Size = MiniSize,
			Position = MiniPosition
		}
	):Play()

	task.delay(0.2,function()

		if State.Minimized and Main.Parent then
			Main.Visible = false
		end

	end)

end)

--==================================================
-- RESTORE
--==================================================

MinimizedBox.MouseButton1Click:Connect(function()

	if not State.Minimized then
		return
	end

	State.Minimized = false

	MinimizedBox.Visible = false

	Main.Visible = true

	Main.Size = MiniSize
	Main.Position = MiniPosition

	Sidebar.Visible = true
	Content.Visible = true

	Tween(
		Main,
		0.4,
		{
			Size = NormalSize,
			Position = NormalPosition
		}
	):Play()

end)

--==================================================
-- CLOSE
--==================================================

Close.MouseButton1Click:Connect(function()

	State.Hidden = true

	Main.Visible = false
	MinimizedBox.Visible = false

end)

--==================================================
-- RIGHT SHIFT
--==================================================

UIS.InputBegan:Connect(function(Input,GameProcessed)

	if GameProcessed then
		return
	end

	if Input.KeyCode ~= Enum.KeyCode.RightShift then
		return
	end

	State.Hidden = not State.Hidden

	if State.Hidden then

		Main.Visible = false
		MinimizedBox.Visible = false

	else

		State.Minimized = false

		Main.Visible = true
		MinimizedBox.Visible = false

		Main.Size = UDim2.fromOffset(0,0)
		Main.Position = NormalPosition

		Sidebar.Visible = true
		Content.Visible = true

		Tween(
			Main,
			0.4,
			{
				Size = NormalSize
			}
		):Play()

	end

end)

--==================================================
-- DRAGGING
--==================================================

local DragArea = New("TextButton", {
	Name = "DragArea",
	Position = UDim2.fromOffset(0,0),
	Size = UDim2.new(1,-340,1,0),
	BackgroundTransparency = 1,
	Text = "",
	AutoButtonColor = false,
	Active = true,
	ZIndex = 6
}, Top)

local Dragging = false
local DragStart
local StartPosition

DragArea.InputBegan:Connect(function(Input)

	if Input.UserInputType ==
		Enum.UserInputType.MouseButton1 then

		Dragging = true
		DragStart = Input.Position
		StartPosition = Main.Position

	end

end)

UIS.InputChanged:Connect(function(Input)

	if not Dragging then
		return
	end

	if Input.UserInputType ==
		Enum.UserInputType.MouseMovement then

		local Delta =
			Input.Position - DragStart

		Main.Position =
			UDim2.new(
				StartPosition.X.Scale,
				StartPosition.X.Offset + Delta.X,
				StartPosition.Y.Scale,
				StartPosition.Y.Offset + Delta.Y
			)

	end

end)

UIS.InputEnded:Connect(function(Input)

	if Input.UserInputType ==
		Enum.UserInputType.MouseButton1 then

		Dragging = false

	end

end)

--==================================================
-- DEFAULT TAB
--==================================================

Pages.MAIN.Visible = true

for Name,Data in pairs(Tabs) do

	local Selected = Name == "MAIN"

	Data.Button.BackgroundColor3 =
		Selected
		and Theme.Accent
		or Theme.Button

	Data.Indicator.BackgroundTransparency =
		Selected
		and 0
		or 1

	Data.Icon.TextColor3 =
		Selected
		and Color3.new(1,1,1)
		or Theme.SubText

end

--==================================================
-- OPENING ANIMATION
--==================================================

Main.Size = UDim2.fromOffset(0,0)

Tween(
	Main,
	0.55,
	{
		Size = NormalSize
	}
):Play()

ApplyTheme("Ultra Purple")

print("FSU ULTRA // NEXUS EDITION v4 LOADED")
