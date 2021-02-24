![JatApp Company](sources/images/company-banner.png)

# Swift Style Guide

## Table of Contents

1. [Correctness](#correctness)
1. [Language](#language)
1. [Naming](#naming)
1. [File structure](#file-structure)
1. [Protocol Conformance](#protocol-conformance)
1. [Class Prefixes](#class-prefixes)
1. [Delegates](#delegates)
1. [Use Type Inferred Context](#use-type-inferred-context)
1. [Generics](#generics)
1. [Unused Code](#unused-code)
1. [Minimal Imports](#minimal-imports)
1. [Spacing](#spacing)
1. [Semicolons](#semicolons)
1. [Parentheses](#parentheses)
1. [Multi-line String Literals](#multi-line-string-literals)
1. [Documentation](#documentation)
1. [Comments](#comments)
1. [Type Inference](#type-inference)
1. [Types](#types)
1. [Constants](#constants)
1. [Classes and Structures](#classes-and-structures)
1. [Use of Self](#use-of-self)
1. [Computed Properties](#computed-properties)
1. [Function Declarations](#function-declarations)
1. [Closure Expressions](#closure-expressions)
1. [Optionals](#optionals)
1. [Lazy Initialization](#lazy-initialization)
1. [Arrays](#arrays)
1. [Type Annotation for Empty Arrays and Dictionaries](#type-annotation-for-empty-arrays-and-dictionaries)
1. [Syntactic Sugar](#syntactic-sugar)
1. [Functions vs Methods](#functions-vs-methods)
1. [Free Function Exceptions](#free-function-exceptions)
1. [Memory Management](#memory-management)
1. [Access Control](#access-control)
1. [Control Flow](#control-flow)
1. [Switch Statements and Enum](#switch-statements-and-enum)
1. [Enum and Protocol](#enum-and-protocol)
1. [Ternary Operator](#ternary-operator)
1. [Golden Path](#golden-path)
1. [Failing Guards](#failing-guards)
    1. [Don’t Guard Against Double Negatives](#don’t-guard-against-double-negatives)
1. [Organization and Bundle Identifier](#organization-and-bundle-identifier)
1. [Strings](#strings)
1. [Operators Overloading](#operators-overloading)
1. [Singleton](#singleton)
1. [References](#references)

## Correctness

Strive to make your code compile without warnings. This rule informs many style decisions such as using **#selector** types instead of string literals.

## Language

Use US English spelling to match Apple's API.

<details open><summary><i>Example</i></summary>

<span style="color:rgb(76,175,80)">**Preferred:**</span>

```swift
    let color = "red"
```

<span style="color:rgb(255,68,51)">**Not Preferred:**</span>

```swift
    let colour = "red"
```

</details>

## Naming

Descriptive and consistent naming makes software easier to read and understand. Use the Swift naming conventions described in the API Design Guidelines.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    class RoundAnimatingButton: UIButton {}
```

Not Preferred:

```swift
    class CustomButton: UIButton {}
```

</details>

Include type information in constant or variable names when it is not obvious otherwise.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
class ConnectionTableViewCell: UITableViewCell {
    let personImageView: UIImageView

    let animationDuration: TimeInterval

    /// it is ok not to include string in the ivar name here because it's obvious
    /// that it's a string from the property name
    let firstName: String

    /// though not preferred, it is OK to use `Controller` instead of `ViewController`
    let popupController: UIViewController
    let popupViewController: UIViewController

    /// when working with a subclass of `UIViewController` such as a table view
    /// controller, collection view controller, split view controller, etc.,
    /// fully indicate the type in the name.
    let popupTableViewController: UITableViewController

    /// when working with outlets, make sure to specify the outlet type in the
    /// property name.
    @IBOutlet weak var submitButton: UIButton!
    @IBOutlet weak var emailTextField: UITextField!
    @IBOutlet weak var nameLabel: UILabel!
}
```

Not Preferred:

```swift
class ConnectionTableViewCell: UITableViewCell {
    /// this isn't a `UIImage`, so shouldn't be called image
    /// use personImageView instead
    let personImage: UIImageView

    /// this isn't a `String`, so it should be `textLabel`
    let text: UILabel

    /// `animation` is not clearly a time interval
    /// use `animationDuration` or `animationTimeInterval` instead
    let animation: TimeInterval

    /// this is not obviously a `String`
    /// use `transitionText` or `transitionString` instead
    let transition: String

    /// this is a view controller - not a view
    let popupView: UIViewController

    /// as mentioned previously, we don't want to use abbreviations, so don't use
    /// `VC` instead of `ViewController`
    let popupVC: UIViewController

    /// even though this is still technically a `UIViewController`, this property
    /// should indicate that we are working with a *Table* View Controller
    let popupViewController: UITableViewController

    /// for the sake of consistency, we should put the type name at the end of the
    /// property name and not at the start
    @IBOutlet weak var btnSubmit: UIButton!
    @IBOutlet weak var buttonSubmit: UIButton!

    /// we should always have a type in the property name when dealing with outlets
    /// for example, here, we should have `firstNameLabel` instead
    @IBOutlet weak var firstName: UILabel!
}
```

</details>

**Some key takeaways include:**

- prioritizing clarity over brevity
- using camelCase (not snake_case)
- using uppercase for types (and protocols), lowercase for everything else
- using names based on roles, not types
- beginning factory methods with _make_
- naming methods for their side effects
  - verb methods follow the _-ed_, _-ing_ rule for the non-mutating version
   `z = x.sorted()`, `z = x.appending(y)`
  - noun methods follow the _formX_ rule for the mutating version
   `y.formUnion(z)`, `c.formSuccessor(&i)`
  - boolean types should read like assertions
  >_assertion is a statement that a predicate is always true at that point in code execution_
  - giving the same base name to methods that share the same meaning
  - choosing good parameter names that serve as documentation
  - preferring to name the first parameter instead of including its name in the method name, except as mentioned under Delegates
  - labeling closure and tuple parameters
  - taking advantage of default parameters
- indent getter and setter definitions and property observers.
- don't add modifiers such as internal when they're already the default. Similarly, don't repeat the access modifier when overriding a method.
- hide non-shared, implementation details inside the extension using private access control.

When dealing with an acronym or other name that is usually written in all caps, actually use all caps in any names that use this in code. The exception is if this word is at the start of a name that needs to start with lowercase - in this case, use all lowercase for the acronym.

<details open><summary><i>Example</i></summary>

```swift
    /// "HTML" is at the start of a constant name, so we use lowercase "html"
    let htmlBodyContent: String = "<p>Hello, World!</p>"

    /// Prefer using ID to Id
    let profileID: Int = 1

    /// Prefer URLFinder to UrlFinder
    class URLFinder {
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## File structure

You should not define multiple public/internal types (ie class, struct, enum) in the same file; each type should have its own file.

The following list should be the standard organization of all your Swift files, in this specific order:

Before the type declaration:

_Typealias declarations should precede any other type declaration._

- Private Class/Struct/Enum
- Inside the type declaration:
  - Override Properties
  - Properties (stored, computed)
  - Static/Class Variables
  - Static/Class Methods
  - Init/Deinit
  - Override Methods
  - Instance Methods
- After the type declaration:
  - Extensions

> Using an extension outside your class/struct implementation code, but in the same source file.

Extensions can be written in another file if they expand capabilities of the entity in that file and do not make sense without the entity.

<details open><summary><i>Example</i></summary>

```swift
    extension UITableViewCell {
        class func register(for view: UITableView) {
            view.register(.for(self), forCellReuseIdentifier: String(describing: self))
        }
    }

    extension Array where Element == UITableViewCell.Type {
        func register(for view: UITableView) {
            forEach {
                $0.register(for: view)
            }
        }
    }
```
</details>

Use extensions to organize your code into logical blocks of functionality. Each extension should be set off with a **// MARK: -** comment to keep things well-organized.

When using **// MARK: - whatever**, don't leave a newline after the comment.

<details open><summary><i>Example</i></summary>

```swift
    class Pirate {
        // MARK: - Instance Properties
        private let pirateName: String

        // MARK: - Initialization
        init() {
        }
    }
```

</details>

Each section above should be organized by accessibility:

- Open
- Public
- Internal
- Fileprivate
- Private

> For UIKit view controllers, consider grouping lifecycle, custom accessors, and IBAction in separate class extensions.

**[⬆ back to top](#table-of-contents)**

## Protocol Conformance

In particular, when adding protocol conformance to a model, prefer adding a separate extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

Preferred:

<details open><summary><i>Example</i></summary>

```swift
    class MyViewController: UIViewController {
      // class stuff here
    }

    // MARK: - UITableViewDataSource
    extension MyViewController: UITableViewDataSource {
      // table view data source methods
    }

    // MARK: - UIScrollViewDelegate
    extension MyViewController: UIScrollViewDelegate {
      // scroll view delegate methods
    }
```

Not Preferred:

```swift
    class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
      // all methods
    }
```

</details>

For generics and associated types, use a PascalCase word that describes the generic. If this word clashes with a protocol that it conforms to or a superclass that it subclasses, you can append a Type suffix to the associated type or generic name.

<details open><summary><i>Example</i></summary>

```swift
    class SomeClass<Model> {
    }

    protocol Modelable {
        associatedtype Model
    }
  
    protocol Sequence {
        associatedtype IteratorType: Iterator
    }
```

</details>

As per Apple's API Design Guidelines, a protocol should be named as _nouns_ if they describe what something is doing (e.g. Collection) and using the suffixes _-able_, _-ible_, or _-ing_ if it describes a capability (e.g. Equatable, ProgressReporting). If neither of those options makes sense for your use case, you can add a _Protocol_ suffix to the protocol's name as well. Some example protocols are below.

<details open><summary><i>Example</i></summary>

```swift
    /// here, the name is a noun that describes what the protocol does
    protocol TableViewSectionProvider {
        func rowHeight(at row: Int) -> CGFloat
        var numberOfRows: Int { get }
    }

    /// here, the protocol is a capability, and we name it appropriately
    protocol Loggable {
        func logCurrentState()
    }

    /// suppose we have an `InputTextView` class, but we also want a protocol
    /// to generalize some of the functionality - it might be appropriate to
    /// use the `Protocol` suffix here
    protocol InputTextViewProtocol {
        func sendTrackingEvent()
        func inputText() -> String
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Class Prefixes

Swift types are automatically namespaced by the module that contains them and you should not add a class prefix such as **JA**. If two names from different modules collide you can disambiguate by prefixing the type name with the module name. However, only specify the module name when there is possibility for confusion which should be rare.

<details open><summary><i>Example</i></summary>

```swift
    import SomeModule

    let myClass = MyModule.UsefulClass()
```

</details>

## Delegates

When creating custom delegate methods, an unnamed first parameter should be the delegate source. (UIKit contains numerous examples of this.)

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
    func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

Not Preferred:

```swift
    func didSelectName(namePicker: NamePickerViewController, name: String)
    func namePickerShouldReload() -> Bool
```

</details>

## Use Type Inferred Context

Use compiler inferred context to write shorter, clear code.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let selector = #selector(viewDidLoad)
    view.backgroundColor = .red
    let toView = context.view(forKey: .to)
    let view = UIView(frame: .zero)
```

Not Preferred:

```swift
    let selector = #selector(ViewController.viewDidLoad)
    view.backgroundColor = UIColor.red
    let toView = context.view(forKey: UITransitionContextViewKey.to)
    let view = UIView(frame: CGRect.zero)
```

</details>

## Generics

Generic type parameters should be descriptive, upper camel case names. When a type name doesn't have a meaningful relationship or role, use a traditional single uppercase letter such as T, U, or V

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    struct Stack<Element> { ... }
    func write<Target: OutputStream>(to target: inout Target)
    func swap<T>(_ a: inout T, _ b: inout T)
```

Not Preferred:

```swift
    struct Stack<T> { ... }
    func write<target: OutputStream>(to target: inout target)
    func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

</details>

**[⬆ back to top](#table-of-contents)**

## Unused Code

Unused (dead) code, including Xcode template code and placeholder comments should be removed. Methods simply calls the superclass should also be removed. This includes any empty/unused UIApplicationDelegate methods.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
      return Database.contacts.count
    }
```

Not Preferred:

```swift
    override func didReceiveMemoryWarning() {
      super.didReceiveMemoryWarning()
      // Dispose of any resources that can be recreated.
    }

    override func numberOfSections(in tableView: UITableView) -> Int {
      // #warning Incomplete implementation, return the number of sections
      return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
      // #warning Incomplete implementation, return the number of rows
      return Database.contacts.count
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Minimal Imports

Import only the modules a source file requires. For example, don't import UIKit when importing Foundation will suffice. Likewise, don't import Foundation if you must import UIKit

> Imports should be listed in alphabetical order.

_The exception is for imports that are for testing only; they should be placed at the bottom of the list, **in alphabetical order**_

<details open><summary><i>Example</i></summary>

```swift
    import AFNetworking
    import UIKit
    @testable import MyLibrary
```

</details>

## Spacing

Indent using **4 spaces** rather than tabs to conserve space and help prevent line wrapping.
Put spaces after commas, and before and after operators.
Do not put spaces between parentheses and what they are enclosing.
Avoid uncomfortably long lines with a hard maximum of **80 characters** per line.
_(Xcode->Preferences->Text Editing->Page guide at column: 80 is helpful for this)_

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    foo("bar")
```

Not Preferred:

```swift
    foo( "bar" )
```

</details>

> Method braces and other braces (if/else/switch/while etc.) always open on the same line as the statement but close on a new line. we use the [1TBS style](https://en.m.wikipedia.org/wiki/Indentation_style#1TBS)

There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods. Avoid trailing whitespaces at the ends of lines.

Colons always have no space on the left and one space on the right. Exceptions are the **ternary operator** ? :, **empty dictionary** [:] and **#selector** syntax addTarget(_:action:)

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    class TestDatabase: Database {
      var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
    }

    /// Declaring a function
    func myFunction<T, U: SomeProtocol>(firstArgument: U, secondArgument: T) where T.RelatedType == U {
    }
```

Not Preferred:

```swift
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

</details>

There should be a space before and after a binary operator such as +, ==, or ->.

<details open><summary><i>Example</i></summary>

Preferred:

```swift

    let myValue = 20 + (30 / 2) * 3
    if 1 + 1 == 3 {
        fatalError("The universe is broken.")
    }

    func pancake(with syrup: Syrup) -> Pancake {
    }
```

Not Preferred:

```swift
    let myValue=20+( 30/2 )*3
    if 1+1==3 {
        fatalError("The universe is broken.")
    }

    func pancake(with syrup: Syrup)->Pancake {
    }
```

</details>

When dealing with an implicit array or dictionary large enough to warrant splitting it into multiple lines, treat the [ and ] as if they were braces in a method, if statement, etc. Closures in a method should be treated similarly.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    someFunctionWithABunchOfArguments(
        someStringArgument: "hello I am a string",
        someArrayArgument: [
            "dadada daaaa daaaa dadada daaaa daaaa dadada daaaa daaaa",
            "string one is crazy - what is it thinking?"
        ],
        someDictionaryArgument: [
            "dictionary key 1": "some value 1, but also some more text here",
            "dictionary key 2": "some value 2"
        ],
        someClosure: { parameter1 in
            print(parameter1)
        })
```

</details>

Prefer using local constants or other mitigation techniques to avoid multi-line predicates where possible.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let firstCondition = x == firstReallyReallyLongPredicateFunction()
    let secondCondition = y == secondReallyReallyLongPredicateFunction()
    let thirdCondition = z == thirdReallyReallyLongPredicateFunction()
    if firstCondition && secondCondition && thirdCondition {
        /// Do something
    }
```

Not Preferred:

```swift
    if x == firstReallyReallyLongPredicateFunction()
        && y == secondReallyReallyLongPredicateFunction()
        && z == thirdReallyReallyLongPredicateFunction() {
        // do something
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

## Parentheses

Parentheses around conditionals are not required and should be omitted.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    if name == "Hello" {
      print("World")
    }
```

Not Preferred:

```swift
    if (name == "Hello") {
      print("World")
    }
```

</details>

In larger expressions, optional parentheses can sometimes make code read more clearly.

Preferred:

<details open><summary><i>Example</i></summary>

```swift
    let playerMark = (player == current ? "X" : "O")
```

</details>

## Multi-line String Literals

When building a long string literal, you're encouraged to use the multi-line string literal syntax. Open the literal on the same line as the assignment but do not include text on that line. Indent the text block one additional level.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let message = """
      You cannot charge the flux \
      capacitor with a 9V battery.
      You must use a super-charger \
      which costs 10 credits. You currently \
      have \(credits) credits available.
      """
```

Not Preferred:

```swift
    let message = """You cannot charge the flux \
      capacitor with a 9V battery.
      You must use a super-charger \
      which costs 10 credits. You currently \
      have \(credits) credits available.
      """
```

Not Preferred:

```swift
    let message = "You cannot charge the flux " +
      "capacitor with a 9V battery.\n" +
      "You must use a super-charger " +
      "which costs 10 credits. You currently " +
      "have \(credits) credits available."
```

</details>

**[⬆ back to top](#table-of-contents)**

## Documentation

Well documented code is critical to help other developers understand what the code is doing. Every **open**, **public** and **internal** types should be documented. Additionally developers should annotate any **private** piece of code when its behavior is not trivial using the regular comment syntax.

If a function is more complicated than a simple _O(1)_ operation, you should generally consider adding a doc comment for the function since there could be some information that the method signature does not make immediately obvious. If there are any quirks to the way that something was implemented, whether technically interesting, tricky, not obvious, etc., this should be documented. Documentation should be added for complex classes/structs/enums/protocols and properties.

## Comments

When they are needed, use comments to explain why a particular piece of code does something. Comments must be kept up-to-date or deleted.
Avoid block comments inline with code, as the code should be as self-documenting as possible.
_Exception: This does not apply to those comments used to generate documentation._
Avoid the use of C-style comments (/* ... */). Prefer the use of **double-** or **triple-slash**.

If you’re going to be documenting the parameters/returns/throws of a method, document all of them, even if some of the documentation ends up being somewhat repetitive (this is preferable to having the documentation look incomplete). Sometimes, if only a single parameter warrants documentation, it might be better to just mention it in the description instead.

For complicated classes, describe the usage of the class with some potential examples as seems appropriate. Remember that markdown syntax is valid in Swift's comment docs. Newlines, lists, etc. are therefore appropriate.

<details open><summary><i>Example</i></summary>

```swift
///
///    ## Feature Support
///
///    This class does some awesome things. It supports:
///
///    - Feature 1
///    - Feature 2
///    - Feature 3
///
///    ## Examples
///
///    Here is an example use case indented by four spaces because that indicates a
///    code block:
///
///        let myAwesomeThing = MyAwesomeClass()
///        myAwesomeThing.makeMoney()
///
///    ## Warnings
///
///    There are some things you should be careful of:
///
///    1. Thing one
///    2. Thing two
///    3. Thing three
///
    class MyAwesomeClass {
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Type Inference

Prefer compact code and let the compiler infer the type for constants or variables of single instances. Type inference is also appropriate for small, non-empty arrays and dictionaries. When required, specify the specific type such as CGFloat or Int16.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let message = "Click the button"
    let currentBounds = computeViewBounds()
    var names = ["Mic", "Sam", "Christine"]
    let maximumWidth: CGFloat = 106.5
```

Not Preferred:

```swift
    let message: String = "Click the button"
    let currentBounds: CGRect = computeViewBounds()
    var names = [String]()
```

</details>

## Types

Always use Swift's native types and expressions when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let width = 120.0                                    // Double
    let widthString = "\(width)"                         // String
```

Less Preferred:

```swift
    let width = 120.0                                    // Double
    let widthString = (width as NSNumber).stringValue    // String
```

Not Preferred:

```swift
    let width: NSNumber = 120.0                          // NSNumber
    let widthString: NSString = width.stringValue        // NSString
```

</details>

In drawing code, use CGFloat if it makes the code more succinct by avoiding too many conversions.

## Constants

Constants are defined using the **let** keyword and variables with the **var** keyword.

_**Tip:** A good technique is to define everything using **let** and only change it to **var** if the compiler complains!_

You can define constants on a type rather than on an instance of that type using type properties. To declare a type property as a constant simply use static let. Type properties declared in this way are generally preferred over global constants because they are easier to distinguish from instance properties.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    enum Math {
      static let e = 2.718281828459045235360287
      static let root2 = 1.41421356237309504880168872
    }

    let hypotenuse = side * Math.root2
```

</details>

_**Note:** The advantage of using a case-less enumeration is that it can't accidentally be instantiated and works as a pure namespace._

<details open><summary><i>Example</i></summary>

Not Preferred:

```swift
    let e = 2.718281828459045235360287  // pollutes global namespace
    let root2 = 1.41421356237309504880168872

    let hypotenuse = side * root2 // what is root2?
```

</details>

Don't namespace constants

<details open><summary><i>Example</i></summary>

Preferred:

```swift
class MyClassName {
    // MARK: - Constants
    static let buttonPadding: CGFloat = 20.0
    static let indianaPi = 3
    static let shared = MyClassName()
}
```

Not Preferred:

```swift
class MyClassName {
    /// Don't use `k`-prefix
    static let kButtonPadding: CGFloat = 20.0

    /// Don't namespace constants
    enum Constant {
        static let indianaPi = 3
    }
}
```

</details>

**[⬆ back to top](#table-of-contents)**

## Classes and Structures

When composing your types, consider carefully what they're going to be used for before choosing what they should end up being. Consider structs for types that are:

- Immutable
- Stateless
- Have a definition for equality
- Swift structs also have other, tangible benefits as well:
  - Faster
  - Safer due to copying rather than referencing
  - Thread safe - copies allow mutations to happen independently of other instances.

> In general, you should favor structs and protocols over classes; even in cases where polymorphism would dictate the usage of a class, consider if you can achieve a similar result via protocols and extensions. This allows you to achieve polymorphism via composition rather than inheritance.

<details open><summary><i>Example</i></summary>

This class hierarchy:

```swift
    class Vehicle {
        let numberOfWheels: Int

        init(numberOfWheels: Int) {
            self.numberOfWheels = numberOfWheels
        }

        func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
            return pressurePerWheel * Float(numberOfWheels)
        }
    }

    class Bicycle: Vehicle {
        init() {
            super.init(numberOfWheels: 2)
        }
    }

    class Car: Vehicle {
        init() {
            super.init(numberOfWheels: 4)
        }
    }
```

could be refactored into these definitions:

```swift
    protocol Vehicle {
        var numberOfWheels: Int { get }
    }

    func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(vehicle.numberOfWheels)
    }

    struct Bicycle: Vehicle {
        let numberOfWheels = 2
    }

    struct Car: Vehicle {
        let numberOfWheels = 4
    }
```

</details>

- Use classes when you need Objective-C interoperability.
- Use classes when you need to control the identity of the data you're modeling.

> Remember, structs have value semantics. Use structs for things that do not have an identity
Classes have reference semantics. Use classes for things that do have an identity or a specific life cycle.

**[⬆ back to top](#table-of-contents)**

## Use of Self

Use self only when required by the compiler (in @escaping closures, or in initializers to disambiguate properties from arguments). In other words, if it compiles without self then omit it.

## Computed Properties

For conciseness, if a computed property is read-only, omit the get clause. The get clause is required only when a set clause is provided.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    var diameter: Double {
      return radius * 2
    }
```

Not Preferred:

```swift
    var diameter: Double {
      get {
        return radius * 2
      }
    }
```

</details>

## Function Declarations

Keep short function declarations on one line including the opening brace:

<details open><summary><i>Example</i></summary>

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  /// Magic happens here
}
```

</details>

For functions with long signatures, put each parameter on a new line and add an extra indent on subsequent lines:

<details open><summary><i>Example</i></summary>

```swift
    func reticulateSplines(
      spline: [Double],
      adjustmentFactor: Double,
      translateConstant: Int, comment: String
    ) -> Bool {
      /// Magic happens here
    }
```

</details>

Don't use (Void) to represent the lack of an input; simply use (). Use Void instead of () for closure and function outputs.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    func updateConstraints() -> Void {
      /// Magic happens here
    }

    typealias CompletionHandler = (result) -> Void
```

Not Preferred:

```swift
    func updateConstraints() -> () {
      /// Magic happens here
    }

    typealias CompletionHandler = (result) -> ()
```

</details>

**[⬆ back to top](#table-of-contents)**

## Closure Expressions

Use trailing closure syntax only if there's a single closure expression parameter at the end of the argument list. Give the closure parameters descriptive names.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    UIView.animate(withDuration: 1.0) {
      self.myView.alpha = 0
    }

    UIView.animate(withDuration: 1.0, animations: {
      self.myView.alpha = 0
    }, completion: { finished in
      self.myView.removeFromSuperview()
    })
```

Not Preferred:

```swift
    UIView.animate(withDuration: 1.0, animations: {
      self.myView.alpha = 0
    })

    UIView.animate(withDuration: 1.0, animations: {
      self.myView.alpha = 0
    }) { f in
      self.myView.removeFromSuperview()
    }
```

</details>

For single-expression closures where the context is clear, use implicit returns.

<details open><summary><i>Example</i></summary>

```swift
    attendeeList.sort { a, b in
      a > b
    }
```

</details>

Chained methods using trailing closures should be clear and easy to read in context. Decisions on spacing, line breaks, and when to use named versus anonymous arguments is left to the discretion of the author.

<details open><summary><i>Example</i></summary>

```swift
    let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

    let value = numbers
      .map {$0 * 2}
      .filter {$0 > 50}
      .map {$0 + 10}
```

</details>

Don't wrap the arguments in the closure in a set of parentheses.

<details open><summary><i>Example</i></summary>

```swift
    let completionBlock: (Bool) -> Void = { success in
        print("Success? \(success)")
    }
```

</details>

If a function returns multiple values, prefer returning a tuple to using inout arguments (it’s best to use labeled tuples for clarity on what you’re returning if it is not otherwise obvious). If you use a certain tuple more than once, consider using a typealias. If you’re returning 3 or more items in a tuple, consider using a struct or class instead.

<details open><summary><i>Example</i></summary>

```swift
    func pirateName() -> (firstName: String, lastName: String) {
        return ("Guybrush", "Threepwood")
    }

    let name = pirateName()
    let firstName = name.firstName
    let lastName = name.lastName
```

</details>

**[⬆ back to top](#table-of-contents)**

## Optionals

Declare variables and function return types as optional with **?** where a _nil_ value is acceptable.

Use implicitly unwrapped types declared with **!** only for instance variables that you know will be initialized later before use, such as subviews that will be set up in viewDidLoad(). Prefer optional binding to implicitly unwrapped optionals in most other cases.

For optional binding, shadow the original name whenever possible rather than using names like unwrappedView or actualLabel (except `self`).

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    var subview: UIView?
    var volume: Double?

    /// later on...
    if let subview = subview, let volume = volume {
      /// do something with unwrapped subview and volume
    }
```

Not Preferred:

```swift
    var optionalSubview: UIView?
    var volume: Double?

    if let unwrappedSubview = optionalSubview {
      if let realVolume = volume {
        /// do something with unwrappedSubview and realVolume
      }
    }
```

</details>

## Lazy Initialization

Consider using lazy initialization for finer grained control over object lifetime
This is especially true for UIViewController that loads views lazily. You can either use a closure that is immediately called { }() or call a private factory method.

**[⬆ back to top](#table-of-contents)**

## Arrays

In general, avoid accessing an array directly with subscripts. When possible, use accessors such as .first or .last, which are optional and won’t crash. Prefer using a for item in items syntax when possible as opposed to something like for i in 0 ..< items.count. If you need to access an array subscript directly, make sure to do proper bounds checking. You can use for (index, value) in items.enumerated() to get both the index and the value.

Never use the += or + operator to append/concatenate to arrays. Instead, use .append() or .append(contentsOf:) as these are far more performant (at least with respect to compilation) in Swift's current state. If you are declaring an array that is based on other arrays and want to keep it immutable, instead of `let myNewArray = arr1 + arr2`, use `let myNewArray = [arr1, arr2].joined()`.

## Type Annotation for Empty Arrays and Dictionaries

For empty arrays and dictionaries, use type annotation.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    var names: [String] = []
    var lookup: [String: Int] = [:]
```

Not Preferred:

```swift
    var names = [String]()
    var lookup = [String: Int]()
```

</details>

## Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    var deviceModels: [String]
    var employees: [Int: String]
    var faxNumber: Int?
```

Not Preferred:

```swift
    var deviceModels: Array<String>
    var employees: Dictionary<Int, String>
    var faxNumber: Optional<Int>
```

</details>

**[⬆ back to top](#table-of-contents)**

## Functions vs Methods

Free functions, which aren't attached to a class or type, should be used sparingly. When possible, prefer to use a method instead of a free function. This aids in readability and discoverability.

Free functions are most appropriate when they aren't associated with any particular type or instance.

Prefer static to class when declaring a function or property that is associated with a class as opposed to an instance of that class. Only use class if you specifically need the functionality of overriding that function or property in a subclass, though consider using a protocol to achieve this instead.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let sorted = items.mergeSorted()  // easily discoverable
    rocket.launch()  // acts on the model
```

Not Preferred:

```swift
    let sorted = mergeSort(items)  // hard to discover
    launch(&rocket)
```

</details>

## Free Function Exceptions

```swift
    let tuples = zip(a, b)  // feels natural as a free function (symmetry)
    let value = max(x, y, z)  // another free function that feels natural
```

## Memory Management

Code should not create reference cycles. Analyze your object graph and prevent strong cycles with weak and unowned references. Alternatively, use value types (struct, enum) to prevent cycles altogether.

Extend object lifetime using the [weak self] and [guard let self = self else { return }](https://github.com/apple/swift-evolution/blob/master/proposals/0079-upgrade-self-from-weak-to-strong.md) idiom. [weak self] is preferred to [unowned self] where it is not immediately obvious that self outlives the closure. Explicitly extending lifetime is preferred to optional chaining.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    resource.request().onComplete { [weak self] response in
      guard let self = self else {
        return
      }
      let model = self.updateModel(response)
      self.updateUI(model)
    }
```

Not Preferred:

```swift
    /// might crash if self is released before response returns
    resource.request().onComplete { [unowned self] response in
      let model = self.updateModel(response)
      self.updateUI(model)
    }
```

Not Preferred:

```swift
    /// deallocate could happen between updating the model and updating UI
    resource.request().onComplete { [weak self] response in
      let model = self?.updateModel(response)
      self?.updateUI(model)
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Access Control

Prefer **private** to **fileprivate**; use **fileprivate** only when the compiler insists.
Only explicitly use **open**, **public**, and **internal** when you require a full access control specification.

Use access control as the leading property specifier. The only things that should come before access control are the static specifier or attributes such as @IBAction, @IBOutlet and @discardableResult.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    private let message = "Great Scott!"

    class TimeMachine {  
      private dynamic lazy var fluxCapacitor = FluxCapacitor()
    }
```

Not Preferred:

```swift
    fileprivate let message = "Great Scott!"

    class TimeMachine {  
      lazy dynamic private var fluxCapacitor = FluxCapacitor()
    }
```

</details>

> When writing methods, keep in mind whether the method is intended to be overridden or not. If not, mark it as **final**, though keep in mind that this will prevent the method from being overwritten for testing purposes. In general, final methods result in improved compilation times, so it is good to use this when applicable. Be particularly careful, however, when applying the final keyword in a library since it is non-trivial to change something to be non-final in a library as opposed to have changing something to be non-final in your local project.

## Control Flow

Prefer the **for-in** style of for loop over the **while-condition-increment** style.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    for _ in 0..<3 {
      print("Hello three times")
    }

    for (index, person) in attendeeList.enumerated() {
      print("\(person) is at position #\(index)")
    }

    for index in stride(from: 0, to: items.count, by: 2) {
      print(index)
    }

    for index in (0...3).reversed() {
      print(index)
    }
```

Not Preferred:

```swift
    var i = 0
    while i < 3 {
      print("Hello three times")
      i += 1
    }

    var i = 0
    while i < attendeeList.count {
      let person = attendeeList[i]
      print("\(person) is at position #\(i)")
      i += 1
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Switch Statements and Enum

Prefer singular case for enum names instead of plural. Avoid writing out an enum type where possible - use shorthand.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    imageView.setImageWithURL(url, type: .person)
```

Not Preferred:

```swift
    imageView.setImageWithURL(url, type: AsyncImageView.Type.person)
```

</details>

When using a switch statement that has a finite set of possibilities (enum), do NOT include a default case. Instead, place unused cases at the bottom and use the break keyword to prevent execution.

Since switch cases in Swift break by default, do not include the break keyword if it is not needed.
The case statements should line up with the switch statement itself as per default Swift standards.

When defining a case that has an associated value, make sure that this value is appropriately labeled as opposed to just types (e.g. case hunger(hungerLevel: Int) instead of case hunger(Int))

<details open><summary><i>Example</i></summary>

```swift
    enum Problem {
        case attitude
        case hair
        case hunger(hungerLevel: Int)
    }

    func handleProblem(problem: Problem) {
        switch problem {
        case .attitude:
            print("At least I don't have a hair problem.")
        case .hair:
            print("Your barber didn't know when to stop.")
        case .hunger(let hungerLevel):
            print("The hunger level is \(hungerLevel).")
        }
    }
```

</details>

> Prefer lists of possibilities (e.g. case 1, 2, 3:) to using the **fallthrough** keyword where possible.

## Enum and Protocol

All enums should live in their own file, except in cases where the enum is declared as private. In cases where the enum is declared private, declare the enum at the top of the file, above the type declaration.

Rationale: With enum and protocol types Swift allows defining functions and extensions. Because of that these types can become complex which is why they should be defined in their own file.

## Ternary Operator

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement or refactored into instance variables. In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    let value = 5
    result = value != 0 ? x : y

    let isHorizontal = true
    result = isHorizontal ? x : y
```

Not Preferred:

```swift
    result = a > b ? x = c > d ? c : d : y
```

</details>

**[⬆ back to top](#table-of-contents)**

## Golden Path

When coding with conditionals, the left-hand margin of the code should be the **"golden"** or **"happy"** path. That is, don't nest if statements. Multiple return statements are OK. The guard statement is built for this.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
      guard let context = context else {
        throw FFTError.noContext
      }
      guard let inputData = inputData else {
        throw FFTError.noInputData
      }

      /// use context and input to compute the frequencies
      return frequencies
    }
```

Not Preferred:

```swift
    func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

      if let context = context {
        if let inputData = inputData {
          /// use context and input to compute the frequencies
          return frequencies
        } else {
          throw FFTError.noInputData
        }
      } else {
        throw FFTError.noContext
      }
    }
```

Even Less Preferred:

```swift
    func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
      if context == nil {
        throw FFTError.noContext
      }

      if inputData == nil {
        throw FFTError.noInputData
      }

      /// use context and input to compute the frequencies
      return frequencies
    }
```

</details>

When multiple optionals are unwrapped either with guard or if let, minimize nesting by using the compound version when possible. In the compound version, place the guard on its own line, then ident each condition on its own line. The else clause is indented to match the conditions and the code is indented one additional level.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    guard
      let number1 = number1,
      let number2 = number2,
      let number3 = number3
      else {
        fatalError("impossible")
    }
    /// do something with numbers
```

Not Preferred:

```swift
    if let number1 = number1 {
      if let number2 = number2 {
        if let number3 = number3 {
          /// do something with numbers
        } else {
          fatalError("impossible")
        }
      } else {
        fatalError("impossible")
      }
    } else {
      fatalError("impossible")
    }
```

</details>

## Failing Guards

Guard statements are required to exit in some way. Generally, this should be simple one line statement such as **return**, **throw**, **break**, **continue**, and **fatalError()**. Large code blocks should be avoided. If cleanup code is required for multiple exit points, consider using a **defer** block to avoid cleanup code duplication.

When deciding between using an **if statement** or a **guard statement** when unwrapping optionals is not involved, the most important thing to keep in mind is the `readability` of the code. There are many possible cases here, such as depending on two different booleans, a complicated logical statement involving multiple comparisons, etc., so in general, use your best judgement to write code that is readable and consistent. If you are unsure whether guard or if is more readable or they seem equally readable, prefer using guard.

<details open><summary><i>Example</i></summary>

```swift
    /// an `if` statement is readable here
    if operationFailed {
        return
    }

    /// a `guard` statement is readable here
    guard isSuccessful else {
        return
    }
```

</details>

### Don’t Guard Against Double Negatives

Keep it simple. Go with the (control) flow. Avoid the double negative.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    /// Aha!
    if string.isEmpty {
        return
    }
```

Not Preferred:

```swift
    /// Huh?
    guard !string.isEmpty else {
        return
    }
```

</details>

If choosing between two different states, it makes more sense to use an if statement as opposed to a guard statement.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    if isFriendly {
        print("Hello, nice to meet you!")
    } else {
        print("You have the manners of a beggar.")
    }
```

Not Preferred:

```swift
    guard isFriendly else {
        print("You have the manners of a beggar.")
        return
    }

    print("Hello, nice to meet you!")
```

</details>

Don’t use one-liners for guard statements.

<details open><summary><i>Example</i></summary>

Preferred:

```swift
    guard let thingOne = thingOne else {
        return
    }
```

Not Preferred:

```swift
    guard let thingOne = thingOne else { return }
```

</details>

**[⬆ back to top](#table-of-contents)**

## Organization and Bundle Identifier

Where an Xcode project is involved, the organization should be set to JatApp and the Bundle Identifier set to com.jatapp.platform.projectname.

## Strings

- all strings presented to the user should be localized using _Localizable.strings_ file.
- all string should be listed in order of appearance in context
- follow the naming format

<details open><summary><i>Example</i></summary>

```swift
 ///==============
 /// Screen Name
 ///==============
 ///
 /// String description
 "screen.title.string-description" = "String to be localized"
 "screen-name.button.action-name" = "String to be localized"
```

</details>

> Localizable keys shouldn't contain special symbols and commands ( \n, \r, $, *, etc.)

- use Localizable.stringsdict for plural forms

## Operators Overloading

Operator overloading is not recommended. Overloads often lead to ambiguous semantics, unintuitive behaviours and obscurities that are difficult for everyone to understand except the person who wrote them. Instead opt for less succinct, yet more descriptive function definitions throughout your code.

## Singleton

Implement a singleton by having private initializer, extension, protocol.

<details open><summary><i>Example</i></summary>

```swift
    protocol ClassAProtocol {
      static func foo()
    }

    class ClassA {
      private init() {

      }

      private func foo() {

      }
    }

    // MARK: - ClassAProtocol Methods
    extension ClassA: ClassAProtocol {
      static let shared = ClassA()

      static func foo() {
        shared.foo()
      }
    }
```

</details>

**[⬆ back to top](#table-of-contents)**

----

### References

<!-- Identifiers, in alphabetical order -->

- [Raywenderlich Style Guide](https://github.com/raywenderlich/swift-style-guide)
- [John Sundell Style Guide](https://github.com/JohnSundell/ios-style)
- [LinkedIn Style Guide](https://github.com/linkedin/swift-style-guide)
- [ProlificInteractive Style Guide](https://github.com/prolificinteractive/swift-style-guide)
- [GitHub Style Guide](https://github.com/github/swift-style-guide)
- [SlideShareInc Style Guide](https://github.com/SlideShareInc/swift-style-guide/blob/master/swift_style_guide.md)
- [Futurice Style Guide](https://github.com/futurice/ios-good-practices)

**[⬆ back to top](#table-of-contents)**
# swiftstyleguide
