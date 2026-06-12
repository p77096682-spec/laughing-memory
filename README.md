# laughing-- [[ X100 LUCKY & AUTO ROLL SCRIPT ]] --
repeat wait() until game:IsLoaded()

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("X100 Lucky Hub - Premium v2.4", "Midnight")

-- MAIN TAB
local Main = Window:NewTab("Main Features")
local MainSection = Main:NewSection("Lucky Boosters")

MainSection:NewToggle("Enable X100 Lucky (Client Boost)", "Tăng tỷ lệ may mắn giả lập & Tối ưu kết nối", function(state)
    getgenv().X100Lucky = state
    if getgenv().X100Lucky then
        spawn(function()
            while getgenv().X100Lucky do
                -- Giả lập gửi gói tin tối ưu hóa thời gian roll (Anti-Lag Roll)
                local args = { [1] = "Lucky_Boost", [2] = true }
                game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("ServerEvent"):FireServer(unpack(args))
                
                -- Tối ưu hóa FPS để roll mượt hơn
                setfpscap(120)
                wait(0.1)
            end
        end)
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "X100 Lucky";
            Text = "Đã kích hoạt chế độ siêu may mắn!";
            Duration = 5;
        })
    else
        setfpscap(60)
    end
end)

MainSection:NewToggle("Auto Roll / Auto Spin", "Tự động thực hiện vòng quay may mắn", function(state)
    getgenv().AutoRoll = state
    while getgenv().AutoRoll do
        -- Thay thế "SpinRemote" bằng tên Remote Roll cụ thể của tựa game bạn đang chơi
        pcall(function()
            game:GetService("ReplicatedStorage").Events.Spin:InvokeServer()
        end)
        wait(0.5) -- Khoảng thời gian giữa các lần roll (tránh bị kích do spam)
    end
end)

-- MISC TAB
local Misc = Window:NewTab("Misc")
local MiscSection = Misc:NewSection("Bypass & Anti-Ban")

MiscSection:NewButton("Anti-Cheat Bypass", "Giảm thiểu tỷ lệ bị phát hiện bởi hệ thống", function()
    -- Lệnh xóa log cơ bản của Roblox Executor
    for i,v in pairs(getgc(true)) do
        if type(v) == "table" and rawget(v, "Detected") then
            v.Detected = function() return nil end
        end
    end
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Bypass";
        Text = "Đã kích hoạt Anti-Ban thành công.";
        Duration = 3;
    })
end)

MiscSection:NewButton("Server Hop", "Chuyển server để đổi vận may (Reset Luck)", function()
    local HttpService = game:GetService("HttpService")
    local TeleportService = game:GetService("TeleportService")
    local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
    for _, s in pairs(Servers.data) do
        if s.playing < s.maxPlayers then
            TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, game.Players.LocalPlayer)
            break
        end
    end
end)
