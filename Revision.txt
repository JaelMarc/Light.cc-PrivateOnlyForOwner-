local V2new = Vector2.new
local V3new = Vector3.new
local Drawnew = Drawing.new

repeat
    wait()
until game:IsLoaded()

grm = getrawmetatable(game)
setreadonly(grm, false)
old = grm.__namecall
grm.__namecall = newcclosure(function(self, ...)
    local args = {...}
    if tostring(args[1]) == "TeleportDetect" then
        return
    elseif tostring(args[1]) == "CHECKER_1" then
        return
    elseif tostring(args[1]) == "CHECKER" then
        return
    elseif tostring(args[1]) == "GUI_CHECK" then
        return
    elseif tostring(args[1]) == "OneMoreTime" then
        return
    elseif tostring(args[1]) == "checkingSPEED" then
        return
    elseif tostring(args[1]) == "BANREMOTE" then
        return
    elseif tostring(args[1]) == "PERMAIDBAN" then
        return
    elseif tostring(args[1]) == "KICKREMOTE" then
        return
    elseif tostring(args[1]) == "BR_KICKPC" then
        return
    elseif tostring(args[1]) == "BR_KICKMOBILE" then
        return
    end
    return old(self, ...)
end)

local library
do

    local folder = "Revision"

    if isfolder("Revision") then
        --Void
    else
        makefolder("Revision")
    end

    local services = setmetatable({}, {
        __index = function(_, service)
            if service == "InputService" then
                return game:GetService("UserInputService")
            end

            return game:GetService(service)
        end
    })

    local utility = {}

    function utility.randomstring(length)
        local str = ""
        local chars = string.split("abcdefghijklmnopqrstuvwxyz1234567890", "")

        for i = 1, length do
            local i = math.random(1, #chars)

            if not tonumber(chars[i]) then
                local uppercase = math.random(1, 2) == 2 and true or false
                str = str .. (uppercase and chars[i]:upper() or chars[i])
            else
                str = str .. chars[i]
            end
        end

        return str
    end

    function utility.create(class, properties)
        local obj = Instance.new(class)

        local forced = {
            AutoButtonColor = false
        }

        for prop, v in next, properties do
            obj[prop] = v
        end

        for prop, v in next, forced do
            pcall(function()
                obj[prop] = v
            end)
        end

        obj.Name = utility.randomstring(16)

        return obj
    end

    function utility.dragify(object, speed)
        local start, objectPosition, dragging

        speed = speed or 0

        object.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = true
                start = input.Position
                objectPosition = object.Position
            end
        end)

        object.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = false
            end
        end)

        services.InputService.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
                utility.tween(object, { speed }, { Position = UDim2.new(objectPosition.X.Scale, objectPosition.X.Offset + (input.Position - start).X, objectPosition.Y.Scale, objectPosition.Y.Offset + (input.Position - start).Y) })
            end
        end)
    end

    function utility.getrgb(color)
        local r = math.floor(color.r * 255)
        local g = math.floor(color.g * 255)
        local b = math.floor(color.b * 255)

        return r, g, b
    end

    function utility.getcenter(sizeX, sizeY)
        return UDim2.new(0.5, -(sizeX / 2), 0.5, -(sizeY / 2))
    end

    function utility.table(tbl)
        tbl = tbl or {}

        local newtbl = {}

        for i, v in next, tbl do
            if type(i) == "string" then
                newtbl[i:lower()] = v
            end
        end

        return setmetatable({}, {
            __newindex = function(_, k, v)
                rawset(newtbl, k:lower(), v)
            end,

            __index = function(_, k)
                return newtbl[k:lower()]
            end
        })
    end

    function utility.tween(obj, info, properties, callback)
        local anim = services.TweenService:Create(obj, TweenInfo.new(unpack(info)), properties)
        anim:Play()

        if callback then
            anim.Completed:Connect(callback)
        end

        return anim
    end

    function utility.makevisible(obj, bool)
        if bool == false then
            local tween

            if not obj.ClassName:find("UI") then
                if obj.ClassName:find("Text") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency + 1, TextTransparency = obj.TextTransparency + 1, TextStrokeTransparency = obj.TextStrokeTransparency + 1 })
                elseif obj.ClassName:find("Image") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency + 1, ImageTransparency = obj.ImageTransparency + 1 })
                elseif obj.ClassName:find("Scrolling") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency + 1, ScrollBarImageTransparency = obj.ScrollBarImageTransparency + 1 })
                else
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency + 1 })
                end
            end

            tween.Completed:Connect(function()
                obj.Visible = false
            end)

            for _, descendant in next, obj:GetDescendants() do
                if not descendant.ClassName:find("UI") then
                    if descendant.ClassName:find("Text") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency + 1, TextTransparency = descendant.TextTransparency + 1, TextStrokeTransparency = descendant.TextStrokeTransparency + 1 })
                    elseif descendant.ClassName:find("Image") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency + 1, ImageTransparency = descendant.ImageTransparency + 1 })
                    elseif descendant.ClassName:find("Scrolling") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency + 1, ScrollBarImageTransparency = descendant.ScrollBarImageTransparency + 1 })
                    else
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency + 1 })
                    end
                end
            end

            return tween
        elseif bool == true then
            local tween

            if not obj.ClassName:find("UI") then
                obj.Visible = true

                if obj.ClassName:find("Text") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency - 1, TextTransparency = obj.TextTransparency - 1, TextStrokeTransparency = obj.TextStrokeTransparency - 1 })
                elseif obj.ClassName:find("Image") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency - 1, ImageTransparency = obj.ImageTransparency - 1 })
                elseif obj.ClassName:find("Scrolling") then
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency - 1, ScrollBarImageTransparency = obj.ScrollBarImageTransparency - 1 })
                else
                    tween = utility.tween(obj, { 0.2 }, { BackgroundTransparency = obj.BackgroundTransparency - 1 })
                end
            end

            for _, descendant in next, obj:GetDescendants() do
                if not descendant.ClassName:find("UI") then
                    if descendant.ClassName:find("Text") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency - 1, TextTransparency = descendant.TextTransparency - 1, TextStrokeTransparency = descendant.TextStrokeTransparency - 1 })
                    elseif descendant.ClassName:find("Image") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency - 1, ImageTransparency = descendant.ImageTransparency - 1 })
                    elseif descendant.ClassName:find("Scrolling") then
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency - 1, ScrollBarImageTransparency = descendant.ScrollBarImageTransparency - 1 })
                    else
                        utility.tween(descendant, { 0.2 }, { BackgroundTransparency = descendant.BackgroundTransparency - 1 })
                    end
                end
            end

            return tween
        end
    end

    function utility.updatescrolling(scrolling, list)
        return list:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            scrolling.CanvasSize = UDim2.new(0, 0, 0, list.AbsoluteContentSize.Y)
        end)
    end

    function utility.changecolor(color, amount)
        local r, g, b = utility.getrgb(color)
        r = math.clamp(r + amount, 0, 255)
        g = math.clamp(g + amount, 0, 255)
        b = math.clamp(b + amount, 0, 255)

        return Color3.fromRGB(r, g, b)
    end

    function utility.gradient(colors)
        local colortbl = {}

        for i, color in next, colors do
            table.insert(colortbl, ColorSequenceKeypoint.new((i - 1) / (#colors - 1), color))
        end

        return ColorSequence.new(colortbl)
    end

    library = utility.table {
        flags = {},
        toggled = true,
        accent = Color3.fromRGB(162, 109, 184),
        outline = { Color3.fromRGB(121, 66, 254), Color3.fromRGB(223, 57, 137) },
        keybind = Enum.KeyCode.RightShift
    }

    local accentobjects = { gradient = {}, bg = {}, text = {} }

    function library:ChangeAccent(accent)
        library.accent = accent

        for obj, color in next, accentobjects.gradient do
            obj.Color = color(accent)
        end

        for _, obj in next, accentobjects.bg do
            obj.BackgroundColor3 = accent
        end

        for _, obj in next, accentobjects.text do
            obj.TextColor3 = accent
        end
    end

    local outlineobjs = {}

    function library:ChangeOutline(colors)
        for _, obj in next, outlineobjs do
            obj.Color = utility.gradient(colors)
        end
    end

    local gui = utility.create("ScreenGui", {})

    local flags = {}

    function library:SaveConfig(name, universal)
        local configtbl = {}
        local placeid = universal and "universal" or game.PlaceId

        for flag, _ in next, flags do
            local value = library.flags[flag]
            if typeof(value) == "EnumItem" then
                configtbl[flag] = tostring(value)
            elseif typeof(value) == "Color3" then
                configtbl[flag] = { math.floor(value.R * 255), math.floor(value.G * 255), math.floor(value.B * 255) }
            else
                configtbl[flag] = value
            end
        end

        local config = services.HttpService:JSONEncode(configtbl)
        local folderpath = string.format("%s//%s", folder, placeid)

        if not isfolder(folderpath) then
            makefolder(folderpath)
        end

        local filepath = string.format("%s//%s.json", folderpath, name)
        writefile(filepath, config)
    end

    function library:DeleteConfig(name, universal)
        local placeid = universal and "universal" or game.PlaceId

        local folderpath = string.format("%s//%s", folder, placeid)

        if isfolder(folderpath) then
            local folderpath = string.format("%s//%s", folder, placeid)
            local filepath = string.format("%s//%s.json", folderpath, name)

            delfile(filepath)
        end
    end

    function library:LoadConfig(name)
        local placeidfolder = string.format("%s//%s", folder, game.PlaceId)
        local placeidfile = string.format("%s//%s.json", placeidfolder, name)

        local filepath
        do
            if isfolder(placeidfolder) and isfile(placeidfile) then
                filepath = placeidfile
            else
                filepath = string.format("%s//universal//%s.json", folder, name)
            end
        end

        local file = readfile(filepath)
        local config = services.HttpService:JSONDecode(file)

        for flag, v in next, config do
            local func = flags[flag]
            func(v)
        end
    end

    function library:ListConfigs(universal)
        local configs = {}
        local placeidfolder = string.format("%s//%s", folder, game.PlaceId)
        local universalfolder = folder .. "//universal"

        for _, config in next, (isfolder(placeidfolder) and listfiles(placeidfolder) or {}) do
            local name = config:gsub(placeidfolder .. "\\", ""):gsub(".json", "")
            table.insert(configs, name)
        end

        if universal and isfolder(universalfolder) then
            for _, config in next, (isfolder(placeidfolder) and listfiles(placeidfolder) or {}) do
                configs[config:gsub(universalfolder .. "\\", "")] = readfile(config)
            end
        end
        return configs
    end

    function library:Watermark(options)
        local text = table.concat(options, " <font color=\"#D2D2D2\">|</font> ")

        local watermarksize = services.TextService:GetTextSize(text:gsub("<font color=\"#D2D2D2\">", ""):gsub("</font>", ""), 14, Enum.Font.Code, V2new(1000, 1000)).X + 16

        local watermark = utility.create("TextLabel", {
            ZIndex = 2,
            Size = UDim2.new(0, watermarksize, 0, 20),
            BorderColor3 = Color3.fromRGB(50, 50, 50),
            Position = UDim2.new(0, 10, 0, 10),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(30, 30, 30),
            FontSize = Enum.FontSize.Size14,
            TextStrokeTransparency = 0,
            TextSize = 14,
            RichText = true,
            TextColor3 = library.accent,
            Text = text,
            Font = Enum.Font.Code,
            Parent = gui
        })

        table.insert(accentobjects.text, watermark)

        utility.create("UIPadding", {
            PaddingBottom = UDim.new(0, 2),
            Parent = watermark
        })

        local outline = utility.create("Frame", {
            Size = UDim2.new(1, 2, 1, 4),
            BorderColor3 = Color3.fromRGB(45, 45, 45),
            Position = UDim2.new(0, -1, 0, -1),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
            Parent = watermark
        })

        utility.create("Frame", {
            ZIndex = 0,
            Size = UDim2.new(1, 2, 1, 2),
            Position = UDim2.new(0, -1, 0, -1),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(45, 45, 45),
            Parent = outline
        })

        local outlinegradient = utility.create("UIGradient", {
            Rotation = 45,
            Color = utility.gradient(library.outline),
            Parent = outline
        })

        table.insert(outlineobjs, outlinegradient)

        local watermarktypes = utility.table()

        function watermarktypes:Update(options)
            local text = table.concat(options, " <font color=\"#D2D2D2\">|</font> ")
            local watermarksize = services.TextService:GetTextSize(text:gsub("<font color=\"#D2D2D2\">", ""):gsub("</font>", ""), 14, Enum.Font.Code, V2new(1000, 1000)).X + 16

            watermark.Size = UDim2.new(0, watermarksize, 0, 20)
            watermark.Text = text
        end

        local toggling = false
        local toggled = true

        function watermarktypes:Toggle()
            if not toggling then
                toggling = true

                toggled = not toggled
                local tween = utility.makevisible(watermark, toggled)
                tween.Completed:Wait()

                toggling = false
            end
        end

        return watermarktypes
    end

    function library:New(options)
        options = utility.table(options)
        local name = options.name
        local accent = options.accent or library.accent
        local outlinecolor = options.outline or { accent, utility.changecolor(accent, -100) }
        local sizeX = options.sizeX or 550
        local sizeY = options.sizeY or 350

        library.accent = accent
        library.outline = outlinecolor

        local holder = utility.create("Frame", {
            Size = UDim2.new(0, sizeX, 0, 24),
            BackgroundTransparency = 1,
            Position = utility.getcenter(sizeX, sizeY),
            Parent = gui
        })

        local toggling = false

        function library:Toggle()
            if not toggling then
                toggling = true

                library.toggled = not library.toggled
                local tween = utility.makevisible(holder, library.toggled)
                tween.Completed:Wait()

                toggling = false
            end
        end

        utility.dragify(holder)

        local title = utility.create("TextLabel", {
            ZIndex = 5,
            Size = UDim2.new(0, 0, 1, -2),
            BorderColor3 = Color3.fromRGB(50, 50, 50),
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 12, 0, 0),
            BackgroundColor3 = Color3.fromRGB(30, 30, 30),
            FontSize = Enum.FontSize.Size14,
            TextStrokeTransparency = 0,
            TextSize = 14,
            TextColor3 = library.accent,
            Text = name,
            Font = Enum.Font.Code,
            TextXAlignment = Enum.TextXAlignment.Left,
            Parent = holder
        })

        table.insert(accentobjects.text, title)

        local main = utility.create("Frame", {
            ZIndex = 2,
            Size = UDim2.new(1, 0, 0, sizeY),
            BorderColor3 = Color3.fromRGB(27, 42, 53),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(30, 30, 30),
            Parent = holder
        })

        local outline = utility.create("Frame", {
            Size = UDim2.new(1, 2, 1, 2),
            BorderColor3 = Color3.fromRGB(45, 45, 45),
            Position = UDim2.new(0, -1, 0, -1),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
            Parent = main
        })

        local outlinegradient = utility.create("UIGradient", {
            Rotation = 45,
            Color = utility.gradient(library.outline),
            Parent = outline
        })

        table.insert(outlineobjs, outlinegradient)

        local border = utility.create("Frame", {
            ZIndex = 0,
            Size = UDim2.new(1, 2, 1, 2),
            Position = UDim2.new(0, -1, 0, -1),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(45, 45, 45),
            Parent = outline
        })

        local tabs = utility.create("Frame", {
            ZIndex = 4,
            Size = UDim2.new(1, -16, 1, -30),
            BorderColor3 = Color3.fromRGB(50, 50, 50),
            Position = UDim2.new(0, 8, 0, 22),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
            Parent = main
        })

        utility.create("UIGradient", {
            Rotation = 90,
            Color = ColorSequence.new(Color3.fromRGB(25, 25, 25), Color3.fromRGB(20, 20, 20)),
            Parent = tabs
        })

        utility.create("Frame", {
            ZIndex = 3,
            Size = UDim2.new(1, 2, 1, 2),
            Position = UDim2.new(0, -1, 0, -1),
            BorderSizePixel = 0,
            BackgroundColor3 = Color3.fromRGB(20, 20, 20),
            Parent = tabs
        })

        local tabtoggles = utility.create("Frame", {
            Size = UDim2.new(0, 395, 0, 22),
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 6, 0, 6),
            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
            Parent = tabs
        })

        utility.create("UIListLayout", {
            FillDirection = Enum.FillDirection.Horizontal,
            SortOrder = Enum.SortOrder.LayoutOrder,
            Padding = UDim.new(0, 4),
            Parent = tabtoggles
        })

        local tabframes = utility.create("Frame", {
            ZIndex = 5,
            Size = UDim2.new(1, -12, 1, -35),
            BorderColor3 = Color3.fromRGB(50, 50, 50),
            Position = UDim2.new(0, 6, 0, 29),
            BackgroundColor3 = Color3.fromRGB(30, 30, 30),
            Parent = tabs
        })

        local tabholder = utility.create("Frame", {
            Size = UDim2.new(1, -16, 1, -16),
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 8, 0, 8),
            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
            Parent = tabframes
        })

        local windowtypes = utility.table()

        local pagetoggles = {}

        local switchingtabs = false

        local firsttab
        local currenttab

        function windowtypes:Page(options)
            options = utility.table(options)
            local name = options.name

            local first = #tabtoggles:GetChildren() == 1

            local togglesizeX = math.clamp(services.TextService:GetTextSize(name, 14, Enum.Font.Code, V2new(1000, 1000)).X, 25, math.huge)

            local tabtoggle = utility.create("TextButton", {
                Size = UDim2.new(0, togglesizeX + 18, 1, 0),
                BackgroundTransparency = 1,
                FontSize = Enum.FontSize.Size14,
                TextSize = 14,
                Parent = tabtoggles
            })

            local antiborder = utility.create("Frame", {
                ZIndex = 6,
                Visible = first,
                Size = UDim2.new(1, 0, 0, 1),
                Position = UDim2.new(0, 0, 1, 0),
                BorderSizePixel = 0,
                BackgroundColor3 = Color3.fromRGB(30, 30, 30),
                Parent = tabtoggle
            })

            local selectedglow = utility.create("Frame", {
                ZIndex = 6,
                Size = UDim2.new(1, 0, 0, 1),
                Visible = first,
                BorderColor3 = Color3.fromRGB(50, 50, 50),
                BorderSizePixel = 0,
                BackgroundColor3 = library.accent,
                Parent = tabtoggle
            })

            table.insert(accentobjects.bg, selectedglow)

            utility.create("Frame", {
                ZIndex = 7,
                Size = UDim2.new(1, 0, 0, 1),
                Position = UDim2.new(0, 0, 1, 0),
                BorderSizePixel = 0,
                BackgroundColor3 = Color3.fromRGB(30, 30, 30),
                Parent = selectedglow
            })

            local titleholder = utility.create("Frame", {
                ZIndex = 6,
                Size = UDim2.new(1, 0, 1, first and -1 or -4),
                BorderColor3 = Color3.fromRGB(50, 50, 50),
                Position = UDim2.new(0, 0, 0, first and 1 or 4),
                BorderSizePixel = 0,
                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                Parent = tabtoggle
            })

            local title = utility.create("TextLabel", {
                ZIndex = 7,
                Size = UDim2.new(1, 0, 1, 0),
                BackgroundTransparency = 1,
                FontSize = Enum.FontSize.Size14,
                TextStrokeTransparency = 0,
                TextSize = 14,
                TextColor3 = first and library.accent or Color3.fromRGB(110, 110, 110),
                Text = name,
                Font = Enum.Font.Code,
                Parent = titleholder
            })

            if first then
                table.insert(accentobjects.text, title)
            end

            local tabglowgradient = utility.create("UIGradient", {
                Rotation = 90,
                Color = first and utility.gradient { utility.changecolor(library.accent, -30), Color3.fromRGB(30, 30, 30) } or utility.gradient { Color3.fromRGB(22, 22, 22), Color3.fromRGB(22, 22, 22) },
                Offset = V2new(0, -0.55),
                Parent = titleholder
            })

            if first then
                accentobjects.gradient[tabglowgradient] = function(color)
                    return utility.gradient { utility.changecolor(color, -30), Color3.fromRGB(30, 30, 30) }
                end
            end

            local tabtoggleborder = utility.create("Frame", {
                ZIndex = 5,
                Size = UDim2.new(1, 2, 1, 2),
                Position = UDim2.new(0, -1, 0, -1),
                BorderSizePixel = 0,
                BackgroundColor3 = Color3.fromRGB(50, 50, 50),
                Parent = title
            })

            pagetoggles[tabtoggle] = {}
            pagetoggles[tabtoggle] = function()
                utility.tween(antiborder, { 0.2 }, { BackgroundTransparency = 1 }, function()
                    antiborder.Visible = false
                end)

                utility.tween(selectedglow, { 0.2 }, { BackgroundTransparency = 1 }, function()
                    selectedglow.Visible = false
                end)

                utility.tween(titleholder, { 0.2 }, { Size = UDim2.new(1, 0, 1, -4), Position = UDim2.new(0, 0, 0, 4) })

                utility.tween(title, { 0.2 }, { TextColor3 = Color3.fromRGB(110, 110, 110) })
                if table.find(accentobjects.text, title) then
                    table.remove(accentobjects.text, table.find(accentobjects.text, title))
                end

                tabglowgradient.Color = utility.gradient { Color3.fromRGB(22, 22, 22), Color3.fromRGB(22, 22, 22) }

                if accentobjects.gradient[tabglowgradient] then
                    accentobjects.gradient[tabglowgradient] = function() end
                end
            end

            local tab = utility.create("Frame", {
                Size = UDim2.new(1, 0, 1, 0),
                BackgroundTransparency = first and 1 or 2,
                Visible = first,
                Parent = tabholder
            })

            if first then
                currenttab = tab
                firsttab = tab
            end

            tab.DescendantAdded:Connect(function(descendant)
                if tab ~= currenttab then
                    task.wait()
                    if not descendant.ClassName:find("UI") then
                        if descendant.ClassName:find("Text") then
                            descendant.TextTransparency = descendant.TextTransparency + 1
                            descendant.TextStrokeTransparency = descendant.TextStrokeTransparency + 1
                        end

                        if descendant.ClassName:find("Scrolling") then
                            descendant.ScrollBarImageTransparency = descendant.ScrollBarImageTransparency + 1
                        end

                        descendant.BackgroundTransparency = descendant.BackgroundTransparency + 1
                    end
                end
            end)

            local column1 = utility.create("ScrollingFrame", {
                Size = UDim2.new(0.5, -5, 1, 0),
                BackgroundTransparency = 1,
                Active = true,
                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                AutomaticCanvasSize = Enum.AutomaticSize.Y,
                CanvasSize = UDim2.new(0, 0, 0, 123),
                ScrollBarImageColor3 = Color3.fromRGB(0, 0, 0),
                ScrollBarThickness = 0,
                Parent = tab
            })

            local column1list = utility.create("UIListLayout", {
                SortOrder = Enum.SortOrder.LayoutOrder,
                Padding = UDim.new(0, 10),
                Parent = column1
            })

            utility.updatescrolling(column1, column1list)

            local column2 = utility.create("ScrollingFrame", {
                Size = UDim2.new(0.5, -5, 1, 0),
                BackgroundTransparency = 1,
                Position = UDim2.new(0.5, 7, 0, 0),
                Active = true,
                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                AutomaticCanvasSize = Enum.AutomaticSize.Y,
                CanvasSize = UDim2.new(0, 0, 0, 0),
                ScrollBarImageColor3 = Color3.fromRGB(0, 0, 0),
                ScrollBarThickness = 0,
                Parent = tab
            })

            local column2list = utility.create("UIListLayout", {
                SortOrder = Enum.SortOrder.LayoutOrder,
                Padding = UDim.new(0, 10),
                Parent = column2
            })

            utility.updatescrolling(column2, column2list)

            local function opentab()
                if not switchingtabs then
                    switchingtabs = true

                    currenttab = tab

                    for toggle, close in next, pagetoggles do
                        if toggle ~= tabtoggle then
                            close()
                        end
                    end

                    for _, obj in next, tabholder:GetChildren() do
                        if obj ~= tab and obj.BackgroundTransparency <= 1 then
                            utility.makevisible(obj, false)
                        end
                    end

                    antiborder.Visible = true
                    utility.tween(antiborder, { 0.2 }, { BackgroundTransparency = 0 })

                    selectedglow.Visible = true
                    utility.tween(selectedglow, { 0.2 }, { BackgroundTransparency = 0 })

                    utility.tween(titleholder, { 0.2 }, { Size = UDim2.new(1, 0, 1, -1), Position = UDim2.new(0, 0, 0, 1) })

                    utility.tween(title, { 0.2 }, { TextColor3 = library.accent })

                    table.insert(accentobjects.text, title)

                    tabglowgradient.Color = utility.gradient { utility.changecolor(library.accent, -30), Color3.fromRGB(30, 30, 30) }

                    accentobjects.gradient[tabglowgradient] = function(color)
                        return utility.gradient { utility.changecolor(color, -30), Color3.fromRGB(30, 30, 30) }
                    end

                    tab.Visible = true
                    if tab.BackgroundTransparency > 1 then
                        task.wait(0.2)

                        local tween = utility.makevisible(tab, true)
                        tween.Completed:Wait()
                    end

                    switchingtabs = false
                end
            end

            tabtoggle.MouseButton1Click:Connect(opentab)

            local pagetypes = utility.table()

            function pagetypes:Section(options)
                options = utility.table(options)
                local name = options.name
                local side = options.side or "left"
                local max = options.max or math.huge
                local column = (side:lower() == "left" and column1) or (side:lower() == "right" and column2)

                local sectionholder = utility.create("Frame", {
                    Size = UDim2.new(1, -1, 0, 28),
                    BackgroundTransparency = 1,
                    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                    Parent = column
                })

                local section = utility.create("Frame", {
                    ZIndex = 6,
                    Size = UDim2.new(1, -2, 1, -2),
                    BorderColor3 = Color3.fromRGB(50, 50, 50),
                    Position = UDim2.new(0, 1, 0, 1),
                    BackgroundColor3 = Color3.fromRGB(22, 22, 22),
                    Parent = sectionholder
                })

                local title = utility.create("TextLabel", {
                    ZIndex = 8,
                    Size = UDim2.new(0, 0, 0, 14),
                    BorderColor3 = Color3.fromRGB(50, 50, 50),
                    BackgroundTransparency = 1,
                    Position = UDim2.new(0, 6, 0, 3),
                    BackgroundColor3 = Color3.fromRGB(30, 30, 30),
                    FontSize = Enum.FontSize.Size14,
                    TextStrokeTransparency = 0,
                    TextSize = 14,
                    TextColor3 = library.accent,
                    Text = name,
                    Font = Enum.Font.Code,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    Parent = section
                })

                table.insert(accentobjects.text, title)

                local glow = utility.create("Frame", {
                    ZIndex = 8,
                    Size = UDim2.new(1, 0, 0, 1),
                    BorderColor3 = Color3.fromRGB(50, 50, 50),
                    BorderSizePixel = 0,
                    BackgroundColor3 = library.accent,
                    Parent = section
                })

                table.insert(accentobjects.bg, glow)

                utility.create("Frame", {
                    ZIndex = 9,
                    Size = UDim2.new(1, 0, 0, 1),
                    Position = UDim2.new(0, 0, 1, 0),
                    BorderSizePixel = 0,
                    BackgroundColor3 = Color3.fromRGB(30, 30, 30),
                    Parent = glow
                })

                local fade = utility.create("Frame", {
                    ZIndex = 7,
                    Size = UDim2.new(1, 0, 0, 20),
                    BorderSizePixel = 0,
                    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                    Parent = glow
                })

                local fadegradient = utility.create("UIGradient", {
                    Rotation = 90,
                    Color = utility.gradient { utility.changecolor(library.accent, -30), Color3.fromRGB(22, 22, 22) },
                    Offset = V2new(0, -0.55),
                    Parent = fade
                })

                accentobjects.gradient[fadegradient] = function(color)
                    return utility.gradient { utility.changecolor(color, -30), Color3.fromRGB(22, 22, 22) }
                end

                local sectioncontent = utility.create("ScrollingFrame", {
                    ZIndex = 7,
                    Size = UDim2.new(1, -7, 1, -26),
                    BorderColor3 = Color3.fromRGB(27, 42, 53),
                    BackgroundTransparency = 1,
                    Position = UDim2.new(0, 6, 0, 20),
                    Active = true,
                    BorderSizePixel = 0,
                    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                    CanvasSize = UDim2.new(0, 0, 0, 1),
                    ScrollBarThickness = 2,
                    Parent = section
                })

                local sectionlist = utility.create("UIListLayout", {
                    SortOrder = Enum.SortOrder.LayoutOrder,
                    Padding = UDim.new(0, 2),
                    Parent = sectioncontent
                })

                utility.updatescrolling(sectioncontent, sectionlist)

                local sectiontypes = utility.table()

                function sectiontypes:Label(options)
                    options = utility.table(options)
                    local name = options.name

                    utility.create("TextLabel", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 0, 0, 13),
                        BorderColor3 = Color3.fromRGB(50, 50, 50),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 6, 0, 3),
                        BackgroundColor3 = Color3.fromRGB(30, 30, 30),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = sectioncontent
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end
                end

                function sectiontypes:Button(options)
                    options = utility.table(options)
                    local name = options.name
                    local callback = options.callback

                    local buttonholder = utility.create("Frame", {
                        Size = UDim2.new(1, -5, 0, 17),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = sectioncontent
                    })

                    local button = utility.create("TextButton", {
                        ZIndex = 10,
                        Size = UDim2.new(1, -4, 1, -4),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 0, 2),
                        BackgroundColor3 = Color3.fromRGB(25, 25, 25),
                        AutoButtonColor = false,
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        Parent = buttonholder
                    })

                    local bg = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, 0, 1, 0),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = button
                    })

                    local bggradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                        Parent = bg
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = button
                    })

                    local blackborder = utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    button.MouseButton1Click:Connect(callback)

                    button.InputBegan:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            bggradient.Color = utility.gradient { Color3.fromRGB(45, 45, 45), Color3.fromRGB(35, 35, 35) }
                        end
                    end)

                    button.InputEnded:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            bggradient.Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) }
                        end
                    end)

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end
                end

                function sectiontypes:Toggle(options)
                    options = utility.table(options)
                    local name = options.name
                    local default = options.default
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    local toggleholder = utility.create("TextButton", {
                        Size = UDim2.new(1, -5, 0, 14),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextSize = 14,
                        TextColor3 = Color3.fromRGB(0, 0, 0),
                        Font = Enum.Font.SourceSans,
                        Parent = sectioncontent
                    })

                    local togglething = utility.create("TextButton", {
                        ZIndex = 9,
                        Size = UDim2.new(1, 0, 0, 14),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        BackgroundTransparency = 1,
                        TextTransparency = 1,
                        Parent = toggleholder
                    })

                    local icon = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(0, 10, 0, 10),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        Position = UDim2.new(0, 2, 0, 2),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = toggleholder
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = icon
                    })

                    utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local icongradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                        Parent = icon
                    })

                    local enablediconholder = utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, 0, 1, 0),
                        BackgroundTransparency = 1,
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = icon
                    })

                    local enabledicongradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                        Parent = enablediconholder
                    })

                    accentobjects.gradient[enabledicongradient] = function(color)
                        return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                    end

                    local title = utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 0, 14),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 20, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(180, 180, 180),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = toggleholder
                    })

                    local toggled = false

                    if flag then
                        library.flags[flag] = toggled
                    end

                    local function toggle()
                        if not switchingtabs then
                            toggled = not toggled

                            if flag then
                                library.flags[flag] = toggled
                            end

                            callback(toggled)

                            local enabledtransparency = toggled and 0 or 1
                            utility.tween(enablediconholder, { 0.2 }, { Transparency = enabledtransparency })

                            local textcolor = toggled and library.accent or Color3.fromRGB(180, 180, 180)
                            utility.tween(title, { 0.2 }, { TextColor3 = textcolor })

                            if toggled then
                                table.insert(accentobjects.text, title)
                            elseif table.find(accentobjects.text, title) then
                                table.remove(accentobjects.text, table.find(accentobjects.text, title))
                            end
                        end
                    end

                    togglething.MouseButton1Click:Connect(toggle)

                    local function set(bool)
                        if type(bool) == "boolean" and toggled ~= bool then
                            toggle()
                        end
                    end

                    if default then
                        set(default)
                    end

                    if flag then
                        flags[flag] = set
                    end

                    local toggletypes = utility.table()

                    function toggletypes:Toggle(bool)
                        set(bool)
                    end

                    function toggletypes:Colorpicker(newoptions)
                        newoptions = utility.table(newoptions)
                        local name = newoptions.name
                        local default = newoptions.default or Color3.fromRGB(255, 255, 255)
                        local colorpickertype = newoptions.mode
                        local toggleflag = colorpickertype and colorpickertype:lower() == "toggle" and newoptions.togglepointer
                        local togglecallback = colorpickertype and colorpickertype:lower() == "toggle" and newoptions.togglecallback or function() end
                        local flag = newoptions.pointer
                        local callback = newoptions.callback or function() end

                        local colorpickerframe = utility.create("Frame", {
                            ZIndex = 9,
                            Size = UDim2.new(1, -70, 0, 148),
                            Position = UDim2.new(1, -168, 0, 18),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Visible = false,
                            Parent = toggleholder
                        })

                        colorpickerframe.DescendantAdded:Connect(function(descendant)
                            if not opened then
                                task.wait()
                                if not descendant.ClassName:find("UI") then
                                    if descendant.ClassName:find("Text") then
                                        descendant.TextTransparency = descendant.TextTransparency + 1
                                        descendant.TextStrokeTransparency = descendant.TextStrokeTransparency + 1
                                    end

                                    if descendant.ClassName:find("Image") then
                                        descendant.ImageTransparency = descendant.ImageTransparency + 1
                                    end

                                    descendant.BackgroundTransparency = descendant.BackgroundTransparency + 1
                                end
                            end
                        end)

                        local bggradient = utility.create("UIGradient", {
                            Rotation = 90,
                            Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                            Parent = colorpickerframe
                        })

                        local grayborder = utility.create("Frame", {
                            ZIndex = 8,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = colorpickerframe
                        })

                        local blackborder = utility.create("Frame", {
                            ZIndex = 7,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        local saturationframe = utility.create("ImageLabel", {
                            ZIndex = 12,
                            Size = UDim2.new(0, 128, 0, 100),
                            BorderColor3 = Color3.fromRGB(50, 50, 50),
                            Position = UDim2.new(0, 6, 0, 6),
                            BorderSizePixel = 0,
                            BackgroundColor3 = default,
                            Image = "http://www.roblox.com/asset/?id=8630797271",
                            Parent = colorpickerframe
                        })

                        local grayborder = utility.create("Frame", {
                            ZIndex = 11,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = saturationframe
                        })

                        utility.create("Frame", {
                            ZIndex = 10,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        local saturationpicker = utility.create("Frame", {
                            ZIndex = 13,
                            Size = UDim2.new(0, 2, 0, 2),
                            BorderColor3 = Color3.fromRGB(10, 10, 10),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = saturationframe
                        })

                        local hueframe = utility.create("ImageLabel", {
                            ZIndex = 12,
                            Size = UDim2.new(0, 14, 0, 100),
                            Position = UDim2.new(1, -20, 0, 6),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 193, 49),
                            ScaleType = Enum.ScaleType.Crop,
                            Image = "http://www.roblox.com/asset/?id=8630799159",
                            Parent = colorpickerframe
                        })

                        local grayborder = utility.create("Frame", {
                            ZIndex = 11,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = hueframe
                        })

                        utility.create("Frame", {
                            ZIndex = 10,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        local huepicker = utility.create("Frame", {
                            ZIndex = 13,
                            Size = UDim2.new(1, 0, 0, 1),
                            BorderColor3 = Color3.fromRGB(10, 10, 10),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = hueframe
                        })

                        local boxholder = utility.create("Frame", {
                            Size = UDim2.new(1, -8, 0, 17),
                            ClipsDescendants = true,
                            Position = UDim2.new(0, 4, 0, 110),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = colorpickerframe
                        })

                        local box = utility.create("TextBox", {
                            ZIndex = 13,
                            Size = UDim2.new(1, -4, 1, -4),
                            BackgroundTransparency = 1,
                            Position = UDim2.new(0, 2, 0, 2),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            FontSize = Enum.FontSize.Size14,
                            TextStrokeTransparency = 0,
                            TextSize = 13,
                            TextColor3 = Color3.fromRGB(210, 210, 210),
                            Text = table.concat({ utility.getrgb(default) }, ", "),
                            PlaceholderText = "R, G, B",
                            Font = Enum.Font.Code,
                            Parent = boxholder
                        })

                        local grayborder = utility.create("Frame", {
                            ZIndex = 11,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = box
                        })

                        utility.create("Frame", {
                            ZIndex = 10,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        local bg = utility.create("Frame", {
                            ZIndex = 12,
                            Size = UDim2.new(1, 0, 1, 0),
                            BorderColor3 = Color3.fromRGB(40, 40, 40),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = box
                        })

                        utility.create("UIGradient", {
                            Rotation = 90,
                            Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                            Parent = bg
                        })

                        local rainbowtoggleholder = utility.create("TextButton", {
                            Size = UDim2.new(1, -8, 0, 14),
                            BackgroundTransparency = 1,
                            Position = UDim2.new(0, 4, 0, 130),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            FontSize = Enum.FontSize.Size14,
                            TextSize = 14,
                            TextColor3 = Color3.fromRGB(0, 0, 0),
                            Font = Enum.Font.SourceSans,
                            Parent = colorpickerframe
                        })

                        local toggleicon = utility.create("Frame", {
                            ZIndex = 12,
                            Size = UDim2.new(0, 10, 0, 10),
                            BorderColor3 = Color3.fromRGB(40, 40, 40),
                            Position = UDim2.new(0, 2, 0, 2),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = rainbowtoggleholder
                        })

                        local enablediconholder = utility.create("Frame", {
                            ZIndex = 13,
                            Size = UDim2.new(1, 0, 1, 0),
                            BackgroundTransparency = 1,
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = toggleicon
                        })

                        local enabledicongradient = utility.create("UIGradient", {
                            Rotation = 90,
                            Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                            Parent = enablediconholder
                        })

                        accentobjects.gradient[enabledicongradient] = function(color)
                            return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                        end

                        local grayborder = utility.create("Frame", {
                            ZIndex = 11,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = toggleicon
                        })

                        utility.create("Frame", {
                            ZIndex = 10,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        utility.create("UIGradient", {
                            Rotation = 90,
                            Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                            Parent = toggleicon
                        })

                        local rainbowtxt = utility.create("TextLabel", {
                            ZIndex = 10,
                            Size = UDim2.new(0, 0, 1, 0),
                            BackgroundTransparency = 1,
                            Position = UDim2.new(0, 20, 0, 0),
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            FontSize = Enum.FontSize.Size14,
                            TextStrokeTransparency = 0,
                            TextSize = 13,
                            TextColor3 = Color3.fromRGB(180, 180, 180),
                            Text = "Rainbow",
                            Font = Enum.Font.Code,
                            TextXAlignment = Enum.TextXAlignment.Left,
                            Parent = rainbowtoggleholder
                        })

                        local colorpicker = utility.create("TextButton", {
                            Size = UDim2.new(1, 0, 0, 14),
                            BackgroundTransparency = 1,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            FontSize = Enum.FontSize.Size14,
                            TextSize = 14,
                            TextColor3 = Color3.fromRGB(0, 0, 0),
                            Font = Enum.Font.SourceSans,
                            Parent = toggleholder
                        })

                        local icon = utility.create("TextButton", {
                            ZIndex = 9,
                            Size = UDim2.new(0, 18, 0, 10),
                            BorderColor3 = Color3.fromRGB(40, 40, 40),
                            Position = UDim2.new(1, -20, 0, 2),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                            Parent = colorpicker,
                            Text = ""
                        })

                        local grayborder = utility.create("Frame", {
                            ZIndex = 8,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                            Parent = icon
                        })

                        utility.create("Frame", {
                            ZIndex = 7,
                            Size = UDim2.new(1, 2, 1, 2),
                            Position = UDim2.new(0, -1, 0, -1),
                            BorderSizePixel = 0,
                            BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                            Parent = grayborder
                        })

                        local icongradient = utility.create("UIGradient", {
                            Rotation = 90,
                            Color = utility.gradient { default, utility.changecolor(default, -200) },
                            Parent = icon
                        })

                        if #sectioncontent:GetChildren() - 1 <= max then
                            sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                        end

                        local colorpickertypes = utility.table()

                        local opened = false
                        local opening = false

                        local function opencolorpicker()
                            if not opening then
                                opening = true

                                opened = not opened

                                if opened then
                                    utility.tween(toggleholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, 168) })
                                end

                                local tween = utility.makevisible(colorpickerframe, opened)

                                tween.Completed:Wait()

                                if not opened then
                                    local tween = utility.tween(toggleholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, 16) })
                                    tween.Completed:Wait()
                                end

                                opening = false
                            end
                        end

                        icon.MouseButton1Click:Connect(opencolorpicker)

                        local hue, sat, val = default:ToHSV()

                        local slidinghue = false
                        local slidingsaturation = false

                        local hsv = Color3.fromHSV(hue, sat, val)

                        if flag then
                            library.flags[flag] = default
                        end

                        local function updatehue(input)
                            local sizeY = 1 - math.clamp((input.Position.Y - hueframe.AbsolutePosition.Y) / hueframe.AbsoluteSize.Y, 0, 1)
                            local posY = math.clamp(((input.Position.Y - hueframe.AbsolutePosition.Y) / hueframe.AbsoluteSize.Y) * hueframe.AbsoluteSize.Y, 0, hueframe.AbsoluteSize.Y - 2)
                            huepicker.Position = UDim2.new(0, 0, 0, posY)

                            hue = sizeY
                            hsv = Color3.fromHSV(sizeY, sat, val)

                            box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                            saturationframe.BackgroundColor3 = hsv
                            icon.BackgroundColor3 = hsv

                            if flag then
                                library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                            end

                            callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                        end

                        hueframe.InputBegan:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                                slidinghue = true
                                updatehue(input)
                            end
                        end)

                        hueframe.InputEnded:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                                slidinghue = false
                            end
                        end)

                        services.InputService.InputChanged:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseMovement then
                                if slidinghue then
                                    updatehue(input)
                                end
                            end
                        end)

                        local function updatesatval(input)
                            local sizeX = math.clamp((input.Position.X - saturationframe.AbsolutePosition.X) / saturationframe.AbsoluteSize.X, 0, 1)
                            local sizeY = 1 - math.clamp((input.Position.Y - saturationframe.AbsolutePosition.Y) / saturationframe.AbsoluteSize.Y, 0, 1)
                            local posY = math.clamp(((input.Position.Y - saturationframe.AbsolutePosition.Y) / saturationframe.AbsoluteSize.Y) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4)
                            local posX = math.clamp(((input.Position.X - saturationframe.AbsolutePosition.X) / saturationframe.AbsoluteSize.X) * saturationframe.AbsoluteSize.X, 0, saturationframe.AbsoluteSize.X - 4)

                            saturationpicker.Position = UDim2.new(0, posX, 0, posY)

                            sat = sizeX
                            val = sizeY
                            hsv = Color3.fromHSV(hue, sizeX, sizeY)

                            box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                            saturationframe.BackgroundColor3 = hsv
                            icon.BackgroundColor3 = hsv

                            if flag then
                                library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                            end

                            callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                        end

                        saturationframe.InputBegan:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                                slidingsaturation = true
                                updatesatval(input)
                            end
                        end)

                        saturationframe.InputEnded:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                                slidingsaturation = false
                            end
                        end)

                        services.InputService.InputChanged:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.MouseMovement then
                                if slidingsaturation then
                                    updatesatval(input)
                                end
                            end
                        end)

                        local function set(color)
                            if type(color) == "table" then
                                color = Color3.fromRGB(unpack(color))
                            end

                            hue, sat, val = color:ToHSV()
                            hsv = Color3.fromHSV(hue, sat, val)

                            saturationframe.BackgroundColor3 = hsv
                            icon.BackgroundColor3 = hsv
                            saturationpicker.Position = UDim2.new(0, (math.clamp(sat * saturationframe.AbsoluteSize.X, 0, saturationframe.AbsoluteSize.X - 4)), 0, (math.clamp((1 - val) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4)))
                            huepicker.Position = UDim2.new(0, 0, 0, math.clamp((1 - hue) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4))

                            box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                            if flag then
                                library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                            end

                            callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                        end

                        local toggled = false

                        local function toggle()
                            if not switchingtabs then
                                toggled = not toggled

                                if toggled then
                                    task.spawn(function()
                                        while toggled do
                                            for i = 0, 1, 0.0015 do
                                                if not toggled then
                                                    return
                                                end

                                                local color = Color3.fromHSV(i, 1, 1)
                                                set(color)

                                                task.wait()
                                            end
                                        end
                                    end)
                                end

                                local enabledtransparency = toggled and 0 or 1
                                utility.tween(enablediconholder, { 0.2 }, { BackgroundTransparency = enabledtransparency })

                                local textcolor = toggled and library.accent or Color3.fromRGB(180, 180, 180)
                                utility.tween(rainbowtxt, { 0.2 }, { TextColor3 = textcolor })

                                if toggled then
                                    table.insert(accentobjects.text, title)
                                elseif table.find(accentobjects.text, title) then
                                    table.remove(accentobjects.text, table.find(accentobjects.text, title))
                                end
                            end
                        end

                        rainbowtoggleholder.MouseButton1Click:Connect(toggle)

                        box.FocusLost:Connect(function()
                            local _, amount = box.Text:gsub(", ", "")

                            if amount == 2 then
                                local values = box.Text:split(", ")
                                local r, g, b = math.clamp(values[1], 0, 255), math.clamp(values[2], 0, 255), math.clamp(values[3], 0, 255)
                                set(Color3.fromRGB(r, g, b))
                            else
                                rgb.Text = math.floor((hsv.r * 255) + 0.5) .. ", " .. math.floor((hsv.g * 255) + 0.5) .. ", " .. math.floor((hsv.b * 255) + 0.5)
                            end
                        end)

                        if default then
                            set(default)
                        end

                        if flag then
                            flags[flag] = set
                        end

                        local colorpickertypes = utility.table()

                        function colorpickertypes:Set(color)
                            set(color)
                        end
                    end

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    return toggletypes
                end

                function sectiontypes:Box(options)
                    options = utility.table(options)
                    local name = options.name
                    local placeholder = options.placeholder or ""
                    local default = options.default
                    local boxtype = options.type or "string"
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    local boxholder = utility.create("Frame", {
                        Size = UDim2.new(1, -5, 0, 32),
                        ClipsDescendants = true,
                        BorderColor3 = Color3.fromRGB(27, 42, 53),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = sectioncontent
                    })

                    utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 0, 13),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 1, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = boxholder
                    })

                    local box = utility.create("TextBox", {
                        ZIndex = 10,
                        Size = UDim2.new(1, -4, 0, 13),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 0, 17),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        PlaceholderColor3 = Color3.fromRGB(120, 120, 120),
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = "",
                        PlaceholderText = placeholder,
                        Font = Enum.Font.Code,
                        Parent = boxholder
                    })

                    local bg = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, 0, 1, 0),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = box
                    })

                    local bggradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                        Parent = bg
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = box
                    })

                    local blackborder = utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    if flag then
                        library.flags[flag] = default or ""
                    end

                    local function set(str)
                        if boxtype:lower() == "number" then
                            str = str:gsub("%D+", "")
                        end

                        box.Text = str

                        if flag then
                            library.flags[flag] = str
                        end

                        callback(str)
                    end

                    if default then
                        set(default)
                    end

                    if boxtype:lower() == "number" then
                        box:GetPropertyChangedSignal("Text"):Connect(function()
                            box.Text = box.Text:gsub("%D+", "")
                        end)
                    end

                    box.FocusLost:Connect(function()
                        set(box.Text)
                    end)

                    if flag then
                        flags[flag] = set
                    end

                    local boxtypes = utility.table()

                    function boxtypes:Set(str)
                        set(str)
                    end

                    return boxtypes
                end

                function sectiontypes:Slider(options)
                    options = utility.table(options)
                    local name = options.name
                    local min = options.minimum or 0
                    local slidermax = options.maximum or 100
                    local valuetext = options.value or "[value]/" .. slidermax
                    local increment = options.decimals or 0.5
                    local default = options.default and math.clamp(options.default, min, slidermax) or min
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    local sliderholder = utility.create("Frame", {
                        Size = UDim2.new(1, -5, 0, 28),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = sectioncontent
                    })

                    local slider = utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, -4, 0, 9),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 1, -11),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = sliderholder
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = slider
                    })

                    utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local bg = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, 0, 1, 0),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = slider
                    })

                    utility.create("UIGradient", {
                        Rotation = 90,
                        Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                        Parent = bg
                    })

                    local fill = utility.create("Frame", {
                        ZIndex = 11,
                        Size = UDim2.new((default - min) / (slidermax - min), 0, 1, 0),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = slider
                    })

                    local fillgradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                        Parent = fill
                    })

                    accentobjects.gradient[fillgradient] = function(color)
                        return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                    end

                    local valuelabel = utility.create("TextLabel", {
                        ZIndex = 12,
                        Size = UDim2.new(1, 0, 1, 0),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = valuetext:gsub("%[value%]", tostring(default)),
                        Font = Enum.Font.Code,
                        Parent = slider
                    })

                    utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 0, 13),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 1, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = sliderholder
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    local sliding = false

                    local function slide(input)
                        local sizeX = math.clamp((input.Position.X - slider.AbsolutePosition.X) / slider.AbsoluteSize.X, 0, 1)
                        local newincrement = (slidermax / ((slidermax - min) / (increment * 4)))
                        local newsizeX = math.floor(sizeX * (((slidermax - min) / newincrement) * 4)) / (((slidermax - min) / newincrement) * 4)

                        utility.tween(fill, { newincrement / 80 }, { Size = UDim2.new(newsizeX, 0, 1, 0) })

                        local value = math.floor((newsizeX * (slidermax - min) + min) * 20) / 20
                        valuelabel.Text = valuetext:gsub("%[value%]", tostring(value))

                        if flag then
                            library.flags[flag] = value
                        end

                        callback(value)
                    end

                    slider.InputBegan:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            sliding = true
                            slide(input)
                        end
                    end)

                    slider.InputEnded:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            sliding = false
                        end
                    end)

                    services.InputService.InputChanged:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseMovement then
                            if sliding then
                                slide(input)
                            end
                        end
                    end)

                    local function set(value)
                        value = math.clamp(value, min, slidermax)

                        local sizeX = ((value - min)) / (slidermax - min)
                        local newincrement = (slidermax / ((slidermax - min) / (increment * 4)))

                        local newsizeX = math.floor(sizeX * (((slidermax - min) / newincrement) * 4)) / (((slidermax - min) / newincrement) * 4)
                        value = math.floor((newsizeX * (slidermax - min) + min) * 20) / 20

                        fill.Size = UDim2.new(newsizeX, 0, 1, 0)
                        valuelabel.Text = valuetext:gsub("%[value%]", tostring(value))

                        if flag then
                            library.flags[flag] = value
                        end

                        callback(value)
                    end

                    if default then
                        set(default)
                    end

                    if flag then
                        flags[flag] = set
                    end

                    local slidertypes = utility.table()

                    function slidertypes:Set(value)
                        set(value)
                    end

                    return slidertypes
                end

                function sectiontypes:Dropdown(options)
                    options = utility.table(options)
                    local name = options.name
                    local content = options["options"]
                    local maxoptions = options.maximum and (options.maximum > 1 and options.maximum)
                    local default = options.default or maxoptions and {}
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    if maxoptions then
                        for i, def in next, default do
                            if not table.find(content, def) then
                                table.remove(default, i)
                            end
                        end
                    else
                        if not table.find(content, default) then
                            default = nil
                        end
                    end

                    local defaulttext = default and ((type(default) == "table" and table.concat(default, ", ")) or default)

                    local opened = false

                    local dropdownholder = utility.create("Frame", {
                        Size = UDim2.new(1, -5, 0, 32),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 0, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = sectioncontent
                    })

                    local dropdown = utility.create("TextButton", {
                        ZIndex = 10,
                        Size = UDim2.new(1, -4, 0, 13),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 0, 17),
                        BackgroundColor3 = Color3.fromRGB(25, 25, 25),
                        AutoButtonColor = false,
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = default and (defaulttext ~= "" and Color3.fromRGB(210, 210, 210) or Color3.fromRGB(120, 120, 120)) or Color3.fromRGB(120, 120, 120),
                        Text = default and (defaulttext ~= "" and defaulttext or "NONE") or "NONE",
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = dropdownholder
                    })

                    local bg = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, 6, 1, 0),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        Position = UDim2.new(0, -6, 0, 0),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = dropdown
                    })

                    local bggradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                        Parent = bg
                    })

                    local textpadding = utility.create("UIPadding", {
                        PaddingLeft = UDim.new(0, 6),
                        Parent = dropdown
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 8, 1, 2),
                        Position = UDim2.new(0, -7, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = dropdown
                    })

                    utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local icon = utility.create("TextLabel", {
                        ZIndex = 11,
                        Size = UDim2.new(0, 13, 1, 0),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(1, -13, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size12,
                        TextStrokeTransparency = 0,
                        TextSize = 12,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = "+",
                        Font = Enum.Font.Gotham,
                        Parent = dropdown
                    })

                    utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 0, 13),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 1, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = dropdownholder
                    })

                    local contentframe = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, -4, 1, -38),
                        Position = UDim2.new(0, 2, 0, 36),
                        BorderSizePixel = 0,
                        Visible = false,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = dropdownholder
                    })

                    local opened = false
                    contentframe.DescendantAdded:Connect(function(descendant)
                        if not opened then
                            task.wait()
                            if not descendant.ClassName:find("UI") then
                                if descendant.ClassName:find("Text") then
                                    descendant.TextTransparency = descendant.TextTransparency + 1
                                    descendant.TextStrokeTransparency = descendant.TextStrokeTransparency + 1
                                end

                                descendant.BackgroundTransparency = descendant.BackgroundTransparency + 1
                            end
                        end
                    end)

                    local contentframegradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                        Parent = contentframe
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = contentframe
                    })

                    utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local dropdowncontent = utility.create("Frame", {
                        Size = UDim2.new(1, -2, 1, -2),
                        Position = UDim2.new(0, 1, 0, 1),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = contentframe
                    })

                    local dropdowncontentlist = utility.create("UIListLayout", {
                        SortOrder = Enum.SortOrder.LayoutOrder,
                        Padding = UDim.new(0, 2),
                        Parent = dropdowncontent
                    })

                    local option = utility.create("TextButton", {
                        ZIndex = 12,
                        Size = UDim2.new(1, 0, 0, 16),
                        BorderColor3 = Color3.fromRGB(50, 50, 50),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 0, 2),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(22, 22, 22),
                        AutoButtonColor = false,
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(150, 150, 150),
                        Text = "",
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left
                    })

                    utility.create("UIPadding", {
                        PaddingLeft = UDim.new(0, 10),
                        Parent = option
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    local opening = false

                    local function opendropdown()
                        if not opening then
                            opening = true

                            opened = not opened

                            utility.tween(icon, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                icon.Text = opened and "-" or "+"
                            end)

                            if opened then
                                utility.tween(dropdownholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, dropdowncontentlist.AbsoluteContentSize.Y + 40) })
                            end

                            local tween = utility.makevisible(contentframe, opened)

                            tween.Completed:Wait()

                            if not opened then
                                local tween = utility.tween(dropdownholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, 32) })
                                tween.Completed:Wait()
                            end

                            utility.tween(icon, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0 })

                            opening = false
                        end
                    end

                    dropdown.MouseButton1Click:Connect(opendropdown)

                    local chosen = maxoptions and {}
                    local choseninstances = {}
                    local optioninstances = {}

                    if flag then
                        library.flags[flag] = default
                    end

                    for _, opt in next, content do
                        if not maxoptions then
                            local optionbtn = option:Clone()
                            optionbtn.Parent = dropdowncontent
                            optionbtn.Text = opt

                            optioninstances[opt] = optionbtn

                            if default == opt then
                                chosen = opt
                                optionbtn.BackgroundTransparency = 0
                                optionbtn.TextColor3 = Color3.fromRGB(210, 210, 210)
                            end

                            optionbtn.MouseButton1Click:Connect(function()
                                if chosen ~= opt then
                                    for _, optbtn in next, dropdowncontent:GetChildren() do
                                        if optbtn ~= optionbtn and optbtn:IsA("TextButton") then
                                            utility.tween(optbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                        end
                                    end

                                    utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                    local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                        dropdown.Text = opt
                                    end)

                                    chosen = opt

                                    tween.Completed:Wait()

                                    utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                    if flag then
                                        library.flags[flag] = opt
                                    end

                                    callback(opt)
                                else
                                    utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })

                                    local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                        dropdown.Text = "NONE"
                                    end)

                                    tween.Completed:Wait()

                                    utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                    chosen = nil

                                    if flag then
                                        library.flags[flag] = nil
                                    end

                                    callback(nil)
                                end
                            end)
                        else
                            local optionbtn = option:Clone()
                            optionbtn.Parent = dropdowncontent
                            optionbtn.Text = opt

                            optioninstances[opt] = optionbtn

                            if table.find(default, opt) then
                                chosen = opt
                                optionbtn.BackgroundTransparency = 0
                                optionbtn.TextColor3 = Color3.fromRGB(210, 210, 210)
                            end

                            optionbtn.MouseButton1Click:Connect(function()
                                if not table.find(chosen, opt) then
                                    if #chosen >= maxoptions then
                                        table.remove(chosen, 1)
                                        utility.tween(choseninstances[1], { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                        table.remove(choseninstances, 1)
                                    end

                                    utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                    table.insert(chosen, opt)
                                    table.insert(choseninstances, optionbtn)

                                    local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                        dropdown.Text = table.concat(chosen, ", ")
                                    end)

                                    tween.Completed:Wait()

                                    utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                    if flag then
                                        library.flags[flag] = chosen
                                    end

                                    callback(chosen)
                                else
                                    utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })

                                    table.remove(chosen, table.find(chosen, opt))
                                    table.remove(choseninstances, table.find(choseninstances, optionbtn))

                                    local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                        dropdown.Text = table.concat(chosen, ", ") ~= "" and table.concat(chosen, ", ") or "NONE"
                                    end)

                                    tween.Completed:Wait()

                                    utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = table.concat(chosen, ", ") ~= "" and Color3.fromRGB(210, 210, 210) or Color3.fromRGB(150, 150, 150) })

                                    if flag then
                                        library.flags[flag] = chosen
                                    end

                                    callback(chosen)
                                end
                            end)
                        end
                    end

                    local function set(opt)
                        if not maxoptions then
                            if optioninstances[opt] then
                                for _, optbtn in next, dropdowncontent:GetChildren() do
                                    if optbtn ~= optioninstances[opt] and optbtn:IsA("TextButton") then
                                        utility.tween(optbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                    end
                                end

                                utility.tween(optioninstances[opt], { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                    dropdown.Text = opt
                                end)

                                chosen = opt

                                tween.Completed:Wait()

                                utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                if flag then
                                    library.flags[flag] = opt
                                end

                                callback(opt)
                            else
                                for _, optbtn in next, dropdowncontent:GetChildren() do
                                    if optbtn:IsA("TextButton") then
                                        utility.tween(optbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                    end
                                end

                                local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                    dropdown.Text = "NONE"
                                end)

                                tween.Completed:Wait()

                                utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                chosen = nil

                                if flag then
                                    library.flags[flag] = nil
                                end

                                callback(nil)
                            end
                        else
                            table.clear(chosen)
                            table.clear(choseninstances)

                            if not opt then
                                local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                    dropdown.Text = table.concat(chosen, ", ") ~= "" and table.concat(chosen, ", ") or "NONE"
                                end)

                                tween.Completed:Wait()

                                utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = table.concat(chosen, ", ") ~= "" and Color3.fromRGB(210, 210, 210) or Color3.fromRGB(150, 150, 150) })

                                if flag then
                                    library.flags[flag] = chosen
                                end

                                callback(chosen)
                            else
                                for _, opti in next, opt do
                                    if optioninstances[opti] then
                                        if #chosen >= maxoptions then
                                            table.remove(chosen, 1)
                                            utility.tween(choseninstances[1], { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                            table.remove(choseninstances, 1)
                                        end

                                        utility.tween(optioninstances[opti], { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        if not table.find(chosen, opti) then
                                            table.insert(chosen, opti)
                                        end

                                        if not table.find(choseninstances, opti) then
                                            table.insert(choseninstances, optioninstances[opti])
                                        end

                                        local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                            dropdown.Text = table.concat(chosen, ", ")
                                        end)

                                        tween.Completed:Wait()

                                        utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        if flag then
                                            library.flags[flag] = chosen
                                        end

                                        callback(chosen)
                                    end
                                end
                            end
                        end
                    end

                    if flag then
                        flags[flag] = set
                    end

                    local dropdowntypes = utility.table()

                    function dropdowntypes:Set(option)
                        set(option)
                    end

                    function dropdowntypes:Refresh(content)
                        if maxoptions then
                            table.clear(chosen)
                        end

                        table.clear(choseninstances)
                        table.clear(optioninstances)

                        for _, optbtn in next, dropdowncontent:GetChildren() do
                            if optbtn:IsA("TextButton") then
                                optbtn:Destroy()
                            end
                        end

                        set()

                        for _, opt in next, content do
                            if not maxoptions then
                                local optionbtn = option:Clone()
                                optionbtn.Parent = dropdowncontent
                                optionbtn.BackgroundTransparency = 2
                                optionbtn.Text = opt
                                optionbtn.TextTransparency = 1
                                optionbtn.TextStrokeTransparency = 1

                                optioninstances[opt] = optionbtn

                                optionbtn.MouseButton1Click:Connect(function()
                                    if chosen ~= opt then
                                        for _, optbtn in next, dropdowncontent:GetChildren() do
                                            if optbtn ~= optionbtn and optbtn:IsA("TextButton") then
                                                utility.tween(optbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                            end
                                        end

                                        utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                            dropdown.Text = opt
                                        end)

                                        chosen = opt

                                        tween.Completed:Wait()

                                        utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        if flag then
                                            library.flags[flag] = opt
                                        end

                                        callback(opt)
                                    else
                                        utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })

                                        local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                            dropdown.Text = "NONE"
                                        end)

                                        tween.Completed:Wait()

                                        utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                        chosen = nil

                                        if flag then
                                            library.flags[flag] = nil
                                        end

                                        callback(nil)
                                    end
                                end)
                            else
                                local optionbtn = option:Clone()
                                optionbtn.Parent = dropdowncontent
                                optionbtn.Text = opt

                                optioninstances[opt] = optionbtn

                                if table.find(default, opt) then
                                    chosen = opt
                                    optionbtn.BackgroundTransparency = 0
                                    optionbtn.TextColor3 = Color3.fromRGB(210, 210, 210)
                                end

                                optionbtn.MouseButton1Click:Connect(function()
                                    if not table.find(chosen, opt) then
                                        if #chosen >= maxoptions then
                                            table.remove(chosen, 1)
                                            utility.tween(choseninstances[1], { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })
                                            table.remove(choseninstances, 1)
                                        end

                                        utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        table.insert(chosen, opt)
                                        table.insert(choseninstances, optionbtn)

                                        local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                            dropdown.Text = table.concat(chosen, ", ")
                                        end)

                                        tween.Completed:Wait()

                                        utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = Color3.fromRGB(210, 210, 210) })

                                        if flag then
                                            library.flags[flag] = chosen
                                        end

                                        callback(chosen)
                                    else
                                        utility.tween(optionbtn, { 0.2 }, { BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(150, 150, 150) })

                                        table.remove(chosen, table.find(chosen, opt))
                                        table.remove(choseninstances, table.find(choseninstances, optionbtn))

                                        local tween = utility.tween(dropdown, { 0.2 }, { TextTransparency = 1, TextStrokeTransparency = 1 }, function()
                                            dropdown.Text = table.concat(chosen, ", ") ~= "" and table.concat(chosen, ", ") or "NONE"
                                        end)

                                        tween.Completed:Wait()

                                        utility.tween(dropdown, { 0.2 }, { TextTransparency = 0, TextStrokeTransparency = 0, TextColor3 = table.concat(chosen, ", ") ~= "" and Color3.fromRGB(210, 210, 210) or Color3.fromRGB(150, 150, 150) })

                                        if flag then
                                            library.flags[flag] = chosen
                                        end

                                        callback(chosen)
                                    end
                                end)
                            end
                        end
                    end

                    return dropdowntypes
                end

                function sectiontypes:Multibox(options)
                    local newoptions = {}
                    for i, v in next, options do
                        newoptions[i:lower()] = v
                    end

                    newoptions.maximum = newoptions.maximum or math.huge
                    return sectiontypes:Dropdown(newoptions)
                end

                function sectiontypes:Keybind(options)
                    options = utility.table(options)
                    local name = options.name
                    local keybindtype = options.mode
                    local default = options.default
                    local toggledefault = keybindtype and keybindtype:lower() == "toggle" and options.toggledefault
                    local toggleflag = keybindtype and keybindtype:lower() == "toggle" and options.togglepointer
                    local togglecallback = keybindtype and keybindtype:lower() == "toggle" and options.togglecallback or function() end
                    local holdflag = keybindtype and keybindtype:lower() == "hold" and options.holdflag
                    local holdcallback = keybindtype and keybindtype:lower() == "hold" and options.holdcallback or function() end
                    local blacklist = options.blacklist or {}
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    table.insert(blacklist, Enum.UserInputType.Focus)

                    local keybindholder = utility.create("TextButton", {
                        Size = UDim2.new(1, -5, 0, 14),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextSize = 14,
                        TextColor3 = Color3.fromRGB(0, 0, 0),
                        Font = Enum.Font.SourceSans,
                        Parent = sectioncontent
                    })

                    local icon, grayborder, enablediconholder
                    do
                        if keybindtype and keybindtype:lower() == "toggle" then
                            icon = utility.create("Frame", {
                                ZIndex = 9,
                                Size = UDim2.new(0, 10, 0, 10),
                                BorderColor3 = Color3.fromRGB(40, 40, 40),
                                Position = UDim2.new(0, 2, 0, 2),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                                Parent = keybindholder
                            })

                            grayborder = utility.create("Frame", {
                                ZIndex = 8,
                                Size = UDim2.new(1, 2, 1, 2),
                                Position = UDim2.new(0, -1, 0, -1),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                                Parent = icon
                            })

                            utility.create("UIGradient", {
                                Rotation = 90,
                                Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                                Parent = icon
                            })

                            utility.create("Frame", {
                                ZIndex = 7,
                                Size = UDim2.new(1, 2, 1, 2),
                                Position = UDim2.new(0, -1, 0, -1),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                                Parent = grayborder
                            })

                            enablediconholder = utility.create("Frame", {
                                ZIndex = 10,
                                Size = UDim2.new(1, 0, 1, 0),
                                BackgroundTransparency = 1,
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                                Parent = icon
                            })

                            local enabledicongradient = utility.create("UIGradient", {
                                Rotation = 90,
                                Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                                Parent = enablediconholder
                            })

                            accentobjects.gradient[enabledicongradient] = function(color)
                                return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                            end
                        end
                    end

                    local title = utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 1, 0),
                        BackgroundTransparency = 1,
                        Position = keybindtype and keybindtype:lower() == "toggle" and UDim2.new(0, 20, 0, 0) or UDim2.new(0, 1, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = (keybindtype and keybindtype:lower() == "toggle" and Color3.fromRGB(180, 180, 180)) or Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = keybindholder
                    })

                    local keytext = utility.create(keybindtype and keybindtype:lower() == "toggle" and "TextButton" or "TextLabel", {
                        ZIndex = 7,
                        Size = keybindtype and keybindtype:lower() == "toggle" and UDim2.new(0, 45, 1, 0) or UDim2.new(0, 0, 1, 0),
                        BackgroundTransparency = 1,
                        Position = keybindtype and keybindtype:lower() == "toggle" and UDim2.new(1, -45, 0, 0) or UDim2.new(1, 0, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        TextColor3 = Color3.fromRGB(150, 150, 150),
                        Text = "[NONE]",
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Right,
                        Parent = keybindholder
                    })

                    utility.create("UIPadding", {
                        PaddingBottom = UDim.new(0, 1),
                        Parent = keytext
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    local keys = {
                        [Enum.KeyCode.LeftShift] = "L-SHIFT",
                        [Enum.KeyCode.RightShift] = "R-SHIFT",
                        [Enum.KeyCode.LeftControl] = "L-CTRL",
                        [Enum.KeyCode.RightControl] = "R-CTRL",
                        [Enum.KeyCode.LeftAlt] = "L-ALT",
                        [Enum.KeyCode.RightAlt] = "R-ALT",
                        [Enum.KeyCode.CapsLock] = "CAPSLOCK",
                        [Enum.KeyCode.One] = "1",
                        [Enum.KeyCode.Two] = "2",
                        [Enum.KeyCode.Three] = "3",
                        [Enum.KeyCode.Four] = "4",
                        [Enum.KeyCode.Five] = "5",
                        [Enum.KeyCode.Six] = "6",
                        [Enum.KeyCode.Seven] = "7",
                        [Enum.KeyCode.Eight] = "8",
                        [Enum.KeyCode.Nine] = "9",
                        [Enum.KeyCode.Zero] = "0",
                        [Enum.KeyCode.KeypadOne] = "NUM-1",
                        [Enum.KeyCode.KeypadTwo] = "NUM-2",
                        [Enum.KeyCode.KeypadThree] = "NUM-3",
                        [Enum.KeyCode.KeypadFour] = "NUM-4",
                        [Enum.KeyCode.KeypadFive] = "NUM-5",
                        [Enum.KeyCode.KeypadSix] = "NUM-6",
                        [Enum.KeyCode.KeypadSeven] = "NUM-7",
                        [Enum.KeyCode.KeypadEight] = "NUM-8",
                        [Enum.KeyCode.KeypadNine] = "NUM-9",
                        [Enum.KeyCode.KeypadZero] = "NUM-0",
                        [Enum.KeyCode.Minus] = "-",
                        [Enum.KeyCode.Equals] = "=",
                        [Enum.KeyCode.Tilde] = "~",
                        [Enum.KeyCode.LeftBracket] = "[",
                        [Enum.KeyCode.RightBracket] = "]",
                        [Enum.KeyCode.RightParenthesis] = ")",
                        [Enum.KeyCode.LeftParenthesis] = "(",
                        [Enum.KeyCode.Semicolon] = ",",
                        [Enum.KeyCode.Quote] = "'",
                        [Enum.KeyCode.BackSlash] = "\\",
                        [Enum.KeyCode.Comma] = ",",
                        [Enum.KeyCode.Period] = ".",
                        [Enum.KeyCode.Slash] = "/",
                        [Enum.KeyCode.Asterisk] = "*",
                        [Enum.KeyCode.Plus] = "+",
                        [Enum.KeyCode.Period] = ".",
                        [Enum.KeyCode.Backquote] = "`",
                        [Enum.UserInputType.MouseButton1] = "MOUSE-1",
                        [Enum.UserInputType.MouseButton2] = "MOUSE-2",
                        [Enum.UserInputType.MouseButton3] = "MOUSE-3"
                    }

                    local keychosen
                    local isbinding = false

                    local function startbinding()
                        isbinding = true
                        keytext.Text = "[...]"
                        keytext.TextColor3 = Color3.fromRGB(210, 210, 210)

                        local binding
                        binding = services.InputService.InputBegan:Connect(function(input)
                            local key = keys[input.KeyCode] or keys[input.UserInputType]
                            keytext.Text = "[" .. (key or tostring(input.KeyCode):gsub("Enum.KeyCode.", "")) .. "]"
                            keytext.TextColor3 = Color3.fromRGB(180, 180, 180)
                            keytext.Size = UDim2.new(0, keytext.TextBounds.X, 1, 0)
                            keytext.Position = UDim2.new(1, -keytext.TextBounds.X, 0, 0)

                            if input.UserInputType == Enum.UserInputType.Keyboard then
                                task.wait()
                                if not table.find(blacklist, input.KeyCode) then
                                    keychosen = input.KeyCode

                                    if flag then
                                        library.flags[flag] = input.KeyCode
                                    end

                                    binding:Disconnect()
                                else
                                    keychosen = nil
                                    keytext.TextColor3 = Color3.fromRGB(180, 180, 180)
                                    keytext.Text = "NONE"

                                    if flag then
                                        library.flags[flag] = nil
                                    end

                                    binding:Disconnect()
                                end
                            else
                                if not table.find(blacklist, input.UserInputType) then
                                    keychosen = input.UserInputType

                                    if flag then
                                        library.flags[flag] = input.UserInputType
                                    end

                                    binding:Disconnect()
                                else
                                    keychosen = nil
                                    keytext.TextColor3 = Color3.fromRGB(180, 180, 180)
                                    keytext.Text = "[NONE]"

                                    keytext.Size = UDim2.new(0, keytext.TextBounds.X, 1, 0)
                                    keytext.Position = UDim2.new(1, -keytext.TextBounds.X, 0, 0)

                                    if flag then
                                        library.flags[flag] = nil
                                    end

                                    binding:Disconnect()
                                end
                            end
                        end)
                    end

                    if not keybindtype or keybindtype:lower() == "hold" then
                        keybindholder.MouseButton1Click:Connect(startbinding)
                    else
                        keytext.MouseButton1Click:Connect(startbinding)
                    end

                    local keybindtypes = utility.table()

                    if keybindtype and keybindtype:lower() == "toggle" then
                        local toggled = false

                        if toggleflag then
                            library.flags[toggleflag] = toggled
                        end

                        local function toggle()
                            if not switchingtabs then
                                toggled = not toggled

                                if toggleflag then
                                    library.flags[toggleflag] = toggled
                                end

                                togglecallback(toggled)

                                local enabledtransparency = toggled and 0 or 1
                                utility.tween(enablediconholder, { 0.2 }, { Transparency = enabledtransparency })

                                local textcolor = toggled and library.accent or Color3.fromRGB(180, 180, 180)
                                utility.tween(title, { 0.2 }, { TextColor3 = textcolor })

                                if toggled then
                                    table.insert(accentobjects.text, title)
                                elseif table.find(accentobjects.text, title) then
                                    table.remove(accentobjects.text, table.find(accentobjects.text, title))
                                end
                            end
                        end

                        keybindholder.MouseButton1Click:Connect(toggle)

                        local function set(bool)
                            if type(bool) == "boolean" and toggled ~= bool then
                                toggle()
                            end
                        end

                        function keybindtypes:Toggle(bool)
                            set(bool)
                        end

                        if toggledefault then
                            set(toggledefault)
                        end

                        if toggleflag then
                            flags[toggleflag] = set
                        end

                        services.InputService.InputBegan:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.Keyboard then
                                if input.KeyCode == keychosen then
                                    toggle()
                                    callback(keychosen)
                                end
                            else
                                if input.UserInputType == keychosen then
                                    toggle()
                                    callback(keychosen)
                                end
                            end
                        end)
                    end

                    if keybindtype and keybindtype:lower() == "hold" then
                        services.InputService.InputBegan:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.Keyboard then
                                if input.KeyCode == keychosen then
                                    if holdflag then
                                        library.flags[holdflag] = true
                                    end

                                    callback(keychosen)
                                    holdcallback(true)
                                end
                            else
                                if input.UserInputType == keychosen then
                                    if holdflag then
                                        library.flags[holdflag] = true
                                    end

                                    callback(keychosen)
                                    holdcallback(true)
                                end
                            end
                        end)

                        services.InputService.InputEnded:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.Keyboard then
                                if input.KeyCode == keychosen then
                                    if holdflag then
                                        library.flags[holdflag] = true
                                    end

                                    holdcallback(false)
                                end
                            else
                                if input.UserInputType == keychosen then
                                    if holdflag then
                                        library.flags[holdflag] = true
                                    end

                                    holdcallback(false)
                                end
                            end
                        end)
                    end

                    if not keybindtype then
                        services.InputService.InputBegan:Connect(function(input)
                            if input.UserInputType == Enum.UserInputType.Keyboard then
                                if input.KeyCode == keychosen then
                                    callback(keychosen)
                                end
                            else
                                if input.UserInputType == keychosen then
                                    callback(keychosen)
                                end
                            end
                        end)
                    end

                    local function setkey(newkey)
                        if tostring(newkey):find("Enum.KeyCode.") then
                            newkey = Enum.KeyCode[tostring(newkey):gsub("Enum.KeyCode.", "")]
                        else
                            newkey = Enum.UserInputType[tostring(newkey):gsub("Enum.UserInputType.", "")]
                        end

                        if not table.find(blacklist, newkey) then
                            local key = keys[newkey]
                            local text = "[" .. (keys[newkey] or tostring(newkey):gsub("Enum.KeyCode.", "")) .. "]"
                            local sizeX = services.TextService:GetTextSize(text, 8, Enum.Font.Code, V2new(1000, 1000)).X

                            keytext.Text = text
                            keytext.Size = UDim2.new(0, sizeX, 1, 0)
                            keytext.Position = UDim2.new(1, -sizeX, 0, 0)

                            keytext.TextColor3 = Color3.fromRGB(180, 180, 180)

                            keychosen = newkey

                            if flag then
                                library.flags[flag] = newkey
                            end

                            callback(newkey, true)
                        else
                            keychosen = nil
                            keytext.TextColor3 = Color3.fromRGB(180, 180, 180)
                            keytext.Text = "[NONE]"
                            keytext.Size = UDim2.new(0, keytext.TextBounds.X, 1, 0)
                            keytext.Position = UDim2.new(1, -keytext.TextBounds.X, 0, 0)

                            if flag then
                                library.flags[flag] = nil
                            end

                            callback(newkey, true)
                        end
                    end

                    if default then
                        task.wait()
                        setkey(default)
                    end

                    if flag then
                        flags[flag] = setkey
                    end

                    function keybindtypes:Set(newkey)
                        setkey(newkey)
                    end

                    return keybindtypes
                end

                function sectiontypes:ColorPicker(options)
                    options = utility.table(options)
                    local name = options.name
                    local default = options.default or Color3.fromRGB(255, 255, 255)
                    local colorpickertype = options.mode
                    local toggleflag = colorpickertype and colorpickertype:lower() == "toggle" and options.togglepointer
                    local togglecallback = colorpickertype and colorpickertype:lower() == "toggle" and options.togglecallback or function() end
                    local flag = options.pointer
                    local callback = options.callback or function() end

                    local colorpickerholder = utility.create("Frame", {
                        Size = UDim2.new(1, -5, 0, 14),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 0, 0, 0),
                        Parent = sectioncontent
                    })

                    local enabledcpiconholder
                    do
                        if colorpickertype and colorpickertype:lower() == "toggle" then
                            local togglecpicon = utility.create("Frame", {
                                ZIndex = 9,
                                Size = UDim2.new(0, 10, 0, 10),
                                BorderColor3 = Color3.fromRGB(40, 40, 40),
                                Position = UDim2.new(0, 2, 0, 2),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                                Parent = colorpickerholder
                            })

                            local grayborder = utility.create("Frame", {
                                ZIndex = 8,
                                Size = UDim2.new(1, 2, 1, 2),
                                Position = UDim2.new(0, -1, 0, -1),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                                Parent = togglecpicon
                            })

                            utility.create("UIGradient", {
                                Rotation = 90,
                                Color = ColorSequence.new(Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25)),
                                Parent = togglecpicon
                            })

                            utility.create("Frame", {
                                ZIndex = 7,
                                Size = UDim2.new(1, 2, 1, 2),
                                Position = UDim2.new(0, -1, 0, -1),
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                                Parent = grayborder
                            })

                            enabledcpiconholder = utility.create("Frame", {
                                ZIndex = 10,
                                Size = UDim2.new(1, 0, 1, 0),
                                BackgroundTransparency = 1,
                                BorderSizePixel = 0,
                                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                                Parent = togglecpicon
                            })

                            local enabledicongradient = utility.create("UIGradient", {
                                Rotation = 90,
                                Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                                Parent = enabledcpiconholder
                            })

                            accentobjects.gradient[enabledicongradient] = function(color)
                                return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                            end
                        end
                    end

                    local colorpickerframe = utility.create("Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(1, -70, 0, 148),
                        Position = UDim2.new(1, -168, 0, 18),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Visible = false,
                        Parent = colorpickerholder
                    })

                    colorpickerframe.DescendantAdded:Connect(function(descendant)
                        if not opened then
                            task.wait()
                            if not descendant.ClassName:find("UI") then
                                if descendant.ClassName:find("Text") then
                                    descendant.TextTransparency = descendant.TextTransparency + 1
                                    descendant.TextStrokeTransparency = descendant.TextStrokeTransparency + 1
                                end

                                if descendant.ClassName:find("Image") then
                                    descendant.ImageTransparency = descendant.ImageTransparency + 1
                                end

                                descendant.BackgroundTransparency = descendant.BackgroundTransparency + 1
                            end
                        end
                    end)

                    local bggradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                        Parent = colorpickerframe
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = colorpickerframe
                    })

                    local blackborder = utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local saturationframe = utility.create("ImageLabel", {
                        ZIndex = 12,
                        Size = UDim2.new(0, 128, 0, 100),
                        BorderColor3 = Color3.fromRGB(50, 50, 50),
                        Position = UDim2.new(0, 6, 0, 6),
                        BorderSizePixel = 0,
                        BackgroundColor3 = default,
                        Image = "http://www.roblox.com/asset/?id=8630797271",
                        Parent = colorpickerframe
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 11,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = saturationframe
                    })

                    utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local saturationpicker = utility.create("Frame", {
                        ZIndex = 13,
                        Size = UDim2.new(0, 2, 0, 2),
                        BorderColor3 = Color3.fromRGB(10, 10, 10),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = saturationframe
                    })

                    local hueframe = utility.create("ImageLabel", {
                        ZIndex = 12,
                        Size = UDim2.new(0, 14, 0, 100),
                        Position = UDim2.new(1, -20, 0, 6),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 193, 49),
                        ScaleType = Enum.ScaleType.Crop,
                        Image = "http://www.roblox.com/asset/?id=8630799159",
                        Parent = colorpickerframe
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 11,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = hueframe
                    })

                    utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local huepicker = utility.create("Frame", {
                        ZIndex = 13,
                        Size = UDim2.new(1, 0, 0, 1),
                        BorderColor3 = Color3.fromRGB(10, 10, 10),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = hueframe
                    })

                    local boxholder = utility.create("Frame", {
                        Size = UDim2.new(1, -8, 0, 17),
                        ClipsDescendants = true,
                        Position = UDim2.new(0, 4, 0, 110),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = colorpickerframe
                    })

                    local box = utility.create("TextBox", {
                        ZIndex = 13,
                        Size = UDim2.new(1, -4, 1, -4),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 2, 0, 2),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = table.concat({ utility.getrgb(default) }, ", "),
                        PlaceholderText = "R, G, B",
                        Font = Enum.Font.Code,
                        Parent = boxholder
                    })

                    local grayborder = utility.create("Frame", {
                        ZIndex = 11,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = box
                    })

                    utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local bg = utility.create("Frame", {
                        ZIndex = 12,
                        Size = UDim2.new(1, 0, 1, 0),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = box
                    })

                    utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                        Parent = bg
                    })

                    local toggleholder = utility.create("TextButton", {
                        Size = UDim2.new(1, -8, 0, 14),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 4, 0, 130),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextSize = 14,
                        TextColor3 = Color3.fromRGB(0, 0, 0),
                        Font = Enum.Font.SourceSans,
                        Parent = colorpickerframe
                    })

                    local toggleicon = utility.create("Frame", {
                        ZIndex = 12,
                        Size = UDim2.new(0, 10, 0, 10),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        Position = UDim2.new(0, 2, 0, 2),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = toggleholder
                    })

                    local enablediconholder = utility.create("Frame", {
                        ZIndex = 13,
                        Size = UDim2.new(1, 0, 1, 0),
                        BackgroundTransparency = 1,
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = toggleicon
                    })

                    local enabledicongradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { library.accent, Color3.fromRGB(25, 25, 25) },
                        Parent = enablediconholder
                    })

                    accentobjects.gradient[enabledicongradient] = function(color)
                        return utility.gradient { color, Color3.fromRGB(25, 25, 25) }
                    end

                    local grayborder = utility.create("Frame", {
                        ZIndex = 11,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = toggleicon
                    })

                    utility.create("Frame", {
                        ZIndex = 10,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { Color3.fromRGB(35, 35, 35), Color3.fromRGB(25, 25, 25) },
                        Parent = toggleicon
                    })

                    local rainbowtxt = utility.create("TextLabel", {
                        ZIndex = 10,
                        Size = UDim2.new(0, 0, 1, 0),
                        BackgroundTransparency = 1,
                        Position = UDim2.new(0, 20, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(180, 180, 180),
                        Text = "Rainbow",
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = toggleholder
                    })

                    local colorpicker = utility.create("TextButton", {
                        Size = UDim2.new(1, 0, 0, 14),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextSize = 14,
                        TextColor3 = Color3.fromRGB(0, 0, 0),
                        Font = Enum.Font.SourceSans,
                        Parent = colorpickerholder
                    })

                    local icon = utility.create(colorpickertype and colorpickertype:lower() == "toggle" and "TextButton" or "Frame", {
                        ZIndex = 9,
                        Size = UDim2.new(0, 18, 0, 10),
                        BorderColor3 = Color3.fromRGB(40, 40, 40),
                        Position = UDim2.new(1, -20, 0, 2),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        Parent = colorpicker
                    })

                    if colorpickertype and colorpickertype:lower() == "toggle" then
                        icon.Text = ""
                    end

                    local grayborder = utility.create("Frame", {
                        ZIndex = 8,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(40, 40, 40),
                        Parent = icon
                    })

                    utility.create("Frame", {
                        ZIndex = 7,
                        Size = UDim2.new(1, 2, 1, 2),
                        Position = UDim2.new(0, -1, 0, -1),
                        BorderSizePixel = 0,
                        BackgroundColor3 = Color3.fromRGB(10, 10, 10),
                        Parent = grayborder
                    })

                    local icongradient = utility.create("UIGradient", {
                        Rotation = 90,
                        Color = utility.gradient { default, utility.changecolor(default, -200) },
                        Parent = icon
                    })

                    local title = utility.create("TextLabel", {
                        ZIndex = 7,
                        Size = UDim2.new(0, 0, 0, 14),
                        BackgroundTransparency = 1,
                        Position = colorpickertype and colorpickertype:lower() == "toggle" and UDim2.new(0, 20, 0, 0) or UDim2.new(0, 1, 0, 0),
                        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                        FontSize = Enum.FontSize.Size14,
                        TextStrokeTransparency = 0,
                        TextSize = 13,
                        TextColor3 = Color3.fromRGB(210, 210, 210),
                        Text = name,
                        Font = Enum.Font.Code,
                        TextXAlignment = Enum.TextXAlignment.Left,
                        Parent = colorpicker
                    })

                    if #sectioncontent:GetChildren() - 1 <= max then
                        sectionholder.Size = UDim2.new(1, -1, 0, sectionlist.AbsoluteContentSize.Y + 28)
                    end

                    local colorpickertypes = utility.table()

                    if colorpickertype and colorpickertype:lower() == "toggle" then
                        local toggled = false

                        if toggleflag then
                            library.flags[toggleflag] = toggled
                        end

                        local function toggle()
                            if not switchingtabs then
                                toggled = not toggled

                                if toggleflag then
                                    library.flags[toggleflag] = toggled
                                end

                                togglecallback(toggled)

                                local enabledtransparency = toggled and 0 or 1
                                utility.tween(enabledcpiconholder, { 0.2 }, { BackgroundTransparency = enabledtransparency })

                                local textcolor = toggled and library.accent or Color3.fromRGB(180, 180, 180)
                                utility.tween(title, { 0.2 }, { TextColor3 = textcolor })

                                if toggled then
                                    table.insert(accentobjects.text, title)
                                elseif table.find(accentobjects.text, title) then
                                    table.remove(accentobjects.text, table.find(accentobjects.text, title))
                                end
                            end
                        end

                        colorpicker.MouseButton1Click:Connect(toggle)

                        local function set(bool)
                            if type(bool) == "boolean" and toggled ~= bool then
                                toggle()
                            end
                        end

                        function colorpickertypes:Toggle(bool)
                            set(bool)
                        end

                        if toggledefault then
                            set(toggledefault)
                        end

                        if toggleflag then
                            flags[toggleflag] = set
                        end
                    end

                    local opened = false
                    local opening = false

                    local function opencolorpicker()
                        if not opening then
                            opening = true

                            opened = not opened

                            if opened then
                                utility.tween(colorpickerholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, 168) })
                            end

                            local tween = utility.makevisible(colorpickerframe, opened)

                            tween.Completed:Wait()

                            if not opened then
                                local tween = utility.tween(colorpickerholder, { 0.2 }, { Size = UDim2.new(1, -5, 0, 16) })
                                tween.Completed:Wait()
                            end

                            opening = false
                        end
                    end

                    if colorpickertype and colorpickertype:lower() == "toggle" then
                        icon.MouseButton1Click:Connect(opencolorpicker)
                    else
                        colorpicker.MouseButton1Click:Connect(opencolorpicker)
                    end

                    local hue, sat, val = default:ToHSV()

                    local slidinghue = false
                    local slidingsaturation = false

                    local hsv = Color3.fromHSV(hue, sat, val)

                    if flag then
                        library.flags[flag] = default
                    end

                    local function updatehue(input)
                        local sizeY = 1 - math.clamp((input.Position.Y - hueframe.AbsolutePosition.Y) / hueframe.AbsoluteSize.Y, 0, 1)
                        local posY = math.clamp(((input.Position.Y - hueframe.AbsolutePosition.Y) / hueframe.AbsoluteSize.Y) * hueframe.AbsoluteSize.Y, 0, hueframe.AbsoluteSize.Y - 2)
                        huepicker.Position = UDim2.new(0, 0, 0, posY)

                        hue = sizeY
                        hsv = Color3.fromHSV(sizeY, sat, val)

                        box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                        saturationframe.BackgroundColor3 = hsv
                        icon.BackgroundColor3 = hsv

                        if flag then
                            library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                        end

                        callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                    end

                    hueframe.InputBegan:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            slidinghue = true
                            updatehue(input)
                        end
                    end)

                    hueframe.InputEnded:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            slidinghue = false
                        end
                    end)

                    services.InputService.InputChanged:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseMovement then
                            if slidinghue then
                                updatehue(input)
                            end
                        end
                    end)

                    local function updatesatval(input)
                        local sizeX = math.clamp((input.Position.X - saturationframe.AbsolutePosition.X) / saturationframe.AbsoluteSize.X, 0, 1)
                        local sizeY = 1 - math.clamp((input.Position.Y - saturationframe.AbsolutePosition.Y) / saturationframe.AbsoluteSize.Y, 0, 1)
                        local posY = math.clamp(((input.Position.Y - saturationframe.AbsolutePosition.Y) / saturationframe.AbsoluteSize.Y) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4)
                        local posX = math.clamp(((input.Position.X - saturationframe.AbsolutePosition.X) / saturationframe.AbsoluteSize.X) * saturationframe.AbsoluteSize.X, 0, saturationframe.AbsoluteSize.X - 4)

                        saturationpicker.Position = UDim2.new(0, posX, 0, posY)

                        sat = sizeX
                        val = sizeY
                        hsv = Color3.fromHSV(hue, sizeX, sizeY)

                        box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                        saturationframe.BackgroundColor3 = hsv
                        icon.BackgroundColor3 = hsv

                        if flag then
                            library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                        end

                        callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                    end

                    saturationframe.InputBegan:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            slidingsaturation = true
                            updatesatval(input)
                        end
                    end)

                    saturationframe.InputEnded:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 then
                            slidingsaturation = false
                        end
                    end)

                    services.InputService.InputChanged:Connect(function(input)
                        if input.UserInputType == Enum.UserInputType.MouseMovement then
                            if slidingsaturation then
                                updatesatval(input)
                            end
                        end
                    end)

                    local function set(color)
                        if type(color) == "table" then
                            color = Color3.fromRGB(unpack(color))
                        end

                        hue, sat, val = color:ToHSV()
                        hsv = Color3.fromHSV(hue, sat, val)

                        saturationframe.BackgroundColor3 = hsv
                        icon.BackgroundColor3 = hsv
                        saturationpicker.Position = UDim2.new(0, (math.clamp(sat * saturationframe.AbsoluteSize.X, 0, saturationframe.AbsoluteSize.X - 4)), 0, (math.clamp((1 - val) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4)))
                        huepicker.Position = UDim2.new(0, 0, 0, math.clamp((1 - hue) * saturationframe.AbsoluteSize.Y, 0, saturationframe.AbsoluteSize.Y - 4))

                        box.Text = math.clamp(math.floor((hsv.R * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.B * 255) + 0.5), 0, 255) .. ", " .. math.clamp(math.floor((hsv.G * 255) + 0.5), 0, 255)

                        if flag then
                            library.flags[flag] = Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255)
                        end

                        callback(Color3.fromRGB(hsv.r * 255, hsv.g * 255, hsv.b * 255))
                    end

                    local toggled = false

                    local function toggle()
                        if not switchingtabs then
                            toggled = not toggled

                            if toggled then
                                task.spawn(function()
                                    while toggled do
                                        for i = 0, 1, 0.0015 do
                                            if not toggled then
                                                return
                                            end

                                            local color = Color3.fromHSV(i, 1, 1)
                                            set(color)

                                            task.wait()
                                        end
                                    end
                                end)
                            end

                            local enabledtransparency = toggled and 0 or 1
                            utility.tween(enablediconholder, { 0.2 }, { BackgroundTransparency = enabledtransparency })

                            local textcolor = toggled and library.accent or Color3.fromRGB(180, 180, 180)
                            utility.tween(rainbowtxt, { 0.2 }, { TextColor3 = textcolor })

                            if toggled then
                                table.insert(accentobjects.text, title)
                            elseif table.find(accentobjects.text, title) then
                                table.remove(accentobjects.text, table.find(accentobjects.text, title))
                            end
                        end
                    end

                    toggleholder.MouseButton1Click:Connect(toggle)

                    box.FocusLost:Connect(function()
                        local _, amount = box.Text:gsub(", ", "")

                        if amount == 2 then
                            local values = box.Text:split(", ")
                            local r, g, b = math.clamp(values[1], 0, 255), math.clamp(values[2], 0, 255), math.clamp(values[3], 0, 255)
                            set(Color3.fromRGB(r, g, b))
                        else
                            rgb.Text = math.floor((hsv.r * 255) + 0.5) .. ", " .. math.floor((hsv.g * 255) + 0.5) .. ", " .. math.floor((hsv.b * 255) + 0.5)
                        end
                    end)

                    if default then
                        set(default)
                    end

                    if flag then
                        flags[flag] = set
                    end

                    local colorpickertypes = utility.table()

                    function colorpickertypes:Set(color)
                        set(color)
                    end
                end

                return sectiontypes
            end

            return pagetypes
        end

        return windowtypes
    end

    function library:Initialize()
        if gethui then
            gui.Parent = gethui()
        elseif syn and syn.protect_gui then
            syn.protect_gui(gui)
            gui.Parent = services.CoreGui
        end
    end

    function library:Init()
        library:Initialize()
    end
end

local Library = library

getgenv().ScriptName = "Revision"
local Window = Library:New({ Name = getgenv().ScriptName, Accent = Color3.fromRGB(133, 87, 242) })
--

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
-- pages , sections --


local Pages = {
    Rage = Window:Page({ Name = "Rage" }),
    Visuals = Window:Page({ Name = "Visuals" }),
    Misc = Window:Page({ Name = "Misc" }),
    Settings = Window:Page({ Name = "Settings" }),
}

local Sections = {
Rage = {
    Main = {
        TargetAim = Pages.Rage:Section({ Name = "Target Aimbot", Side = "Left", Max = 1000  }), --Max Is Amount Of Stuff Visible At The Section (If It's Lower Than The Amount Of Stuff You Put Then It Will Become Scrollable)
        Visuals = Pages.Rage:Section({ Name = "Visuals", Side = "Right", Max = 1000  }),
        AntiAim = Pages.Rage:Section({ Name = "Anti-Aim", Side = "Right", Max = 1000  }),
    }
},

Visuals = {
    Main = {
        PlayerESP = Pages.Visuals:Section({ Name = "Player ESP", Side = "Left", Max = 1000  }), --Max Is Amount Of Stuff Visible At The Section (If It's Lower Than The Amount Of Stuff You Put Then It Will Become Scrollable)
        World = Pages.Visuals:Section({ Name = "World", Side = "Right", Max = 1000  }),
        Local = Pages.Visuals:Section({ Name = "Local", Side = "Left", Max = 1000  }),
    }
},

Misc = {
    Main = {
        Movement = Pages.Misc:Section({ Name = "Movement", Side = "Right", Max = 1000  }), --Max Is Amount Of Stuff Visible At The Section (If It's Lower Than The Amount Of Stuff You Put Then It Will Become Scrollable)
        MainMisc = Pages.Misc:Section({ Name = "Main", Side = "Left", Max = 1000  }),
    }
},

Settings = {
    Main = {
        UI = Pages.Settings:Section({ Name = "GUI Interface", Side = "Left", Max = 1000  }), --Max Is Amount Of Stuff Visible At The Section (If It's Lower Than The Amount Of Stuff You Put Then It Will Become Scrollable)
        Configuration = Pages.Settings:Section({ Name = "Configuration", Side = "Right", Max = 1000  }),
    }
}
}


local ragebotsettings = { 
    HookingMethod = "",
    enabled = false,
    key = Enum.KeyCode.Q,
    prediction = 0,
    part = "Head",
    radius = 180,
    fov = false,
    WallCheck = false,
    ClosestBodyPart = false,
    KnockedCheck = false,
    HitLogs = false,
    Tracer = false,
    HealthIndicator = false,
    AmmoIndicator = false,
    Chams = false,
    Glow = false,
    Resolver = false,
    AutoShoot = false,
    FireRateDelay = 0.1,
    Jitter = false,
    JitterAt = "Move Direction",
    Yaw = "Forwards",
    Freestand = false,
    FreestandOffset = 1,
    FakeLag = false,
    FakeLagType = "Normal",
    TickTime = 0.14,
    Walk = false,
    WalkSpeed = 1,
    AutoReload = false,
    ChatSpam = false,
    ChatSpamType = "avon.hook",
    ChatSpamDelay = 2,
    HitSound = false,
    HitSoundType = "",
    DesyncResolver = false,
}


if game.PlaceId == 2788229376 then
    ragebotsettings.HookingMethod = "UpdateMousePos"
elseif game.PlaceId == 9825515356 then
    ragebotsettings.HookingMethod = "GetMousePos"
else
    ragebotsettings.HookingMethod = "UpdateMousePos"
end


getgenv().oldhealth = 0

--Drawing Lib Sets
local Line = Drawnew("Line")
Line.Color = Color3.fromRGB(133, 87, 242)
Line.Thickness = 1
Line.Transparency = 1
Line.Visible = false
Line.ZIndex = 1

--Health Indicator
local HealthIndicator = Drawnew("Text")
    HealthIndicator.Transparency = 1
    HealthIndicator.Visible = false
    HealthIndicator.Color = Color3.fromRGB(255, 255, 255)
    HealthIndicator.Size = 13
    HealthIndicator.Center = true
    HealthIndicator.Outline = true
    HealthIndicator.Font = 2
    HealthIndicator.Text = "Name [N/A]"

    --Ammo Indicator
local AmmoIndicator = Drawnew("Text")
AmmoIndicator.Transparency = 1
AmmoIndicator.Visible = false
AmmoIndicator.Color = Color3.fromRGB(1, 112, 255)
AmmoIndicator.Size = 13
AmmoIndicator.Center = true
AmmoIndicator.Outline = true
AmmoIndicator.Font = 2
AmmoIndicator.Text = "[N/A]"


local plr = game.Players.LocalPlayer
local mouse = game.Players.LocalPlayer:GetMouse()
local runserv = game:GetService('RunService')

fov = Drawnew("Circle")
fov.Color = Color3.fromRGB(133, 87, 242)
fov.Thickness = 0
fov.NumSides = 400
fov.Radius = ragebotsettings.radius
fov.Thickness = 0
fov.Transparency = 1
fov.Visible = ragebotsettings.fov
fov.Filled = false

local camera = game.Workspace.Camera
local target = false

function UpdateFOVFunctions()
fov.Thickness = 0
fov.Position = V2new(mouse.X, mouse.Y+35)
fov.Radius = ragebotsettings.radius
fov.Visible = ragebotsettings.fov

return fov
end

function WallCheck(part, ignore) 
if ragebotsettings.WallCheck then
    Origin = camera.CFrame.Position
    CheckRay = Ray.new(Origin, part- Origin)
    Hit = workspace:FindPartOnRayWithIgnoreList(CheckRay, ignore)
    return Hit == nil
else
    return true
end
end

function AutoWallCheck(part, ignore) 
    if ragebotsettings.AutoShoot and game.Players.LocalPlayer.Character then
        Origin = game.Players.LocalPlayer.Character:FindFirstChild("RightHand").Position
        CheckRay = Ray.new(Origin, part- Origin)
        Hit = workspace:FindPartOnRayWithIgnoreList(CheckRay, ignore)
        return Hit == nil
    else
        return true
    end
    end

function KnockCheck(player)
if ragebotsettings.KnockedCheck then
    if player.Character.BodyEffects:FindFirstChild("K.O").Value == false then
        return true
    end 
else
    return true
end
end

local gs = game:GetService("GuiService")
local ggi = gs.GetGuiInset

function FindMagnitudeOfPart(player)
local pos = camera:WorldToViewportPoint(player.Position)
local magnitude = (V2new(pos.X, pos.Y) - V2new(mouse.X, mouse.Y + ggi(gs).Y)).magnitude
return magnitude
end

function ClosestBodyPart(player)  
local Body = nil
local shortestDistance = math.huge
for i,v in ipairs(player.Character:GetChildren()) do
    if v.ClassName == "Part" or v.ClassName == "MeshPart" then
        local pos = FindMagnitudeOfPart(v)
        if pos < shortestDistance then
            Body = v.Name
            shortestDistance = pos
        end 
    end
end
return Body
end

local UIS = game:GetService('UserInputService')
local Workspace = game:GetService('Workspace')

UIS.InputBegan:Connect(function(key, GameProccesedEvent)
if (not GameProccesedEvent) then
    if (key.KeyCode == ragebotsettings.key) then
        if ragebotsettings.enabled then
            target = not target

            if target then

                maintarget = getclosestplayer()
                getgenv().oldhealth = maintarget.Character.Humanoid.Health

                if maintarget == nil then
                    if ragebotsettings.notifications then
                        Notification:Notify(
                        {Title = "gamesense", Description = "Targetted: (Nil)"},
                        {OutlineColor = Color3.fromRGB(80, 80, 80),Time = 2, Type = "image"},
                        {Image = "http://www.roblox.com/asset/?id=8850953349", ImageColor = Color3.fromRGB(255, 255, 255)}
                    )
                    end
                end

                if ragebotsettings.notifications then
                    Notification:Notify(
                        {Title = "gamesense", Description = "Targetted: ("..tostring(maintarget.Character.Humanoid.DisplayName)..")"},
                        {OutlineColor = Color3.fromRGB(80, 80, 80),Time = 2, Type = "image"},
                        {Image = "http://www.roblox.com/asset/?id=8850953349", ImageColor = Color3.fromRGB(255, 255, 255)}
                    )
                end
            elseif not target then
                if ragebotsettings.notifications then
                    Notification:Notify(
                        {Title = "gamesense", Description = "Unlocked"},
                        {OutlineColor = Color3.fromRGB(80, 80, 80),Time = 2, Type = "image"},
                        {Image = "http://www.roblox.com/asset/?id=8850953349", ImageColor = Color3.fromRGB(255, 255, 255)}
                    )
                end
            end
        end
    end
end
end)

function getclosestplayer()
local closest
local distance = fov.Radius

for i, v in pairs(game.Players:GetPlayers()) do
    if v ~= game.Players.LocalPlayer and v.Character and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health ~= 0 and WallCheck(v.Character.HumanoidRootPart.Position,{plr.Character,v.Character}) and KnockCheck(v) and v.Character:FindFirstChild("LowerTorso") then
        

        local pos,vis = camera:WorldToViewportPoint(v.Character.PrimaryPart.Position)
        local magnitude = (V2new(pos.X, pos.Y) - V2new(mouse.X, mouse.Y)).Magnitude

        if (fov.Radius > magnitude and magnitude < distance and vis) then
            closest = v
            distance = magnitude
        end
    end
    end
    return closest
end

local Chams = Instance.new("Highlight")
Chams.Parent = game.CoreGui
Chams.FillColor = Color3.fromRGB(26, 220, 32)
Chams.OutlineColor = Color3.fromRGB(255,255,255)
Chams.FillTransparency = 1
Chams.OutlineTransparency = 1

getgenv().GunChams = false
getgenv().GunColor = Color3.fromRGB(255,255,255)
getgenv().SelfChams = false
getgenv().SelfColor = game.Players.LocalPlayer.Character.Head.Color
getgenv().AntiStomp = false
getgenv().AntiFling = false
getgenv().FakeAimViewer = false

--Main Loop--
runserv.RenderStepped:Connect(function()
    fov.Position = V2new(mouse.X,mouse.Y+35)

if target and ragebotsettings.ClosestBodyPart then
    ragebotsettings.part = ClosestBodyPart(maintarget)
end

    local Vector, OnScreen = camera:worldToViewportPoint(game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").Position)
    Line.From = V2new(Vector.X, Vector.Y)

if ragebotsettings.Tracer == true and target then
    local Vector, OnScreen = camera:worldToViewportPoint(maintarget.Character[ragebotsettings.part].Position + maintarget.Character:FindFirstChild("Humanoid").MoveDirection*ragebotsettings.prediction)
    Line.Visible = true
    Line.To = V2new(Vector.X, Vector.Y)
else
    Line.Visible = false
end

if ragebotsettings.HealthIndicator == true and target then
    local Vector, OnScreen = camera:worldToViewportPoint(maintarget.Character.Head.Position)
    local Health = maintarget.Character.Humanoid.Health
    local Name = maintarget.Character.Humanoid.DisplayName
    local Armor = maintarget.Character.BodyEffects.Armor.Value
    HealthIndicator.Visible = true
    HealthIndicator.Position = V2new(Vector.X, Vector.Y - 30)
    HealthIndicator.Text = ""..Name.." ["..math.floor(Health).."/100] Armor: ["..Armor.."/130]"
else
    HealthIndicator.Visible = false
end

pcall(function()
if ragebotsettings.AmmoIndicator == true and target then
    local Vector, OnScreen = camera:worldToViewportPoint(maintarget.Character.Head.Position)
    AmmoIndicator.Visible = true
    AmmoIndicator.Position = V2new(Vector.X, Vector.Y + 30)
    if maintarget.Character:FindFirstChildOfClass("Tool") and maintarget.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo") then
        local Ammo = maintarget.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo").Value
    AmmoIndicator.Text = "["..Ammo.."]"
    else
        AmmoIndicator.Text = "[N/A]"
    end
else
    AmmoIndicator.Visible = false
end
end)

if target then
    Chams.Parent = maintarget.Character

    if ragebotsettings.Chams then
        Chams.FillTransparency = 0
    else
        Chams.FillTransparency = 1
    end

    if ragebotsettings.Glow then
        Chams.OutlineTransparency = 0
    else
        Chams.OutlineTransparency = 1
    end
else
    Chams.Parent = game.CoreGui
end

if ragebotsettings.AutoShoot and target and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo") then
    if AutoWallCheck(maintarget.Character.HumanoidRootPart.Position,{plr.Character,maintarget.Character}) and KnockCheck(maintarget) then
        game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):Activate()
                wait(ragebotsettings.FireRateDelay)
    end
end

if ragebotsettings.AutoReload and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo") then
    local GunName = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Name

    if game.Players.LocalPlayer.Character[GunName]:FindFirstChild("Ammo").Value <= 0 then

local ReloadArgument = "Reload"
local GunPos = game.Players.LocalPlayer.Character[GunName]

if game.Players.LocalPlayer.Character.BodyEffects.Reload.Value == false then
game:GetService("ReplicatedStorage").MainEvent:FireServer(ReloadArgument, GunPos)
task.wait(2)
else
    task.wait(2)
end
    end
end

if getgenv().GunChams then
    pcall(function()
        if game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo") then
    game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Default.Material = "ForceField"
    game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Default.Color = getgenv().GunColor
        end
    end)
else
    pcall(function()
    game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Default.Material = "Plastic"
    end)
end

if getgenv().SelfChams then
    pcall(function()
    if game.Players.LocalPlayer and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character.Humanoid and game.Players.LocalPlayer.Character.Humanoid.Health ~= 0 then
        for i,v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if v:IsA("MeshPart") or v:IsA("Part") then
                v.Material = "ForceField"
                v.Color = getgenv().SelfColor
            end
        end
    end
end)
else
    pcall(function()
    if game.Players.LocalPlayer and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character.Humanoid and game.Players.LocalPlayer.Character.Humanoid.Health ~= 0 then
        for i,v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if v:IsA("MeshPart") or v:IsA("Part") then
                v.Material = "Plastic"
                end
            end
        end
    end)
end

if getgenv().AntiStomp then
    pcall(function()
        if game.Players.LocalPlayer.Character.Humanoid.Health <= 5 then
            for i, v in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                if v:IsA("BasePart") then
                    v:Destroy()
                end
            end
        end
    end)
end

if getgenv().FakeAimViewer then
    pcall(function()
        game.Players.LocalPlayer.Character.BodyEffects.MousePos.Value = V3new(0,0,0)
    end)
end

pcall(function()
    if target and ragebotsettings.DesyncResolver then
        maintarget.Character.HumanoidRootPart.Velocity = V3new(0,0,0)
        maintarget.Character.HumanoidRootPart.AssemblyLinearVelocity = V3new(0,0,0)
    end
end)

UpdateFOVFunctions()

end)

local gm = getrawmetatable(game)
local old_index = gm.__namecall
setreadonly(gm, false)
gm.__namecall = newcclosure(function(...)
local args = {...}
if target and getnamecallmethod() == "FireServer" and args[2] == ragebotsettings.HookingMethod then
        args[3] = maintarget.Character[ragebotsettings.part].Position+(maintarget.Character.Humanoid.MoveDirection*ragebotsettings.prediction)
    return old_index(unpack(args))
end
return old_index(...)
end)

UIS.InputBegan:Connect(function(input, gameProcessedEvent)
    if ragebotsettings.HitSound then
if input.UserInputType == Enum.UserInputType.MouseButton1 then
    if game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):FindFirstChild("Ammo") then
        task.wait(0.3)
        if maintarget.Character.Humanoid.Health < getgenv().oldhealth then
        if ragebotsettings.HitSoundType == "Bubble" then
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://7147454322"
            sound.Parent = game:GetService("SoundService")
            sound.Volume = 6
            sound.PlaybackSpeed = 0.4
            sound:Play()
            getgenv().oldhealth = maintarget.Character.Humanoid.Health
        elseif ragebotsettings.HitSoundType == "GL" then
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://1981976520"
            sound.Parent = game:GetService("SoundService")
            sound.Volume = 6
            sound.PlaybackSpeed = 1
            sound:Play()
            getgenv().oldhealth = maintarget.Character.Humanoid.Health
        elseif ragebotsettings.HitSoundType == "PewPew" then
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://3008769599"
            sound.Parent = game:GetService("SoundService")
            sound.Volume = 6
            sound.PlaybackSpeed = 5
            sound:Play()
            getgenv().oldhealth = maintarget.Character.Humanoid.Health
        end
    end
end
end
end
end)

Sections.Rage.Main.TargetAim:Keybind(
    {
    Name = "Enabled",
    Callback = function(key) end,
    Default = false,
    Pointer = "1",
    Mode = "Toggle",
    toggleflag = "1.1",
    togglecallback = function(state)
        ragebotsettings.enabled = state
    end
}
)

Sections.Rage.Main.TargetAim:Keybind { Name = "Keybind", Default = Enum.KeyCode.Q, Blacklist = { Enum.UserInputType.MouseButton1 }, Flag = "Bind", Callback = function(key, fromsetting)
    ragebotsettings.key = key
end }

Sections.Rage.Main.TargetAim:Box { Name = "Prediction Amount", Callback = function(text)
    ragebotsettings.prediction = text
end }

Sections.Rage.Main.TargetAim:Dropdown(
                {
                Name = "Aimpart",
                Options = {"Head", "Torso", "Stomach", "Pelvis"},
                Default = "Default",
                Pointer = "38",
                callback = function(state)
                    if state == "Head" then
                        ragebotsettings.part = "Head"
                    elseif state == "Torso" then
                        ragebotsettings.part = "UpperTorso"
                    elseif state == "Stomach" then
                        ragebotsettings.part = "HumanoidRootPart"
                    elseif state == "Pelvis" then
                        ragebotsettings.part = "LowerTorso"
                    end
                end
            }
            )

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Desync Resolver",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.DesyncResolver = state
                end
            }
            )

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Hitsound",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.HitSound = state
                end
            }
            )

            Sections.Rage.Main.TargetAim:Dropdown(
                {
                Name = "Hitsound Type",
                Options = {"Bubble", "GL", "PewPew"},
                Default = "Default",
                Pointer = "38",
                callback = function(state)
                    ragebotsettings.HitSoundType = state
                end
            }
            )

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Autoshoot",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.AutoShoot = state
                end
            }
            )

            getgenv().Onshot = false
            getgenv().Ticks = 0
            
            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Onshot",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    getgenv().Onshot = state
                end
            }
            )

            function GetGun()
                if game.Players.LocalPlayer.Character then
                    for i, v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
                        if v:FindFirstChild 'Ammo' then
                            return v
                        end
                    end
                end
                return nil
            end            

            spawn(function()
                while task.wait() do
                    gun = GetGun()
                    if gun then
                        LastAmmo = gun.Ammo.Value
                        gun.Ammo:GetPropertyChangedSignal("Value"):Connect(function()
                            if getgenv().Onshot and target and gun.Ammo.Value < LastAmmo then
                                LastAmmo = gun.Ammo.Value
                                if maintarget.Character:FindFirstChild("HumanoidRootPart") and maintarget.Character:FindFirstChild("Humanoid") and maintarget.Character:FindFirstChild("Humanoid").Health > 0 then 
                                    local OldCFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
                                    maintarget.Character:FindFirstChild("Humanoid").AutoRotate = false
                                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position, V3new(maintarget.Character.HumanoidRootPart.CFrame.X, game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position.Y, maintarget.Character.HumanoidRootPart.CFrame.Z))
                                wait(getgenv().Ticks)
                                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = OldCFrame
                                maintarget.Character:FindFirstChild("Humanoid").AutoRotate = true
                                end
                            end
                        end)
                    end
                end
            end)

            Sections.Rage.Main.TargetAim:Slider(
                {
                Name = "Recharge Speed",
                Minimum = 0,
                Maximum = 1,
                Default = 0,
                Decimals = 0.1,
                Pointer = "Yasss",
                callback = function(state)
                    getgenv().Ticks = state
                end
            }
            )
            

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Wall Check",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.WallCheck = state
                end
            }
            )

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Knock Check",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.KnockedCheck = state
                end
            }
            )

            Sections.Rage.Main.TargetAim:Toggle(
                {
                Name = "Closest Part",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.ClosestBodyPart = state
                end
            }
            )

            local FovColor = Sections.Rage.Main.Visuals:Toggle(
                {
                Name = "Draw FOV",
                Default = false,
                Pointer = "3",
                callback = function(state)
                    ragebotsettings.fov = state
                end
            }
            )
            
            
            FovColor:Colorpicker(
                {
                Name = "Color",
                Info = "Info Here",
                Alpha = 0,
                Default = Color3.fromRGB(133, 87, 242),
                Pointer = "Yes",
                callback = function(shit)
                    fov.Color = shit
                end
            }
            )

            Sections.Rage.Main.Visuals:Slider(
        {
        Name = "FOV Size",
        Minimum = 1,
        Maximum = 180,
        Default = 30,
        Decimals = 1,
        Pointer = "Yasss",
        callback = function(state)
            ragebotsettings.radius = state*3
        end
    }
    )

    
    local TracerColor = Sections.Rage.Main.Visuals:Toggle(
        {
        Name = "Tracer",
        Default = false,
        Pointer = "3",
        callback = function(state)
            ragebotsettings.Tracer = state
        end
    }
    )
    
    
    TracerColor:Colorpicker(
        {
        Name = "Color",
        Info = "Info Here",
        Alpha = 0,
        Default = Color3.fromRGB(133, 87, 242),
        Pointer = "Yes",
        callback = function(shit)
            Line.Color = shit
        end
    }
    )

    Sections.Rage.Main.Visuals:Toggle(
        {
        Name = "Health Indicator",
        Default = false,
        Pointer = "3",
        callback = function(state)
            ragebotsettings.HealthIndicator = state
        end
    }
    )

    Sections.Rage.Main.Visuals:Toggle(
        {
        Name = "Ammo Indicator",
        Default = false,
        Pointer = "3",
        callback = function(state)
            ragebotsettings.AmmoIndicator = state
        end
    }
    )

    local ChamsColor = Sections.Rage.Main.Visuals:Toggle(
        {
        Name = "Chams",
        Default = false,
        Pointer = "3",
        callback = function(state)
            ragebotsettings.Chams = state
        end
    }
    )

    ChamsColor:Colorpicker(
        {
        Name = "Color",
        Info = "Info Here",
        Alpha = 0,
        Default = Color3.fromRGB(133, 87, 242),
        Pointer = "Yes",
        callback = function(shit)
            Chams.FillColor = shit
        end
    }
    )

    local GlowColor = Sections.Rage.Main.Visuals:Toggle(
        {
        Name = "Glow",
        Default = false,
        Pointer = "3",
        callback = function(state)
            ragebotsettings.Glow = state
        end
    }
    )

    GlowColor:Colorpicker(
        {
        Name = "Color",
        Info = "Info Here",
        Alpha = 0,
        Default = Color3.fromRGB(255,255,255),
        Pointer = "Yes",
        callback = function(shit)
            Chams.OutlineColor = shit
        end
    }
    )
    
    Sections.Rage.Main.AntiAim:Slider(
        {
        Name = "tick-time",
        Minimum = 1,
        Maximum = 16,
        Default = 1,
        Decimals = 1,
        Pointer = "Yasss",
        callback = function(state)
            ragebotsettings.TickTime = state/100
        end
    }
    )

    Sections.Rage.Main.AntiAim:Dropdown(
                {
                Name = "Type",
                Options = {"Normal", "Fluctuate"},
                Default = "Normal",
                Pointer = "38",
                callback = function(state)
                    ragebotsettings.FakeLagType = state
                end
            }
            )



    
local maintable = {
    visuals = {
        box = {
            masterswitch = false,
            enabled = false,
            outline = false,
            healthbar = false,
            color1 = Color3.fromRGB(255, 255, 255),
            color2 = Color3.fromRGB(0, 0, 0),
            healthbarcolor = Color3.fromRGB(255, 255, 255),
            filled = false,
            filledtrans = 0,
            self = false,
            rainbow = false,
        },
                tracer = {
                    enabled = false,
                    unlocktracers = false,
                    color = Color3.fromRGB(255, 255, 255)
                },
                name = {
                    enabled = false,
                    outline = false,
                    size = 13,
                    font = 2,
                    color = Color3.fromRGB(255, 255, 255)
                },
                misc = {
                    teamcheck = false,
                    useteamcolors = false,
                    visibleonly = true
                },
                Toolsshow = {
                    enable = false,
                    outline = false,
                    size = 8,
                    font = 2,
                    color = Color3.fromRGB(255, 255, 255)
    },
},
}

    local lplr = game.Players.LocalPlayer
    local camera = game:GetService("Workspace").CurrentCamera
    local CurrentCamera = workspace.CurrentCamera
    local worldToViewportPoint = CurrentCamera.worldToViewportPoint
    local mouse = game.Players.LocalPlayer:GetMouse()
    local UserInput = game:GetService("UserInputService")
    
    assert(maintable.visuals, "Loaded LLOLL")

function esp(v)
-- box --
local BLOCKOUTLINE = Drawnew("Square")
BLOCKOUTLINE.Visible = false
BLOCKOUTLINE.Color = Color3.new(0,0,0)
BLOCKOUTLINE.Thickness = 3
BLOCKOUTLINE.Transparency = 1

local BOXPLAYER = Drawnew("Square")
BOXPLAYER.Visible = false
BOXPLAYER.Color = maintable.visuals.box.color1
BOXPLAYER.Thickness = 1
BOXPLAYER.Transparency = 1
BOXPLAYER.Filled = maintable.visuals.box.filled

local HealthBarOUTLINE = Drawnew("Square")
HealthBarOUTLINE.Thickness = 2
HealthBarOUTLINE.Filled = false
HealthBarOUTLINE.Color = Color3.new(0,0,0)
HealthBarOUTLINE.Transparency = 1
HealthBarOUTLINE.Visible = false

local HealthBarITSELF = Drawnew("Square")
HealthBarITSELF.Thickness = 1
HealthBarITSELF.Filled = false
HealthBarITSELF.Transparency = 1
HealthBarITSELF.Visible = false



-- tracer --

local Tracer = Drawnew("Line")
Tracer.Visible = false
Tracer.Color = Color3.new(1,1,1)
Tracer.Thickness = 1
Tracer.Transparency = 1

-- name --

local Name = Drawnew("Text")
Name.Transparency = 1
Name.Visible = false
Name.Color = maintable.visuals.name.color
Name.Size = 16
Name.Center = true
Name.Outline = false
Name.Font = 2
Name.Text = "name [100/100]"


--tool--

local toolshow = Drawnew("Text")
toolshow.Transparency = 1
toolshow.Visible = false
toolshow.Color = maintable.visuals.Toolsshow.color
toolshow.Size = 16
toolshow.Center = true
toolshow.Outline = false
toolshow.Font = 2
toolshow.Text = ""















runserv.RenderStepped:Connect(function()
    if maintable.visuals.box.self == true then
    if v.Character ~= nil and v.Character:FindFirstChild("Humanoid") ~= nil and v.Character:FindFirstChild("HumanoidRootPart") ~= nil and v.Character.Humanoid.Health  > 0 and (not maintable.visuals.misc.teamcheck or v.TeamColor == lplr.TeamColor) then
        local Vector, onScreen = camera:worldToViewportPoint(v.Character.HumanoidRootPart.Position)
        local Distance = (CurrentCamera.CFrame.p - v.Character.HumanoidRootPart.Position).Magnitude
        local RootPart = v.Character.HumanoidRootPart
        local Head = v.Character.Head
        local RootPosition, RootVis = worldToViewportPoint(CurrentCamera, RootPart.Position)
        local HeadPosition = worldToViewportPoint(CurrentCamera, Head.Position + V3new(0,0.5,0))
        local LegPosition = worldToViewportPoint(CurrentCamera, RootPart.Position - V3new(0,3,0))
        if (not maintable.visuals.misc.visibleonly or onScreen) then
            if maintable.visuals.box.masterswitch then
                BLOCKOUTLINE.Size = V2new(2500 / RootPosition.Z, HeadPosition.Y - LegPosition.Y)
                BLOCKOUTLINE.Position = V2new(RootPosition.X - BLOCKOUTLINE.Size.X / 2, RootPosition.Y - BLOCKOUTLINE.Size.Y / 2)
                BLOCKOUTLINE.Visible = maintable.visuals.box.outline
                BLOCKOUTLINE.Color = Color3.fromRGB(0, 0, 0)
                BLOCKOUTLINE.Filled = false
                

                BOXPLAYER.Size = V2new(2490 / RootPosition.Z, HeadPosition.Y - LegPosition.Y)
                BOXPLAYER.Position = V2new(RootPosition.X - BOXPLAYER.Size.X / 2, RootPosition.Y - BOXPLAYER.Size.Y / 2)
                BOXPLAYER.Visible = maintable.visuals.box.enabled
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    BOXPLAYER.Color = maintable.visuals.box.color1
                else
                    BOXPLAYER.Color = maintable.visuals.box.color1
                end
                BOXPLAYER.Filled = maintable.visuals.box.filled
                BOXPLAYER.Transparency = 1
                    
                HealthBarOUTLINE.Size = V2new(2, (HeadPosition.Y - LegPosition.Y) / (v.Character.Humanoid.MaxHealth / math.clamp(v.Character.Humanoid.Health, 0,v.Character.Humanoid.MaxHealth)))
                HealthBarOUTLINE.Position = V2new(BOXPLAYER.Position.X - 8, BOXPLAYER.Position.Y + (1 / HealthBarOUTLINE.Size.Y))
                HealthBarOUTLINE.Color = Color3.fromRGB(0, 0, 0)
                HealthBarOUTLINE.Visible = maintable.visuals.box.healthbaroutline

                HealthBarITSELF.Size = V2new(2, (HeadPosition.Y - LegPosition.Y) / (v.Character.Humanoid.MaxHealth / math.clamp(v.Character.Humanoid.Health, 0,v.Character.Humanoid.MaxHealth)))
                HealthBarITSELF.Position = V2new(BOXPLAYER.Position.X - 8, BOXPLAYER.Position.Y + (1 / HealthBarITSELF.Size.Y))
                HealthBarITSELF.Color = maintable.visuals.box.healthbarcolor
                HealthBarITSELF.Visible = maintable.visuals.box.healthbar
                HealthBarITSELF.Filled = true
                
                
                
                
            else
                BLOCKOUTLINE.Visible = false
                BOXPLAYER.Visible = false
                HealthBarOUTLINE.Visible = false
                HealthBarITSELF.Visible = false
            end
            if maintable.visuals.tracer.enabled then
                if maintable.visuals.tracer.unlocktracers then
                    Tracer.From = V2new(mouse.X, mouse.Y + 36)
                else
                    Tracer.From = V2new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 1)
                end
                Tracer.To = V2new(Vector.X, Vector.Y)
                Tracer.Visible = maintable.visuals.tracer.enabled
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    Tracer.Color = maintable.visuals.tracer.color
                else
                    Tracer.Color = maintable.visuals.tracer.color
                end
            else
                Tracer.Visible = false
            end

            if maintable.visuals.Toolsshow.enable then
                  local Equipped = v.Character:FindFirstChildOfClass("Tool") and v.Character:FindFirstChildOfClass("Tool").Name or "None"
                toolshow.Text = Equipped
                toolshow.Position = V2new(workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).X, workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).Y + 30)
                toolshow.Color = maintable.visuals.Toolsshow.color
                toolshow.Font = 3
                Name.Size = maintable.visuals.Toolsshow.size
                toolshow.Visible = true
                else
                    toolshow.Visible = false
            end


            if maintable.visuals.name.enabled then
                Name.Text = tostring(v.Name).. " [" .. tostring(math.floor(v.Character.Humanoid.Health + 0.5)) .. "/" .. tostring(v.Character.Humanoid.MaxHealth) .. "]"
                Name.Position = V2new(workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).X, workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).Y - 30)
                Name.Visible = true
                Name.Size = maintable.visuals.name.size
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    Name.Color = maintable.visuals.name.color
                else
                    Name.Color = maintable.visuals.name.color
                end
                Name.Outline = maintable.visuals.name.outline
            else
                Name.Visible = false
            end
        else
            BLOCKOUTLINE.Visible = false
            BOXPLAYER.Visible = false
            toolshow.Visible=false
            HealthBarOUTLINE.Visible = false
            HealthBarITSELF.Visible = false
            Tracer.Visible = false
            Name.Visible = false
        end
    else
        toolshow.Visible=false
        BLOCKOUTLINE.Visible = false
        BOXPLAYER.Visible = false
        HealthBarOUTLINE.Visible = false
        HealthBarITSELF.Visible = false
        Tracer.Visible = false
        Name.Visible = false
    end
else
    if v.Character ~= nil and v.Character:FindFirstChild("Humanoid") ~= nil and v.Character:FindFirstChild("HumanoidRootPart") ~= nil and v ~= lplr and v.Character.Humanoid.Health  > 0 and (not maintable.visuals.misc.teamcheck or v.TeamColor == lplr.TeamColor) then
        local Vector, onScreen = camera:worldToViewportPoint(v.Character.HumanoidRootPart.Position)
        local Distance = (CurrentCamera.CFrame.p - v.Character.HumanoidRootPart.Position).Magnitude
        local RootPart = v.Character.HumanoidRootPart
        local Head = v.Character.Head
        local RootPosition, RootVis = worldToViewportPoint(CurrentCamera, RootPart.Position)
        local HeadPosition = worldToViewportPoint(CurrentCamera, Head.Position + V3new(0,0.5,0))
        local LegPosition = worldToViewportPoint(CurrentCamera, RootPart.Position - V3new(0,3,0))
        if (not maintable.visuals.misc.visibleonly or onScreen) then
            if maintable.visuals.box.masterswitch then
                BLOCKOUTLINE.Size = V2new(2500 / RootPosition.Z, HeadPosition.Y - LegPosition.Y)
                BLOCKOUTLINE.Position = V2new(RootPosition.X - BLOCKOUTLINE.Size.X / 2, RootPosition.Y - BLOCKOUTLINE.Size.Y / 2)
                BLOCKOUTLINE.Visible = maintable.visuals.box.outline
                BLOCKOUTLINE.Color = Color3.fromRGB(0, 0, 0)
                BLOCKOUTLINE.Filled = false

                BOXPLAYER.Size = V2new(2490 / RootPosition.Z, HeadPosition.Y - LegPosition.Y)
                BOXPLAYER.Position = V2new(RootPosition.X - BOXPLAYER.Size.X / 2, RootPosition.Y - BOXPLAYER.Size.Y / 2)
                BOXPLAYER.Visible = maintable.visuals.box.enabled
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    BOXPLAYER.Color = maintable.visuals.box.color1
                else
                    BOXPLAYER.Color = maintable.visuals.box.color1
                end
                BOXPLAYER.Filled = maintable.visuals.box.filled
                BOXPLAYER.Transparency = 1
                    
                HealthBarOUTLINE.Size = V2new(2, (HeadPosition.Y - LegPosition.Y) / (v.Character.Humanoid.MaxHealth / math.clamp(v.Character.Humanoid.Health, 0,v.Character.Humanoid.MaxHealth)))
                HealthBarOUTLINE.Position = V2new(BOXPLAYER.Position.X - 8, BOXPLAYER.Position.Y + (1 / HealthBarOUTLINE.Size.Y))
                HealthBarOUTLINE.Color = Color3.fromRGB(0, 0, 0)
                HealthBarOUTLINE.Visible = maintable.visuals.box.healthbaroutline

                HealthBarITSELF.Size = V2new(2, (HeadPosition.Y - LegPosition.Y) / (v.Character.Humanoid.MaxHealth / math.clamp(v.Character.Humanoid.Health, 0,v.Character.Humanoid.MaxHealth)))
                HealthBarITSELF.Position = V2new(BOXPLAYER.Position.X - 8, BOXPLAYER.Position.Y + (1 / HealthBarITSELF.Size.Y))
                HealthBarITSELF.Color = maintable.visuals.box.healthbarcolor
                HealthBarITSELF.Visible = maintable.visuals.box.healthbar
                HealthBarITSELF.Filled = true
                
                
                
                
            else
                BLOCKOUTLINE.Visible = false
                BOXPLAYER.Visible = false
                HealthBarOUTLINE.Visible = false
                HealthBarITSELF.Visible = false
            end
            if maintable.visuals.tracer.enabled then
                if maintable.visuals.tracer.unlocktracers then
                    Tracer.From = V2new(mouse.X, mouse.Y + 36)
                else
                    Tracer.From = V2new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 1)
                end
                Tracer.To = V2new(Vector.X, Vector.Y)
                Tracer.Visible = maintable.visuals.tracer.enabled
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    Tracer.Color = maintable.visuals.tracer.color
                else
                    Tracer.Color = maintable.visuals.tracer.color
                end
            else
                Tracer.Visible = false
            end

            if maintable.visuals.Toolsshow.enable then
                  local Equipped = v.Character:FindFirstChildOfClass("Tool") and v.Character:FindFirstChildOfClass("Tool").Name or "None"
                toolshow.Text = Equipped
                toolshow.Position = V2new(workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).X, workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).Y + 30)
                toolshow.Color = maintable.visuals.Toolsshow.color
                toolshow.Font = 3
                Name.Size = maintable.visuals.Toolsshow.size
                toolshow.Visible = true
                else
                    toolshow.Visible = false
            end


            if maintable.visuals.name.enabled then
                Name.Text = tostring(v.Name).. " [" .. tostring(math.floor(v.Character.Humanoid.Health + 0.5)) .. "/" .. tostring(v.Character.Humanoid.MaxHealth) .. "]"
                Name.Position = V2new(workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).X, workspace.Camera:WorldToViewportPoint(v.Character.Head.Position).Y - 30)
                Name.Visible = true
                Name.Size = maintable.visuals.name.size
                if not maintable.visuals.misc.useteamcolors then
                    local color = v.TeamColor
                    Name.Color = maintable.visuals.name.color
                else
                    Name.Color = maintable.visuals.name.color
                end
                Name.Outline = maintable.visuals.name.outline
            else
                Name.Visible = false
            end
        else
            BLOCKOUTLINE.Visible = false
            BOXPLAYER.Visible = false
            toolshow.Visible=false
            HealthBarOUTLINE.Visible = false
            HealthBarITSELF.Visible = false
            Tracer.Visible = false
            Name.Visible = false
        end
    else
        toolshow.Visible=false
        BLOCKOUTLINE.Visible = false
        BOXPLAYER.Visible = false
        HealthBarOUTLINE.Visible = false
        HealthBarITSELF.Visible = false
        Tracer.Visible = false
        Name.Visible = false
    end
end
end)
end

for i,v in pairs(game.Players:GetChildren()) do
esp(v)
end

game.Players.PlayerAdded:Connect(function(v)
esp(v)
end)

Sections.Visuals.Main.PlayerESP:Keybind(
    {
    Name = "MasterSwitch",
    Callback = function(key) end,
    Default = key,
    Pointer = "1",
    Mode = "Toggle",
    toggleflag = "1.1",
    togglecallback = function(state)
        maintable.visuals.box.masterswitch = state
    end
}
)

Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "Self",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.box.self = state
    end
}
)

local BoxColor = Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "Box",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.box.enabled = state
        maintable.visuals.box.outline = state
    end
}
)


BoxColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        maintable.visuals.box.color1 = shit
    end
}
)

local HealthBarColor = Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "HealthBar",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.box.healthbar = state
        maintable.visuals.box.healthbaroutline = state
    end
}
)

HealthBarColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        maintable.visuals.box.healthbarcolor = shit
    end
}
)

local NameColor = Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "Name",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.name.enabled = state
        maintable.visuals.name.outline = state
    end
}
)

NameColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        maintable.visuals.name.color = shit
    end
}
)

local TracerColor = Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "Tracer",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.tracer.enabled = state
    end
}
)

TracerColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        maintable.visuals.tracer.color = shit
    end
}
)

local ToolColor = Sections.Visuals.Main.PlayerESP:Toggle(
    {
    Name = "Tool",
    Default = false,
    Pointer = "3",
    callback = function(state)
        maintable.visuals.Toolsshow.enable = state
        maintable.visuals.Toolsshow.outline = state
    end
}
)

ToolColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        maintable.visuals.Toolsshow.color = shit
    end
}
)




Sections.Visuals.Main.World:Toggle(
    {
    Name = "Shadows",
    Default = true,
    Pointer = "3",
    callback = function(state)
        game.Lighting.GlobalShadows = state
    end
}
)

Sections.Visuals.Main.World:Slider(
    {
    Name = "Exposure",
    Minimum = 0,
    Maximum = 1,
    Default = 0,
    Decimals = 0.1,
    Pointer = "Yasss",
    callback = function(state)
        game.Lighting.ExposureCompensation = state
    end
}
)


Sections.Visuals.Main.World:Colorpicker(
    {
    Name = "Fog",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(100,87,72),
    Pointer = "Yes",
    callback = function(shit)
        game.Lighting.FogColor = shit
    end
}
)

Sections.Visuals.Main.World:Toggle(
    {
    Name = "No Fog",
    Default = false,
    Pointer = "3",
    callback = function(state)
        if state == true then
            game.Lighting.FogEnd = 100000000000000000000
            game.Lighting.FogStart = 100000000000000000000
        else
            game.Lighting.FogEnd = 500
            game.Lighting.FogStart = 0
        end
    end
}
)

Sections.Visuals.Main.World:Slider(
    {
    Name = "Brightness",
    Minimum = 0,
    Maximum = 10,
    Default = 2,
    Decimals = 0.5,
    Pointer = "Yasss",
    callback = function(state)
        game.Lighting.Brightness = state
    end
}
)

Sections.Visuals.Main.World:Colorpicker(
    {
    Name = "Ambient",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(1,1,1),
    Pointer = "Yes",
    callback = function(shit)
        game.Lighting.Ambient = shit
    end
}
)

local MaterialColor = Sections.Visuals.Main.Local:Toggle(
    {
    Name = "Local Chams",
    Default = false,
    Pointer = "3",
    callback = function(state)
        getgenv().SelfChams = state
end
}
)

MaterialColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = game.Players.LocalPlayer.Character.Head.Color,
    Pointer = "Yes",
    callback = function(shit)
        getgenv().SelfColor = shit
    end
}
)

local GunColor = Sections.Visuals.Main.Local:Toggle(
    {
    Name = "Gun Chams",
    Default = false,
    Pointer = "3",
    callback = function(state)
        getgenv().GunChams = state
end
}
)

GunColor:Colorpicker(
    {
    Name = "Color",
    Info = "Info Here",
    Alpha = 0,
    Default = Color3.fromRGB(255,255,255),
    Pointer = "Yes",
    callback = function(shit)
        getgenv().GunColor = shit
    end
}
)

getgenv().Speed = 0.1

Sections.Misc.Main.Movement:Keybind(
    {
    Name = "Enabled",
    Callback = function(key) end,
    Default = false,
    Pointer = "1",
    Mode = "Toggle",
    toggleflag = "1.1",
    togglecallback = function(state)
        ragebotsettings.Walk = state
    end
}
)

Sections.Misc.Main.Movement:Slider(
    {
    Name = "Speed",
    Minimum = 0,
    Maximum = 99,
    Default = 0,
    Decimals = 0.5,
    Pointer = "Yasss",
    callback = function(state)
        ragebotsettings.WalkSpeed = state/10
    end
}
)

runserv.Heartbeat:connect(function()   
    if ragebotsettings.Walk == true then
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame + game.Players.LocalPlayer.Character.Humanoid.MoveDirection * ragebotsettings.WalkSpeed
        runserv.Stepped:wait()
        task.wait(1)
    if game.Players.LocalPlayer.Character.Humanoid.Health < 5 then
        for _, v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if v:IsA("Script") and v.Name ~= "Health" and v.Name ~= "Sound" and v:FindFirstChild("LocalScript") then
                v:Destroy()
            end
        end
        game.Players.LocalPlayer.CharacterAdded:Connect(function(char)
            char.ChildAdded:Connect(function(child)
                if child:IsA("Script") then 
                    task.wait(0.1)
                    if child:FindFirstChild("LocalScript") then
                        child.LocalScript:FireServer()
                    end
                end
            end)
        end)

    end
    end
end)

for _, v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
    if v:IsA("Script") and v.Name ~= "Health" and v.Name ~= "Sound" and v:FindFirstChild("LocalScript") then
        v:Destroy()
    end
end


game.Players.LocalPlayer.CharacterAdded:Connect(function(char)
    repeat
        wait()
    until game.Players.LocalPlayer.Character
    char.ChildAdded:Connect(function(child)
        if child:IsA("Script") then 
            wait(0.1)
            if child:FindFirstChild("LocalScript") then
                child.LocalScript:FireServer()
            end
        end
    end)
end)

Sections.Misc.Main.MainMisc:Toggle(
    {
    Name = "ChatSpam",
    Default = false,
    Pointer = "3",
    callback = function(state)
        ragebotsettings.ChatSpam = state
end
}
)


Sections.Misc.Main.MainMisc:Dropdown(
                {
                Name = "Type",
                Options = {"Revision", "avon.hook", "FUCK OFF"},
                Default = "avon.hook",
                Pointer = "38",
                callback = function(state)
                    ragebotsettings.ChatSpamType = state
                end
            }
            )

            Sections.Misc.Main.MainMisc:Slider(
                {
                Name = "Delay",
                Minimum = 0,
                Maximum = 5,
                Default = 2,
                Decimals = 0.5,
                Pointer = "Yasss",
                callback = function(state)
                    ragebotsettings.ChatSpamDelay = state
                end
            }
            )   


            Sections.Misc.Main.MainMisc:Toggle(
    {
    Name = "Fake Aim Viewer",
    Default = false,
    Pointer = "3",
    callback = function(state)
        getgenv().FakeAimViewer = state
end
}
)

            Sections.Misc.Main.MainMisc:Toggle(
    {
    Name = "Auto Reload",
    Default = false,
    Pointer = "3",
    callback = function(state)
        ragebotsettings.AutoReload = state
end
}
)

Sections.Misc.Main.MainMisc:Toggle(
    {
    Name = "Anti Stomp",
    Default = false,
    Pointer = "3",
    callback = function(state)
        getgenv().AntiStomp = state
end
}
)



local watermark = library:Watermark {}

task.spawn(function()
    local frames = 0

    runserv.RenderStepped:Connect(function()
        frames = frames + 1
    end)

    watermark:Update { getgenv().ScriptName, "User", frames .. " FPS", string.format("%s:%s %s", tonumber(os.date("%I")), os.date("%M"), os.date("%p")) }

    while task.wait(1) do
        watermark:Update { getgenv().ScriptName, "User", frames .. " FPS", string.format("%s:%s %s", tonumber(os.date("%I")), os.date("%M"), os.date("%p")) }
        frames = 0
    end
end)

watermark:Toggle()

UiToggle = Sections.Settings.Main.UI:Keybind { Name = "Keybind", Default = Enum.KeyCode.RightShift, Blacklist = { Enum.UserInputType.MouseButton1 }, Flag = "CurrentBind", Callback = function(key, fromsetting)
    if not fromsetting then

        library:Toggle()
    end
end }

WatermarkToggle = Sections.Settings.Main.UI:Toggle(
    {
    Name = "Watermark",
    Default = false,
    Pointer = "3",
    callback = function(state)
        watermark:Toggle()
    end
}
)

Sections.Settings.Main.UI:Box { Name = "Watermark Text", Callback = function(text)
    getgenv().ScriptName = text
end }

local currentconfig = ""
local configname = ""

ConfigStuff = {


    configdropdown = Sections.Settings.Main.Configuration:Dropdown { Name = "Main", Options = Library:ListConfigs(), Callback = function(option)
        currentconfig = option
    end },



    Sections.Settings.Main.Configuration:Box { Name = "Config Name", Callback = function(text)
        configname = text
    end },

    Sections.Settings.Main.Configuration:Button { Name = "Save", Callback = function()
        Library:SaveConfig(configname)
        ConfigStuff.configdropdown:Refresh(Library:ListConfigs())
    end },

    Sections.Settings.Main.Configuration:Button { Name = "Load", Callback = function()
        Library:LoadConfig(currentconfig)
    end },
    Sections.Settings.Main.Configuration:Button { Name = "Delete", Callback = function()
        Library:DeleteConfig(currentconfig)
        ConfigStuff.configdropdown:Refresh(Library:ListConfigs())
    end }


}


        Library:ChangeAccent(Color3.fromRGB(133, 87, 242))
Library:ChangeOutline { Color3.fromRGB(133, 87, 242), Color3.fromRGB(133, 87, 242) }

Library:Initialize()


local avonhook = {
    [1] = "avon.hook on top👿",
    [2] = "i play nerf gun games🔫",
    [3] = "🇼 🇦 🇱 🇰 🇦 🇧 🇱 🇪   🇩 🇪 🇸 🇾 🇳 🇨",
}

local fuckoff = {
    [1] = "BotlagLOL",
    [2] = "SHUT UPP😡",
    [3] = "FAT NEEK🤣🤣",
}

local revision = {
    [1] = "Revision > You🤣",
    [2] = "I adopted you with Revision👼",
    [3] = "1 Dahoodian down🥱",
}

while task.wait(ragebotsettings.ChatSpamDelay) do
if ragebotsettings.ChatSpam then
if ragebotsettings.ChatSpamType == "avon.hook" then
local random = math.random(1,3)

local A_1 = avonhook[random]
local A_2 = "All"
local Event = game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest
Event:FireServer(A_1, A_2)
elseif ragebotsettings.ChatSpamType == "FUCK OFF" then
    local random = math.random(1,3)

    local A_1 = fuckoff[random]
    local A_2 = "All"
    local Event = game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest
    Event:FireServer(A_1, A_2)
elseif ragebotsettings.ChatSpamType == "Revision" then
    local random = math.random(1,3)

    local A_1 = revision[random]
    local A_2 = "All"
    local Event = game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest
    Event:FireServer(A_1, A_2)
        end
    end
end