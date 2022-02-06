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
Go Back to your visual studio project and right click on refrences, then click **Add Reference**

![image](https://github.com/JD3006/Modding-Crabgame/blob/main/images/Capture3.PNG?raw=true)

Click On the browse tab, then click ***Browse***, navigate to wherever you made your libs folder, then select all of the dlls inside your libs folder and click Add

# Lets get coding!!

Now lets start by renaming the Class1.cs to BepInExLoader.cs

Then Paste this code in:

```Csharp
using BepInEx;
using UnhollowerRuntimeLib;
using HarmonyLib;
using UnityEngine;
using UnityEngine.EventSystem;
using Object = UnityEngine.Object;

namespace Trainer
{
    [BepInPlugin(GUID, MODNAME, VERSION)]
    public class BepInExLoader : BepInEx.IL2CPP.BasePlugin
    {
        public const string
            MODNAME = "INSERT MOD NAME",
            AUTHOR = "INSERT PERSON NAME",
            GUID = "com." + AUTHOR + "." + MODNAME,
            VERSION = "1.0.0.0";

        public static BepInEx.Logging.ManualLogSource log;

        public BepInExLoader()
        {
            log = Log;
        }

        public override void Load()
        {
            log.LogMessage("[Trainer] Registering TrainerComponent in Il2Cpp");

            try
            {
                // Register our custom Types in Il2Cpp
                ClassInjector.RegisterTypeInIl2Cpp<TrainerComponent>();

                var go = new GameObject("TrainerObject");
                go.AddComponent<TrainerComponent>();
                Object.DontDestroyOnLoad(go);
            }
            catch
            {
                log.LogError("[Trainer] FAILED to Register Il2Cpp Type: TrainerComponent!");
            }

            try
            {
                var harmony = new Harmony("wh0am15533.trainer.il2cpp");

                // Our Primary Unity Event Hooks 

                // Awake
                var originalAwake = AccessTools.Method(typeof(SOME_GAME_TYPE), "Awake"); // Change Type!
                log.LogMessage("[Trainer] Harmony - Original Method: " + originalAwake.DeclaringType.Name + "." + originalAwake.Name);
                var postAwake = AccessTools.Method(typeof(TrainerComponent), "Awake");
                log.LogMessage("[Trainer] Harmony - Postfix Method: " + postAwake.DeclaringType.Name + "." + postAwake.Name);
                harmony.Patch(originalAwake, postfix: new HarmonyMethod(postAwake));

                // Start
                var originalStart = AccessTools.Method(typeof(SOME_GAME_TYPE), "Start"); // Change Type!
                log.LogMessage("[Trainer] Harmony - Original Method: " + originalStart.DeclaringType.Name + "." + originalStart.Name);
                var postStart = AccessTools.Method(typeof(TrainerComponent), "Start");
                log.LogMessage("[Trainer] Harmony - Postfix Method: " + postStart.DeclaringType.Name + "." + postStart.Name);
                harmony.Patch(originalStart, postfix: new HarmonyMethod(postStart));

                // Update
                var originalUpdate = AccessTools.Method(typeof(SOME_GAME_TYPE), "Update"); // Change Type!
                log.LogMessage("[Trainer] Harmony - Original Method: " + originalUpdate.DeclaringType.Name + "." + originalUpdate.Name);
                var postUpdate = AccessTools.Method(typeof(TrainerComponent), "Update");
                log.LogMessage("[Trainer] Harmony - Postfix Method: " + postUpdate.DeclaringType.Name + "." + postUpdate.Name);
                harmony.Patch(originalUpdate, postfix: new HarmonyMethod(postUpdate));
                log.LogMessage("[Trainer] Harmony - Runtime Patch's Applied");
            }
            catch
            {
                log.LogError("[Trainer] Harmony - FAILED to Apply Patch's!");
            }
        }
    }
}
```

# An Explaination

Next thing you need to do is set MODNAME to your mods name and AUTHOR to yours.

You know how Unity has 3 main classes:
Awake(),
Update(),
Start()

You cant use any of those, but you can piggy back off of others.

To find these scripts you want a software called dnspy.
you can look through all of the unhollowed scripts for any good candidates to yoink off of,
they need to always exist and **never get deleted**.

Currently I only know one for the Update() function
Which is: 
```Csharp 
EventSystem.Update();
```
If you find any more of these for awake or start please just make a thread in issues and ill add it.

Now with your Classes to hook onto, in this case : EventSystem

Goto originalUpdate and where it says SOME_GAME_TYPE set that to EventSystem

Comment out the other scripts for Awake and Start.

# Actually Modding the game

Create a New Class and call it TrainerComponent, then paste this code in:

```Csharp
using System;
using System.Collections.Generic;
using BepInEx;
using UnhollowerBaseLib;
using UnhollowerRuntimeLib;
using HarmonyLib;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;
using Object = UnityEngine.Object;
using Input = BepInEx.IL2CPP.UnityEngine.Input;
 
namespace Trainer
{
    public class TrainerComponent : MonoBehaviour
    {
        public TrainerComponent(IntPtr ptr) : base(ptr)
        {
            BepInExLoader.log.LogMessage("[Trainer] Entered Constructor");
        }
 
		// Harmony Patch's must be static!
        [HarmonyPostfix]
        public static void Awake() 
        {
			BepInExLoader.log.LogMessage("[Trainer] I'm Awake!");
        }
 
        [HarmonyPostfix]
        public static void Start()
        {
			BepInExLoader.log.LogMessage("[Trainer] I'm Starting Up...");
        }
 
        [HarmonyPostfix]
        public static void Update()
        {
			//BepInExLoader.log.LogMessage("[Trainer] I'm Updating, disable this message after testing.");
			
			// Note the difference for getting keypress.
            if (Input.GetKeyInt(BepInEx.IL2CPP.UnityEngine.KeyCode.Backslash) && Event.current.type == EventType.KeyDown)
            {
                //... do something
 
                Event.current.Use();
            }
		}
	}
}
```

Unfortunately the Start and Awake Functions wont work until someone finds a class to hook it onto but the update function will work

There is also a difference for somethings like getting Keypresses as you can see its now BepInEx.IL2CPP.UnityEngine.Keycode

# Lets decompile the game

Now one mod that will be extremly useful for getting scripts and looking around the game is unity explorer for il2cpp

Get it here: https://github.com/sinai-dev/UnityExplorer/releases/tag/4.5.5
Make sure you download the il2cpp Bepinex one.
Then Extract it and get the .dll file
Then in crab game folder goto **bepinex/plugins** and paste it there, thats where you put all the mods

# The final step to making a mod
In the top middle area on your screen it will say Debug (if it dosent skip this step), click on it and set it to Release

Now you can build your mod hit control+b or goto the build tab and click build
goto where you mod project folder is usually in

C:\Users\YOURUSERNAMEHERE\source\repos\PROJECTNAMEHERE

then goto the folder with your project name on it then bin, then open Release and you should find a bunch of dlls, just copy the one with your project name on it
and the paste it in **bepinex\plugins** , now run your game.

# Cool things you can do
There is a txt file with all of the translations i have so far, so you can look through them with UnityExplorer or code with them and make a bunch of cool things

# I need help with decompiling
Most of the game is obfuscated due to il2cpp, but if you use UnityExplorer and go around the game mess with things, run a few functions and then write down what you think they do you can create a new issue forum and post your findings there and if it works i'll add it to the list of classes and what they do

# DONT hack in public lobbys

# Sources

https://www.unknowncheats.me/forum/unity/422559-il2cpp-modding-quick-start-guide.html
https://github.com/CodeName-Anti/CrabGame-Cheat/tree/12e14476d4a5f35b40bf8b006013ee9a12c69db6
