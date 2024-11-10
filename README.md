# Figma to Godot Experiment v1
An experimental Figma json importer that auto-builds nodes in Godot. 

+ <ins>**It is**</ins> a project to enable a jump-start on UI design and build within the Godot Engine. This is an attempt to mimic Figma or similar design programs user experience in Godot.
+ <ins>**It is not**</ins> a program that will automatically build a functional app from Figma.

### Support and Development
+ This is currently a for-fun personal project. I will be updating and working on making this a more flexible plugin in my free time. If you would like to contract me to update or customize this for your project or organization, contact Nate at mightymochigames@gmail.com.
+ General info / communication Discord: https://discord.gg/4JsqksKMhg

### Compatibility
+ The importer does not support vectors/polygons/stars/arrows, but it will add a frame in it's place. To use these types of images you will need to export them separately and place them manually within Godot. When an image is missing or unsupported you will see the image below:
  + ![Screenshot of the Figma Importer Inspector](/doc_images/errorTexture.png)

+ Figma image crop. Godot will place the image but you will need to re-crop within the frame.
+ Gradients. I am using Godot gradient textures and they do not support the squash and stretch of radial gradients.
+ Shadows. Figma shadows have more settings than Godot StyleBox shadows support.

### Disclamer
+ Game performance has not been tested. There may be, and probably is, more optimized ways of building UI elements depending on the game or application. Use the importer and related classes to jump start your development. 
+ I have not yet accounted for all error handling.

## Exporting from Figma
### Data Export
Exporting the necessary data from figma requires this plugin: https://github.com/yagudaev/figma-to-json

Follow the plugin instructions to export a json into your Godot project. This plugin requires the desktop app. 

### Image Export
To export images using the necessary hash names for the importer, use this plugin: https://www.figma.com/community/plugin/1070707193730369068/tojson

It will export a zip file of images and json. Discard that json as the data is not sufficient enough for my importer.

Place all of the exported images into single folder in your Godot project. Occasionally the images exported may be seen as corrupt by Godot. Simply open those files in an image editor and resave them with the same file name. Currently the importer is only set up for PNG image files.

### Fonts
Either copy fonts from your system or download them for a web source. Add them to a single folder within your Godot project. The font files must be named to match the font name in the JSON export file. 

For example the font file name is "Inter_Bold.ttf" to match the code below:
```
"fontName": {
  "family": "Inter",
  "style": "Bold"
}
```

## Using the Godot Importer
### Importer
+ Create a User Interface Scene, or a 2D scene with a Control node.
+ Attach the FigmaImporter.gd script to the control node in which you want to place the imported content.

![Screenshot of the Figma Importer Inspector](/doc_images/FigmaJsonImporterInspector.PNG)

+ <ins>**Document Json File:**</ins> Look at the Inspector under "Figma JSON Import." Select the json you exported from Figma.
+ <ins>**Process Json:**</ins> Click the Process Json "On" button to load the list of pages.
+ <ins>**Select Page:**</ins> Select the page from your Figma document you want to load.
+ <ins>**Select Frame:**</ins> Select the Frame from the selected page you want to import. Currently the importer will display a list of top level frames.
+ <ins>**Import Options: Fonts Folder:**</ins> Select the folder to where you copied your fonts. Leaving this empy will result in using Godot's default font.
+ <ins>**Import Options: Images Folder:**</ins> Select the folder to where you copied your images. Leaving this empty will result in images not being imported. Selecting a folder will automatically check the Auto Place Images option.
+ <ins>**Import Options: Auto Place Images:**</ins> Select or deselect if you want the importer to automatically import images. This option is unavailable if you have not selected an Images Folder.
+ <ins>**Import Options: Component Json Dictionary:**</ins> *Not required. This is an advanced Feature, more details below.* Select the json file with the details of which instantiated scene should be used with specific frame ids.
+ <ins>**Import Options: Comp Inst to Scenes:**</ins> Set to On to process Figma instances as instantiated Godot scenes using Component Json Dictionary as reference.
+ <ins>**Import Layouts: Import Frames:**</ins> Click On to run the importer.

### Frame Types Explained
I created "Frame" node classes to mimic the functionality of Figma layout controls. All of these types of controls exist within Godot, but they are not as straightforward as they are in Figma. These classes are not required to retain a functioning UI so you can detach the scripts as desired.

+ <ins>**Designer Frame**</ins>: Mimics the controls of the standard frame in Figma. It is an extension of Godot's ScrollContainer.
+ <ins>**Designer Image Panel**</ins>: Mimics Figma's image "rectangle." It has the same basic functions as the Designer Frame except it is not intending to contain children nodes. It is an extension of Godot's Panel node.

## Designer Frame Properties
This is a class extension of a ScrollContainer. In Figma, all frames have the potential to become scrollable. To account for this the easiest solution was to use ScrollContainer for any frame that could contain children. ScrollContainers require a single Control child to function properly, and that child houses all other children. Almost all of these custom settings, aside from fill texture and max size, are a repackaging of existing Godot node functions. I have simply consolidated them into one spot.

![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameInspector.PNG)

+ <ins>**Inner Container:**</ins> Select the node that will be the content container for the ScrollContainer. Start with a basic Control. This is important as the Auto Layout functions will change the node type depending on the settings.
+ <ins>**Horizontal and Vertical Anchor:**</ins> Set the anchor positions to determine how the frame will move or scale with the parent node. This differs from the default Godot anchors in that changing the anchors does not change the position or size of the frame/node. 

![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameSizeInspector.PNG)

+ <ins>**Width and Height Size Mode:**</ins> Determines how the node will scale when in an auto layout.
+ <ins>**Min Size:**</ins> The node's smallest size.
+ <ins>**Max Size:**</ins> The node's largest size.
+ <ins>**Frame Rotation:**</ins> A duplication of node rotation for ease of access.
+ <ins>**Center Rotation:**</ins> Centers the rotation/transform pivot point. This will be maintain even when changing the node size.
+ <ins>**Clip Frame Contents:**</ins> Clips the contents of the frame. Note that in Godot clipping will not rotate with the frame/node.
+ <ins>**Scrolling Mode:**</ins> Change the frame scroll direction or whether it scrolls at all.

![Screenshot of the Designer Frame Inspector](/doc_images/stylepaddingFillColor.PNG)

+ <ins>**Break Style Links:**</ins> If you find changing one node is changing others, click this to break the style link. This will duplicate the Stylebox and shader to break it free of others.
+ <ins>**Fill Color:**</ins> Set the fill color.
+ <ins>**Use Solid Fill:**</ins> When placing an image with transparency, this option determines if the fill color appears behind it.
+ <ins>**Fill Gradient:**</ins> Add a gradient. Gradients always appear above the fill color.
+ <ins>**Gradient Behind Image:**</ins> If there is a fill image, you can set whether it appears in front of or behind that image.

![Screenshot of the Designer Frame Inspector](/doc_images/designerFramefillimage.PNG)

+ <ins>**Fill Texture:**</ins> Add a background image to the frame.
+ <ins>**Edge Fill:**</ins> If there is an anti-aliased border, use this option to reduce an unexpected color bleeding.
+ <ins>**Texture Size Mode:**</ins> Godot will repeat the edge of pixels if there is not transparency and image does not fill the frame.
  + Fill: Fills the frame with the image.
  + Fit: Makes sure the image completely fits within the frame.
  + Stretch: Morphs the image to fill the frame edge to edge without cropping.
  + Keep Size: The image size will stay the same size even if you change the frame size.
+ <ins>**Flip X:**</ins>
+ <ins>**Flip Y:**</ins>
+ <ins>**Zoom:**</ins>
+ <ins>**Size Stretch:**</ins>
+ <ins>**Position Offset:**</ins>
+ <ins>**Tint Color:**</ins>

![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameborder.PNG)

+ <ins>**Border Line Weight All:**</ins>
+ <ins>**Border Weights:**</ins>
+ <ins>**Border Color:**</ins>
+ <ins>**Anti Alias Border:**</ins>

![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameCornersPNG.PNG)

+ <ins>**Corner Radius All:**</ins>
+ <ins>**Corner Radius:**</ins>

![Screenshot of the Designer Frame Inspector](/doc_images/designerFramePadding.PNG)

+ <ins>**Padding All:**</ins>
+ <ins>**Padding:**</ins>

![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameShadow.PNG)

+ <ins>**Shadow:**</ins> Self explanatory. There is no blur in Godot shadows.
+ 
![Screenshot of the Designer Frame Inspector](/doc_images/designerFrameAutoLayout.PNG)

+ <ins>**Layout Mode:**</ins>
+ <ins>**Layout Wrap:**</ins>
+ <ins>**H Layout Align:**</ins>
+ <ins>**V Layout Align:**</ins>
+ <ins>**Spacing:**</ins>
+ <ins>**Secondary Spacing:**</ins>
+ <ins>**Auto Space:**</ins>
+ <ins>**Grid Columns:**</ins>
