--[[
  ColorPicker v1.0 für grandMA2
  -----------------------------------------------------------------
  Baut aus vorhandenen Groups und Color-Presets (Typ 4) ein
  Color-Picker-Raster: Für jede Group und jede Farbe wird ein Macro
  angelegt, das „Group X At Preset 4.Y“ ausführt. Die Macros werden
  benannt, passend eingefärbt und in einem Layout als Raster
  angeordnet (Zeilen = Groups, Spalten = Farben).

  Voraussetzungen:
    - Groups sind angelegt (z. B. 1–6)
    - Color-Presets sind angelegt (z. B. 4.1–4.10)
    - der Macro-Bereich ab der Startnummer ist frei
      (sonst fragt das Plugin, ob überschrieben werden soll)

  Getestet als Ablauf im Simulator. Vor dem Einsatz in einer
  echten Show bitte auf einer Test-Show (onPC) ausprobieren.
]]

local NAME = "ColorPicker v1.0"

-- ===== Standardwerte (werden beim Start abgefragt) ==============
local CFG = {
  groups  = "1-6",   -- Groups, z. B. "1-6" oder "1,3,5,7"
  presets = "1-10",  -- Color-Presets 4.x, z. B. "1-10" = 4.1 bis 4.10
  macro   = 1001,    -- erstes Macro
  layout  = 101,     -- Layout-Nummer (0 = kein Layout anlegen)
  allRow  = "ja",    -- zusätzliche Zeile „Alle“ (alle Groups zusammen)
}

-- ===== Farbnamen -> Macro-Farbe (Rot, Grün, Blau in Prozent) ====
-- Wird über den Namen des Presets erkannt (deutsch oder englisch).
-- Lange Namen zuerst, damit z. B. „Hellblau“ nicht als „Blau“ gilt.
local COLORS = {
  {"warmweiss", 100, 85, 60}, {"warm white", 100, 85, 60},
  {"kaltweiss", 85, 92, 100}, {"cold white", 85, 92, 100},
  {"hellblau", 40, 75, 100},  {"light blue", 40, 75, 100},
  {"dunkelblau", 0, 0, 60},   {"dark blue", 0, 0, 60},
  {"congo", 25, 0, 70},       {"lavendel", 70, 55, 100}, {"lavender", 70, 55, 100},
  {"magenta", 100, 0, 100},   {"pink", 100, 40, 70},      {"rosa", 100, 60, 75},
  {"violett", 55, 0, 100},    {"lila", 55, 0, 100},       {"purple", 55, 0, 100}, {"uv", 40, 0, 100},
  {"tuerkis", 0, 90, 80},     {"turquoise", 0, 90, 80},
  {"cyan", 0, 100, 100},      {"amber", 100, 65, 0},      {"orange", 100, 50, 0},
  {"gelb", 100, 100, 0},      {"yellow", 100, 100, 0},    {"lime", 70, 100, 0},
  {"gruen", 0, 100, 0},       {"green", 0, 100, 0},
  {"blau", 0, 20, 100},       {"blue", 0, 20, 100},
  {"rot", 100, 0, 0},         {"red", 100, 0, 0},
  {"weiss", 100, 100, 100},   {"white", 100, 100, 100},
  {"cto", 100, 75, 45},       {"ctb", 75, 85, 100},
}

-- ===== Hilfsfunktionen ==========================================
local function echo(text)
  gma.echo(NAME .. ": " .. text)
end

-- "1-6, 8, 10-12" -> {1,2,3,4,5,6,8,10,11,12}
local function parseList(text)
  local list = {}
  for part in string.gmatch(text or "", "[^,]+") do
    local a, b = string.match(part, "^%s*(%d+)%s*%-%s*(%d+)%s*$")
    if a then
      a, b = tonumber(a), tonumber(b)
      local step = (a <= b) and 1 or -1
      for i = a, b, step do table.insert(list, i) end
    else
      local n = string.match(part, "^%s*(%d+)%s*$")
      if n then table.insert(list, tonumber(n)) end
    end
  end
  return list
end

local function exists(object)
  return gma.show.getobj.handle(object) ~= nil
end

local function nameOf(object, fallback)
  local h = gma.show.getobj.handle(object)
  local label = h and gma.show.getobj.label(h)
  if label == nil or label == "" then label = fallback end
  return (string.gsub(label, '"', ""))   -- Anführungszeichen entfernen
end

local function colorFor(name)
  local low = string.lower(name)
  -- Umlaute vereinheitlichen, damit „Weiß“ und „Weiss“ gleich erkannt werden
  low = string.gsub(low, "ß", "ss")
  low = string.gsub(low, "ü", "ue")
  low = string.gsub(low, "Ü", "ue")
  low = string.gsub(low, "ö", "oe")
  low = string.gsub(low, "ä", "ae")
  for _, c in ipairs(COLORS) do
    if string.find(low, c[1], 1, true) then return c[2], c[3], c[4] end
  end
  return nil
end

-- Fragt einen Wert ab. Gibt nil zurück, wenn abgebrochen wurde.
local function ask(title, default)
  local answer = gma.textinput(title, tostring(default))
  if answer == nil then return nil end
  answer = string.match(answer, "^%s*(.-)%s*$")
  if answer == "" then return tostring(default) end
  return answer
end

-- ===== Hauptprogramm ============================================
local function Start()
  -- 1. Einstellungen abfragen
  local gText = ask("ColorPicker: Groups (z. B. 1-6)", CFG.groups);        if not gText then return end
  local pText = ask("ColorPicker: Color-Presets 4.x (z. B. 1-10)", CFG.presets); if not pText then return end
  local mText = ask("ColorPicker: erstes Macro", CFG.macro);              if not mText then return end
  local lText = ask("ColorPicker: Layout-Nummer (0 = keins)", CFG.layout); if not lText then return end
  local aText = ask("ColorPicker: Zeile 'Alle' anlegen? (ja/nein)", CFG.allRow); if not aText then return end

  local firstMacro = tonumber(mText)
  local layout = tonumber(lText) or 0
  local allRow = string.lower(aText) == "ja" or string.lower(aText) == "j" or string.lower(aText) == "yes"
  if not firstMacro or firstMacro < 1 then
    gma.gui.msgbox(NAME, "Ungültige Macro-Nummer: " .. mText)
    return
  end

  -- 2. Nur vorhandene Groups und Presets verwenden
  local groups, presets = {}, {}
  for _, g in ipairs(parseList(gText)) do
    if exists("Group " .. g) then table.insert(groups, g) else echo("Group " .. g .. " gibt es nicht – übersprungen") end
  end
  for _, p in ipairs(parseList(pText)) do
    if exists("Preset 4." .. p) then table.insert(presets, p) else echo("Preset 4." .. p .. " gibt es nicht – übersprungen") end
  end
  if #groups == 0 or #presets == 0 then
    gma.gui.msgbox(NAME, "Keine passenden Groups oder Color-Presets gefunden.\nBitte zuerst Groups und Presets 4.x anlegen.")
    return
  end

  -- 3. Zeilen festlegen (jede Group + optional „Alle“)
  local rows = {}
  for _, g in ipairs(groups) do
    table.insert(rows, { select = "Group " .. g, label = nameOf("Group " .. g, "G" .. g), group = g })
  end
  if allRow and #groups > 1 then
    local sel = "Group " .. table.concat(groups, " + ")
    table.insert(rows, { select = sel, label = "Alle" })
  end

  local cols = #presets
  local total = #rows * cols
  local lastMacro = firstMacro + total - 1

  -- 4. Prüfen, ob der Macro-Bereich frei ist
  local used = 0
  for m = firstMacro, lastMacro do
    if exists("Macro " .. m) then used = used + 1 end
  end
  if used > 0 then
    local ok = gma.gui.confirm(NAME, used .. " Macros zwischen " .. firstMacro .. " und " .. lastMacro .. " sind belegt.\nÜberschreiben?")
    if not ok then echo("abgebrochen"); return end
    gma.cmd("Delete Macro " .. firstMacro .. " Thru " .. lastMacro .. " /nc")
  end

  -- 5. Layout anlegen
  if layout > 0 then
    if not exists("Layout " .. layout) then
      gma.cmd("Store Layout " .. layout)
    end
    gma.cmd('Label Layout ' .. layout .. ' "ColorPicker"')
  end

  -- 6. Macros bauen
  gma.cmd("ClearAll")
  local count = 0
  for r, row in ipairs(rows) do
    -- Group selbst als Zeilenkopf ins Layout (Spalte 0)
    if layout > 0 and row.group then
      gma.cmd("Assign Group " .. row.group .. " At Layout " .. layout .. " /x=0 /y=" .. (r - 1))
    end
    for c, p in ipairs(presets) do
      local m = firstMacro + (r - 1) * cols + (c - 1)
      local pName = nameOf("Preset 4." .. p, "4." .. p)
      local command = row.select .. " At Preset 4." .. p

      gma.cmd("Store Macro 1." .. m)
      gma.cmd("Store Macro 1." .. m .. ".1")
      gma.cmd('Assign Macro 1.' .. m .. '.1 /cmd="' .. command .. '"')
      gma.cmd('Label Macro ' .. m .. ' "' .. string.sub(row.label, 1, 10) .. " " .. pName .. '"')

      local cr, cg, cb = colorFor(pName)
      if cr then
        gma.cmd(string.format("Appearance Macro %d /r=%d /g=%d /b=%d", m, cr, cg, cb))
      end
      if layout > 0 then
        gma.cmd("Assign Macro " .. m .. " At Layout " .. layout .. " /x=" .. c .. " /y=" .. (r - 1))
      end
      count = count + 1
    end
  end

  -- 7. Fertig
  echo(count .. " Macros erstellt (" .. firstMacro .. " bis " .. lastMacro .. ")")
  if layout > 0 then echo("Layout " .. layout .. " heißt „ColorPicker“") end
  gma.feedback(NAME .. ": " .. count .. " Macros erstellt")
end

local function Cleanup()
end

return Start, Cleanup
