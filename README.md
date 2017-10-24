## End-to-end Detection & Visualization of Written Math Expressions in AR System
Features:
- Voice-initiated image capture using the command "Detect!"
- Written expression on whiteboard -> LaTex conversion using Mathpix API integration
- Dynamic 3D mesh generation for visual representation based on LaTex result

Completed as part of COMP590: *Exploring Virtual Worlds* w/ [Dr. Henry Fuchs](https://www.google.com/search?q=henry+fuchs&oq=henry+fuchs&aqs=chrome..69i57j0l5.967j0j7&sourceid=chrome&ie=UTF-8)
* [Project files (full configuration too large for github)](https://drive.google.com/drive/folders/0BzCHDVeHLa0yUTBmcUpVVHN0UFE)
* [Final Presentation Slides](https://docs.google.com/presentation/d/1KWAsIZi9pAkUHzN-lfM9JT1vxZ9pp_INDFsZa68Rlls/edit?usp=sharing)

---
![Demo](https://image.ibb.co/j90th5/Screen_Shot_2017_07_22_at_2_42_28_PM.png)

# Sample equations
z = x+y
![](https://image.ibb.co/d45iMR/Screen_Shot_2017_10_24_at_5_13_00_PM.png)

z = sqrt(x^2+y^2)
![](https://image.ibb.co/gh9m25/Screen_Shot_2017_07_22_at_2_48_28_PM.png)

## Build 
From Unity, choose File->Build Settings to bring up the Build Settings
window. All of the scenes in the Scenes to Build section should be checked.
Choose Windows Store as the Platform. On the right side, choose Universal 10
as the SDK, "any device" as the Target device, XAML as the UWP Build Type,
check "Copy References", check "Unity C# Projects" and then click Build.
Select the folder called 'UWP' and choose this folder.

After the build completes successfully, an explorer window will pop up.
Navigate into the UWP folder and double-click HoloLensRecognition.sln to launch
Visual Studio. From Visual Studio, set the Configuration to **Release**
for faster builds (doesn't use .NET Native) or **Master** to build the
type of package the Store needs (uses .NET Native).

## Configuring HoloLens

Make sure to change ARM or x64 to **x86**.
Now you can deploy to the Emulator, a Remote Device, or create a Store
package to deploy at a later time.

## Tools

The Tools are contained in a ToolPanel, which manages the visibility of the Back, Grab, Zoom, Tilt, Reset, About, and Controls UI elements. It has accessor functions to fade in/out all of the UI elements together, and the update logic implements the tag-along functionality.

In the ToolPanel, there are Buttons (Back, Grab, Reset, About, and Controls) and Tools (Zoom and Tilt). The buttons perform an action on selection, and tools enter a toggle state that change the functionality of the cursor. (Even though Grab was implemented as a Button, it could also be a Tool that toggles itself off on placement of the content.)

The ToolManager handles the Button and Tool settings that can be called from anywhere in script. It also has global settings that other content is dependent on. For example, the min and max zoom sizes are calculated when new content is loaded and stored inside the ToolManager. Tools are locked and unlocked to enable/disable their functionality, and the ToolPanel can be raised or lowered through the manager. Most of the functions provided here are utility functions expected to be called anywhere a script wants to control tool functionality.

## TransitionManager

Each view (detection window, resulting text, graphical mesh) is a scene in Unity. The ViewLoader handles loading these scenes and the TransitionManager manages how flow moves from an old scene to a new scene through callbacks from the ViewLoader. This system handles the animations that are run between scenes to easily flow between scenes.

Forward transitions are marked by using a PointOfInterest to load a scene. The viewer looks at a destination marker or target (i.e.: a planet in the solar system view) and clicks or air taps to start the transition to the new scene. These transitions add a new scene to a stack in the ViewLoader. Back transitions pop the ViewLoader scene stack to determine the scene to go back to. This is triggered through the UI back button or voice command.

Forward and backward transition flow:
* The next scene is loaded asynchronously, objects slow to a stop, collisions are disabled, and POIs fade out.
* After all of the above is complete, the transition starts by placing the newly loaded scene in the existing scene. The scenes are parented, translated, rotated, and scaled into position while the old scene fades out (handles deletion of the scene when it fades out completely).

For forward transitions, the new content fits in the POI target.
For backward transitions, the new content is scaled up to match up the POI target with the old scene. For these transitions the POI target is completely visible while all other content in the new scene is faded in during the transition.

* After the transition is complete, POIs are faded in, collisions are enabled, and objects start to move.

Performance choices:
* Removing the POIs during a transition is both a design and performance bonus. When the POIs are hidden, they are disabled, saving rendering costs.
* The orbit updater has some expensive computation that may not converge quickly, so the planets in the solar system stop updating during transitions.
* The first transition from the earth to the solar system is taxing, especially on load. We preload the solar system during a blank screen and delete it at the end of the introduction to expedite the first time the solar system is loaded (part of transition logic).

The TransitionManager publicly exposes the FadeContent coroutine, so any script logic can fade in/out an object and all of its children overtime, given an animation curve.



