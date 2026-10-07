local a=game:GetService("Players")
local b=game:GetService("Workspace")
local c=game:GetService("Lighting")
local d=UserSettings()
local e=d:GetService("UserGameSettings")
local f=a.LocalPlayer

local g=true
local h=true
local i=false
local j=true
local k=true

pcall(function()e.SavedQualityLevel=Enum.SavedQualitySetting.QualityLevel1 end)
pcall(function()e.GraphicsOptimizationMode=Enum.GraphicsOptimizationMode.Performance end)

pcall(function()
	c.GlobalShadows=false
	c.Brightness=0
	c.EnvironmentDiffuseScale=0
	c.EnvironmentSpecularScale=0
	c.ExposureCompensation=0
	c.FogStart=100000
	c.FogEnd=100000
	c.Ambient=Color3.fromRGB(128,128,128)
	c.OutdoorAmbient=Color3.fromRGB(128,128,128)
	c.PrioritizeLightingQuality=false
	c.LightingStyle=Enum.LightingStyle.Soft
end)

pcall(function()
	local t=b:FindFirstChildOfClass("Terrain")
	if t then
		t.Decoration=false
		t.WaterWaveSize=0
		t.WaterWaveSpeed=0
		t.WaterReflectance=0
		t.WaterTransparency=1
	end
end)

local l={
	"particle","particles","vfx","fx","effect","effects",
	"trail","beam","smoke","fire","spark","sparks","glow",
	"flash","explosion","blast","shockwave","wave","aura",
	"ring","slash","energy","projectile","bullet","missile",
	"attack","skill","ability","portal","control","zone",
	"field","domain","charge","impact","hit","damage"
}

local function m(o)
	if not j then return false end
	local x=o
	while x and x~=b do
		if string.find(string.lower(x.Name),"dragon") then
			return true
		end
		x=x.Parent
	end
	return false
end

local function n(o)
	local x=string.lower(o.Name)
	for _,w in ipairs(l) do
		if string.find(x,w) then
			return true
		end
	end
	return false
end

local function o(x)
	if m(x) then return end
	if x:IsA("ParticleEmitter") then
		x.Enabled=false
		x.Rate=0
		x.TimeScale=0
		pcall(function()x.LocalTransparencyModifier=1 end)
		x:Clear()
	end
end

local function p(x)
	if m(x) then return end
	if x:IsA("Trail") then
		x.Enabled=false
		x.Lifetime=0
		pcall(function()x.LocalTransparencyModifier=1 end)
	end
end

local function q(x)
	if m(x) then return end
	if x:IsA("Beam") then
		x.Enabled=false
		pcall(function()x.LocalTransparencyModifier=1 end)
	end
end

local function r(x)
	if m(x) then return end

	if x:IsA("Smoke") then
		x.Enabled=false
		x.Opacity=0
	end

	if x:IsA("Fire") then
		x.Enabled=false
		x.Heat=0
		x.Size=0
	end

	if x:IsA("Sparkles") then
		x.Enabled=false
	end

	if x:IsA("Highlight") then
		x.Enabled=false
		x.FillTransparency=1
		x.OutlineTransparency=1
	end

	if x:IsA("PointLight")
		or x:IsA("SpotLight")
		or x:IsA("SurfaceLight") then
		x.Enabled=false
		x.Brightness=0
		x.Range=0
	end

	if x:IsA("Explosion") then
		x.Visible=false
	end
end

local function s(x)
	if m(x) then return end

	if x:IsA("Decal") then
		x.Transparency=1
	end

	if x:IsA("Texture") then
		x.Transparency=1
	end

	if x:IsA("SurfaceAppearance") then
		pcall(function()x:Destroy()end)
	end
end

local function t(x)
	if m(x) then return end
	if not x:IsA("BasePart") then return end
	if not n(x) then return end

	x.CastShadow=false
	x.Reflectance=0
	x.LocalTransparencyModifier=1
end

local function u(x)
	if not k then return end
	if m(x) then return end
	if not x:IsA("BasePart") then return end

	x.CastShadow=false
	x.Reflectance=0

	pcall(function()
		x.Material=Enum.Material.SmoothPlastic
	end)
end

local function v(ch)
	if not h then return end

	if ch==f.Character and not i then
		return
	end

	if m(ch) then return end

	for _,x in ipairs(ch:GetDescendants()) do
		if x:IsA("Accessory") then
			for _,z in ipairs(x:GetDescendants()) do
				if z:IsA("BasePart") then
					z.LocalTransparencyModifier=1
				elseif z:IsA("Decal") or z:IsA("Texture") then
					z.Transparency=1
				end
			end
		end

		if x:IsA("Shirt")
			or x:IsA("Pants")
			or x:IsA("ShirtGraphic") then
			pcall(function()x.Parent=nil end)
		end
	end
end

local function w(x)
	if not g then return end
	if not x or not x.Parent then return end

	o(x)
	p(x)
	q(x)
	r(x)
	s(x)
	t(x)
	u(x)
end

for _,x in ipairs(c:GetDescendants()) do
	w(x)
end

for _,x in ipairs(b:GetDescendants()) do
	w(x)
end

for _,x in ipairs(b:GetDescendants()) do
	if x:IsA("Clouds") then
		pcall(function()x.Enabled=false end)
	end
end

for _,x in ipairs(c:GetChildren()) do
	if x:IsA("BloomEffect")
		or x:IsA("BlurEffect")
		or x:IsA("ColorCorrectionEffect")
		or x:IsA("DepthOfFieldEffect")
		or x:IsA("SunRaysEffect") then
		x.Enabled=false
	end

	if x:IsA("Atmosphere") then
		x.Density=0
		x.Haze=0
		x.Glare=0
	end
end

for _,pl in ipairs(a:GetPlayers()) do
	if pl.Character then
		v(pl.Character)

		for _,x in ipairs(pl.Character:GetDescendants()) do
			w(x)
		end
	end
end

a.PlayerAdded:Connect(function(pl)
	pl.CharacterAdded:Connect(function(ch)
		task.wait(.3)
		v(ch)

		for _,x in ipairs(ch:GetDescendants()) do
			w(x)
		end
	end)
end)

b.DescendantAdded:Connect(function(x)
	task.defer(function()
		if x and x.Parent then
			w(x)

			local ch=x:FindFirstAncestorOfClass("Model")

			if ch then
				local pl=a:GetPlayerFromCharacter(ch)

				if pl and pl~=f then
					v(ch)
				end
			end
		end
	end)
end)

c.DescendantAdded:Connect(function(x)
	task.defer(function()
		if x and x.Parent then
			w(x)
		end
	end)
end)

local x=Instance.new("ScreenGui")
x.Name="SpaceAntLag"
x.ResetOnSpawn=false
x.IgnoreGuiInset=false
x.Parent=f:WaitForChild("PlayerGui")

local y=Instance.new("Frame")
y.Size=UDim2.new(0,300,0,72)
y.Position=UDim2.new(1,-315,0,20)
y.BackgroundColor3=Color3.fromRGB(5,5,8)
y.BackgroundTransparency=.12
y.Parent=x

local z=Instance.new("UICorner")
z.CornerRadius=UDim.new(0,10)
z.Parent=y

local A=Instance.new("UIStroke")
A.Thickness=1
A.Color=Color3.fromRGB(0,120,255)
A.Parent=y

local B=Instance.new("TextLabel")
B.Size=UDim2.new(1,-10,0,32)
B.Position=UDim2.new(0,5,0,3)
B.BackgroundTransparency=1
B.TextColor3=Color3.fromRGB(255,255,255)
B.TextSize=17
B.Font=Enum.Font.GothamBold
B.Text="SPACE ANT LAG"
B.Parent=y

local C=Instance.new("TextLabel")
C.Size=UDim2.new(1,-10,0,25)
C.Position=UDim2.new(0,5,0,38)
C.BackgroundTransparency=1
C.TextColor3=Color3.fromRGB(150,190,255)
C.TextSize=13
C.Font=Enum.Font.Gotham
C.Text="https://discord.gg/WMa9NDzS6"
C.Parent=y

task.delay(30,function()
	if x and x.Parent then
		x:Destroy()
	end
end)
