[![Build status](https://build.appcenter.ms/v0.1/apps/70a55ab5-1147-43c7-8d31-535b26dc90e9/branches/master/badge)](https://appcenter.ms)
# Flex Color Picker
Modern color picker library written in Swift 4 that can be easily extended and customized. Good performance and stable, quality code are its core values.

![Default Flex Color Picker Preview](https://github.com/RastislavMirek/FlexColorPicker/blob/master/GifsAndScreenshots/Flex_color_picker_for_swift_preview1.gif)
![Color Picker with All Controls Preview](https://github.com/RastislavMirek/FlexColorPicker/blob/master/GifsAndScreenshots/Flex_color_picker_for_swift_preview2.gif)

## Supported Use Cases
1. ready-to-use color picker that works great out-of-box
2. agile library that supports components positioning with autolayout and customisation directly from storyboard (or from code)     
3. framework that allows adding your own sliders, palettes &amp; previews or modifying existing ones without changing the code of the library
4. combine 3 approaches above freely to get level of customisation that you need

![Default Color Picker with Rectangular Palette Preview](https://github.com/RastislavMirek/FlexColorPicker/blob/master/GifsAndScreenshots/Flex_color_picker_for_swift_preview3.gif)
![Custom Color Picker Controls Written in Swift Preview](https://github.com/RastislavMirek/FlexColorPicker/blob/master/GifsAndScreenshots/Flex_color_picker_for_swift_preview4.gif)

## Features
- supports HSB and RGB color models, radial and rectangular hue/saturation palette
- there is _great UX_ "just set the delegate" view controller _if you need something simple_
- freely combine, leave out or add your own picker components
- _highly customisable_
- _storyboard support_ with realistic, design time preview and customisation directly from storyboard
- robust, easy to understand code
- well documented*
- works with any controller, no need to subclass some specific controller
- _protocols_ for adding custom picker controls and reusing existing sliders/palettes
- key classes opened &amp ready for extending

* work in progress

## Instalation

### Cocoapods
Add this to your podfile:

    pod FlexColorPicker

You can also try the Demo project with following comand:

    pod try FlexColorPicker

### Direct Instalation
If you do not use Cocoapods clone color picker from repository with this command:

    git clone https://github.com/RastislavMirek/FlexColorPicker

Then open the cloned project in XCode and compile target _FlexColorPicker_. That will create file FlexColorPicker.framework in _Products_ directory of the project. Open project that you want to add color picker to in XCode, select project file, select your application's target on the left side, select _General_ tab and add FlexColorPicker.framework under _Embedded Binaries_ section using little plus button.  

![Working with Color Picker in XCode Storyboard](https://github.com/RastislavMirek/FlexColorPicker/blob/master/GifsAndScreenshots/Working_with_flex_color_picker_from_storyboard.png)

## How to Use
There are several ways how to use FlexColorPicker depending on how much customization you require. The fastest and simplest option is using `DefaultColorPickerViewController`.

### Adding Default Color Picker
In storyboard, FlexColorPicker can be used by specifying _Class_ of a view controler to be `DefaultColorPickerViewController`. That is done in _Identity Inspector_ in right panel under _Custom Class_. Delegate of `DefaultColorPickerViewController` can only be set in code. Same basic customisation of the controller is supported in storyboard via `IBInspectable` properties in [_Atributes Inspector_](https://www.quora.com/Where-is-an-attributes-inspector-in-Xcode).

In code,  `DefaultColorPickerViewController` can be setup like this if using navigation controller:
    
    let colorPickerController = DefaultColorPickerViewController()
    colorPickerController.delegate = self
    navigationController?.pushViewController(colorPickerController, animated: true)

Or when presented modally:

    let colorPickerController = DefaultColorPickerViewController()
    colorPickerController.delegate = self
    let navigationController = UINavigationController(rootViewController: colorPickerController)
    present(navigationController, animated: true, completion: nil)

### Customisation
FlexColorPicker consists of _color controls_ and _color picker controllers_ that manage them. _Color controls_ are (usually) subclasses of [`UIControl`](https://developer.apple.com/documentation/uikit/uicontrol)) that allow user to pick desired color. Predefined _color controls_ include hue/saturation palettes (circular or rectangular), sliders for saturation, brightness and for RGB components and a picked color preview control. Additional can by added by implementing [`ColorControl`](./FlexColorPicker/Classes/Controls/ColorControl.swift) protocol.

If you want to customize your color picker, you can choose and lay out _color controls_ that you want, set their properties if needed and connect them add them to a _color picker controller_. All that can also be done in storyboard. After laying out your _color controls_ and setting their classes  in _Identity Inspector_ set your controller's class to  `CustomColorPickerViewController`, open its _Connection Inspector_ and connect corresponding outlets with controls you have used.

The same can be done from code by simply assigning _color controls_ to appropriate properties of `CustomColorPickerViewController`. If you cannot subclass `CustomColorPickerViewController` e.g. becuase your controller is subclass of another class use `ColorPickerController` instead. It can also be used in storyboard as interface builder custom object. It has same properties as  `CustomColorPickerViewController` (actually, `CustomColorPickerViewController` is just a convinience wrapper for `ColorPickerController`). You can also add  _color controls_ to it via `ColorPickerController.addControl(:)`  so you are not limited to properties.

Once added to a _color picker controller_ (e.g. `ColorPickerController`) a _color control_ will be synchronized with other controls managed by the same controller together selecting single color.

Each _color control_ has some properties (some of them can be set in storyboard) that can be used for customisation of that control's look and feel.
This is the list of bundled _color controls_:

    ColorPreviewWithHex
    RadialPaletteControl
    RectangularPaletteControl
    SaturationSliderControl
    BrightnessSliderControl
    RedSliderControl
    GreenSliderControl
    BlueSliderControl

### Extending & Overriding
FlexColorPicker is made to be tweeked and extended with minimum effort. You can add you own _color control_ by implementing `ColorControl` protocol or extending one of following subclass-ready classes:
- `AbstractColorControl` - aways subclass if you can
- `AdjustedHitBoxColorControl` - if you need extended hit box margin around the control
- `ColorSliderControl` - e.g. if you need sliders for another color model then HSB or RGB
- `ColorPaletteControl` - if you want to create another color palette

However, in many cases there will be no need to subclass `ColorSliderControl` or `ColorPaletteControl`. They can be extended via composition: They relay on their _color delegates_ in how they handle color updates, present themseves and how they interpret user interactions. Therefore, you can instead implement `ColorSliderDelegate` or `ColorPaletteDelegate` protocols respectivelly to change their look and behavior without changing the code of the control itself. You can save significant amount of work this way.  Demo project has good examples on both aproaches (overriding and composition) and their combination, feel free to check it. 

## Tips
When setting up slider controls in storyboard it is a good practise to set its backgound to transparent. Alignment rectangle (rectangle that autolayout uses to lay out the control) is smaller than the actual frame of the slider to allow for extra hit box margin as well as background framing of the slider. Therefore, if background is solid white it can overlap other views close to it. If you do not want this behavior, set Hit Box Inset to 0 in Attributes Inspector or set `hitBoxInset` to `0` in code.

`ColorPreviewWithHex` can be tapped. When it it tapped, `ColorPickerController` calls `ColorPickerDelegate.colorPicker(_:selectedColor:usingControl:)` on its delegate. You can comunicate this feature to your users or opt out by setting `ColorPreviewWithHex.tapToConfirm` to `false`. 

If a _palette color controls_ is added as subview of  `UIScrollView` it might cause issues becuase  _palette color controls_ make use of pan gestures as well as `UIScrollView`. `UIScrollView` will take priority, making any palette control hard to work with. Using `PaletteAwareScrollView` instead of `UIScrollView` to solves that issue.    

## Getting in Touch
If you like it, have a question or want to hire iOS developers shoot me a message at

**[my first name, see profile] _at_ [epytysae spelled backwards] _dot_ [first 4 letters of word information]**

Emails go directly to author FlexColorPicker, cryptic format is just spam bot protection. 

Suggestions, feedback, bug reports & pull requests are very wellcomed.

### Thanks
Visual of slider control was inspired by popular Objective-C library HRColorPicker.  Thank you  for using FlexColorPicker! If you just have 3 seconds to give back, please star this repository.
