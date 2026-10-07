## Storyboard Practical Worksheet: Building an Emoji Dictionary App

This worksheet is the Storyboard and UIKit version of the **Emoji Dictionary**. The app displays a list of emojis, each with a symbol, name, description, and usage, and allows adding, editing, removing, and reordering emojis.

---

### 0. Project Setup

1. Open **Xcode** → **File > New > Project…**

2. Template: **iOS > App**

3. Name: **EmojiDictionary**

4. Interface: **Storyboard**

5. Language: **Swift**


Xcode will create:

* `AppDelegate.swift` and `SceneDelegate.swift` (app lifecycle entry points)
* `ViewController.swift` (initial view controller)
* `Main.storyboard` (visual interface canvas)

> #### Storyboards vs. XIB Files
> 
> 
> * **Storyboard (`.storyboard`)**: A visual canvas representing multiple screens (view controllers) and the navigational transitions (segues) between them. It provides a high-level map of the app's user flow.
> 
> 
> * **XIB / NIB (`.xib`)**: Represents a single visual view or interface element (such as a single reusable table view cell or custom modal view).
> * Both formats are XML files under the hood that Xcode compiles into binary `.nib` files bundled into your app.
> 
> 

> #### View Controller, Navigation Controller, and Table View Controller
> 
> 
> * **`UIViewController`**: The foundational building block of iOS apps. It manages a root view hierarchy, receives lifecycle events (`viewDidLoad`, `viewWillAppear`), and coordinates between data models and views.
> * **`UINavigationController`**: A container view controller that manages a hierarchical, stack-based navigation model (drill-down). It provides the navigation bar at the top with a title and back button.
> 
> 
> * **`UITableViewController`**: A specialized subclass of `UIViewController` that comes pre-configured with a `UITableView`. It automatically wires itself as both the table view's **Data Source** and **Delegate**, handles keyboard inset adjustments, and manages editing modes.
> 
> 
> 
> 
> Docs:
> * UIKit Overview — Apple Developer - [https://developer.apple.com/documentation/uikit](https://developer.apple.com/documentation/uikit)
> * UINavigationController — Apple Developer - [https://developer.apple.com/documentation/uikit/uinavigationcontroller](https://developer.apple.com/documentation/uikit/uinavigationcontroller)
> 
> 

---

## 1. Model

Create a new file **Emoji.swift** (**File > New > File... > Swift File**).

```swift
import Foundation

struct Emoji {
    var symbol: String
    var name: String
    var description: String
    var usage: String
}

// MARK: - Sample Data
extension Emoji {
    static var sampleEmojis: [Emoji] {
        [
            Emoji(symbol: "😀", name: "Grinning Face", description: "A typical smiley face.", usage: "happiness"),
            Emoji(symbol: "😕", name: "Confused Face", description: "A puzzled face.", usage: "unsure"),
            Emoji(symbol: "😍", name: "Heart Eyes", description: "Loves something.", usage: "love"),
            Emoji(symbol: "🧑‍💻", name: "Developer", description: "Working on Mac.", usage: "software, coding"),
            Emoji(symbol: "🐢", name: "Turtle", description: "Slow but steady.", usage: "slow"),
            Emoji(symbol: "📚", name: "Books", description: "Stack of books.", usage: "study")
        ]
    }
}

```

> #### Cocoa Touch Class vs. Swift File
> 
> 
> * **Swift File**: A blank Swift file used for pure Swift data models, structs, enums, protocols, and utility logic without UIKit boilerplate.
> 
> 
> * **Cocoa Touch Class**: An Xcode file template configured to subclass Apple framework classes (such as `UIViewController`, `UITableViewController`, or `UITableViewCell`). It automatically imports `UIKit` and provides standard lifecycle method stubs.
> 
> 
> 
> 

---

## 2. Navigation & Main List Setup

### 2.1 Replace the Initial View Controller in Main.storyboard

1. In `Main.storyboard`, select the default **View Controller** scene in the canvas or Document Outline (left pane of the editor) and press **Delete**.


2. Open the **Object Library** (click the **+** button in the top toolbar or press **Shift + Command + L**), search for **Navigation Controller**, and drag it onto the canvas. A **Table View Controller** is automatically created as its root view controller.


3. Select the **Navigation Controller** in the canvas or Document Outline.


4. In the right utility area, open the **Attributes Inspector** (slider icon) and check **Is Initial View Controller**.



### 2.2 Create the Subclass and Configure Navigation Items

1. Create a new file (**File > New > File... > Cocoa Touch Class**), name it `EmojiTableViewController`, and make it a subclass of `UITableViewController`.


2. In `Main.storyboard`, select the root **Table View Controller** scene. In the right utility area, open the **Identity Inspector** (newspaper/badge icon) and set **Class** to `EmojiTableViewController`.


3. In the Document Outline, expand `EmojiTableViewController`, select the **Navigation Item** under it, open the **Attributes Inspector**, and set the **Title** field to `Emoji Dictionary`.
4. Drag a **Bar Button Item** from the Object Library to the **right** side of the navigation bar, and in the **Attributes Inspector**, set **System Item** to **Add**.



> #### Object Library & Inspectors (Attributes vs. Identity)
> 
> 
> * **Object Library (`Shift + Cmd + L`)**: The catalog containing all UIKit UI components (labels, buttons, stack views, table view controllers, gesture recognizers) ready to be placed on the canvas.
> 
> 
> * **Attributes Inspector**: Configures visual and behavioral properties of the selected component (e.g., text, font, tint color, placeholder, table view style, system bar button items).
> 
> 
> * **Identity Inspector**: Manages the object's class mapping, runtime metadata, and identifiers (e.g., binding an interface element to a custom Swift subclass, User Defined Runtime Attributes, Accessibility identifiers).
> 
> 
> * **Size Inspector**: Controls dimensions, margins, Auto Layout constraint constants, and layout priorities (Content Hugging, Compression Resistance).
> 
> 

---

## 3. Custom Table View Cell & Layout

To keep the project concise and structured similarly to the SwiftUI worksheet, the cell subclass `EmojiTableViewCell` will be declared directly inside `EmojiTableViewController.swift` alongside its controller.

### 3.1 Configure Prototype Cell in Storyboard

1. Select the **Table View** inside the `EmojiTableViewController` scene, open the **Attributes Inspector**, and verify **Prototype Cells** is set to `1`.


2. Select the prototype cell in the Document Outline or canvas:


* In the **Identity Inspector**, set **Class** to `EmojiTableViewCell`.


* In the **Attributes Inspector**, set **Identifier** to `EmojiCell`.




3. Drag three **Labels** from the Object Library into the cell's **Content View**:


* **Symbol Label**: Font = **Large Title** (or size 32).
* **Name Label**: Font = **Headline**.


* **Description Label**: Font = **Subheadline** (or Caption), Text Color = **Secondary Label**, **Lines** = `0`, **Line Break** = **Word Wrap**.




4. Select the **Name Label** and **Description Label**, then click the **Embed In** button (or choose **Editor > Embed In > Stack View** from the menu bar) to embed them in a **Vertical Stack View** (set Spacing = `2`, Alignment = `Leading`).


5. Select the **Symbol Label** and the newly created vertical stack view, and embed them in a **Horizontal Stack View** (Spacing = `12`, Alignment = `Center`).


6. Pin the horizontal stack view to the cell's **Content View** margins using the **Add New Constraints** tool (the square icon in the canvas bottom/top toolbar):
* Set **Leading**: `16`, **Trailing**: `16`, **Top**: `8`, **Bottom**: `8`.




7. Select the **Symbol Label**, open the **Size Inspector**, and set **Horizontal Content Hugging Priority** to `252` and **Horizontal Compression Resistance** to `751` to keep it firmly wrapped around the emoji character.

> #### Table View Cells & Prototype Cells
> 
> 
> * **`UITableViewCell`**: The visual container for displaying a single row in a table view. It includes a predefined `contentView` that manages dynamic layout boundaries.
> 
> 
> * **Prototype Cell**: A template designed directly inside Interface Builder. At runtime, the table view dequeues and duplicates this template for every item in your data source using its reuse identifier (`EmojiCell`).
> 
> 
> 
> 

> #### Auto Layout & "Embed In"
> 
> 
> * **Auto Layout**: A constraint-based layout system that dynamically calculates the size and position of all views based on mathematical relationships and device screen bounds.
> 
> 
> * **Embed In**: An Xcode action (**Editor > Embed In**) that wraps selected views inside containers like **Stack Views** (`UIStackView`), **Scroll Views**, or **Navigation Controllers** without having to manually recreate existing subviews. Stack views simplify Auto Layout by automating internal alignment and spacing.
> 
> 
> 
> 

### 3.2 Connect Outlets via Assistant Editor

1. In Xcode, open the secondary editor by clicking the **Editor Options** button (top-right of the editor pane) and selecting **Assistant** (or use **Navigate > Open in Assistant Editor**). Ensure `EmojiTableViewController.swift` is open.


2. In `EmojiTableViewController.swift`, place the class definition for `EmojiTableViewCell` at the top of the file:

```swift
import UIKit

class EmojiTableViewCell: UITableViewCell {
    @IBOutlet weak var symbolLabel: UILabel!
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var descriptionLabel: UILabel!

    func update(with emoji: Emoji) {
        symbolLabel.text = emoji.symbol
        nameLabel.text = emoji.name
        descriptionLabel.text = emoji.description
    }
}

```

3. **Control-Drag Connections**: Hold **Control** and drag from each label in the storyboard cell directly to its respective `@IBOutlet` property in code:


* Symbol label → `symbolLabel`

* Name label → `nameLabel`

* Description label → `descriptionLabel`




> #### Assistant Editor & Control-Dragging
> 
> 
> * **Assistant Editor**: Splits your Xcode canvas so you can view an interface file (`Main.storyboard`) side-by-side with its backing Swift code file.
> 
> 
> * **Control-Dragging**: Holding the **Control** key and dragging a line from a visual storyboard element to a code file creates connections:
> * Dragging into variable scope creates an **`@IBOutlet`**.
> 
> 
> * Dragging into function scope creates an **`@IBAction`**.
> 
> 
> * Dragging to an existing variable/action reconnects it.
> 
> 
> 
> 
> 
> 

> #### Outlets (`@IBOutlet`) and Actions (`@IBAction`)
> 
> 
> * **`@IBOutlet` (Interface Builder Outlet)**: A property attribute that acts as a pointer in code referencing a visual element constructed in Interface Builder (`@IBOutlet weak var nameLabel: UILabel!`).
> 
> 
> * **`@IBAction` (Interface Builder Action)**: A method attribute that allows Interface Builder controls (buttons, switches, text fields) to trigger Swift functions when specific touch or editing events occur (`@IBAction func buttonTapped(_ sender: UIButton)`).
> 
> 
> 
> 

---

## 4. List Screen Implementation

Open **EmojiTableViewController.swift** and assemble the complete table view controller code:

```swift
import UIKit

// MARK: - Custom Cell
class EmojiTableViewCell: UITableViewCell {
    @IBOutlet weak var symbolLabel: UILabel!
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var descriptionLabel: UILabel!

    func update(with emoji: Emoji) {
        symbolLabel.text = emoji.symbol
        nameLabel.text = emoji.name
        descriptionLabel.text = emoji.description
    }
}

// MARK: - Table View Controller
class EmojiTableViewController: UITableViewController {
    var emojis: [Emoji] = Emoji.sampleEmojis

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Built-in edit button toggles delete and reorder controls
        navigationItem.leftBarButtonItem = editButtonItem
        
        // Dynamic self-sizing cell calculation
        tableView.rowHeight = UITableView.automaticDimension
        tableView.estimatedRowHeight = 60.0
    }

    // MARK: - Table View Data Source

    override func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return emojis.count
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "EmojiCell", for: indexPath) as! EmojiTableViewCell
        let emoji = emojis[indexPath.row]
        cell.update(with: emoji)
        cell.showsReorderControl = true
        return cell
    }

    // MARK: - Deletion & Reordering

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            emojis.remove(at: indexPath.row)
            tableView.deleteRows(at: [indexPath], with: .fade)
        }
    }

    override func tableView(_ tableView: UITableView, moveRowAt fromIndexPath: IndexPath, to: IndexPath) {
        let movedEmoji = emojis.remove(at: fromIndexPath.row)
        emojis.insert(movedEmoji, at: to.row)
    }
}

```

> #### Table View Data Source, Deletion, and Reordering
> 
> 
> * **Data Source Methods (`UITableViewDataSource`)**:
> * `numberOfSections(in:)`: Returns the total sections in the table (1 for simple lists).
> 
> 
> * `tableView(_:numberOfRowsInSection:)`: Instructs the table view how many rows to render based on model collection size (`emojis.count`).
> 
> 
> * `tableView(_:cellForRowAt:)`: Dequeues a reusable cell, passes the model data to it, and returns the configured cell.
> 
> 
> 
> 
> * **Deletion (`commit editingStyle:`)**: Triggered when a user confirms a swipe-to-delete or taps the delete badge in edit mode. You **must** remove the item from the data array (`emojis.remove(at:)`) and notify the table view via `tableView.deleteRows(at:with:)`.
> 
> 
> * **Reordering (`moveRowAt:to:`)**: Triggered when a user drags a reorder handle. Update your in-memory array by removing the item from its origin index and inserting it at the destination index.
> 
> 
> 
> 

---

## 5. Add / Edit Screen (Static Form)

### 5.1 Create the Controller Class

Create a new file (**File > New > File... > Cocoa Touch Class**), name it `AddEditEmojiTableViewController`, and set it as a subclass of `UITableViewController`.

```swift
import UIKit

class AddEditEmojiTableViewController: UITableViewController {
    @IBOutlet weak var symbolTextField: UITextField!
    @IBOutlet weak var nameTextField: UITextField!
    @IBOutlet weak var descriptionTextField: UITextField!
    @IBOutlet weak var usageTextField: UITextField!
    @IBOutlet weak var saveButton: UIBarButtonItem!

    var emoji: Emoji?

    override func viewDidLoad() {
        super.viewDidLoad()

        if let emoji = emoji {
            symbolTextField.text = emoji.symbol
            nameTextField.text = emoji.name
            descriptionTextField.text = emoji.description
            usageTextField.text = emoji.usage
            title = "Edit Emoji"
        } else {
            title = "Add Emoji"
        }

        updateSaveButtonState()
    }

    @IBAction func textEditingChanged(_ sender: UITextField) {
        updateSaveButtonState()
    }

    @IBAction func cancelButtonTapped(_ sender: UIBarButtonItem) {
        dismiss(animated: true)
    }

    func updateSaveButtonState() {
        let symbol = symbolTextField.text?.trimmingCharacters(in: .whitespaces) ?? ""
        let name = nameTextField.text?.trimmingCharacters(in: .whitespaces) ?? ""
        let description = descriptionTextField.text?.trimmingCharacters(in: .whitespaces) ?? ""
        let usage = usageTextField.text?.trimmingCharacters(in: .whitespaces) ?? ""
        
        saveButton.isEnabled = !symbol.isEmpty && !name.isEmpty && !description.isEmpty && !usage.isEmpty
    }

    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        guard segue.identifier == "saveUnwind" else { return }

        let symbol = symbolTextField.text ?? ""
        let name = nameTextField.text ?? ""
        let description = descriptionTextField.text ?? ""
        let usage = usageTextField.text ?? ""
        emoji = Emoji(symbol: symbol, name: name, description: description, usage: usage)
    }
}

```

### 5.2 Build the Static Scene in Storyboard

1. Drag a new **Table View Controller** from the Object Library onto `Main.storyboard`.


2. With this new Table View Controller selected, choose **Editor > Embed In > Navigation Controller** from Xcode's menu bar.


3. Select the Table View Controller scene, open the **Identity Inspector**, and set **Class** to `AddEditEmojiTableViewController`.


4. Select the **Table View** inside this scene:


* In the **Attributes Inspector**, change **Content** to **Static Cells**.


* Change **Style** to **Inset Grouped**.


* Set **Sections** to `4`.




5. **Adjust Section Rows**: By default, Xcode populates each section with 3 rows. In the Document Outline, select each section one by one and, in the **Attributes Inspector**, change **Rows** from `3` to `1` (or select and delete the extra two rows in each section).
6. Configure each section's header and content:
* **Section 0**: Header = `Symbol`. Drag a **Text Field** into the cell, placeholder = `The emoji`.


* **Section 1**: Header = `Name`. Drag a **Text Field** into the cell, placeholder = `The emoji's name`.


* **Section 2**: Header = `Description`. Drag a **Text Field** into the cell, placeholder = `Describe this emoji`.


* **Section 3**: Header = `Usage`. Drag a **Text Field** into the cell, placeholder = `When do you use it?`.


* Pin each text field to its cell's Content View margins (Leading: 16, Trailing: 16, Center Vertically).


7. Configure the Navigation Bar:
* Drag a **Bar Button Item** to the **left** of the navigation bar, set title to `Cancel`.
* Drag a **Bar Button Item** to the **right** of the navigation bar, set **System Item** to **Save**.




8. Connect Outlets & Actions:
* Connect all 4 text fields to `symbolTextField`, `nameTextField`, `descriptionTextField`, and `usageTextField`.


* Connect the Save button to `@IBOutlet weak var saveButton: UIBarButtonItem!`.


* Connect the Cancel button to `@IBAction func cancelButtonTapped(_ sender: UIBarButtonItem)`.
* For **all four** text fields: Control-drag from each text field to `@IBAction func textEditingChanged(_ sender: UITextField)` (Event: **Editing Changed**).





> #### Static Table Views
> 
> 
> * **Static Cells**: Ideal for data entry forms and settings screens where the number of rows and sections is predetermined.
> 
> 
> * **Requirements**: Static cells can **only** be hosted in a `UITableViewController` in Interface Builder. You do not implement data source methods (`numberOfRowsInSection`, `cellForRowAt`) for static tables—UIKit renders them automatically from the storyboard.
> 
> 
> 
> 

---

## 6. Navigation, Segues & Data Passing

### 6.1 Create Presentation Segues in Storyboard

1. **Add Emoji Segue**: Control-drag from the **+** (Add) button in `EmojiTableViewController` to the **Navigation Controller** embedding `AddEditEmojiTableViewController`. In the popup, choose **Present Modally** and set its **Identifier** to `AddEmoji` in the Attributes Inspector.


2. **Edit Emoji Segue**: Control-drag from the **Prototype Cell** (`EmojiCell`) in `EmojiTableViewController` to the same Navigation Controller. In the popup, choose **Present Modally** and set its **Identifier** to `EditEmoji` in the Attributes Inspector.



### 6.2 Handle Segue Preparation and Unwind Actions

Add the navigation and unwind methods to **EmojiTableViewController.swift**:

```swift
// MARK: - Navigation & Unwind

override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    guard segue.identifier == "AddEmoji" || segue.identifier == "EditEmoji" else { return }
    let navController = segue.destination as! UINavigationController
    let addEditController = navController.topViewController as! AddEditEmojiTableViewController

    if segue.identifier == "EditEmoji", let indexPath = tableView.indexPathForSelectedRow {
        addEditController.emoji = emojis[indexPath.row]
    }
}

@IBAction func unwindToEmojiTableView(_ segue: UIStoryboardSegue) {
    guard segue.identifier == "saveUnwind",
          let sourceViewController = segue.source as? AddEditEmojiTableViewController,
          let emoji = sourceViewController.emoji else { return }

    if let selectedIndexPath = tableView.indexPathForSelectedRow {
        // Edit mode: update existing row
        emojis[selectedIndexPath.row] = emoji
        tableView.reloadRows(at: [selectedIndexPath], with: .none)
    } else {
        // Add mode: insert new row at the end
        let newIndexPath = IndexPath(row: emojis.count, section: 0)
        emojis.append(emoji)
        tableView.insertRows(at: [newIndexPath], with: .automatic)
    }
}

```

### 6.3 Connect the Unwind Segue in Storyboard

1. In `AddEditEmojiTableViewController` scene, control-drag from the **Save** button up to the **Exit** icon (the orange square with an exit arrow in the scene header bar).


2. In the popup menu, choose `unwindToEmojiTableView:`.


3. In the Document Outline under `AddEditEmojiTableViewController`, expand the newly created unwind segue, open the **Attributes Inspector**, and set **Identifier** to `saveUnwind`.



> #### Segues, `prepare(for:sender:)`, and Unwind Segues
> 
> 
> * **Segue (`UIStoryboardSegue`)**: Represents a visual screen transition defined in Interface Builder.
> 
> 
> * **`prepare(for:sender:)`**: The lifecycle hook executed right before a segue transition occurs. It allows the presenting view controller to access `segue.destination` and pass data forward to the destination view controller.
> 
> 
> * **Unwind Segue**: A backwards navigation mechanism. By defining an `@IBAction` accepting a `UIStoryboardSegue` parameter on a parent controller, any child controller can unwind back to that parent, execute `prepare(for:sender:)` to assemble returning data, and deliver it to the parent's unwind action.
> 
> 


## 7. To Learn More

* Developing in Swift – Data Collections, Unit 1, Lessons 5–7
