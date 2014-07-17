![](https://raw.github.com/HaxeFlixel/haxeflixel.com/master/src/files/images/flixel-logos/flixel-ui.png)

[flixel](https://github.com/HaxeFlixel/flixel) | [addons](https://github.com/HaxeFlixel/flixel-addons) | [ui](https://github.com/HaxeFlixel/flixel-ui) | [demos](https://github.com/HaxeFlixel/flixel-demos) | [tools](https://github.com/HaxeFlixel/flixel-tools) | [templates](https://github.com/HaxeFlixel/flixel-templates) | [docs](https://github.com/HaxeFlixel/flixel-docs) | [haxeflixel.com](https://github.com/HaxeFlixel/haxeflixel.com)

[![Build Status](https://travis-ci.org/HaxeFlixel/flixel-ui.png)](https://travis-ci.org/HaxeFlixel/flixel-ui) [![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/HaxeFlixel/flixel-ui/trend.png)](https://bitdeli.com/HaxeFlixel "Bitdeli Badge")

#Documentation
(Work in progress)

##Demo Project!
 A [test project](https://github.com/HaxeFlixel/flixel-demos/tree/master/User%20Interface/RPG%20Interface) is available in [flixel-demos](http://github.com/HaxeFlixel/flixel-demos). You should really, really, check it out. It has a lot of inline documentation in the xml files and showcases some complex and subtle features.

Please note the test project in flixel-demos requires the localization library **[fireTongue](https://github.com/larsiusprime/firetongue)**, which can be installed thus:

    haxelib git firetongue https://github.com/larsiusprime/firetongue

(at least until I get around to submitting it to haxelibs officially)

##Getting Started

###Install flixel-ui:

latest dev version:

    haxelib git flixel-ui https://github.com/HaxeFlixel/flixel-ui

when we finally upload it to haxelib:

    haxelib install flixel-ui

###Quick project setup

1. In your openfl assets folder, create an "xml" directory
2. Create an xml layout file for each state you want a UI for
3. Make your UI-driven states extend flixel.addons.ui.FlxUIState
4. In the create() function, set:

````
_xml_id = "state_battle"; //looks for "state_battle.xml"
````
Provided you've set up your XML layout correctly, flixel-ui will fetch that xml file and auto-generate a _ui:FlxUI member variable.

FlxUI is basically a giant glorified FlxGroup, so using this method will set you up with one UI container and all of your UI widgets inside it.

###Manually creating widgets

You can also create FlxUI widgets directly with Haxe code rather than using the XML setup. 

To see this in action, look at the [demo project](https://github.com/HaxeFlixel/flixel-demos/tree/master/User%20Interface/RPG%20Interface), specifically [State_CodeTest](https://github.com/HaxeFlixel/flixel-demos/blob/master/User%20Interface/RPG%20Interface/source/State_CodeTest.hx)  (in the compiled demo, just click "Code Test" to see it in action.)

You can compare this to [State_DefaultTest](https://github.com/HaxeFlixel/flixel-demos/blob/master/User%20Interface/RPG%20Interface/source/State_DefaultTest.hx), which creates virtually the same UI output, but uses [this xml layout](https://github.com/HaxeFlixel/flixel-demos/blob/master/User%20Interface/RPG%20Interface/assets/xml/state_default.xml) to achieve those results.

###Graphic assets for Widgets

#####Default Assets

Flixel-UI has a default set of assets (see [FlxUIAssets](https://github.com/HaxeFlixel/flixel-ui/blob/master/flixel/addons/ui/FlxUIAssets.hx) and the [assets folder](https://github.com/HaxeFlixel/flixel-ui/tree/master/assets)) for basic skinning. If you provide incomplete data and/or definitions for your widgets, FlxUI will automatically attempt to fall back on the default assets. 

#####Custom Assets

If you want to provide your own assets, you should put them in your project's "assets" folder, using the same structure you see in the [demo project](https://github.com/HaxeFlixel/flixel-demos/tree/master/User%20Interface/RPG%20Interface).

##FlxUI public functions

Most commonly used public functions in class FlxUI:

````
//Initiate from XML Fast object:
//(This is handled automatically in the recommended setup)
load(data:Fast):Void

//Get some widget:
getAsset(key:String,recursive:Bool=true):FlxBasic

//Get some group:
getGroup(key:String,recursive:Bool=true):FlxUIGroup

//Get a text object:
getFlxText(key:String,recursive:Bool=true):FlxText

//Get a mode definition (xml list of things to show/hide):
getMode(Key:String,recursive:Bool=true):Fast

//Get a widget definition:
getDefinition(key:String,recursive:Bool=true):Fast
````

less commonly used public functions:
````
//These implement the IEventGetter interface for lightweight events
getEvent(id:String, sender:Dynamic, data:Dynamic):Void
getRequest(id:String, sender:Dynamic, data:Dynamic):Dynamic
//Both are empty - to be defined by the user in extended classes

//Get, Remove, and Replace assets:
removeAsset(key:String,destroy:Bool=true):FlxBasic
replaceAsset(key:String,replace:FlxBasic,center_x:Bool=true,center_y:Bool=true,destroy_old:Bool=true):FlxBasic

//Set a mode for the UI:
//  mode_id is the mode you want
//  target_id is the FlxUI object to target -
//            "" for this FlxUI, something else for a child
setMode(mode_id:String,target_id:String=""):Void
````

##XML layout basics
Everything in flixel-ui is done with xml layout files. Here's a very simple example:

````
<?xml version="1.0" encoding="utf-8" ?>
<data>	
	<sprite src="ui/title_back" x="0" y="0"/>
</data>
````

That will create a FlxUI object whose sole child is a single sprite. The "src" parameter specifies the path to the image - FlxUI will use this to internally load the object via OpenFL thus:

````
Assets.getBitmapData("assets/gfx/ui/title_back.png")
````

As you can see, all image source entries assume two things:
* The format is PNG (might add JPG/GIF/etc support later)
* The specified directory is inside "assets/gfx/"

###Types of tags
There are several basic types of xml tags in a Flixel-UI layout file.

**Widget**, **\<definition>**, **\<include>**, **\<group>**, **\<align>**, **\<layout>**, **\<failure>**, and **\<mode>**.

Let's go over these one by one.

--

####1. Widget
This is any of the many Flixel-UI widgets, such as **\<sprite\>**, **\<button\>**, **\<checkbox\>**, etc. We'll go into more detail on each one below, but all widget tags have a few things in common:

*Attributes:*
* **id** - string, optional, should be unique. Lets you reference this widget throughout the layout, and also lets you fetch it by name with FlxUI's getAsset("some_id") function.
* **x/y** - integer, specifies position. If no anchor tag exists as a child node, the position is absolute. If an anchor tag exists, the position is relative to the anchor.
* **use_def** - string, optional, references a \<definition\> tag by id to use for this widget.
* **group** - string, optional, references a \<group\> tag by id. Will make this widget the child of that group instead of the FlxUI itself.

--

*Child nodes:*
* **\<locale id="xx-YY">** - optional, lets you specify a locale (like "en-US" or "nb-NO") for [fireTongue](https://github.com/larsiusprime/firetongue) integration. This lets you specify changes based on the current locale:
Example:

````
<button center_x="true" x="0" y="505" id="battle" use_def="text_button" group="top" label="$TITLE_BATTLES">
	<param type="string" value="battle"/>
	<locale id="nb-NO">
		<change width="96"/>
		<!--if norwegian, do 96 pixels wide instead of the default-->
	</locale>			
</button>
````
* **\<anchor\>** - optional, lets you position this widget relative to another object's position.

* **size tags** - optional, lets you dynamically size a widget according to some formula.

More info on Anchor and Size tags appears towards the bottom in the "Dynamic Position & Size" section.

--

####2.\<definition>
This lets you offload a lot of re-usable details into a separate tag with a unique id, and then call them in to another tag using the use_def="definition_id" attribute. A definition tag is exactly like a regular widget tag, except the tag name is "definition."

If you provide details in the widget tag and also use a definition, it will override *some* of the information in the definition... I still need to stabilize how this works. Look at the RPG Interface demo for more details.

####3.\<include>
Include tags let you reference definitions stored in another xml file. This is a convenience feature to cut down on file bloat, and aid organization:

This invocation will include all the definitions found in "some_other_file.xml":
````
<include id="some_other_file"/>
````

*Only* definition tags will be included. It also adds a bit of scoping to your project - in the case that an included definition has the same id as one defined locally, the local definition will be used. Only in the case that FlxUI can't find your definition locally will it check for included ones. 

This recursion is only one level deep. If you put \<include> tags in your included file, they'll be ignored. 


####4. \<group>
Creates a FlxGroup (specifically a FlxUIGroup) that you can assign widgets to. Note that you do NOT add things to a group by making widget tags as child xml nodes to the \<group\> tag, but by setting the "group" attribute in a widget tag to the group's id.

Groups are stacked in the order you define them, with those at the top of the file created first, and thus stacked "underneath" those that come later. 

A group tag takes one attribute - id. Just define your groups somewhere in the order you want them to stack, then add widgets to them by setting the group attribute to the ids you want.

####5. \<align>
Dynamically aligns, centers, and/or spaces objects relative to one another. 
This is complex enough to deserve its own section below.

####6. \<layout>
Creates a child FlxUI object inside your master FlxUI, and childs all the widgets inside to it. This is especially useful if you want to create multiple layouts for, say, different devices and screen sizes. Combined with **failure** tags, this will let you automatically calculate the best layout depending on screen size.

A layout has only one attribute, id, and then its child nodes. Think of a \<layout> as its own sub-section of your xml file. It can have its own versions of anything you can put in the regular file since it is a full-fledged FlxUI - ie, definitions, groups, widgets, modes, presumably even other layout tags (haven't tested this). 

Note that in a layout, scope comes into play when referencing ids. Definitions and object references will first look in the scope of the layout (ie, that FlxUI object), and if none is found, will try to find them in the parent FlxUI. 

####7. \<failure>
Specifies "failure" conditions for a certain layout, so FlxUI can determine which of multiple layouts to choose from in the event that one works better than another. Useful for simultaneously targeting, say, PC's with variable resolutions and mobile devices.

Here's an example:
````
<failure target ="wave_bar" property="height" compare=">" value="15%"/>		
````

"Fail if wave_bar.height is greater than 15% of total flixel canvas height."

Legal values for attributes:

* value - restricted to a percentage (inferring a % of total width/height) or an absolute number. 
* property - **"width"** and **"height"**
* compare - **< , > , <= , >= , = , ==** (= and == are synonymous in this context)

After your FlxUI has loaded, you can fetch your individual layouts using getAsset(), and then check these public properties, which give you the result of the failure checks:

* **failed:Bool** - has this FlxUI "failed" according to the specified rules?
* **failed_by:Float** - if so, by how much?

Sometimes multiple layouts have "failed" according to your rules, and you want to pick the one that failed by the least. If the failure condition was "some_thing's width is greater than 100 pixels", than if some_thing.width = 176, failed_by is 76.

To *respond* to failure conditions, you need to write your own code. In the RPG Interface demo, there are two battle layouts, one that is more appropriate for 4:3 resolutions, and another that works better in 16:9. The custom FlxUIState for that state will check failure conditions on load, and set the mode depending on which layout works best. Speaking of modes...

####8. \<mode>
Specifies UI "modes" that you can switch between. Basically just a glorified way of quickly hiding and showing specific assets. For instance, in Defender's Quest we had four states for our save slots - empty, play, new_game+ (New Game+ eligible), and play+ (New Game+ started). This would determine what buttons were visible ("New Game", "Play", "Play+", "Import", "Export"). 

The "empty" and "play" modes might look like this:

````
<mode id="empty">
	<show id="new_game"/>
	<show id="import"/>
			
	<hide id="space_icon"/>
	<hide id="play_big"/>
	<hide id="play_small"/>
	<hide id="play+"/>
	<hide id="export"/>
	<hide id="delete"/>
	<hide id="name"/>
	<hide id="time"/>
	<hide id="date"/>
	<hide id="icon"/>
	<hide id="new_game+"/>
</mode>
		
<mode id="play">
	<show id="play_big"/>
	<show id="export"/>
	<show id="delete"/>
	<show id="name"/>
	<show id="time"/>
	<show id="date"/>
	<show id="icon"/>			
			
	<hide id="play_small"/>
	<hide id="play+"/>
	<hide id="import"/>
	<hide id="new_game"/>
	<hide id="new_game+"/>
</mode>
````

The only tags available in a **\<mode>** element are \<hide> and \<show>, which each only take id as an attribute. They just toggle the "visible" property on and off. 

##List of Widgets

* **Image, vanilla** (FlxUISprite) - \<sprite>
* **9-slice sprite/chrome** (FlxUI9SliceSprite) - \<9slicesprite> or \<chrome>
* **Button, vanilla** (FlxUIButton) - \<button>
* **Button, toggle** (FlxUIButton) - \<button_toggle>
* **Check box** (FlxUICheckBox) - \<checkbox>
* **Text, vanilla** (FlxUIText) - \<text>
* **Text, input** (FlxUIInputText) - \<input_text>
* **Radio button group** (FlxUIRadioGroup) - \<radio_group>
* **Tabbed menu** (FlxUITabMenu) - \<tab_menu>
* **Line** (FlxUISprite) - \<line>
* **Numeric Stepper** (FlxUINumericStepper) - \<numeric_stepper>
* **Dropdown/Pulldown Menu** (FlxUIDropDownMenu) - \<dropdown_menu>

Lets go over these one by one.

###1. Image (FlxUISprite)

**\<sprite>**

Just a regular sprite, with a fixed size.

Attributes:
* x/y
* src (path to source, no extension, appended to "assets/gfx/")
* use_def (definition id)
* group (group id)

###2. 9-slice sprite/chrome (FlxUI9SliceSprite)

**\<9slicesprite> or \<chrome>**

A 9-slice sprite can be scaled in a more pleasing way than just stretching it directly. It divides the object up into a user-defined grid of 9 cells, (4 corners, 4 edges, 1 interior), and then repositions and scales those individually to construct a resized image. Works best for stuff like chrome and buttons.

Attributes:
* x/y, use_def, group
* src
* width/height
* slice9 - string, two points that define the slice9 grid, format "x1,y1,x2,y2". For example, "6,6,12,12" works well for the 12x12 chrome images in the demo project.
* tile - bool, optional (assumes false if not exist). If true, uses tiling rather than scaling for stretching 9-slice cells. Boolean true == "true", not "True" or "TRUE", or "T".
* smooth - bool, optional (assumes false if not exist). If ture, ensures the scaling uses smooth interpolation rather than nearest-neighbor (stretched blocky pixels).

###3. Button (FlxUIButton)
**\<button>**

Just a regular clicky button, optionally with a label.

Attributes:
* x/y, use_def, group
* width/height - optional, only needed if you're using a 9-slice sprite as source
* text_x/text_y - label offsets
* label - text to show
* active - whether the button should be active initially
* visible - whether the button should be visible initially

Child tags:
* \<text> - just like a regular \<text> node
* \<param> - parameter to pass to the callback/event system (details below)
* \<graphic> - graphic source (details below)

#####3.1 Button Parameters

A **\<param>** tag takes two attributes: **type** and **value**. 

**type**: "string", "int", "float", and "color" or "hex" for e.g: "0xFF00FF"

**value**: the value, as a string. The type attribute will ensure it typecasts correctly.

````
<button id="new_game" use_def="big_button_gold" x="594" y="11" group="top" label="New Game">
	<param type="string" value="new"/>
</button>
````

You can add as many <param> tags as you want. When you click this button, it will by default call FlxUI's internal public static event callback:

````
FlxUI.event(CLICK_EVENT, this, null, params);
````

This, in turn, will call getEvent() on whatever IEventGetter "owns" this FlxUI object. In the default setup, this is your FlxUIState. So extend this function in your FlxUIState:

````
getEvent(id:String,sender:Dynamic,data:Dynamic):Void
````
The "sender" parameter will be the widget that originated the event -- in this case, the button. On a FlxUIButton click, the other parameters will be:

* **event id**: "click_button" (ie, FlxUITypedButton.CLICK_EVENT)
* **data**: null
* **params**: an **Array\<Dynamic>** containing all the parameters you've defined.

Some other interactive widgets can take parameters, and they work in basically the same way.

#####3.2 Button Graphics

Graphics for buttons can be kinda complex. You can put in multiple graphic tags, one for each button state you want to specify, or just one with the id "all" that combines all the states into one vertically stacked image, and asks the FlxUIButton to sort the individual frames out itself.

The system can sometimes infer what the frame size should be based on the image and width/height are not set, but it helps to be explicit with width/height if they are statically sized and you're not using 9-slice scaling.

Static, individual frames:
````
<definition id="button_blue" width="96" height="32">
	<graphic id="up" image="ui/buttons/static_button_blue_up"/>
	<graphic id="over" image="ui/buttons/static_button_blue_over"/>
	<graphic id="down" image="ui/buttons/static_button_blue_down"/>
</definition>
````

9-slice scaling, individual frames:
````
<definition id="button_blue" width="96" height="32">
	<graphic id="up" image="ui/buttons/9slice_button_blue_up" slice9="6,6,12,12"/>
	<graphic id="over" image="ui/buttons/9slice_button_blue_over slice9="6,6,12,12""/>
	<graphic id="down" image="ui/buttons/9slice_button_blue_down slice9="6,6,12,12""/>
</definition>
````

9-slice scaling, all-in-one frame:
````
<definition id="button_blue" width="96" height="32">
	<graphic id="all" image="ui/buttons/button_blue_all" slice9="6,6,12,12"/>
<definition>
````

I'm not 100% sure what will happen if you do individual frames and omit one, but I think I set it up to copy one of the other ones in some kind of "smart" way. Again, it's always best to be explicit about what you want rather than be ambiguous and have the system guess.

#####3.3 Button Text
To specify what the text in a button looks like, you create a \<text> child node.
You can specify all the properties right here, or use a definition. There's a few special considerations for <text> nodes inside of a button.

The main "color" attribute (hexadecimal format, "0xffffff") is the main label color

If you want to specify colors for other states, you add \<color> tags inside the \<text> tag for each state:
````
<button x="200" y="505" id="some_button" use_def="text_button" label="Click Me">
	<text use_def="vera10" color="0xffffff">
		<color id="over" value="0xffff00"/>
	</text>
</button>	
````


###4. Button, Toggle (FlxUIButton) 
**\<button_toggle>**

Toggle buttons are made from the same class as regular buttons, FlxUIButton.

Toggle buttons are different in that they have 6 states, 3 for up/over/down when toggled, and 3 for up/over/down when not toggled. By default, a freshly loaded toggle button's "toggle" value is false.

Toggle buttons need more graphics than a regular button. To do this, you need to provide graphic tags for both the regular and untoggled states. The toggled \<graphic> tags are the same, they just need an additional toggle="true" attribute:

````
<definition id="tab_button_toggle" width="50" height="20" text_x="-2" text_y="0">			
	<text use_def="sans10c" color="0xcccccc">
		<color id="over" value="0xccaa00"/>
		<color id="up" toggle="true" value="0xffffff"/>
		<color id="over" toggle="true" value="0xffff00"/>
	</text>
		
	<graphic id="up" image="ui/buttons/tab_grey_back" slice9="6,6,12,12"/>
	<graphic id="over" image="ui/buttons/tab_grey_back_over" slice9="6,6,12,12"/>
	<graphic id="down" image="ui/buttons/tab_grey_back_over" slice9="6,6,12,12"/>
		
	<graphic id="up" toggle="true" image="ui/buttons/tab_grey" slice9="6,6,12,12"/>
	<graphic id="over" toggle="true" image="ui/buttons/tab_grey_over" slice9="6,6,12,12"/>				
	<graphic id="down" toggle="true" image="ui/buttons/tab_grey_over" slice9="6,6,12,12"/>				
</definition>
````

Of course, if you create a single asset with 6 images stacked vertically, you can save yourself some room:
````
<definition id="button_toggle" width="50" height="20">		
	<text use_def="sans10c" color="0xffffff">
		<color id="over" value="0xffff00"/>
	</text>
	
	<graphic id="all" image="ui/buttons/button_blue_toggle" slice9="6,6,12,12"/>		
</definition>
````

Note that you can create a vertical stack of 9-slice assets, or regular statically-sized assets, the system can use either one. 

###5. Check box (FlxUICheckBox)
**\<checkbox>**

A Check Box is a FlxUIGroup which contains three objects: a "box" image, a "check" image, and a label.

Attributes:
* x/y, use_def, group
* check_src - source image for box (not 9-sliceable)
* box_src - source image for check mark (not 9-sliceable)
* text_x/text_y - label offsets
* label - text to show

Child tags:
* \<text> - same as \<button>
* \<param> - same as \<button>

Event:
* id - "click_checkbox"
* params - as defined by user, but with this at the end: "checked:true" or "checked:false"

###6. Text (FlxUIText)
**\<text>**

A regular text field. 

Attributes:
* x/y, use_def, group
* font - string, something like "vera" or "verdana"
* size - integer, size of font
* style - string, "regular", "bold", "italic", or "bold-italic"
* color - hex string, ie, "0xffffff" is white
* shadow - shadow color (not working yet maybe?)
* align - "left", "center", or "right". Haven't tested "justify"

The system will look for a font file in your assets/fonts/ directory, formatted like this:

|Filename|Family|Style|
|---|---|---|
vera.ttf|Vera|Regular
verab.ttf|Vera|Bold
verai.ttf|Vera|Italic
veraz.ttf|Vera|Bold-Italic

So far just .ttf fonts are supported, and you MUST name them according to this scheme (for now at least).

FlxUI does not yet support FlxBitmapFonts, but we'll be adding it soon.

###7. Text, input (FlxUIInputText)
**\<input_text>**

This has not been thoroughly tested, but it exists.

Attributes:
* x/y, use_def, group
* font - string, something like "vera" or "verdana"
* size - integer, size of font
* style - string, "regular", "bold", "italic", or "bold-italic"
* color - hex string, ie, "0xffffff" is white
* shadow - shadow color (not working yet maybe?)
* align - "left", "center", or "right". Haven't tested "justify"
* password_mode - bool, hides text if true

The system will look for a font file in your assets/fonts/ directory, formatted like this:

|Filename|Family|Style|
|---|---|---|
vera.ttf|Vera|Regular
verab.ttf|Vera|Bold
verai.ttf|Vera|Italic
veraz.ttf|Vera|Bold-Italic

So far just .ttf fonts are supported, and you MUST name them according to this scheme (for now at least).

FlxUI does not yet support FlxBitmapFonts, but we'll be adding it soon.


###8. Radio button group (FlxUIRadioGroup)
**\<radio_group>**

Radio groups are a set of buttons where only one can be clicked at a time. We implement these as a FlxUIGroup of FlxUICheckBox'es, and then internal logic makes only one clickable at a time. 

Attributes:
* x/y, use_def, group
* radio_src - image src for radio button back (ie, checkbox "box")
* dot_src - image src for radio dot (ie, checkbox "check mark")
* \<param> - same as \<button>, 
* \<radio> - two attributes, id (string) and label (string)

You construct a radio group by providing as many \<radio> tags as you want radio buttons. Give each of them an id and a label.

Child Nodes:

Event:
* id - "click_radio_group"
* params - as defined by user

###9. Tabbed menu (FlxUITabMenu)
**\<tab_menu>**

Tab menus are the most complex FlxUI widget. FlxUITabMenu extends FlxUI and is thus a full-fledged FlxUI in and of itself, just like the \<layout> tag.

This provides a menu with various tabbed buttons on top. When you click on one tab, it will show the content for that tab and hide the rest. 

Attributes:
* x/y, use_def, group
* width/height
* back_def - id for a 9-slice chrome definition (MUST be 9-sliceable!)
* slice9

Child Nodes:
* \<tab> - attributes are "id" and "label", much like in \<radio_group>
* \<group> - attributes are only "id"
 * Put regular FlxUI content tags here, within the \<group>\</group> node.

##Dynamic position & size

###1. Anchor Tags

Here's an example of a health bar from an RPG:
````
<9slicesprite id="health_bar" x="10" y="5" width="134" height="16" use_def="health">
	<anchor x="portrait.right" y="portrait.top" x-flush="left" y-flush="top"/>
</9slicesprite>
````

There is presumably another sprite defined somewhere called "portrait" and we want our health bar to show up relative to wherever that is. 

The anchor's x and y specify a specific point, and x-flush and y-flush specify which corner of the object should be aligned to that point. The main object's x / y will be added on as offsets after the object is flushed to the anchor.

Acceptable values for x/y:
* "left", "right", "top", or "bottom": edges of the flixel canvas.
* object properties (ie, "some_id.some_property"):
 * "left", "right", "top", "bottom": edges of that object
 * "center": center of that object (axis inferred from x or y attribute)

Acceptable values for x-flush/y-flush:
* "left"
* "right"
* "top"
* "bottom"
* "center"

**Note to non-native speakers of English:** "flush" is a carpentry term, so if one side of one object is parallel to and touching another object's side with no air between them, the objects are "flush." This has nothing to do with toilets :)

--
###2. Size Tags

Let's add a size tag to our health bar:
````
<9slicesprite id="health_bar" x="10" y="5" width="134" height="16" use_def="health" group="mcguffin">
	<anchor x="portrait.right" y="portrait.top" x-flush="left" y-flush="top"/>
	<exact_size width="stretch:portrait.right+10,right-10"/>
</9slicesprite>
````

There are three size tags: **\<min_size>**, **\<max_size>**, and **\<exact_size>**

This lets you either specify dynamic lower/upper bounds for an object's size, or force it to be an exact size. This lets you create a UI that can work in multiple resolutions, or just avoid having to do manual pixel calculations yourself. 

Size tags take only two attributes, **width** and **height**. If one is not specified, it is ignored.

There are several ways to formulate a width/height attribute:

* **number** (ie, width="100")
* **stretch** (ie, width="stretch:some_value,another_value")
* **reference** (ie, width="some_id.some_value")

A **stretch** formula will tell FlxUI to calculate the difference between two values separated by a comma. These values are formatted and calculated just like a **reference** formula. The axis is inferred from whether it's width or height. 

So, if you have a scoreboard at the top of the screen, and you want the playfield to stretch from the bottom of the scoreboard to the bottom of the screen:

````
<exact_size height="stretch:scoreboard.bottom,bottom"/>
````

Acceptable property values for reference formula, used alone or in a stretch:
* **naked reference** (ie, "some_id") - returns inferred x or y position of that thing.
* **property reference** (ie, "some_id.some_value") - returns a thing's property
 * "left", "right", "top", "bottom", "width", "height"
 * _NOTE: "center" not yet implemented for size formulas!_
* **arithmetic formula** (ie, "some_id.some_value+10") - do some math
 * You can tack on **one** operator and **one** _numeric_ operand to any of the above.
 * Legal operators = (+, -, *, \, ^)
 * Don't try to get too crazy here. If you need to do some super duper math, just add some code in your FlxUIState, call getAsset("some_id") to grab your assets, and do the craziness yourself.

--
###3. Alignment Tags

This is still very experimental and I don't have a live example in the RPG Interface demo yet. 

An \<align> tag lets you automatically align and space various objects together.

````
<align axis="horizontal" spacing="2" resize="true">
	<bounds left="options.left" right="options.right"/>
	<objects value="spell_0,spell_1,spell_2,spell_3,spell_4,spell_5"/>
</align>
		
````

Attributes:
* axis - "horizontal" or "vertical"
* spacing - number
* resize - bool, optional, "true" or "false" (if not exist, assumes false)

Child tags:
* \<bounds> - string, reference formula, specify left & right for horizontal, or top & bottom for vertical
* \<objects> - string, comma separated list of object id's
...

##Localization (FireTongue)
First, Firetongue has some [documentation](https://github.com/larsiusprime/firetongue) on its Github page. Read that. 

In your local project, follow these steps:

**1. Create a FireTongue wrapper class**

 It just needs to:
 1. Extend **firetongue.FireTongue**
 2. Implement **flixel.addons.ui.IFireTongue** 
 3. Source is below, "FireTongueEx" [1]

**2. Create a FireTongue instance somewhere**

Add this variable declaration in Main, for instance:
````
public static var tongue:FireTongueEx;
````
Note that it's type is FireTongueEx, not FireTongue. (This way the instance implements IFireTongue, which FlxUI needs).

**3. Initialize your FireTongue instance**

Add this initialization block anywhere in your early setup code (either in Main or in the create() block of your first FlxUIState, for instance):
````
if (Main.tongue == null) {
	Main.tongue = new FireTongueEx();
	Main.tongue.init("en-US");
	FlxUIState.static_tongue = Main.tongue;
}
````
Setting **FlxUIState.static_tongue** will make every FlxUIState instance use this FireTongue instance without any additional setup. If you don't want to use a static reference, you can just do this on a per-state basis:

````
//In the create() function of some FlxUIState object:
_tongue = someFireTongueInstance;	
````

**4. Start using FireTongue flags**

Once a FlxUIState is hooked up to a FireTongue instance, it will automatically attempt to translate any raw text information as if it were a FireTongue flag -- see FireTongue's [documentation](https://github.com/larsiusprime/firetongue).

Here's an example, where the word "Back" is translated via the localization flag "$MISC_BACK":
````
<button center_x="true" x="0" y="535" id="start" label="$MISC_BACK">		
	<param type="string" value="back"/>
</button>
````
In English (en-US) this will be "Back," in Norwegian (nb-NO) this will be "Tilbake."

...

[1] Here's the source code snippet for FireTongueEx.hx:
````
import firetongue.FireTongue;
import flixel.addons.ui.IFireTongue;

/**
 * This is a simple wrapper class to solve a dilemma:
 * 
 * I don't want flixel-ui to depend on firetongue
 * I don't want firetongue to depend on flixel-ui
 * 
 * I can solve this by using an interface, IFireTongue, in flixel-ui
 * However, that interface has to go in one namespace or the other and if I put
 * it in firetongue, then there's a dependency. And vice-versa.
 * 
 * This is solved by making a simple wrapper class in the actual project
 * code that includes both libraries. 
 * 
 * The wrapper extends FireTongue, and implements IFireTongue
 * 
 * The actual extended class does nothing, it just falls through to FireTongue.
 * 
 * @author 
 */
class FireTongueEx extends FireTongue implements IFireTongue
{
	public function new() 
	{
		super();
	}	
}
````
