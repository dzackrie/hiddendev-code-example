# hiddendev code example
 
--// VARIABLES \\ --
local i_n = Instance.new
local v3 = Vector3.new
local ud2 = UDim2.new
local c3 = Color3.new
local bcn = BrickColor.new
local cfn = CFrame.new
local cfa = CFrame.Angles
local ray = Ray.new
local rand = math.random
local ceil = math.ceil
local floor = math.floor
local sqr = math.sqrt
local abs = math.abs
local hst = Enum.HumanoidStateType
local uit = Enum.UserInputType
local kc = Enum.KeyCode
local tinf = TweenInfo.new
-- SERVICES
local ts = game:GetService("TweenService")
local cs = game:GetService("CollectionService")
local uis = game:GetService("UserInputService")
local rs = game:GetService("ReplicatedStorage")
-- MODULES
local DATA = nil
local Universal = require(rs._Main._Modules.Universal)
local StatusChecker = Universal["StatusCheck"]
local Hitbox = Universal["Hitbox"]
local Cooldown = Universal["Cooldown"]
local AnimCache = Universal["AnimCache"].new()
local Sound = Universal["Sound"]
-- MISC
local plr = game.Players.LocalPlayer
local cam = workspace.CurrentCamera
local char = plr.Character or plr.CharacterAdded:wait()
local hrp = char:FindFirstChild("HumanoidRootPart")
local hume = char:FindFirstChildOfClass("Humanoid")
local mouse = plr:GetMouse()
local UI = nil

game.ReplicatedStorage._Main._Remotes.LoadClass.OnClientEvent:Connect(function(data)
	--get the cd data and stuff lol
	DATA = data
	UI = plr.PlayerGui:FindFirstChild("CLASSUI")
end)

repeat wait() until UI ~= nil
--// SPECIAL FUNCTIONS \\ --

local UsingAbility = false
local inStance = false
local inStance2 = false
local usingCrit = false
local Critical = char:WaitForChild("Critical")

local atkcount = 1
function atk()

	local is = {
		"ATK"
	}
	if StatusChecker.CheckPlayer({plr, char}) then
		if Cooldown.CheckCooldown2({plr, is}) == false then
			if usingCrit ==  true then
				local is2 = is[1]
				displaycd(is2,.6)
				Cooldown.AddCooldown2({
					plr,
					{is2, .6}
				})
				is2 = "ATKALT"
				playSound({
					id = DATA.Sounds.SpinCut,
					to = hrp,
					volume = 1
				})
				local track = nil
				if atkcount == 1 then
					track = cacheAnim(DATA.Animations.SSlashRight)
					track:Play()
					track:AdjustSpeed(1.5)
					atkcount = 2 
				else
					track = cacheAnim(DATA.Animations.SSlashLeft)
					track:Play()
					track:AdjustSpeed(1.5)
					atkcount = 1
				end
				LoadEffect("SamuraiSpinSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
				ts:Create(cam,TweenInfo.new(.15, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut,0,true),{FieldOfView = 80}):Play()
				local hitdb = {}
				coroutine.resume(coroutine.create(function()
					for i = 1,3 do
						local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame,DATA[is2].HitboxSize,.5}
						--hitboxinfo = {nameofhbx,parent,position,size}
						local box = Hitbox.CreateHitbox({plr, HitboxInfo})
						for i,v in pairs(box) do
							local Hit = v.Parent
							if Hit:FindFirstChild("Humanoid") then
								if StatusChecker.CheckPlayer2({plr, Hit}) then
									if not table.find(hitdb, Hit.Name) then
										table.insert(hitdb, Hit.Name)
										rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
									end
								end
							end
						end 
						swait(3)
					end
				end))
			else
				local is2 = is[1]
				displaycd(is2)
				Cooldown.AddCooldown2({
					plr,
					{is2, DATA[is2].CD}
				})
				local track = nil
				if atkcount == 1 then
					track = cacheAnim(DATA.Animations.SlashA)
					track:Play()
					atkcount = 2 
				else
					track = cacheAnim(DATA.Animations.SlashB)
					track:Play()
					atkcount = 1
				end
				playSound({
					id = DATA.Sounds.Slash,
					to = hrp,
					volume = 0.3
				})
				local hitdb = {}
				coroutine.resume(coroutine.create(function()
					for i = 1,3 do
						local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame * CFrame.new(0,0,-3),DATA[is2].HitboxSize,.5}
						--hitboxinfo = {nameofhbx,parent,position,size}
						local box = Hitbox.CreateHitbox({plr, HitboxInfo})
						for i,v in pairs(box) do
							local Hit = v.Parent
							if Hit:FindFirstChild("Humanoid") then
								if StatusChecker.CheckPlayer2({plr, Hit}) then
									if not table.find(hitdb, Hit.Name) then
										table.insert(hitdb, Hit.Name)
										rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
									end
								end
							end
						end 
						swait(3)
					end
				end))
				track.Stopped:Wait()
			end
		end
	end
end

function ab1()
	local is = {"AB1"}
	if StatusChecker.CheckPlayer({plr, char}) then
		if Cooldown.CheckCooldown2({plr, is}) == false then
			if inStance2 == true then
				inStance2 = false
				UsingAbility = false
				local is2 = is[1]
				displaycd(is2, 1.5)
				Cooldown.AddCooldown2({
					plr,
					{is2, 1.5}
				})
				is2 = "AB1ALT"
				task.spawn(function()
					for _, v in pairs(hume:GetPlayingAnimationTracks()) do
						if v.Name == "Stance" then
							v:Stop()
						end
					end
				end)
				playSound({
					id = 163621622,
					to = hrp,
					volume = 1
				})
				playSound({
					id = DATA.Sounds.DownSlash,
					to = hrp,
					volume = 1
				})
				local track = cacheAnim(DATA.Animations.DownSlash)
				track:Play()
				LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.Occluded, t = .5})
				LoadEffect("SamuraiDownSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
				local hitdb = {}
				coroutine.resume(coroutine.create(function()
					for i = 1,1 do
						local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame * CFrame.new(0,0,-2.85),DATA[is2].HitboxSize,.5}
						--hitboxinfo = {nameofhbx,parent,position,size}
						local box = Hitbox.CreateHitbox({plr, HitboxInfo})
						for i,v in pairs(box) do
							local Hit = v.Parent
							if Hit:FindFirstChild("Humanoid") then
								if StatusChecker.CheckPlayer2({plr, Hit}) then
									if not table.find(hitdb, Hit.Name) then
										table.insert(hitdb, Hit.Name)
										rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
									end
								end
							end
						end 
						swait(3)
					end
				end))
			else
				if hume.FloorMaterial ~= Enum.Material.Air then
					if UsingAbility then return end
					UsingAbility = true
					inStance = true
					local is2 = is[1]
					displaycd(is[1])
					Cooldown.AddCooldown2({
						plr,
						{is[1], DATA[is[1]].CD}
					})
					hume.JumpPower = 0
					hume.WalkSpeed = 0
					task.delay(.5, function()
						hume.JumpPower = 50
						hume.WalkSpeed = 30
						UsingAbility = false
					end)
					local track = cacheAnim(DATA.Animations.DashSlash, "Stance")
					track:Play()
					track:AdjustSpeed(1.5)
					playSound({
						id = 163621622,
						to = hrp,
						volume = 1
					})
					LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.Occluded, t = .5})
					task.delay(.4, function()
					
						if inStance == true then
							inStance = false
							if not char:FindFirstChild("Disabled") then
								playSound({
									id = DATA.Sounds.DashSlash,
									to = hrp,
									volume = 1
								})
								local KB = Instance.new("BodyVelocity", hrp)
								KB.Name = "KB"
								KB.Velocity = hrp.CFrame.lookVector * 150
								KB.MaxForce = Vector3.new(50000,50000,50000)
								game.Debris:AddItem(KB, 0.15)
								LoadEffect("DashSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
								local hitdb = {}
								coroutine.resume(coroutine.create(function()
									for i = 1,1 do
										local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame * CFrame.new(0,0,-15),DATA[is2].HitboxSize,.5}
										--hitboxinfo = {nameofhbx,parent,position,size}
										local box = Hitbox.CreateHitbox({plr, HitboxInfo})
										for i,v in pairs(box) do
											local Hit = v.Parent
											if Hit:FindFirstChild("Humanoid") then
												if StatusChecker.CheckPlayer2({plr, Hit}) then
													if not table.find(hitdb, Hit.Name) then
														table.insert(hitdb, Hit.Name)
														rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
													end
												end
											end
										end 
										swait(3)
									end
								end))
							end
						end
					end)
				end
			end
		end
	end
end

function ab2()
	local is = {"AB2"}
	if StatusChecker.CheckPlayer({plr, char}) then
		if Cooldown.CheckCooldown2({plr, is}) == false then
			if inStance == true then
				inStance = false
				local is2 = is[1]
				displaycd(is2, 1.5)
				Cooldown.AddCooldown2({
					plr,
					{is2, 1.5}
				})
				is2 = "AB2ALT"
				task.spawn(function()
					for _, v in pairs(hume:GetPlayingAnimationTracks()) do
						if v.Name == "Stance" then
							v:Stop()
						end
					end
				end)
				playSound({
					id = 163621622,
					to = hrp,
					volume = 1
				})
				playSound({
					id = DATA.Sounds.UpSlash,
					to = hrp,
					volume = 1
				})
				local track = cacheAnim(DATA.Animations.UpSlash)
				track:Play()
				LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.Occluded, t = .5})
				LoadEffect("SamuraiUpSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
				local hitdb = {}
				coroutine.resume(coroutine.create(function()
					for i = 1,1 do
						local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame,DATA[is2].HitboxSize,.5}
						--hitboxinfo = {nameofhbx,parent,position,size}
						local box = Hitbox.CreateHitbox({plr, HitboxInfo})
						for i,v in pairs(box) do
							local Hit = v.Parent
							if Hit:FindFirstChild("Humanoid") then
								if StatusChecker.CheckPlayer2({plr, Hit}) then
									if not table.find(hitdb, Hit.Name) then
										table.insert(hitdb, Hit.Name)
										rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
									end
								end
							end
						end 
						swait(3)
					end
				end))
			else
				if UsingAbility then return end
				UsingAbility = true
				inStance2 = true
				local is2 = is[1]
				displaycd(is[1])
				Cooldown.AddCooldown2({
					plr,
					{is[1], DATA[is[1]].CD}
				})
				local track = cacheAnim(DATA.Animations.SpinSlash, "Stance")
				track:Play()
				track:AdjustSpeed(1.3)
				playSound({
					id = 163621622,
					to = hrp,
					volume = 1
				})
				LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.Occluded, t = .5})
				task.delay(.3, function()
			
					if inStance2 == true then
						inStance2 = false
						if not char:FindFirstChild("Disabled") then
							for i = 1, 3 do
								playSound({
									id = DATA.Sounds.SpinCut,
									to = hrp,
									volume = 1
								})
								local KB = Instance.new("BodyVelocity", hrp)
								KB.Name = "KB"
								KB.Velocity = hrp.CFrame.lookVector * 60
								KB.MaxForce = Vector3.new(50000,50000,50000)
								game.Debris:AddItem(KB, 0.15)
								LoadEffect("SamuraiSpinSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
								local hitdb = {}
								coroutine.resume(coroutine.create(function()
									for i = 1,1 do
										local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame,DATA[is2].HitboxSize,.5}
										--hitboxinfo = {nameofhbx,parent,position,size}
										local box = Hitbox.CreateHitbox({plr, HitboxInfo})
										for i,v in pairs(box) do
											local Hit = v.Parent
											if Hit:FindFirstChild("Humanoid") then
												if StatusChecker.CheckPlayer2({plr, Hit}) then
													if not table.find(hitdb, Hit.Name) then
														table.insert(hitdb, Hit.Name)
														rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit})
													end
												end
											end
										end 
										swait(3)
									end
								end))
								wait(.25)
							end
						end
						wait(.2)
						UsingAbility = false
					end
				end)
			end
		end
	end
end

function crit()
	local is = {"CRT"}
	if StatusChecker.CheckPlayer({plr, char}) then
		if Cooldown.CheckCooldown2({plr, is}) == false then
			if Critical.Value == Critical:GetAttribute("MaxCritical") then
				EndCrit(Critical.Value)
				UsingAbility = true
				playSound({
					id = 163621622,
					to = hrp,
					volume = 1
				})
				LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.AlwaysOnTop, t = .5})

				playSound({
					id = DATA.Sounds.Unsheathe,
					to = hrp,
					volume = 1
				})
				playSound({
					id = DATA.Sounds.Summon,
					to = hrp,
					volume = 1
				})
				local track = cacheAnim(DATA.Animations.Unsheathe)
				track:Play()
				LoadEffect("SamuraiSwordBuff",{char = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, b = true})
				track.Stopped:Wait()
				local track2 = cacheAnim(DATA.Animations.Run)
				track2.Looped = true
				track2:Play()
				ts:Create(cam,TweenInfo.new(.3), {FieldOfView = 90}):Play()
				usingCrit = true
				task.spawn(function()
					local BodyVelocity = Instance.new("BodyVelocity")
					BodyVelocity.MaxForce = Vector3.new(50000, 0, 50000)
					BodyVelocity.Velocity = hrp.CFrame.LookVector * 40
					BodyVelocity.Parent = hrp
					
					local originalOrientation = hrp.Orientation

					local connection = nil
					connection = game:GetService("RunService").Stepped:Connect(function()
						local newOrientation = hrp.Orientation

						if newOrientation ~= originalOrientation then
							originalOrientation = newOrientation

							BodyVelocity.Velocity = hrp.CFrame.LookVector * 40
						end
					end)
					
					task.spawn(function()
						repeat
							playSound({
								id = DATA.Sounds.Step,
								to = hrp,
								volume = 1
							})
							wait(.2)
						until usingCrit == false
					end)
					
					BodyVelocity.Destroying:Connect(function()
						connection:Disconnect()
					end)
					task.delay(10, function()
						hume.JumpPower = 0
						BodyVelocity:Destroy()
						usingCrit = false
						track2:Stop()
						ts:Create(cam,TweenInfo.new(.3), {FieldOfView = 70}):Play()
						LoadEffect("SamuraiSwordBuff",{char = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, b = false})
						local is2 = "AB1"
						hume.JumpPower = 0
						hume.WalkSpeed = 0
						task.delay(.5, function()
							hume.JumpPower = 50
							hume.WalkSpeed = 30
							UsingAbility = false
						end)
						local track = cacheAnim(DATA.Animations.DashSlash, "Stance")
						track:Play()
						track:AdjustSpeed(1.5)
						playSound({
							id = 163621622,
							to = hrp,
							volume = 1
						})
						LoadEffect("Flash",{to = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, ontop = Enum.HighlightDepthMode.Occluded, t = .5})
						task.delay(.4, function()
							playSound({
								id = DATA.Sounds.DashSlash,
								to = hrp,
								volume = 1
							})
							local KB = Instance.new("BodyVelocity", hrp)
							KB.Name = "KB"
							KB.Velocity = hrp.CFrame.lookVector * 150
							KB.MaxForce = Vector3.new(50000,50000,50000)
							game.Debris:AddItem(KB, 0.15)
							LoadEffect("DashSlash",{char = char, col = game.Players.LocalPlayer.ProfileData.TertiaryColor.Value})
							local hitdb = {}
							coroutine.resume(coroutine.create(function()
								for i = 1,1 do
									local HitboxInfo = {"Cube", char, char.HumanoidRootPart.CFrame * CFrame.new(0,0,-15),DATA[is2].HitboxSize,.5}
									--hitboxinfo = {nameofhbx,parent,position,size}
									local box = Hitbox.CreateHitbox({plr, HitboxInfo})
									for i,v in pairs(box) do
										local Hit = v.Parent
										if Hit:FindFirstChild("Humanoid") then
											if StatusChecker.CheckPlayer2({plr, Hit}) then
												if not table.find(hitdb, Hit.Name) then
													table.insert(hitdb, Hit.Name)
													rs._Main._Remotes.Damage:FireServer({attack =  is2, targ = Hit, dmg = 5})
												end
											end
										end
									end 
									swait(3)
								end
							end))	
						end)
					end)
				end)
			end
		end
	end
end

-- // FUNCTIONS \\ --
function displaycd(a,extra)
	local cd = nil
	if extra then
		cd = extra
	else
		cd = DATA[a].CD
	end
	if cd > 0 then
		local button = UI.Main[a]
		button.BackgroundTransparency = .8
		button.ImageTransparency = .8

		task.spawn(function()
			repeat 
				cd -= .1
				button.CD.Text = tostring(math.round(cd*100)/100)
				task.wait(.1)
			until cd <= 0.2

			cd = 0
			button.BackgroundTransparency = 0.4
			button.ImageTransparency = 0
			button.CD.Text = ""
			button.Frame.BackgroundTransparency = 0
			game.TweenService:Create(button.Frame,TweenInfo.new(.3),{BackgroundTransparency  =1}):Play()
		end)
	end
end

function EndCrit(CriticalAmount)
	if CriticalAmount >= 100 then
		local EndCrit = game.ReplicatedStorage._Main._Remotes.EndCrit
		EndCrit:FireServer()
	end
end

function Dash(Player : Player, DashTime : number, Amount : number)
	local Player = Player
	local DashTime = DashTime
	local Amount = Amount

	local MaxForce = Vector3.new(35000,35000,35000) 
	local P = 1250

	local Character = Player.Character
	local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

	local BodyVelocity = Instance.new("BodyVelocity",HumanoidRootPart)
	BodyVelocity.MaxForce = MaxForce

	BodyVelocity.Velocity = HumanoidRootPart.CFrame.lookVector * Amount

	task.wait(DashTime)
	BodyVelocity:Destroy()
end

function getfps()
	return math.floor(1/game:GetService('RunService').RenderStepped:Wait())
end

function LoadEffect(EffectName,data)
	local EffectsRemote = rs._Main._Remotes.LoadEffect
	local EffectsModule = require(rs._Main._Modules.Universal.Effects)

	EffectsRemote:FireServer(EffectName,data)
	for Effect, EffectFunction in pairs(EffectsModule) do
		if Effect == EffectName then
			task.spawn(function()
				EffectFunction(data)
			end)
		end
	end
end
function swait(n)
	local rs = game:GetService('RunService')
	--if n is not provided, wait 1 frame
	if not n then
		rs.RenderStepped:wait()
	else
		--define the current unix time
		local t = time() -- this provides a value, like 1600, it would be a lot bigger but its just a time frame
		--constantly repeat frames until the time current time, 1670-"1600" is greater then or equal to the number we provided, divided by 60
		--what this means idk i havent slept
		repeat rs.RenderStepped:wait() until time()-t >= n/getfps()
	end
end


function playSound(data)
	Sound.PlaySound(data)
	rs._Main._Remotes.PlaySound:FireServer(data)
end

function cacheAnim(id,name)
	local Animation = Instance.new("Animation",hume)
	Animation.AnimationId = "rbxassetid://"..id
	if name then
		Animation.Name = name
	end
	local track = AnimCache.Load(hume, Animation)
	game.Debris:AddItem(Animation,1)
	return track
end
playSound({
	id = DATA.Sounds.Unsheathe,
	to = hrp,
	volume = 1
})
playSound({
	id = DATA.Sounds.Summon,
	to = hrp,
	volume = 1
})
LoadEffect("SamuraiSwordBuff",{char = char, col = plr:FindFirstChild("ProfileData").TertiaryColor.Value, t = 3})

--// INPUT \\ --
uis.InputBegan:Connect(function(input,gpe)
	if gpe == false then
		--CONSOLE
		if input.KeyCode ==  Enum.KeyCode.ButtonR2 then
			atk()
		end
		if input.KeyCode ==  Enum.KeyCode.ButtonX then
			ab1()
		end
		if input.KeyCode ==  Enum.KeyCode.ButtonY then
			ab2()
		end
		if input.KeyCode ==  Enum.KeyCode.ButtonB then
			crit()
		end
		--KEYBOARD
		if input.KeyCode == Enum.KeyCode.Q then
			ab1()
		end
		if input.KeyCode == Enum.KeyCode.E then
			ab2()
		end
		if input.KeyCode == Enum.KeyCode.F then
			crit()
		end	
	end
end)

--mouse event
local held = false

function changed()
	while held == true do
		if held == false then
			break
		else
			atk()
			task.wait()
		end
	end
end

mouse.Button1Down:Connect(function()
	held = true
	changed()
end)

mouse.Button1Up:Connect(function()
	held = false
	changed()
end)

--/ mobile support \--
UI.Main.ATK.MouseButton1Up:Connect(function()
	atk()
end)
UI.Main.AB1.MouseButton1Up:Connect(function()
	ab1()
end)
UI.Main.AB2.MouseButton1Up:Connect(function()
	ab2()
end)
UI.Main.CRT.MouseButton1Up:Connect(function()
	crit()
end)

