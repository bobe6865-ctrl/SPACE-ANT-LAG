--==================================================
-- SPACE ANT LAG
-- ULTRA LOW GRAPHICS - PROJETO X SUPREME
--==================================================

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local UserSettingsService = UserSettings()
local UserGameSettings = UserSettingsService:GetService("UserGameSettings")

local LocalPlayer = Players.LocalPlayer

--==================================================
-- CONFIGURAÇÃO
--==================================================

local MAX_GRAPHICS = true
local HIDE_PLAYER_SKINS = true
local HIDE_OWN_SKIN = false
local PRESERVE_DRAGON = true
local SIMPLIFY_MATERIALS = true

--==================================================
-- QUALIDADE NO MÍNIMO
--==================================================

pcall(function()
	UserGameSettings.SavedQualityLevel =
		Enum.SavedQualitySetting.QualityLevel1
end)

pcall(function()
	UserGameSettings.GraphicsOptimizationMode =
		Enum.GraphicsOptimizationMode.Performance
end)

--==================================================
-- LIGHTING
--==================================================

pcall(function()
	Lighting.GlobalShadows = false
	Lighting.Brightness = 0
	Lighting.EnvironmentDiffuseScale = 0
	Lighting.EnvironmentSpecularScale = 0
	Lighting.ExposureCompensation = 0

	Lighting.FogStart = 100000
	Lighting.FogEnd = 100000

	Lighting.Ambient = Color3.fromRGB(128,128,128)
	Lighting.OutdoorAmbient = Color3.fromRGB(128,128,128)

	Lighting.PrioritizeLightingQuality = false
	Lighting.LightingStyle = Enum.LightingStyle.Soft
end)

--==================================================
-- TERRAIN
--==================================================

pcall(function()

	local Terrain = Workspace:FindFirstChildOfClass("Terrain")

	if Terrain then
		Terrain.Decoration = false
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterReflectance = 0
		Terrain.WaterTransparency = 1
	end

end)

--==================================================
-- EFEITOS
--==================================================

local EFFECT_WORDS = {
	"particle",
	"particles",
	"vfx",
	"fx",
	"effect",
	"effects",
	"trail",
	"beam",
	"smoke",
	"fire",
	"spark",
	"sparks",
	"glow",
	"flash",
	"explosion",
	"blast",
	"shockwave",
	"wave",
	"aura",
	"ring",
	"slash",
	"energy",
	"projectile",
	"bullet",
	"missile",
	"attack",
	"skill",
	"ability",
	"portal",
	"control",
	"zone",
	"field",
	"domain",
	"charge",
	"impact",
	"hit",
	"damage"
}

--==================================================
-- DRAGON
--==================================================

local function IsDragon(obj)

	if not PRESERVE_DRAGON then
		return false
	end

	local current = obj

	while current and current ~= Workspace do

		if string.find(
			string.lower(current.Name),
			"dragon"
		) then
			return true
		end

		current = current.Parent
	end

	return false
end

--==================================================
-- DETECTAR EFEITO PELO NOME
--==================================================

local function IsEffectName(obj)

	local name = string.lower(obj.Name)

	for _,word in ipairs(EFFECT_WORDS) do

		if string.find(name, word) then
			return true
		end

	end

	return false
end

--==================================================
-- PARTICLES
--==================================================

local function DisableParticle(obj)

	if IsDragon(obj) then
		return
	end

	if obj:IsA("ParticleEmitter") then

		obj.Enabled = false
		obj.Rate = 0
		obj.TimeScale = 0

		pcall(function()
			obj.LocalTransparencyModifier = 1
		end)

		obj:Clear()

	end
end

--==================================================
-- TRAIL
--==================================================

local function DisableTrail(obj)

	if IsDragon(obj) then
		return
	end

	if obj:IsA("Trail") then

		obj.Enabled = false
		obj.Lifetime = 0

		pcall(function()
			obj.LocalTransparencyModifier = 1
		end)

	end
end

--==================================================
-- BEAM
--==================================================

local function DisableBeam(obj)

	if IsDragon(obj) then
		return
	end

	if obj:IsA("Beam") then

		obj.Enabled = false

		pcall(function()
			obj.LocalTransparencyModifier = 1
		end)

	end
end

--==================================================
-- OUTROS EFEITOS
--==================================================

local function DisableOtherEffects(obj)

	if IsDragon(obj) then
		return
	end

	if obj:IsA("Smoke") then
		obj.Enabled = false
		obj.Opacity = 0
	end

	if obj:IsA("Fire") then
		obj.Enabled = false
		obj.Heat = 0
		obj.Size = 0
	end

	if obj:IsA("Sparkles") then
		obj.Enabled = false
	end

	if obj:IsA("Highlight") then
		obj.Enabled = false
		obj.FillTransparency = 1
		obj.OutlineTransparency = 1
	end

	if obj:IsA("PointLight")
		or obj:IsA("SpotLight")
		or obj:IsA("SurfaceLight") then

		obj.Enabled = false
		obj.Brightness = 0
		obj.Range = 0
	end

	if obj:IsA("Explosion") then
		obj.Visible = false
	end
end

--==================================================
-- TEXTURAS
--==================================================

local function DisableTextures(obj)

	if IsDragon(obj) then
		return
	end

	if obj:IsA("Decal") then
		obj.Transparency = 1
	end

	if obj:IsA("Texture") then
		obj.Transparency = 1
	end

	if obj:IsA("SurfaceAppearance") then

		pcall(function()
			obj:Destroy()
		end)

	end
end

--==================================================
-- PARTES DE EFEITO
--==================================================

local function ReduceEffectPart(obj)

	if IsDragon(obj) then
		return
	end

	if not obj:IsA("BasePart") then
		return
	end

	if not IsEffectName(obj) then
		return
	end

	obj.CastShadow = false
	obj.Reflectance = 0
	obj.LocalTransparencyModifier = 1
end

--==================================================
-- MATERIAIS
--==================================================

local function SimplifyPart(obj)

	if not SIMPLIFY_MATERIALS then
		return
	end

	if IsDragon(obj) then
		return
	end

	if not obj:IsA("BasePart") then
		return
	end

	obj.CastShadow = false
	obj.Reflectance = 0

	pcall(function()
		obj.Material = Enum.Material.SmoothPlastic
	end)
end

--==================================================
-- SKINS DOS PLAYERS
--==================================================

local function HidePlayerSkin(character)

	if not HIDE_PLAYER_SKINS then
		return
	end

	if character == LocalPlayer.Character
		and not HIDE_OWN_SKIN then
		return
	end

	if IsDragon(character) then
		return
	end

	for _,obj in ipairs(character:GetDescendants()) do

		if obj:IsA("Accessory") then

			for _,child in ipairs(obj:GetDescendants()) do

				if child:IsA("BasePart") then
					child.LocalTransparencyModifier = 1

				elseif child:IsA("Decal")
					or child:IsA("Texture") then

					child.Transparency = 1
				end

			end
		end

		if obj:IsA("Shirt")
			or obj:IsA("Pants")
			or obj:IsA("ShirtGraphic") then

			pcall(function()
				obj.Parent = nil
			end)

		end
	end
end

--==================================================
-- PROCESSAR OBJETO
--==================================================

local function Process(obj)

	if not MAX_GRAPHICS then
		return
	end

	if not obj or not obj.Parent then
		return
	end

	DisableParticle(obj)
	DisableTrail(obj)
	DisableBeam(obj)
	DisableOtherEffects(obj)
	DisableTextures(obj)
	ReduceEffectPart(obj)
	SimplifyPart(obj)
end

--==================================================
-- PROCESSAR MAPA
--==================================================

for _,obj in ipairs(Lighting:GetDescendants()) do
	Process(obj)
end

for _,obj in ipairs(Workspace:GetDescendants()) do
	Process(obj)
end

--==================================================
-- CLOUDS
--==================================================

for _,obj in ipairs(Workspace:GetDescendants()) do

	if obj:IsA("Clouds") then

		pcall(function()
			obj.Enabled = false
		end)

	end
end

--==================================================
-- PÓS-PROCESSAMENTO
--==================================================

for _,obj in ipairs(Lighting:GetChildren()) do

	if obj:IsA("BloomEffect")
		or obj:IsA("BlurEffect")
		or obj:IsA("ColorCorrectionEffect")
		or obj:IsA("DepthOfFieldEffect")
		or obj:IsA("SunRaysEffect") then

		obj.Enabled = false
	end

	if obj:IsA("Atmosphere") then

		obj.Density = 0
		obj.Haze = 0
		obj.Glare = 0

	end
end

--==================================================
-- PLAYERS EXISTENTES
--==================================================

for _,player in ipairs(Players:GetPlayers()) do

	if player.Character then

		HidePlayerSkin(player.Character)

		for _,obj in ipairs(player.Character:GetDescendants()) do
			Process(obj)
		end

	end
end

--==================================================
-- NOVOS PLAYERS
--==================================================

Players.PlayerAdded:Connect(function(player)

	player.CharacterAdded:Connect(function(character)

		task.wait(0.3)

		HidePlayerSkin(character)

		for _,obj in ipairs(character:GetDescendants()) do
			Process(obj)
		end

	end)

end)

--==================================================
-- NOVOS OBJETOS
--==================================================

Workspace.DescendantAdded:Connect(function(obj)

	task.defer(function()

		if obj and obj.Parent then

			Process(obj)

			local character =
				obj:FindFirstAncestorOfClass("Model")

			if character then

				local player =
					Players:GetPlayerFromCharacter(character)

				if player
					and player ~= LocalPlayer then

					HidePlayerSkin(character)

				end
			end
		end

	end)

end)

--==================================================
-- NOVOS EFEITOS NO LIGHTING
--==================================================

Lighting.DescendantAdded:Connect(function(obj)

	task.defer(function()

		if obj and obj.Parent then
			Process(obj)
		end

	end)

end)

--==================================================
-- NOTIFICAÇÃO SPACE ANT LAG
--==================================================

local gui = Instance.new("ScreenGui")
gui.Name = "SpaceAntLag"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = false
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")

frame.Size = UDim2.new(0, 300, 0, 72)
frame.Position = UDim2.new(1, -315, 0, 20)

frame.BackgroundColor3 =
	Color3.fromRGB(5, 5, 8)

frame.BackgroundTransparency = 0.12
frame.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = frame

local stroke = Instance.new("UIStroke")
stroke.Thickness = 1
stroke.Color = Color3.fromRGB(0, 120, 255)
stroke.Parent = frame

--==================================================
-- NOME
--==================================================

local title = Instance.new("TextLabel")

title.Size = UDim2.new(1, -10, 0, 32)
title.Position = UDim2.new(0, 5, 0, 3)

title.BackgroundTransparency = 1
title.TextColor3 =
	Color3.fromRGB(255,255,255)

title.TextSize = 17
title.Font = Enum.Font.GothamBold
title.Text = "SPACE ANT LAG"

title.Parent = frame

--==================================================
-- DISCORD
--==================================================

local discord = Instance.new("TextLabel")

discord.Size = UDim2.new(1, -10, 0, 25)
discord.Position = UDim2.new(0, 5, 0, 38)

discord.BackgroundTransparency = 1
discord.TextColor3 =
	Color3.fromRGB(150,190,255)

discord.TextSize = 13
discord.Font = Enum.Font.Gotham

discord.Text = "https://discord.gg/WMa9NDzS6"

discord.Parent = frame

--==================================================
-- 30 SEGUNDOS
--==================================================

task.delay(30, function()

	if gui and gui.Parent then
		gui:Destroy()
	end

end)

--==================================================
-- FINAL
--==================================================

print("======================================")
print(" SPACE ANT LAG ATIVADO")
print(" QUALIDADE: MÍNIMA")
print(" PERFORMANCE: MÁXIMA")
print(" PARTICULAS: OFF")
print(" TRAILS: OFF")
print(" BEAMS: OFF")
print(" LIGHTS: OFF")
print(" SOMBRAS: OFF")
print(" TEXTURAS: REDUZIDAS")
print(" SKINS: REDUZIDAS")
print(" MATERIAIS: SIMPLIFICADOS")
print(" DRAGON: PRESERVADO")
print("======================================")
