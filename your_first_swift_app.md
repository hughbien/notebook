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

Storyboards are used to construct and arrange views/interfaces. Views have a hierarchy, every view
has a `superview` and an array of `subviews`. Views are positioned via a `frame`, which is a struct
of type `CGRect`, which is made up of `origin` and `size`. The `origin` is a `CGPoint` with `x` and
`y` values. The `size` is a `CGSize` with `width` and `height` values.

The `origin` is usually the top left corner of the view. `x` and `y` denotes the distance of the
view's origin from the superview's origin. `width` and `height` will tell you the view's size.

Open Storyboard and hit `Cmd-Opt-3` for the object library. Drag a button onto the view and open
the size inspector `Cmd-Opt-5`. Play with the origin/size values. Our view now has a single subview.

Storyboard can also be used to connect our views to code. Open Assistant Editor by clicking the
middle icon in the top right bar. `Ctrl-click` on the button and drag over to your code, below the
`didReceiveMemoryWarning` method. Select `Action`, name it `buttonWasPressed`, event should be
"Touch Up Inside", and choose `Sender` for arguments.

    // ViewController is the target, buttonWasPressed is the action or target action
    @IBAction func buttonWasPressed(sender: AnyObject) { // sender is our button
      println("Button was pressed.")
    }

You may also need a reference to views in your controllers. This is what outlets are for. You can
`Ctrl-click` and drag any view into your code to create an outlet. Try it with a label:

    @IBOutlet weak var label: UILabel! // weak will be explained in Chapter 7

Both IBActions and IBOutlets do not have compile time safety. If you remove/rename a connection in
code, be sure to update it in Storyboard also.

    @IBAction func buttonWasPressed(sender: AnyObject) {
      let date = NSDate()
      label.text = "Button pressed at \(date)"
    }

# View Controllers

iOS controllers are "view controllers" and control view hiearchies: it controls a view and all of its
subviews using outlets.

In Xcode, choose File -> New -> File -> Cocoa Touch -> Cocoa Touch Class. Subclass NSObject and
name your class TimerModel.

    class TimerModel: NSObject {
      var coffeeName = ""
      var duration = 0
    
      override var description: String {
        get {
          return "TimerModel(\(coffeeName))"
        }
      }
    
      init(coffeeName: String, duration: Int) {
        self.coffeeName = coffeeName
        self.duration = duration
        super.init()
      }
    }

Note the description property can also be written in shorthand since it's read-only:

    override var description: String {
      return "TimerModel(\(coffeeName))"
    }

Common functions in UIViewController to override include:

* viewDidLoad()
* viewWillAppear(animated: Bool)
* viewWillDisappear(animated: Bool)
* viewDidAppear(animated: Bool)
* viewDidDisappear(animated: Bool)
* viewWillTransitionToSize(: CGSize, coordinator: UIViewControllerTransitionCoordinator!)

In ViewController:

    var timerModel: TimerModel!
    
    func setupModel() {
      timerModel = TimerModel(coffeeName: "Colombian Coffee", duration: 240)
    }
    
    override func viewDidLoad() {
      super.viewDidLoad()
      setupModel()
    }
    
    func updateUserInterface() {
      label.text = timerModel.coffeeName
    }

`updateUserInterface` isn't being called anywhere yet. Luckily in Swift we can use observers:

    var timerModel: TimerModel! {
      willSet(newModel) {
        println("About to change model to \(newModel)")
      }
     
      didSet {
        updateUserInterface()
      }
    }

ViewControllers can also contain other ViewControllers. You'll se this with `UITabBarController`
and `UINavigationController`. These controllers are used in the iPhone's Clock app and Settings
app. It's common to put navigation controllers within tab bar controllers, but don't do it the
other way around. Tab bars sit at a higher level.

# UITableView

# Interactivity

# Key-Value Observation

# Memory Management

# Application Design

# Localization

# Core Data

# App Submission
