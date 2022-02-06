# Modding-Crabgame
How to mod crab game and useful tools for it

# How to do it?
First, off your going to need bepinex 6.0 for ill2cpp, Crab Game is an ill2cpp game meaning its much more annoying to mod and stuff is obfuscated.

Download BEPINEX 6.0 Preview Here:
https://builds.bepinex.dev/projects/bepinex_be

Once You Have Downloaded it extract it and put Bepinex, Mono, doorstop.ini and winhttp.dll into Crab Games root folder.
You can find the root folder by going to steam, and right clicking on crab game then manage and click Browse local files.

Now run crab game, then close it, if you open the Bepinex folder you should see a folder called 'Unhollowed' that means that its successfully decompiled the game.

Now your going to need **Visual Studio**, in visual studio your going to create a new **Dynamic Link Dll using .NET**

![image](https://github.com/JD3006/Modding-Crabgame/blob/main/images/Capture.PNG?raw=true)

Then Set The .NET Framework Targeting anywhere up to 4.8 but 4.7.1 works best

![image](https://github.com/JD3006/Modding-Crabgame/blob/main/images/Capture2.PNG?raw=true)

Now Create the project, before we can do any coding we need to add all the refrences that we need for the project.
Go back to your crab game root folder, open Bepinex and create a new folder, libs, then take

0Harmony.dll
BepInEx.Core.dll
BepInEx.Il2Cpp.dll
Mono.Cecil.dll
Mono.Cecil.Mdb.dll
Mono.Cecil.Pdb.dll
Mono.Cecil.Rocks.dll

from the **Bepinex/Core** folder and **Copy** it into your libs folder

Then take

Il2Cppmscorlib.dll
Il2CppSystem.dll
Il2CppSystem.Core.dll
Il2CppSystem.Xml.dll
UnhollowerBaseLib.dll
UnhollowerRuntimeLib.dll		
UnityEngine.dll
UnityEngine.CoreModule.dll
UnityEngine.IMGUIModule.dll
UnityEngine.UI.dll
UnityEngine.UIModule.dll
UnityEngine.TextRenderingModule.dll
Assembly-CSharp.dll <-- The actual code of the game

from the **Bepinex/Unhollowed** folder and **Copy** it into your libs folder
