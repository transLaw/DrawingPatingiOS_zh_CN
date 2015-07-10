# About Drawing and Printing in iOS

This document covers three related subjects:

* Drawing custom UI views. Custom UI views allow you to draw content that cannot easily be drawn with standard UI elements. For example, a drawing program might use a custom view for the user’s drawing, or an arcade game might use a custom view into which it draws sprites.
* Drawing into offscreen bitmap and PDF content. Whether you plan to display the images later, export them to a file, or print the images to an AirPrint-enabled printer, offscreen drawing lets you do so without interrupting the user’s workflow.
* Adding AirPrint support to your app. The iOS printing system lets you draw your content differently to fit on the page.

Figure I-1  You can combine custom views with standard views, and even draw things offscreen.
![Figure I-1 Image][./img/FI1.png]

## At a Glance

The iOS native graphics system combines three major technologies: UIKit, Core Graphics, and Core Animation. UIKit provides views and some high-level drawing functionality within those views, Core Graphics provides additional (lower-level) drawing support within UIKit views, and Core Animation provides the ability to apply transformations and animation to UIKit views. Core Animation is also responsible for view compositing.

### Custom UI Views Allow Greater Drawing Flexibility
This document describes how to draw into custom UI views using native drawing technologies. These technologies, which include the Core Graphics and UIKit frameworks, support 2D drawing.

Before you consider using a custom UI view, you should make certain that you really need to do so. Native drawing is suitable for handling more complex 2D layout needs. However, because custom views are processor-intensive, you should limit the amount of drawing you do using native drawing technologies.

As an alternative to custom drawing, an iOS app can draw things onscreen in several other ways.

* **Using standard (built-in) views.** Standard views let you draw common user-interface primitives, including lists, collections, alerts, images, progress bars, tables, and so on without the need to explicitly draw anything yourself. Using built-in views not only ensures a consistent user experience between iOS apps, but also saves you programming effort. If built-in views meet your needs, you should read [View Programming Guide for iOS][1].
* **Using Core Animation layers.** Core Animation lets you create complex, layered 2D views with animation and transformations. Core Animation is a good choice for animating standard views, or for combining views in complex ways to present the illusion of depth, and can be combined with custom-drawn views as described in this document. To learn more about Core Animation, read Core Animation Overview.
* **Using OpenGL ES in a GLKit view or a custom view.** The OpenGL ES framework provides a set of open-standard graphics libraries geared primarily toward game development or apps that require high frame rates, such as virtual prototyping apps and mechanical and architectural design apps. It conforms to the OpenGL ES 2.0 and OpenGL ES v1.1 specifications. To learn more about OpenGL drawing, read [OpenGL ES Programming Guide for iOS][2].
* **Using web content.** The [UIWebView][3] class lets you display web-based user interfaces in an iOS app. To learn more about displaying web content in a web view, read [Using UIWebView to display select document types][4] and [UIWebView Class Reference][5].
Depending on the type of app you are creating, it may be possible to use little or no custom drawing code. Although immersive apps typically make extensive use of custom drawing code, utility and productivity apps can often use standard views and controls to display their content.

The use of custom drawing code should be limited to situations where the content you display needs to change dynamically. For example, a drawing app typically needs to use custom drawing code to track the user’s drawing commands, and an arcade-style game may need to update the screen constantly to reflect the changing game environment. In those situations, you should choose an appropriate drawing technology and create a custom view class to handle events and update the display appropriately.

On the other hand, if the bulk of your app’s interface is fixed, you can render the interface in advance to one or more image files and display those images at runtime using the [UIImageView][6] class. You can layer image views with other content as needed to build your interface. You can also use the [UILabel][7] class to display configurable text and include buttons or other controls to provide interactivity. For example, an electronic version of a board game can often be created with little or no custom drawing code.

Because custom views are generally more processor-intensive (with less help from the GPU), if you can do what you need to do using standard views, you should always do so. Also, you should make your custom views as small as possible, containing only content that you cannot draw in any other way, use use standard views for everything else. If you need to combine standard UI elements with custom drawing, consider using a Core Animation layer to superimpose a custom view with a standard view so that you draw as little as possible.

## A Few Key Concepts Underpin Drawing With the Native Technologies

When you draw content with UIKit and Core Graphics, you should be familiar with a few concepts in addition to the view drawing cycle.

* For the [drawRect:][8] method, UIKit creates a graphics context for rendering to the display. This graphics context contains the information the drawing system needs to perform drawing commands, including attributes such as fill and stroke color, the font, the clipping area, and line width. You can also create and draw into custom graphics context for bitmap images and PDF content.
* UIKit has a default coordinate system where the origin of drawing is at the top-left of a view; positive values extend downward and to the right of that origin. You can change the size, orientation, and position of the default coordinate system relative to the underlying view or window by modifying the current transformation matrix, which maps a view’s coordinate space to the device screen.
* In iOS, the logical coordinate space, which measures distances in points, is not equal to the device coordinate space, which measures in pixels. For greater precision, points are expressed in floating-point values.
> Relevant Chapter: [iOS Drawing Concepts][9]

### UIKit, Core Graphics, and Core Animation Give Your App Many Tools For Drawing

The UIKit and Core Graphics have many complementary graphics capabilities that encompass graphics contexts, Bézier paths, images, bitmaps, transparency layers, colors, fonts, PDF content, and drawing rectangles and clipping areas. In addition, Core Graphics has functions related to line attributes, color spaces, pattern colors, gradients, shadings, and image masks. The Core Animation framework enables you to create fluid animations by manipulating and displaying content created with other technologies.

> Relevant Chapters: [iOS Drawing Concepts][9], [Drawing Shapes Using Bézier Paths][10], [Drawing and Creating Images][11], [Generating PDF Content][12]

### Apps Can Draw Into Offscreen Bitmaps or PDFs
It is often useful for an app to draw content offscreen:

* Offscreen bitmap contexts are often used when scaling down photographs for upload, rendering content into an image file for storage purposes, or using Core Graphics to generate complex images for display.
* Offscreen PDF contexts are often used when drawing user-generated content for printing purposes.
After you create an offscreen context, you can draw into it just as you would draw within the `drawRect:` method of a custom view.

> Relevant Chapters: [Drawing and Creating Images][11], [Generating PDF Content][12]

### Apps Have a Range of Options for Printing Content
As of iOS 4.2, apps can print content wirelessly to supported printers using AirPrint. When assembling a print job, they have three ways to give UIKit the content to print:

* They can give the framework one or more objects that are directly printable; such objects require minimal app involvement. These are instances of the NSData, NSURL, UIImage, or ALAsset classes containing or referencing image data or PDF content.
* They can assign a print formatter to the print job. A print formatter is an object that can lay out content of a certain type (such as plain text or HTML) over multiple pages.
* They can assign a page renderer to the print job. A page renderer is usually an instance of a custom subclass of UIPrintPageRenderer that draws the content to be printed in part or in full. A page renderer can use one or more print formatters to help it draw and format its printable content.
> Relevant Chapter: [Printing][13]

### It’s Easy to Update Your App for High-Resolution Screens
Some iOS devices feature high-resolution screens, so your app must be prepared to run on these devices and on devices with lower-resolution screens. iOS handles much of the work required to handle the different resolutions, but your app must do the rest. Your tasks include providing specially named high-resolution images and modifying your layer- and image-related code to take the current scale factor into account.

> Relevant Appendix: [Supporting High-Resolution Screens In Views][14]

## See Also

For complete examples of printing, see the PrintPhoto, Sample Print Page Renderer, and UIKit Printing with UIPrintInteractionController and UIViewPrintFormatter sample code projects.

[1]:https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009503
[2]:https://developer.apple.com/library/ios/documentation/3DDrawing/Conceptual/OpenGLES_ProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008793
[3]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIWebView_Class/index.html#//apple_ref/occ/cl/UIWebView
[4]:https://developer.apple.com/library/ios/qa/qa1630/_index.html#//apple_ref/doc/uid/DTS40008749
[5]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIWebView_Class/index.html#//apple_ref/doc/uid/TP40006950
[6]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/index.html#//apple_ref/occ/cl/UIImageView
[7]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/index.html#//apple_ref/occ/cl/UILabel
[8]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/drawRect:
[9]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html#//apple_ref/doc/uid/TP40010156-CH14-SW1
[10]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/BezierPaths/BezierPaths.html#//apple_ref/doc/uid/TP40010156-CH11-SW1
[11]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/HandlingImages/Images.html#//apple_ref/doc/uid/TP40010156-CH13-SW1
[12]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GeneratingPDF/GeneratingPDF.html#//apple_ref/doc/uid/TP40010156-CH10-SW1
[13]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/Printing/Printing.html#//apple_ref/doc/uid/TP40010156-CH12-SW1
[14]:https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/SupportingHiResScreensInViews/SupportingHiResScreensInViews.html#//apple_ref/doc/uid/TP40010156-CH15-SW1
[image-1]:./img/FI1.png