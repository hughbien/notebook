# Your First Swift App

How to build an iOS app with Swift by Ash Furrow.

These notes will cover iOS framework related pieces. For Swift language related notes, see 
`swift_programming_language.md`.

# Introduction

We're building "Coffee Timer" in this book. Start by creating a new Xcode project -> iOS -> 
Application -> Single View Application. Use the name "Coffee Timer".

At anytime, hit `Cmd-1` to open the Project Navigator. Select `AppDelegate.swift`.

    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate {

`@UIApplicationMain` indicates the entry point for the app.

Every app has a single app delegate -- it's responsible for being notified about important app
events, such as finish launch or quitting.

    func application(application: UIApplication, 
                     didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
      return true
    }

The stubbed methods are all named extensively, so you'll know exactly what they do. Except for
`applicationWillResignActive()`. This is called when an app is in the foreground but isn't being
actively used by the user (eg. when someone double taps their Home Button to open the multitasking
interface).

Feel free to add logging information in each method with `println`, then re-run the app to play
with it.

`MainStoryboard.storyboard` contains the UI for your app. Hit `Cmd-Alt-3` for the Identity Inspector
and click on the View Controller. You'll see a "Custom Class" field set as `ViewController`. That
corresponds to a file in Xcode which was automatically created for us.

    class ViewController: UIViewController {
      override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .orangeColor() // type inference FTW! -> UIColor.orangeColor()
      }
      // ...
    }

`viewDidLoad` is where you can customize your view after it's loaded from storyboard but before it's
displayed to the user. Here we change the background to orange.

Another important file is `Images.xcassets` which contains all of your image assets. `AppIcon` should
already be defined, which is our default app icon.

The above files will be where you work 99% of the time. There's a "Supporting Files" group which
includes behind the scenes action. See `Info.plist` for metadata about our app.

# Views and Storyboards

# View Controllers

# UITableView

# Interactivity

# Key-Value Observation

# Memory Management

# Application Design

# Localization

# Core Data

# App Submission
