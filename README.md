<p align="center">
    <img src ="Resources/Logo.png" width="400" />
</p>

<p align="center">
    <a href="https://github.com/danielsaidi/Tutti">
        <img src="https://badge.fury.io/gh/danielsaidi%2FTutti.svg?style=flat" alt="Version" />
    </a>
    <a href="https://cocoapods.org/pods/Tutti">
        <img src="https://img.shields.io/cocoapods/v/Tutti.svg?style=flat" alt="CocoaPods" />
    </a>
    <a href="https://github.com/Carthage/Carthage">
        <img src="https://img.shields.io/badge/carthage-supported-green.svg?style=flat" alt="Carthage" />
    </a>
    <img src="https://img.shields.io/cocoapods/p/Tutti.svg?style=flat" alt="Platform" />
    <img src="https://img.shields.io/badge/Swift-5.0-orange.svg" alt="Swift 5.0" />
    <img src="https://badges.frapsoft.com/os/mit/mit.svg?style=flat&v=102" alt="License" />
    <a href="https://twitter.com/danielsaidi">
        <img src="https://img.shields.io/badge/contact-@danielsaidi-blue.svg?style=flat" alt="Twitter: @danielsaidi" />
    </a>
</p>


## About Tutti

Tutti is a Swift library that can be used to handle user onboarding expericences. It lets you create quick hints as well as single- or multi-screen tutorials.

<p align="center">
    <img src ="Resources/Demo.gif" />
</p>

Tutti only displays a hint/tutorial once by default. This means that even if you tell it to show a tutorial/hint every time an app starts, it will only be shown once. You can reset the display state of a hint/tutorial if you want to display it multiple times.

Tutti also supports user-specific hints/tutorials. Just set the `userId` parameter to the current user's id/name, and Tutti will display the hint/tutorial for every new user.


## Install

### CocoaPods

To install Tutti with CocoaPods, add this to your `Podfile` and run `pod install`:

```
pod 'Tutti'
```

Remember to use the generated workspace (not the project file) after installing.

### Carthage

To install Tutti with Carthage, add this to your `Cartfile`, run `carthage update`:

```
github "danielsaidi/Tutti"
```

Once the update completes, link in the built framework from `Carthage/Build`.

### Manual

To add `Tutti` to your app without using a dependency manager, first clone this repo and place it somewhere in the project folder, then add `Tutti.xcodeproj` to your project, select your app target and add the Tutti framework as an embedded binary under `General` and as a target dependency under `Build Phases`.


## Example App

This repository contains an example app. To try it out, open the `Tutti` project and run the `TuttiExample` target. The example app lets you try different types of hints and tutorials.


## Hints

Tutti comes with a set of built-in hint types that can be used for different purposes.

* `StandardHint` - This is a basic hint with no specific behavior.
* `DeferredHint` - This type requires a certain number of presentation attempts before it is actually presented.
* `CorrectBehaviorHint` - This type can be used to show a hint when the user is behaving "incorrectly".

You can also create your own custom hint types by implementing the `Hint` protocol.


### Creating a hint

This is the most basic way to create a hint with Tutti:

```swift
func createHint(for userId: String?) -> Hint {
    return StandardHint(
        identifier: "hint",
        title: "This is a hint!",
        text: "This hint will only be displayed once.",
        userId: userId)
}
```

In a large app, you may want to use localized strings and have a more sophisticated setup. Check out the example app for more examples.


### Presenting a hint

In Tutti, hints are just data carriers. They don't know anything about how they are to be presented. To present a hint, you'll use a `hint presenter`, for instance:

```swift
self.presenter = CalloutHintPresenter()     // You must keep a strong reference!
presenter.present(hint: hint, in: self, from: view)
```

This will only have an effect if the hint can actually be presented. This means that you don't have to keep track of if a certain hint has been presented or not. Tutti handles this for you.


### Hint presenters

Tutti comes with a couple of built-in hint presenters:

* `AlertHintPresenter` - This presenter presents hints in a regular `UIAlertController`. You should probably not use it in production apps, since it's not a good experience.
* `CalloutHintPresenter` - This presenter is a copy of `EasyTipView` and presents hints in a callout bubble that can point at any view. You can style these callouts to great extent. Have a look [this styling guide](Appearance) and replace `EasyTipView` with `CalloutView`.

You can also create your own custom presented by implementing the `HintPresenter` protocol.


## Tutorials

Tutti comes with a set of built-in tutorial types that can be used for different purposes.

* `StandardTutorial` - This is a basic tutorial with no specific behavior.
* `DeferredTutorial` - This type requires a certain number of presentation attempts before it is actually presented.
* `CorrectBehaviorTutorial` - This type can be used to show a tutorial when the user behaves "incorrectly".

You can also create your own custom tutorial types by implementing the `Tutorial` protocol.


### Creating a tutorial

This is the most basic way to create a standard tutorial with Tutti:

```swift
func createTutorial(for userId: String?) -> Tutorial {
    return StandardTutorial(
        identifier: "standard",
        pageCount: 2,
        userId: userId)
}
```

You can then use the tutorial's `resourceName(for key: String, at pageIndex: Int)` function to get a suggested name for any kind of resource at any page.

For instance, adding the following localized strings to your app would provide a title and a text for two pages in a tutorial with the id `standard`:

```swift
/*

In Localizable.strings, add this to support the standard tutorial:
    "tutorial_standard_0_title" = "Page 1 title";
    "tutorial_standard_0_text" = "Page 1 text";
    "tutorial_standard_1_title" = "Page 2 title";
    "tutorial_standard_1_text" = "Page 2 text";

*/
```

You can also use the `fromLocalizationWithPageIndicationKey` initializer to automatically resolve the correct number of pages. For instance, using `title` as indication key would resolve two pages for the strings above.


### Presenting a tutorial

In Tutti, tutorials are also just data carriers. They don't know anything about how they are to be presented. To present a tutorial, you'll use a `tutorial presenter`, for instance:

```swift
self.presenter = TutorialViewController(nibName: nil, bundle: nil)   // You must keep a strong reference!
presenter.present(tutorial: tutorial, in: self, from: view)
```

This will only have an effect if the tutorial can actually be presented. This means that you don't have to keep track of if a certain tutorial has been presented or not. Tutti handles this for you.


### Hint presenters

Tutti only comes with a single built-in tutorial presenter: `TutorialViewController`.

This presenter presents tutorials in a modal view controller that takes over the entire screen. To use it, just add a `TutorialViewController.xib` and a `TutorialViewControllerCell.xib` file to your app, then connect your subviews to any outlets provided by `TutorialViewController`. You can also subclass `TutorialViewController` and add as many outlets and triggers as you want.


### TutorialViewController

This tutorial presenter presents tutorials in a modal view controller that takes
over the entire screen.

To use it, just add a `YourCustomTutorialViewController.xib` and a
`YourCustomTutorialViewControllerCell.xib` to your app and set'em up the way you want them
to look.
```swift
let presenter = TutorialViewController(nibName: "YourCustomTutorialViewController", bundle: Bundle.main)
```

The `TutorialViewController` class comes with some outlets, but you can
subclass it and add as many outlets and triggers as you want.  
Creating your own xibs require a bit of work, but you can always copy the xibs from the example app and configure them to look the way you want them to look.

Or if you use build-in template, just initialize `TutorialViewController` with `nil` parameter.
```swift
let presenter = TutorialViewController(nibName: nil, bundle: nil)
```


## Contact me

I hope you like this library. Feel free to reach out if you have questions or if you want to contribute in any way:

* E-mail: [daniel.saidi@gmail.com](mailto:daniel.saidi@gmail.com)
* Twitter: [@danielsaidi](http://www.twitter.com/danielsaidi)
* Web site: [danielsaidi.com](http://www.danielsaidi.com)


## License

Tutti is available under the MIT license. See LICENSE file for more info.


[Carthage]: https://github.com/Carthage/Carthage
[CocoaPods]: https://cocoapods.org/

[Appearance]: https://github.com/teodorpatras/EasyTipView/#-customising-the-appearance-
