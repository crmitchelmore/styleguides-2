#Swift code style

## Table of Contents

- [Naming](#naming)
- [Whitespace](#whitespace)
- [Comments](#comments)
- [Closure Expressions](#closure-expressions)
- [Avoid Using Force-Unwrapping of Optionals](#avoid-using-force-unwrapping-of-optionals)
- [Avoid Using Implicitly Unwrapped Optionals](#avoid-using-implicitly-unwrapped-optionals)
- [Avoid redundant type declarations](#avoid-redundant-type-declarations)
- [When specifying a type, always associate the colon with the identifier](#when-specifying-a-type,-always-associate-the-colon--with-the-identifier)
- [Only explicitly refer to `self` when required](#only-explicitly-refer-to-`self`-when-required)
- [Prefer structs over classes](#prefer-structs-over-classes)
- [Omit type parameters where possible](#omit-type-parameters-where-possible)
- [Protocol Conformance](#protocol-conformance)

### Naming

Use descriptive names with camel case for classes, methods, variables, etc. Class names should be capitalized, while method names and variables should start with a lower case letter. View properties should have their type following the name. 
Preferred:
```
private let maximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  var widgetTextField: UITextField
  let widgetHeightPercentage = 0.85
}
```

Not Preferred:
```
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  var wBut: UITextField
  let wHeightPct = 0.85
}
```

####Enumerations

Use UpperCamelCase for enumeration values. Prefer one enum value per line:

```
enum Shape {
  case Rectangle
  case Square
  case Triangle
  case Circle
}
```
### Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.
 * Don’t leave trailing whitespace.
   * Not even leading indentation on blank lines.

###Comments

When they are needed, use comments to explain why a particular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self-documenting as possible. 

### Avoid Using Force-Unwrapping of Optionals

If you have an identifier `foo` of type `FooType?` or `FooType!`, don't force-unwrap it to get to the underlying value (`foo!`) if possible.

Instead, prefer this:

```swift
if let foo = foo {
    // Use unwrapped `foo` value in here
} else {
    // If appropriate, handle the case where the optional is nil
}
```

Alternatively, you might want to use Swift's Optional Chaining in some of these cases, such as:

```swift
// Call the function if `foo` is not nil. If `foo` is nil, ignore we ever tried to make the call
foo?.callSomethingIfFooIsNotNil()
```

_Rationale:_ Explicit `if let`-binding of optionals results in safer code. Force unwrapping is more prone to lead to runtime crashes.

### Avoid Using Implicitly Unwrapped Optionals

Where possible, use `let foo: FooType?` instead of `let foo: FooType!` if foo may be nil (Note that in general, `?` can be used instead of `!`).

_Rationale:_ Explicit optionals result in safer code. Implicitly unwrapped optionals have the potential of crashing at runtime.

#### Prefer implicit getters on read-only properties and subscripts

When possible, omit the `get` keyword on read-only computed properties and
read-only subscripts.

So, write these:

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… not these:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.

### Avoid redundant type declarations

When type information can be inferred from an initial value, avoid declaring it explicitly, unless doing so is an active decision to improve code legibility.

```swift
var isOnFire = false
var referenceFrame = CGRect(x: 0, y: 0, width: 100, height: 100)
```

instead of:

```swift
var isOnFire: Bool = false
var referenceFrame: CGRect = CGRect(x: 0, y: 0, width: 100, height: 100)
```

When type information _cannot_ be inferred from an initial value then it should be explicitly added to the declaration; you should not add type information to a constructor for the purpose of allowing type inference.

```swift
var lookupTable: [Int: String] = [:] 
var duration: NSTimeInterval = 1.2
```

instead of:

```swift
var lookupTable = [Int: String]()
var duration = NSTimeInterval(1.2)
```
_Rationale_: Avoiding redundant declarations results in shorter, easier to read code. 

### When specifying a type, always associate the colon with the identifier

When specifying the type of an identifier, always put the colon immediately
after the identifier, followed by a space and then the type name.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_Rationale:_ The type specifier is saying something about the _identifier_ so
it should be positioned with it.

Also, when specifying the type of a dictionary, always put the colon immediately
after the key type, followed by a space and then the value type.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

### Only explicitly refer to `self` when required

When accessing properties or methods on `self`, leave the reference to `self` implicit by default:

```swift
private class History {
	var events: [Event]

	func rewrite() {
		events = []
	}
}
```

Only include the explicit keyword when required by the language—for example, in a closure, or when parameter names conflict:

```swift
extension History {
	init(events: [Event]) {
		self.events = events
	}

	var whenVictorious: () -> Void {
		return {
			self.rewrite()
		}
	}
}
```

_Rationale:_ This makes the capturing semantics of `self` stand out more in closures, and avoids verbosity elsewhere.

### Prefer structs over classes

Unless you require functionality that can only be provided by a class with reference semantics (like identity or deinitializers), implement a struct instead.

Note that inheritance is (by itself) usually _not_ a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.

For example, this class hierarchy:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * numberOfWheels
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
    return pressurePerWheel * vehicle.numberOfWheels
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_Rationale:_ Value types are simpler, easier to reason about, and behave as expected with the `let` keyword.

### Omit type parameters where possible

Methods of parameterized types can omit type parameters on the receiving type when they’re identical to the receiver’s. For example:

```swift
struct Composite<T> {
	…
	func compose(other: Composite<T>) -> Composite<T> {
		return Composite<T>(self, other)
	}
}
```

could be rendered as:

```swift
struct Composite<T> {
	…
	func compose(other: Composite) -> Composite {
		return Composite(self, other)
	}
}
```

_Rationale:_ Omitting redundant type parameters clarifies the intent, and makes it obvious by contrast when the returned type takes different type parameters.

### Protocol Conformance

When adding protocol conformance to a class, prefer adding a separate class extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

Also, don't forget the // MARK: - comment to keep things well-organized!

Preferred:
```
class MyViewcontroller: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // scroll view delegate methods
}
```
Not Preferred:
```
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

### Closure Expressions

Use trailing closure syntax only if there's a single closure expression parameter at the end of the argument list. Give the closure parameters descriptive names.

Preferred:
```
UIView.animateWithDuration(1.0) {
  self.myView.alpha = 0
}

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  },
  completion: { finished in
    self.myView.removeFromSuperview()
  }
)
```
Not Preferred:
```
UIView.animateWithDuration(1.0, animations: {
  self.myView.alpha = 0
})

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  }) { f in
    self.myView.removeFromSuperview()
}
For single-expression closures where the context is clear, use implicit returns:

attendeeList.sort { a, b in
  a > b
}
```

When defining a closure as a parameter that has a void return type.

Preferred:

```
func doSomething(callback: (String) -> void)
```

Not Preferred:

```
func doSomething(callback: (String) -> ())