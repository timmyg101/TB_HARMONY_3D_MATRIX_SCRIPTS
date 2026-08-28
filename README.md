Toon Boom Harmony 3D Matrix (raw 3D data & camera nulls) to Blender 3D

By Tim Giovanni
8.28.2026

📦 Installation


First install the scripts:

# Blender Add-on Setup
1. Open **Blender**
2. Go to **Edit > Preferences > Add-ons**
3. Click **Install...** button
4. Select the `SCRPT-BLENDER-3D MATRIX IMPORT.py` file
5. Check the box to enable the add-on
6. Click **Save Preferences** (optional)


#Toon Boom Harmony Script

1. Copy and paste TG_3D_MATRIX_EXPORT.js to C:\Program Files (x86)\Toon Boom Animation\Toon Boom Harmony 25.2 Premium\resources\scripts
2. Open Harmony and go to your 'manage scripts' button, in the 'scripting tab'
3. Move over the script inside the script manager to the right using the right arrows and apply the icon provided in the folder (so it looks nice)
4. Apply it and close the script manager




# Scene Setup (IMPORTANT!)

1. Create a TOP_SCENE Peg
   - Create a peg node named `TOP_SCENE`
   - This will be needed to export the pegs in the scene
   - When you make pegs and camera peg, make sure they are connected to the 'TOP_SCENE" Peg (make sure to always use underscore, no spaces)
   - Do not change the name or the script will not work

2. Set Up Your Camera
   - Create a peg named `Camera_M-P` (or any name with "camera" in it) (M is for Master Camera, but I used 'M')
   - Enable 3D on the peg (check the "Enable 3D" box)
   - Set *Position* to **3D Path**
   - Set *Rotation* to **Euler Angles** (NOT Quaternion), very important
   - Parent your actual Camera node to this peg
   - Camera peg is connected to the TOP_SCENE

3. Organize Your Scene
   - Parent ALL pegs (including `Camera_M-P`) to `TOP_SCENE`
   - This ensures everything imports with correct hierarchy

 ⚠️ CRITICAL SETTINGS

*For Camera Pegs:*
- ✅ **Enable 3D**: MUST be checked
- ✅ **3D Path**: Selected (not "Separate")
- ✅ **Euler Angles**: Selected (NOT Quaternion)
- ✅ **Locked Scale**: Unchecked (use "Separate")

*For Regular Pegs:*
- ✅ **Enable 3D**: Checked (if you want 3D movement)
- ✅ *Euler Angles*: Selected for rotation
- ✅ Move the *PEG*, not just the drawing pivot

4. Exporting

1. Select Nodes and camera to Export:
      - In the **Node View**, select:
      - `TOP_SCENE` peg
      - `Camera_M-P` peg
      - Any other pegs you want to export
      - Camera
      - Hold **Shift** to select multiple nodes

2. Run Export Script:
   - Run your the script
   - A window will appear listing all selected objects
   - Rename objects if needed (optional)
   - Click **Export JSON**
   - Save the `.json` file


5. Start Blender

    - Importing

1. Import the JSON:
   - Go to File > Import > Harmony 3D Matrix (.json)
   - Select your exported `.json` file
   - Click 'Import Harmony Scene'

2. **Check the Console:**
   - Look at the System Console (Window > Toggle System Console)
   - You should see:
     ```
     ✓ Created camera rig from peg: Camera_M-P
     ✓ Found TOP_SCENE root
     ✓ Parented Camera_M-P_Rig to TOP_SCENE
     ✅ Import complete
     ```

# Verify the Import

1. **Check the Outliner:**
   - You should see:
     ```
     Collection
     └── TOP_SCENE
         ├── Camera_M-P_Rig
         │   └── Camera_M_M_Cam
         ├── first_peg
         ├── second_peg
         └── etc...
     ```

2. Check Camera Settings: This all should be setup automatically
   - Select the camera
   - In Camera Properties (green camera icon):
     - **Sensor Fit**: Horizontal
     - **Sensor Width**: 36.0 mm
     - **Sensor Height**: 27.0 mm (for 4:3)
     - **Focal Length**: Should be ~27mm (for 41.112° FOV)



3. Test the Animation:
   - Press **Spacebar** to play
   - The camera should move and rotate
   - You can see the null objects and camera



⚠️ IMPORTANT WARNINGS

1. **Always Move Pegs, Not Drawings**
- ❌ **DON'T**: Move the drawing node's pivot point
- ✅ **DO** ADD KEYFRAMES TO EVERYTHING - EVERY PEG SHOULD HAVE A KEYFRAME ON X,Y,Z, and ROTATION 
- The script reads PEG transformations, not drawing positions

2. **Enable 3D on Camera Pegs**
- If "Enable 3D" is NOT checked in Harmony, the camera won't export correctly
- Always verify this before exporting

3. **Use Euler Angles, NOT Quaternion**
-  **DON'T**: Use Quaternion rotation in Harmony
- ✅ **DO**: Use Euler Angles
- Quaternion doesn't export properly to our JSON format

4. **Select Nodes in Node View**
- The export script uses `selection.selectedNode()` to get the full path
- If you don't select nodes properly, they'll all report "Top" as parent
- Always select in the **Node View**, not Timeline

5. **Aspect Ratio Matching**
- Harmony uses **4:3** field ratio (unitsAspectRatio)
- Blender renders at **16:9** (1920x1080)
- The script automatically adjusts the FOV, but verify the camera framing matches

6. **Camera Hierarchy**
- In Harmony: Camera is child of `Camera_M-P` peg
- In Blender: Camera is child of `Camera_M-P_Rig` empty
- The rig inherits all transformations from the peg
- **Don't** animate the camera object directly - animate the rig

7. **Scale/Zoom Behavior**
- Scale in Harmony affects the camera's FOV (zoom)
- The script converts scale to focal length changes
- Test zoom animations to ensure they look correct

8. **Frame Numbers**
- Harmony frames start at **1**
- Blender frames start at **1** (after import)
- Verify timeline range matches (check frame 1 and last frame)



🔧 Troubleshooting

Camera Facing Wrong Direction
- Check camera rotation: Should have **90°** on X-axis (static offset)
- If still wrong, try **-90°** instead

Objects Not Aligned with Background
- Verify FOV matches: Should be ~**67.4°** (or 27mm lens)
- Check sensor settings: **36mm width**, **27mm height**
- Make sure render resolution is *1920x1080* ~ I will test later for 4K

Camera Not Following Peg
- Check parenting in Outliner: `Camera_M-P_Rig` should be child of `TOP_SCENE`
- Verify the rig has animation keyframes (green dots in timeline)
- Check console for parenting errors

All Objects at Origin (0,0,0)
- You moved the drawing, not the peg
- Go back to Harmony, move the actual PEG node
- Re-export

Rotation Values Wrong
- In Harmony, make sure you're using **Euler Angles**
- Check console output to see what values are being exported
- Verify rotation keyframes exist in Blender



✅ Quick Checklist Before Exporting

- [ ] Created `TOP_SCENE` peg
- [ ] Camera peg has **Enable 3D** checked
- [ ] Camera peg uses **Euler Angles** (not Quaternion)
- [ ] All pegs are parented to `TOP_SCENE`
- [ ] Selected ALL nodes in Node View (Shift+click)
- [ ] Moved PEGs (not just drawings)
- [ ] Tested animation in Harmony Camera view


Need Help?

If something isn't working:
1. Check the **System Console** in Blender for errors
2. Verify your Harmony export JSON has correct values
3. Test with a simple scene first (1 peg + camera)
4. Render out the scene from Toon Boom Harmony and import it to the Camera 'Background' image to have to stream over the imported pegs to see ~ I will make a tutorial video later.

Good luck with your 2D/3D pipeline! 🎬
