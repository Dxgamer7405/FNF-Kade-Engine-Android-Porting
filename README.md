# Kade-Engine-Android-Porting:

The things im using when i port a Kade Engine mod to android

**This should be used for Kade Engine (all versions I think)**

### PC compile instructions For Android:

1. Download
* [JDK](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html) - Download version `11` of it
* [Android Studio](https://developer.android.com/studio) - I recomend you to download the latest version
* [NDK](https://developer.android.com/ndk/downloads/older_releases?hl=fi) - Download version `r21e` (This is the version recomended by Lime)

2. Install JDK, Android Studio 
Unzip the NDK (the NDK does not need to be installed because its a zip archive)

3. We need to set up Android Studio for this go to android studio and find android sdk (in settings -> Appearance & Behavior -> system settings -> android sdk)
![andr](https://user-images.githubusercontent.com/59097731/104179652-44346000-541d-11eb-8ad1-1e4dfae304a8.PNG)
![andr2](https://user-images.githubusercontent.com/59097731/104179943-a9885100-541d-11eb-8f69-7fb5a4bfdd37.PNG)

4. Run command `lime setup android` in CMD/PowerShell (You need to insert the program paths)

5. Open project in CMD/PowerShell `cd (path to fnf source)`
And run command `lime build android -final`
The apk will be generated in this path (path to source)\export\release\android\bin\app\build\outputs\apk\debug
_____________________________________

## Instructions:

1. You Need to install extension-androidtools 

To Install it You Need To Open Command prompt/PowerShell And Type
```cmd
haxelib git extension-androidtools https://github.com/MAJigsaw77/extension-androidtools.git
```

Also you need to install Sirox extension-webm for android support
```cmd
haxelib git extension-webm https://github.com/Sirox228/extension-webm.git
```

And you need to install the github version of Newgrounds because it has android support
```cmd
haxelib git newgrounds https://github.com/Geokureli/Newgrounds.git
```

Last thing is to install M.A Jigsaw Linc_Luajit because it has android support
```cmd
haxelib git linc_luajit https://github.com/MAJigsaw77/linc_luajit.git
```

**For ppl that doesn't have a pc, I left a yml file to compile with github actions. Everytime you do a commit it makes a build. Be sure to enable workflows.**

2. Download the repository code and paste it in your source code folder

3. You Need to add these things in project.xml

On This Line
```xml
	<!--Mobile-specific-->
	<window if="mobile" orientation="landscape" fullscreen="true" width="0" height="0" resizable="false" />
```

Replace It With
```xml
	<!--Mobile-specific-->
	<window if="mobile" orientation="landscape" fullscreen="true" width="1280" height="720" resizable="false" />
```

Add

```xml
	<assets path="assets/android" if="android" /> 
```

Then, After the Libraries, or where the packeges are located add

```xml
	<haxelib name="extension-androidtools" if="android" />
```

Before the line <!--Custom--> add
```xml
	<!--Make's-the-Game-use-less-ram-->
	<haxedef name="HXCPP_GC_BIG_BLOCKS" />

	<!--Always-enable-Null-Object-Reference-check-->
	<haxedef name="HXCPP_CHECK_POINTER" if="release" />
	<haxedef name="HXCPP_STACK_LINE" if="release" />

	<!--Android-Things-->
	<android permission="android.permission.ACCESS_NETWORK_STATE" />
	<android permission="android.permission.ACCESS_WIFI_STATE" />
	<android permission="android.permission.INTERNET" />
	<android target-sdk-version="29" />
	<android gradle-version="5.4.1" />
	<android gradle-plugin="3.5.0" />
```

4. Setup Controls.hx

after these lines
```haxe
import flixel.input.actions.FlxActionSet;
import flixel.input.keyboard.FlxKey;
```
add

```haxe
#if android
import android.flixel.FlxButton;
import android.flixel.FlxHitbox;
import android.flixel.FlxVirtualPad;
#end
```

before these lines
```haxe
override function update()
{
	super.update();
}
```

add
```haxe
	#if android
	public var trackedinputsUI:Array<FlxActionInput> = [];
	public var trackedinputsNOTES:Array<FlxActionInput> = [];

	public function addbuttonNOTES(action:FlxActionDigital, button:FlxButton, state:FlxInputState)
	{
		var input:FlxActionInputDigitalIFlxInput = new FlxActionInputDigitalIFlxInput(button, state);
		trackedinputsNOTES.push(input);
		action.add(input);
	}

	public function addbuttonUI(action:FlxActionDigital, button:FlxButton, state:FlxInputState)
	{
		var input:FlxActionInputDigitalIFlxInput = new FlxActionInputDigitalIFlxInput(button, state);
		trackedinputsUI.push(input);
		action.add(input);
	}

	public function setHitBox(Hitbox:FlxHitbox) 
	{
		inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, Hitbox.buttonUp, state));
		inline forEachBound(Control.DOWN, (action, state) -> addbuttonNOTES(action, Hitbox.buttonDown, state));
		inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, Hitbox.buttonLeft, state));
		inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, Hitbox.buttonRight, state));
	}

	public function setVirtualPadUI(VirtualPad:FlxVirtualPad, DPad:FlxDPadMode, Action:FlxActionMode)
	{
		switch (DPad)
		{
			case UP_DOWN:
				inline forEachBound(Control.UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
			case LEFT_RIGHT:
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case UP_LEFT_RIGHT:
				inline forEachBound(Control.UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case LEFT_FULL | RIGHT_FULL:
				inline forEachBound(Control.UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case BOTH_FULL:
				inline forEachBound(Control.UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
				inline forEachBound(Control.UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp2, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown2, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft2, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight2, state));
			case NONE: // do nothing
		}

		switch (Action)
		{
			case A:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonUI(action, VirtualPad.buttonA, state));
			case B:
				inline forEachBound(Control.BACK, (action, state) -> addbuttonUI(action, VirtualPad.buttonB, state));
			case A_B | A_B_C | A_B_E | A_B_X_Y | A_B_C_X_Y | A_B_C_X_Y_Z | A_B_C_D_V_X_Y_Z:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonUI(action, VirtualPad.buttonA, state));
				inline forEachBound(Control.BACK, (action, state) -> addbuttonUI(action, VirtualPad.buttonB, state));
			case NONE: // do nothing
		}
	}

	public function setVirtualPadNOTES(VirtualPad:FlxVirtualPad, DPad:FlxDPadMode, Action:FlxActionMode) 
	{
		switch (DPad)
		{
			case UP_DOWN:
				inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
			case LEFT_RIGHT:
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case UP_LEFT_RIGHT:
				inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case LEFT_FULL | RIGHT_FULL:
				inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case BOTH_FULL:
				inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
				inline forEachBound(Control.UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp2, state));
				inline forEachBound(Control.DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown2, state));
				inline forEachBound(Control.LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft2, state));
				inline forEachBound(Control.RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight2, state));
			case NONE: // do nothing
		}

		switch (Action)
		{
			case A:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonA, state));
			case B:
				inline forEachBound(Control.BACK, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonB, state));
			case A_B | A_B_C | A_B_E | A_B_X_Y | A_B_C_X_Y | A_B_C_X_Y_Z | A_B_C_D_V_X_Y_Z:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonA, state));
				inline forEachBound(Control.BACK, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonB, state));
			case NONE: // do nothing
		}
	}

	public function removeAControlsInput(Tinputs:Array<FlxActionInput>)
	{
		for (action in this.digitalActions)
		{
			var i = action.inputs.length;
			while (i-- > 0)
			{
				var x = Tinputs.length;
				while (x-- > 0)
				{
					if (Tinputs[x] == action.inputs[i])
						action.remove(action.inputs[i]);
				}
			}
		}
	}
	#end
```

5. Setup MusicBeatState.hx

in the lines you import things add
```haxe
#if android
import android.AndroidControls;
import android.flixel.FlxVirtualPad;
import flixel.FlxCamera;
import flixel.input.actions.FlxActionInput;
import flixel.util.FlxDestroyUtil;
#end
```

after these lines
```haxe
inline function get_controls():Controls
	return PlayerSettings.player1.controls;
```

add
```haxe
	#if android
	var androidControls:AndroidControls;
	var virtualPad:FlxVirtualPad;
	var trackedinputsUI:Array<FlxActionInput> = [];
	var trackedinputsNOTES:Array<FlxActionInput> = [];

	public function addVirtualPad(DPad:FlxDPadMode, Action:FlxActionMode)
	{
		virtualPad = new FlxVirtualPad(DPad, Action);
		add(virtualPad);

		controls.setVirtualPadUI(virtualPad, DPad, Action);
		trackedinputsUI = controls.trackedinputsUI;
		controls.trackedinputsUI = [];
	}

	public function removeVirtualPad()
	{
		if (trackedinputsUI != [])
			controls.removeAControlsInput(trackedinputsUI);

		if (virtualPad != null)
			remove(virtualPad);
	}

	public function addAndroidControls(DefaultDrawTarget:Bool = true)
	{
		androidControls = new AndroidControls();

		switch (AndroidControls.getMode())
		{
			case 'Pad-Right' | 'Pad-Left' | 'Pad-Custom':
				controls.setVirtualPadNOTES(androidControls.virtualPad, RIGHT_FULL, NONE);
			case 'Pad-Duo':
				controls.setVirtualPadNOTES(androidControls.virtualPad, BOTH_FULL, NONE);
			case 'Hitbox':
				controls.setHitBox(androidControls.hitbox);
			case 'Keyboard': // do nothing
		}

		trackedinputsNOTES = controls.trackedinputsNOTES;
		controls.trackedinputsNOTES = [];

		var camControls:FlxCamera = new FlxCamera();
		FlxG.cameras.add(camControls, DefaultDrawTarget);
		camControls.bgColor.alpha = 0;

		androidControls.cameras = [camControls];
		androidControls.visible = false;
		add(androidControls);
	}

	public function removeAndroidControls()
	{
		if (trackedinputsNOTES != [])
			controls.removeAControlsInput(trackedinputsNOTES);

		if (androidControls != null)
			remove(androidControls);
	}

	public function addPadCamera(DefaultDrawTarget:Bool = true)
	{
		if (virtualPad != null)
		{
			var camControls:FlxCamera = new FlxCamera();
			FlxG.cameras.add(camControls, DefaultDrawTarget);
			camControls.bgColor.alpha = 0;
			virtualPad.cameras = [camControls];
		}
	}
	#end

	override function destroy()
	{
		#if android
		if (trackedinputsNOTES != [])
			controls.removeAControlsInput(trackedinputsNOTES);

		if (trackedinputsUI != [])
			controls.removeAControlsInput(trackedinputsUI);
		#end

		super.destroy();

		#if android
		if (virtualPad != null)
		{
			virtualPad = FlxDestroyUtil.destroy(virtualPad);
			virtualPad = null;
		}

		if (androidControls != null)
		{
			androidControls = FlxDestroyUtil.destroy(androidControls);
			androidControls = null;
		}
		#end
	}
```

6. Setup MusicBeatSubstate.hx

in the lines you import things add
```haxe
#if android
import android.flixel.FlxVirtualPad;
import flixel.FlxCamera;
import flixel.input.actions.FlxActionInput;
import flixel.util.FlxDestroyUtil;
#end
```

after these lines
```haxe
inline function get_controls():Controls
	return PlayerSettings.player1.controls;
```

add
```haxe
	#if android
	var virtualPad:FlxVirtualPad;
	var trackedinputsUI:Array<FlxActionInput> = [];

	public function addVirtualPad(DPad:FlxDPadMode, Action:FlxActionMode)
	{
		virtualPad = new FlxVirtualPad(DPad, Action);
		add(virtualPad);

		controls.setVirtualPadUI(virtualPad, DPad, Action);
		trackedinputsUI = controls.trackedinputsUI;
		controls.trackedinputsUI = [];
	}

	public function removeVirtualPad()
	{
		if (trackedinputsUI != [])
			controls.removeAControlsInput(trackedinputsUI);

		if (virtualPad != null)
			remove(virtualPad);
	}

	public function addPadCamera(DefaultDrawTarget:Bool = true)
	{
		if (virtualPad != null)
		{
			var camControls:FlxCamera = new FlxCamera();
			FlxG.cameras.add(camControls, DefaultDrawTarget);
			camControls.bgColor.alpha = 0;
			virtualPad.cameras = [camControls];
		}
	}
	#end

	override function destroy()
	{
		#if android
		if (trackedinputsUI != [])
			controls.removeAControlsInput(trackedinputsUI);
		#end

		super.destroy();

		#if android
		if (virtualPad != null)
		{
			virtualPad = FlxDestroyUtil.destroy(virtualPad);
			virtualPad = null;
		}
		#end
	}
```

And somehow you finished adding the android controls to your kade engine copy

now on every state/substate add (this is the most confusing part, I suggest to check my Kade Engine ports)
```haxe
#if android
addVirtualPad(LEFT_FULL, A_B);
#end

//if you want to remove it at some moment use
#if android
removeVirtualPad();
#end

//if you want it to have a camera
#if android
addPadCamera();
#end

//in states, these need to be added before super.create();
//in substates, in fuction new at the last line add these

//on Playstate.hx after all of the
//obj.cameras = [...];
//things, add
#if android
addAndroidControls();
#end

//if you want to remove it at some moment use
#if android
removeAndroidControls();
#end

//to make the controls visible the code is
#if android
androidControls.visible = true;
#end

//to make the controls invisible the code is
#if android
androidControls.visible = false;
#end
```

7. Prevent the Android BACK Button

in TitleState.hx

after
```haxe
override public function create():Void
```

add
```haxe
#if android
FlxG.android.preventDefaultKeys = [BACK];
#end
```

8. Set An action to the BACK Button

you can set one with
```haxe
#if android || FlxG.android.justReleased.BACK #end
```

9. On sys.FileSystem and sys.io.File for modding and polymod stuff

this is not working with app storage but on phone storage it will work with this

```haxe
SUtil.getPath() + 
```
this will make the game use the phone storage
but you will have to add one thing in Your source

in Main.hx before 
```haxe
#if cpp
initialState = Caching;
game = new FlxGame(gameWidth, gameHeight, initialState, zoom, framerate, framerate, skipSplash, startFullscreen);
#else
game = new FlxGame(gameWidth, gameHeight, initialState, zoom, framerate, framerate, skipSplash, startFullscreen);
#end
```

add 
```haxe
SUtil.check();
```
this will check for android storage permisions and the assets/mods directories

then replace
```haxe
var framerate:Int = 120; // How many frames per second the game should run at.
```

with
```haxe
var framerate:Int = 60; // How many frames per second the game should run at.
```
you should do this if you don't want to have performance issues

last thing is replacing
```haxe
#if !mobile
fpsCounter = new FPS(10, 3, 0xFFFFFF);
addChild(fpsCounter);
toggleFPS(FlxG.save.data.fps);
#end

with
```haxe
#if mobile
fpsCounter = new FPS(10, 3, 0xFFFFFF);
addChild(fpsCounter);
toggleFPS(FlxG.save.data.fps);
#end
```

10. On Crash Application Alert

on Main.hx after
```haxe
public function new()
{
	super();
```	
add
```haxe
SUtil.uncaughtErrorHandler();
```

11. File Saver

This is a feature to save files with sys.io.File
This is the code
```haxe
SUtil.saveContent("your file name", ".txt", "lololol");
```

12. Able to change the android controls in game

In OptionsMenu.hx before
```haxe
super.create();
```
add
```haxe
#if android
var tipText:FlxText = new FlxText(10, 14, 0, 'Press C to customize your android controls', 16);
tipText.setFormat(Paths.font('vcr.ttf'), 16, FlxColor.WHITE, LEFT, FlxTextBorderStyle.OUTLINE, FlxColor.BLACK);
tipText.borderSize = 2.4;
tipText.scrollFactor.set();
add(tipText);
#end
```
and after
```haxe
override function update(elapsed:Float)
{
	super.update(elapsed);
```
add
```haxe
#if android
if (virtualPad.buttonC.justPressed) {
	#if android
	removeVirtualPad();
	#end
	openSubState(new android.AndroidControlsSubState());
}
#end
```

13. Do an action when you press on the screen

```haxe
#if android
var justTouched:Bool = false;

for (touch in FlxG.touches.list)
{
        if (touch.justPressed)
        {
               //insert your code
        }
}
#end
```
For making dialogues go, in DialogueBox.hx after
```haxe
if (dialogueOpened && !dialogueStarted)
{
	startDialogue();
	dialogueStarted = true;
}
```
add
```haxe
#if android
var justTouched:Bool = false;

for (touch in FlxG.touches.list)
{
        if (touch.justPressed)
        {
               justTouched = true;
        }
}
#end
```
and replace
```haxe
if (PlayerSettings.player1.controls.ACCEPT && dialogueStarted == true)
```
with
```haxe
if (PlayerSettings.player1.controls.ACCEPT #if android || justTouched #end && dialogueStarted == true)
```

14. How to make pause work 

In Playstate.hx replace
```haxe
if (FlxG.keys.justPressed.ENTER && startedCountdown && canPause)
```
with
```haxe
if (FlxG.keys.justPressed.ENTER #if android || FlxG.android.justReleased.BACK #end && startedCountdown && canPause)
```

15.

To make modcharts work, in Project.xml replace
```haxe
<haxelib name="linc_luajit" if "windows"/>
```
with
```haxe
<haxelib name="linc_luajit"/>
```
now go to ModchartState.hx and in the lines where you import stuff replace
```haxe
#if windows
import flixel.tweens.FlxEase;
import openfl.filters.ShaderFilter;
import flixel.tweens.FlxTween;
import flixel.util.FlxColor;
import openfl.geom.Matrix;
import openfl.display.BitmapData;
import lime.app.Application;
import flixel.FlxSprite;
import llua.Convert;
import llua.Lua;
import llua.State;
import llua.LuaL;
import flixel.FlxBasic;
import flixel.FlxCamera;
import flixel.FlxG;
```
with
```haxe
#if (windows || android)
import flixel.tweens.FlxEase;
import openfl.filters.ShaderFilter;
import flixel.tweens.FlxTween;
import flixel.util.FlxColor;
import openfl.geom.Matrix;
import openfl.display.BitmapData;
import lime.app.Application;
import flixel.FlxSprite;
import llua.Convert;
import llua.Lua;
import llua.State;
import llua.LuaL;
import flixel.FlxBasic;
import flixel.FlxCamera;
import flixel.FlxG;
``` 
just replace the if windows with if (windows || android), pretty much self explanatory, makes support for android

replace
```haxe
var data:BitmapData = BitmapData.fromFile(Sys.getCwd() + "assets/data/" + PlayState.SONG.song.toLowerCase() + '/' + spritePath + ".png");
```
with
```haxe
var data:BitmapData = BitmapData.fromFile(SUtil.getPath() + "assets/data/" + PlayState.SONG.song.toLowerCase() + '/' + spritePath + ".png");
```
and
```haxe
sprite.frames = FlxAtlasFrames.fromSparrow(FlxGraphic.fromBitmapData(data), Sys.getCwd() + "assets/data/" + PlayState.SONG.song.toLowerCase() + "/" + spritePath + ".xml");
```
with
```haxe
sprite.frames = FlxAtlasFrames.fromSparrow(FlxGraphic.fromBitmapData(data), SUtil.getPath() + "assets/data/" + PlayState.SONG.song.toLowerCase() + "/" + spritePath + ".xml");
```
replace
```haxe
var data:BitmapData = BitmapData.fromFile(Sys.getCwd() + "assets/data/" + PlayState.SONG.song.toLowerCase() + '/' + spritePath + ".png");
```
with
```haxe
var data:BitmapData = BitmapData.fromFile(SUtil.getPath() + "assets/data/" + PlayState.SONG.song.toLowerCase() + '/' + spritePath + ".png");
```
and replace
```haxe
var result = LuaL.dofile(lua, Paths.lua(PlayState.SONG.song.toLowerCase() + "/modchart")); // execute le file
```
with
```haxe
var result = LuaL.dostring(lua, openfl.utils.Assets.getText("assets/data/" + PlayState.SONG.song.toLowerCase() + "/modchart.lua")); // execute le file
```

You finished with ModchartState.hx. Next is to edit PasueSubState.hx

Replace
```haxe
#if windows
import llua.Lua;
#end
```
with
```haxe
#if (windows || android)
import llua.Lua;
#end
```
then replace
```haxe
#if windows
if (PlayState.luaModchart != null)
{
	PlayState.luaModchart.die();
	PlayState.luaModchart = null;
}
#end
```
with
```haxe
#if (windows || android)
if (PlayState.luaModchart != null)
{
	PlayState.luaModchart.die();
	PlayState.luaModchart = null;
}
#end
```

And you're also done with PauseSubState.hx. Last thing to do is to edit Playstate.hx

replace
```haxe
#if windows
executeModchart = openfl.utils.Assets.exists("assets/data/" + PlayState.SONG.song.toLowerCase() + "/modchart.lua");
#end
```haxe
#if (windows || android)
executeModchart = openfl.utils.Assets.exists("assets/data/" + PlayState.SONG.song.toLowerCase() + "/modchart.lua");
#end
```
then replace
```haxe
#if windows
public static var luaModchart:ModchartState = null;
#end
```
with
```haxe
#if (windows || android)
public static var luaModchart:ModchartState = null;
#end
```
then replace
```haxe
#if windows
if (executeModchart)
{
	luaModchart = ModchartState.createModchartState();
	luaModchart.executeState('start',[PlayState.SONG.song]);
}
#end
```
with
```
#if (windows || android)
if (executeModchart)
{
	luaModchart = ModchartState.createModchartState();
	luaModchart.executeState('start',[PlayState.SONG.song]);
}
#end
```
then replace
```
#if windows
if (executeModchart && luaModchart != null && songStarted)
{
	luaModchart.setVar('songPos',Conductor.songPosition);
	luaModchart.setVar('hudZoom', camHUD.zoom);
	luaModchart.setVar('cameraZoom',FlxG.camera.zoom);
	luaModchart.executeState('update', [elapsed]);

	for (i in luaWiggles)
	{
		trace('wiggle le gaming');
		i.update(elapsed);
	}

	/*for (i in 0...strumLineNotes.length) {
		var member = strumLineNotes.members[i];
		member.x = luaModchart.getVar("strum" + i + "X", "float");
		member.y = luaModchart.getVar("strum" + i + "Y", "float");
		member.angle = luaModchart.getVar("strum" + i + "Angle", "float");
	}*/

	FlxG.camera.angle = luaModchart.getVar('cameraAngle', 'float');
	camHUD.angle = luaModchart.getVar('camHudAngle','float');

	if (luaModchart.getVar("showOnlyStrums",'bool'))
	{
		healthBarBG.visible = false;
		kadeEngineWatermark.visible = false;
		healthBar.visible = false;
		iconP1.visible = false;
		iconP2.visible = false;
		scoreTxt.visible = false;
	}
	else
	{
		healthBarBG.visible = true;
		kadeEngineWatermark.visible = true;
		healthBar.visible = true;
		iconP1.visible = true;
		iconP2.visible = true;
		scoreTxt.visible = true;
	}

	var p1 = luaModchart.getVar("strumLine1Visible",'bool');
	var p2 = luaModchart.getVar("strumLine2Visible",'bool');

	for (i in 0...4)
	{
		strumLineNotes.members[i].visible = p1;
		if (i <= playerStrums.length)
			playerStrums.members[i].visible = p2;
	}
}
#end
```
with
```haxe
#if (windows || android)
if (executeModchart && luaModchart != null && songStarted)
{
	luaModchart.setVar('songPos',Conductor.songPosition);
	luaModchart.setVar('hudZoom', camHUD.zoom);
	luaModchart.setVar('cameraZoom',FlxG.camera.zoom);
	luaModchart.executeState('update', [elapsed]);

	for (i in luaWiggles)
	{
		trace('wiggle le gaming');
		i.update(elapsed);
	}

	/*for (i in 0...strumLineNotes.length) {
		var member = strumLineNotes.members[i];
		member.x = luaModchart.getVar("strum" + i + "X", "float");
		member.y = luaModchart.getVar("strum" + i + "Y", "float");
		member.angle = luaModchart.getVar("strum" + i + "Angle", "float");
	}*/

	FlxG.camera.angle = luaModchart.getVar('cameraAngle', 'float');
	camHUD.angle = luaModchart.getVar('camHudAngle','float');

	if (luaModchart.getVar("showOnlyStrums",'bool'))
	{
		healthBarBG.visible = false;
		kadeEngineWatermark.visible = false;
		healthBar.visible = false;
		iconP1.visible = false;
		iconP2.visible = false;
		scoreTxt.visible = false;
	}
	else
	{
		healthBarBG.visible = true;
		kadeEngineWatermark.visible = true;
		healthBar.visible = true;
		iconP1.visible = true;
		iconP2.visible = true;
		scoreTxt.visible = true;
	}

	var p1 = luaModchart.getVar("strumLine1Visible",'bool');
	var p2 = luaModchart.getVar("strumLine2Visible",'bool');

	for (i in 0...4)
	{
		strumLineNotes.members[i].visible = p1;
		if (i <= playerStrums.length)
			playerStrums.members[i].visible = p2;
	}
}
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
there are 2 of this near actually, replace both with this.

then replace
```haxe
#if windows
if (luaModchart != null)
	luaModchart.setVar("mustHit",PlayState.SONG.notes[Std.int(curStep / 16)].mustHitSection);
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.setVar("mustHit",PlayState.SONG.notes[Std.int(curStep / 16)].mustHitSection);
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
{
	offsetX = luaModchart.getVar("followXOffset", "float");
	offsetY = luaModchart.getVar("followYOffset", "float");
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	offsetX = luaModchart.getVar("followXOffset", "float");
	offsetY = luaModchart.getVar("followYOffset", "float");
}
#end
```
then replace
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.executeState('playerTwoTurn', []);
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
{
	offsetX = luaModchart.getVar("followXOffset", "float");
	offsetY = luaModchart.getVar("followYOffset", "float");
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	offsetX = luaModchart.getVar("followXOffset", "float");
	offsetY = luaModchart.getVar("followYOffset", "float");
}
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
	luaModchart.executeState('playerOneTurn', []);
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.executeState('playerOneTurn', []);
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
	luaModchart.executeState('playerTwoSing', [Math.abs(daNote.noteData), Conductor.songPosition]);
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.executeState('playerTwoSing', [Math.abs(daNote.noteData), Conductor.songPosition]);
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
then replace 
```haxe
#if windows
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
{
	luaModchart.die();
	luaModchart = null;
}
#end
```
then replace
```haxe
#if windows
if (luaModchart != null){
if (controls.LEFT_P){luaModchart.executeState('keyPressed',["left"]);};
if (controls.DOWN_P){luaModchart.executeState('keyPressed',["down"]);};
if (controls.UP_P){luaModchart.executeState('keyPressed',["up"]);};
if (controls.RIGHT_P){luaModchart.executeState('keyPressed',["right"]);};
};
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null){
if (controls.LEFT_P){luaModchart.executeState('keyPressed',["left"]);};
if (controls.DOWN_P){luaModchart.executeState('keyPressed',["down"]);};
if (controls.UP_P){luaModchart.executeState('keyPressed',["up"]);};
if (controls.RIGHT_P){luaModchart.executeState('keyPressed',["right"]);};
};
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
	luaModchart.executeState('playerOneMiss', [direction, Conductor.songPosition]);
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.executeState('playerOneMiss', [direction, Conductor.songPosition]);
#end
```
then replace
```haxe
#if windows
if (luaModchart != null)
	luaModchart.executeState('playerOneSing', [note.noteData, Conductor.songPosition]);
#end
```
with
```haxe
#if (windows || android)
if (luaModchart != null)
	luaModchart.executeState('playerOneSing', [note.noteData, Conductor.songPosition]);
#end
```
then replace
```haxe
#if windows
if (executeModchart && luaModchart != null)
{
	luaModchart.setVar('curStep',curStep);
	luaModchart.executeState('stepHit',[curStep]);
}
#end
```
with
```haxe
#if (windows || android)
if (executeModchart && luaModchart != null)
{
	luaModchart.setVar('curStep',curStep);
	luaModchart.executeState('stepHit',[curStep]);
}
#end
```
and finally, replace
```haxe
#if windows
if (executeModchart && luaModchart != null)
{
	luaModchart.setVar('curBeat',curBeat);
	luaModchart.executeState('beatHit',[curBeat]);
}
#end
```
with
```haxe
#if (windows || android)
if (executeModchart && luaModchart != null)
{
	luaModchart.setVar('curBeat',curBeat);
	luaModchart.executeState('beatHit',[curBeat]);
}
#end
```

To avoid a crash after you finish a song, remove
```haxe
if (!loadRep)
	rep.SaveReplay(saveNotes);
else
{
	FlxG.save.data.botplay = false;
	FlxG.save.data.scrollSpeed = 1;
	FlxG.save.data.downscroll = false;
}
```
this code is found on function endSong():Void

MOST STUFF ISN'T EXPLAINED SO I SUGGEST YOU TO LOOK TO MY PORTS OF KADE ENGINE AND KADE ENGINE MODS

## Credits:
* VegethYT me - Doing the Kade Engine android code support
* Saw (M.A. JIGSAW) - Doing the majority of the code, utils, pad buttons and other things
* luckydog7 - Original code for android controls and hitbox original design.
* Goldie - Pad designer.
