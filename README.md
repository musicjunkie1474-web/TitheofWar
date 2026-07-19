# Tithe of War (TithesOfWar) - MacroQuest Quest Automation Framework

Complete, production-ready quest automation framework for EverQuest Tithe of War quest with manual pickup/turn-in preserved.

## Quick Start

1. **Place the folder in your MacroQuest lua directory:**
   ```
   <MacroQuest>/lua/TithesOfWar/
   ```

2. **Load the script in-game:**
   ```
   /lua run main
   ```

3. **Open the UI window:**
   ```
   /lua do tow_ui_toggle()
   ```

4. **Use the UI buttons to navigate through quest steps**
   - "Go to Zayenaekk (Plane of Justice)"
   - "Go to Murdunk's Sword (Plane of Earth)"
   - "Go to Murdunk (Korascian Warrens)"
   - "Go to Etsinthinikonarifet (Plane of War)"

## Console Commands

- `/lua do TOW:start()` — Start the quest framework
- `/lua do TOW:stop()` — Stop the quest framework
- `/lua do tow_status()` — Display current status
- `/lua do tow_ui_toggle()` — Toggle ImGui window
- `/lua do tow_nav_step('step_name')` — Navigate to a specific quest step
- `/lua do tow_nav_sword()` — Navigate to Murdunk's Sword (Plane of Earth)

## Project Structure

```
TithesOfWar/
├── main.lua              ← Entry point (run this)
├── config.lua            ← Global configuration
├── logger.lua            ← Logging system (colored output)
├── ui.lua                ← ImGui control window
├── utils.lua             ← Helper functions
├── state.lua             ← Quest state machine
├── navigation.lua        ← Zone travel and pathfinding
├── events.lua            ← Event handling
├── npc.lua               ← NPC interaction helpers
├── inventory.lua         ← Inventory checks
├── and more...
│
├── modules/              ← Reusable utility modules
│   ├── nav.lua           ← Navigation wrapper
│   ├── spawn.lua         ← Spawn utilities
│   ├── path.lua          ← Pathfinding helpers
│   ├── chat.lua          ← Chat utilities
│   ├── parser.lua        ← Interaction sequence parser
│   ├── inventory.lua     ← Inventory wrapper
│   └── group.lua         ← Group utilities
│
└── data/                 ← User-editable quest data
    ├── config.lua        ← Quest settings (loopDelay, arrivalDistance, etc.)
    ├── npcs.lua          ← NPC names and interactions (EDIT THIS)
    ├── zones.lua         ← Zone definitions (EDIT THIS)
    └── items.lua         ← Items to collect (EDIT THIS)
```

## Quest Steps

The Tithe of War quest has 7 steps:

1. **Speak to Zayenaekk (Plane of Justice)**
   - Script will navigate and execute: say "za ye na ekk" → say "ets in thini konar ifet"

2. **Speak to Murdunk (Korascian Warrens)**
   - Script will navigate and execute: hail → say "i seek an audience with zayenaekk"
   - **Note:** Requires Ogre language set and language skill ≥ 90 (manual requirement)

3. **Recover Murdunk's Sword (Plane of Earth A)**
   - Script navigates to ground spawn location: -1999.49, -114.75, 55.81
   - **Manual:** Player picks up the sword

4. **Return Murdunk's Sword (Korascian Warrens)**
   - Script navigates to Murdunk
   - **Manual:** Player turns in the sword to Murdunk

5. **Speak to Zayenaekk (Toskirakk)**
   - Script navigates and executes: say "i seek the blessing of your sister"
   - **Manual:** Right-click and scribe the book received

6. **Create Tribute**
   - **Manual:** Player combines Stonewood Compound Bow + Zekarian Diamond in Fletching Kit
   - (No tradeskill automation; players do this manually)

7. **Turn in Tribute (Plane of War)**
   - Script navigates to Etsinthinikonarifet
   - **Manual:** Player turns in the tribute item

## Configuration

### Edit `data/config.lua` for defaults:
```lua
return {
  loopDelay = 250,          -- ms between script ticks
  arrivalDistance = 10,     -- distance to consider "arrived"
  navigationTimeout = 60,   -- seconds to wait for travel
  auto_pickup = false,      -- do not auto-pickup items
  auto_turnin = false,      -- do not auto-turnin items
}
```

### Edit `data/npcs.lua` to customize NPC names:
```lua
return {
  zayenaekk_poj = {
    name = "Zayenaekk",       -- exact spawn name
    zone = "plane_of_justice",
    coords = { x = -350, y = -400, z = 95 },
    interactions = {
      { type = 'say', text = 'za ye na ekk' },
      { type = 'wait', ms = 1000 },
      { type = 'say', text = 'ets in thini konar ifet' },
    },
  },
  -- ... other NPCs ...
}
```

### Edit `data/zones.lua` to customize zone waypoints:
```lua
return {
  plane_of_justice = {
    name = "Plane of Justice",
    shortname = "plane_of_justice",
    waypoints = {
      start = { x = -280, y = -380, z = 100 },
      zayenaekk = { x = -350, y = -400, z = 95 },
    },
  },
  -- ... other zones ...
}
```

### Edit `data/items.lua` to customize reward items:
```lua
return {
  items = {
    { id = 0, name = "Tithe Token" },
    { id = 0, name = "Blessing Book" },
  }
}
```

## Safety Features

- **All MQ calls guarded:** The script uses `pcall` to safely load MacroQuest libraries; if MQ is unavailable, the script gracefully degrades.
- **Manual pickup/turn-in:** Critical quest items (Murdunk's Sword, tribute) require manual player action; the script will not auto-pickup or auto-turnin by default.
- **No tradeskill automation:** Tribute creation (fletching) is a manual player step; the script will not perform tradeskills.
- **Confirmation dialogs:** UI buttons show a confirmation before navigating to avoid accidental navigation.
- **Error handling:** All interactions, navigation, and rendering are wrapped in error handlers to prevent crashes.

## Troubleshooting

### Script won't load
- Check that MacroQuest `mq` library is available
- Verify the folder is in the correct lua directory
- Check console for errors: `/lua run main` should print "[TOW] starting persistent loop"

### Navigation fails
- Confirm `/travelto` command is available in your MQ setup
- Check that zone shortnames in data/zones.lua match your server's MQ TLO zone names
- Verify waypoint coordinates are correct (use `/lua do print(mq.TLO.Me.X(), mq.TLO.Me.Y(), mq.TLO.Me.Z())`)

### UI doesn't appear
- Make sure ImGui is enabled in MacroQuest
- Try toggling: `/lua do tow_ui_toggle()`
- Check MQ console for "UI render error" messages

### NPC interaction fails
- Confirm exact NPC spawn names match in data/npcs.lua (case-sensitive)
- Use `/target <NPC Name>` in-game to find exact spelling
- Check that language requirements are met (Ogre for Murdunk, Ogre skill ≥ 90)

## Commands Reference

| Command | Effect |
|---------|--------|
| `/lua do TOW:start()` | Start quest |
| `/lua do TOW:stop()` | Stop quest |
| `/lua do tow_status()` | Print status |
| `/lua do tow_reload()` | Reload data files |
| `/lua do tow_ui_toggle()` | Toggle UI window |
| `/lua do tow_nav_sword()` | Go to Murdunk's Sword |
| `/lua do tow_nav_step('zayenaekk_poj')` | Navigate to Zayenaekk (Plane of Justice) |

## Notes

- **Manual steps:** Picking up Murdunk's Sword, turning it in to Murdunk, scribing the book, and creating the tribute are all manual player actions. The script will navigate you to the correct location and pause.
- **Ogre requirement:** Step 2 (Murdunk) requires Ogre language skill ≥ 90. The script cannot auto-adjust language; you must set this before hailing.
- **Zone travel:** Script uses `/travelto <zone>` to move between zones. Ensure your MQ setup supports this command.

## Version

v1.0.0 - Complete Tithe of War quest automation with manual pickup/turn-in

## Support

If you need to modify or debug:
1. Check `data/*.lua` for missing/incorrect NPC names or coordinates
2. Review logger output in MQ console for error messages
3. Test navigation manually: `/lua do tow_nav_step('step_name')`
