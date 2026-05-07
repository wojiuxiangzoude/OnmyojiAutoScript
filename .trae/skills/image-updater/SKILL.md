---
name: "image-updater"
description: "Updates game image assets and their coordinates in assets.py and JSON config files. Invoke when user provides new images with coordinate parameters in the filename (format: original_name_xY_wZ_hW.png)."
---

# Image Updater Skill

## Purpose
Updates game image assets in the OnmyojiAutoScript project, including:
1. Replacing old image files with new ones
2. Updating coordinate parameters (roi_front, roi_back) in assets.py
3. Updating JSON configuration files (page_img.json, etc.)

## Workflow

### 1. Identify Files to Update
User provides new images with naming convention:
```
original_filename_x{X}_y{Y}_w{W}_h{H}.png
```
Example: `page_demon_encounter_goto_town_x22_y19_w41_h37.png`
This replaces: `page_demon_encounter_goto_town.png`
With coordinates: `(22, 19, 41, 37)`

### 2. File Operations
```bash
# Remove old image
Remove-Item -Force <path>/<original_name>.png

# Rename new image
Rename-Item -Path "<path>/<original_name>_x{X}_y{Y}_w{W}_h{H}.png" -NewName "<original_name>.png"
```

### 3. Update assets.py
Find and replace the RuleImage definition:
```python
# Old:
I_<NAME> = RuleImage(roi_front=(OLD_X,OLD_Y,OLD_W,OLD_H), roi_back=(OLD_X2,OLD_Y2,OLD_W2,OLD_H2), ...)

# New (use exact coordinates from filename):
I_<NAME> = RuleImage(roi_front=(X,Y,W,H), roi_back=(X,Y,W,H), ...)
```

### 4. Update JSON Files
Search in all JSON files under `tasks/GameUi/page/`:
- page_img.json
- page_img_2.json
- page_img_supplement.json
- page_img_3.json

Replace the corresponding entry:
```json
{
  "itemName": "<name>",
  "imageName": "<original_name>.png",
  "roiFront": "X,Y,W,H",
  "roiBack": "X,Y,W,H",
  ...
}
```

### 5. Verify
Run `git status --short` to show all modified files.

## Notes
- Always update both roi_front and roi_back to the same values unless otherwise specified
- JSON format uses string coordinates: "X,Y,W,H"
- Python format uses tuple coordinates: (X,Y,W,H)
- Check which JSON file contains the item before updating
