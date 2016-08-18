# SwiftMessages

[![Twitter: @TimothyMoose](https://img.shields.io/badge/contact-@TimothyMoose-blue.svg?style=flat)](https://twitter.com/TimothyMoose)
[![Version](https://img.shields.io/cocoapods/v/SwiftMessages.svg?style=flat)](http://cocoadocs.org/docsets/SwiftMessages)
[![License](https://img.shields.io/cocoapods/l/SwiftMessages.svg?style=flat)](http://cocoadocs.org/docsets/SwiftMessages)
[![Platform](https://img.shields.io/cocoapods/p/SwiftMessages.svg?style=flat)](http://cocoadocs.org/docsets/SwiftMessages)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

SwiftMessages is an iOS library for displaying brief messages in the form of a status bar across the top or bottom of the screen.

In addition to providing numerous layouts, themes and configuration options, SwiftMessages allows you to fully customize the view:

* Copy one of the included nib files into your project and change it.
* Subclass `MessageView` and add elements, etc.
* Or just supply an arbitrary instance of `UIView`.

Try exploring [the demo app](./Demo/Demo.xcworkspace) to get a feel for the extensive configurability of SwiftMessages.

<p align="center">
  <img src="./Demo/demo.png" />
</p>

## Installation

### CocoaPods

Add the following line to your Podfile:

````
pod 'SwiftMessages'
````

### Carthage

Add the following line to your Cartfile:

````
github "SwiftKickMobile/SwiftMessages"
````

## Usage

### Basics

````swift
SwiftMessages.show(view: myView)
````

Although you can show any instance of `UIView`, SwiftMessages provides a `MessageView` class
and assortment of nib-based layouts that should handle most cases:

````swift
// Instantiate a message view from the provided card view layout. SwiftMessages searches for nib
// files in the main bundle first, so you can easily copy them into your project and make changes.
let view = MessageView.viewFromNib(layout: .CardView)

// Theme message elements with the warning style.
view.configureTheme(.Warning)

// Add a drop shadow.
view.configureDropShadow()

// Set message title, body, and icon. Here, we're overriding the default warning
// image with an emoji character.
view.configureContent(title: "Warning", body: "Consider yourself warned.", iconText: "🤔")

// Show the message.
SwiftMessages.show(view: view)
````

You may wish to use the view provider variant `show(viewProvider:)` to ensure that
your UIKit code is executed on the main queue:

````swift
SwiftMessages.show {
    let view = MessageView.viewFromNib(layout: .CardView)
    // ... configure the view
    return view
}
````

The `SwiftMessages.Config` struct provides numerous configuration options that can be passed to `show()`:

````swift
var config = SwiftMessages.Config()

// Slide up from the bottom.
config.presentationStyle = .Bottom

// Display in a window at the specified window level: UIWindowLevelStatusBar
// displays over the status bar while UIWindowLevelNormal displays under.
config.presentationContext = .Window(windowLevel: UIWindowLevelStatusBar)

// Disable the default auto-hiding behavior.
config.duration = .Forever

// Dim the background like a popover view. Hide when the background is tapped.
config.dimMode = .Gray(interactive: true)

// Disable the interactive pan-to-hide gesture.
config.interactiveHide = false

// Specify a status bar style to if the message is displayed directly under the status bar.
config.preferredStatusBarStyle = .LightContent

SwiftMessages.show(config: config, view: view)
````

### Customization

`MessageView` provides the following UI elements:

* __Title__ (`UILabel`)
* __Message body__ (`UILabel`)
* __Icon__ (both `UIImageView` and `UILabel` variants)
* __Button__ (`UIButton`)

All of these are defined as optional `@IBOutlets`, so you can freely omit the ones you don't need. The easiest way to customize `MessageView` is to drag-and-drop one of the provide nib files into your project and make changes. When using one of the `UIStackView`-based layouts as a starting point, you can simply delete elements from the nib file or hide them — no need to adjust the Auto Layout constraints.

`MessageView` provides some convenience methods for creating the included layouts while `SwiftMessages` provides additional, more generic nib loading methods:

````swift
// Instantiate MessageView from one of the provided nibs in a type-safe way.
// SwiftMessages searches the main bundle first, so you easily copy the nib into
// your project and modify it while still using this type-safe call.
let view = MessageView.viewFromNib(layout: .CardView)

// Instantiate MessageView from a named nib.
let view: MessageView = try! SwiftMessages.viewFromNib(named: "MyCustomNib")

// Instantiate MyCustomView from a nib named MyCustomView.nib.
let view: MyCustomView = try! SwiftMessages.viewFromNib()
````

`MessageView` provides optional block-based tap handler for the button and another for the view itself:

````swift
// Hide when button tapped
messageView.buttonTapHandler = { _ in SwiftMessages.hide() }

// Hide when message view tapped
messageView.tapHandler = { _ in SwiftMessages.hide() }
````

### Message Queueing

You can call `SwiftMessages.show()` as many times as you like. SwiftMessages maintains a queue shows messages in order, one at a time. If your view implements the `Identifiable` protocol (as `MessageView` does), duplicate messages are removed. The pause between messages can be adjusted:

````swift
SwiftMessages.pauseBetweenMessages = 1.0
````

There are a few ways to hide messages programatically:

````swift
// Hide the current message.
SwiftMessages.hide()

// Or hide the current message and clear the queue.
SwiftMessages.hideAll()

// Or for a view that implements `Identifiable`:
SwiftMessages.hide(id: someId)
````

Multiple instances of `SwiftMessages` can be used to show more than one message at a time. Note that the static `SwiftMessages.show()` and other static APIs on `SwiftMessage` are just convenience wrappers around the shared instance `SwiftMessages.sharedInstance`):

````swift
let otherMessages = SwiftMessages()
SwiftMessages.show(...)
otherMessages.show(...)
````

## License

SwiftMessages is distributed under the MIT license. [See LICENSE](./LICENSE.md) for details.