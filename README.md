# iOS Practical Worksheet: Building an Emoji Dictionary App with Add, Edit, Delete, and Reorder Functionalities

In this worksheet, you will build an Emoji Dictionary app using a table view to display a list of emojis, each with a symbol, name, description, and usage.

---

## Part 1: Setting Up the Project and Navigation Controller

1.1 Create a New Xcode Project

   * Open Xcode and create a new iOS project with the **App** template.
   * Name the project **EmojiDictionary** and select **Swift** as the language and **Storyboard** as the interface.

1.2 Adding a Navigation Controller

   * In **Main.storyboard**, select the **View Controller** that was created by default and delete it.
   * From the **Object Library**, drag a **Navigation Controller** into the storyboard.
   * A **Table View Controller** will automatically appear as the root view controller of this navigation controller.
     This table view controller will display the list of emojis.

1.3 Set the Navigation Controller as the Initial View Controller

   * Select the **Navigation Controller** in the storyboard.
   * In the **Attributes Inspector**, check **Is Initial View Controller**.

1.4 Setting the Title and Adding Buttons

   * Select the **Table View Controller**.
   * In the **Navigation Bar**, double-click the title and change it to **Emoji Dictionary**.
   * Add a **Bar Button Item** to the **right** side of the navigation bar and set its **System Item** to **Add**.
   * The **Edit** button will be added programmatically later to enable delete and reorder functionalities.

1.5 Creating a Custom Table View Controller Class

   * Go to **File > New > File...**, choose **Cocoa Touch Class**, and create a class named **EmojiTableViewController**, subclass of `UITableViewController`.
   * In **Main.storyboard**, select the **Table View Controller** and set its **Class** to `EmojiTableViewController` in the **Identity Inspector**.

---

## Part 2: Setting Up the Emoji Model and Sample Data

2.1 Defining the Emoji Model

   * Create a new Swift file named `Emoji.swift`.
   * Define the `Emoji` struct:

     ```swift
     struct Emoji {
         var symbol: String
         var name: String
         var description: String
         var usage: String
     }
     ```

2.2 Setting Up Sample Emoji Data

   * In `EmojiTableViewController.swift`, define sample emojis:

     ```swift
     var emojis: [Emoji] = [
         Emoji(symbol: "😀", name: "Grinning Face", description: "A typical smiley face.", usage: "happiness"),
         Emoji(symbol: "😕", name: "Confused Face", description: "A confused, puzzled face.", usage: "unsure what to think; displeasure"),
         Emoji(symbol: "😍", name: "Heart Eyes", description: "A smiley face with hearts for eyes.", usage: "love of something; attractive"),
         Emoji(symbol: "🐢", name: "Turtle", description: "A cute turtle.", usage: "something slow")
     ]
     ```

---

## Part 3: Customizing the Table View Cell with Stack Views and Labels

3.1 Adding a Prototype Cell

   * Select the **Table View**.
   * In the **Attributes Inspector**, set **Prototype Cells** to `1`.

3.2 Creating the Custom Cell Class

   * Go to **File > New > File...**, choose **Cocoa Touch Class**, and create a class named **EmojiTableViewCell**, subclass of `UITableViewCell`.
   * Define it as follows:

     ```swift
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

3.3 Configuring the Prototype Cell in the Storyboard

   * Select the **Prototype Cell** in the `EmojiTableViewController` scene.
   * In the **Identity Inspector**, set its **Class** to `EmojiTableViewCell`.
   * In the **Attributes Inspector**, set its **Identifier** to `EmojiCell`.

3.4 Adding and Arranging the Labels in the Prototype Cell

   * Drag a **Horizontal Stack View** from the Object Library into the prototype cell.
     * Use **Auto Layout constraints** to pin the main stack view: Top, Bottom, Leading, and Trailing to the cell’s **content view** (with standard spacing).

   * Inside the stack view, add **three labels**:
     1. **Symbol Label** (for the emoji character)
        * In the **Size Inspector**, increase its **Horizontal Content Hugging Priority** in order for it to wrap text.
     2. **Name Label** (for the emoji name)
     3. **Description Label** (for the emoji description)

   * Embed the **Name Label** and **Description Label** in a **Vertical Stack**.

3.5 Connecting Outlets for EmojiTableViewCell

   * Open the **Assistant Editor** with `EmojiTableViewCell.swift` visible.
   * Control-drag from each label (or the other way around) to create these connections:

     * Emoji symbol label → `symbolLabel`
     * Emoji name label → `nameLabel`
     * Emoji description label → `descriptionLabel`

---

## Part 4: Implementing the Table View Data Source and Editing Features

4.1 Implement the Data Source Methods

   ```swift
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
   ```

4.2 Add Edit Button

   ```swift
   override func viewDidLoad() {
       super.viewDidLoad()
       navigationItem.leftBarButtonItem = editButtonItem
   }
   ```

4.3 Support Deletion

   ```swift
   override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
       if editingStyle == .delete {
           emojis.remove(at: indexPath.row)
           tableView.deleteRows(at: [indexPath], with: .fade)
       }
   }
   ```

4.4 Support Reordering

   ```swift
   override func tableView(_ tableView: UITableView, moveRowAt fromIndexPath: IndexPath, to: IndexPath) {
       let movedEmoji = emojis.remove(at: fromIndexPath.row)
       emojis.insert(movedEmoji, at: to.row)
   }
   ```

---

## Part 5: Adding and Editing Emojis with Modal Presentation and Unwind Segues

5.1 Create the Add/Edit View Controller

   * Go to **File > New > File...**, choose **Cocoa Touch Class**, and create **AddEditEmojiTableViewController**, subclass of `UITableViewController`.
   * In **Main.storyboard**, add a new **Table View Controller**.
   * Set its **Class** to `AddEditEmojiTableViewController`.
   * Embed it in a **Navigation Controller**.

   * In `AddEditEmojiTableViewController.swift`, declare a property to hold the edited/created emoji:
     ```swift
     // AddEditEmojiTableViewController.swift
     class AddEditEmojiTableViewController: UITableViewController {
         var emoji: Emoji?
         // ...existing outlets and methods...
     }
     ```

5.2 Create Segues

   * From the **Add** button on the Emoji Dictionary screen, control-drag to the **Navigation Controller** of the Add/Edit screen and choose **Present Modally**.
     Set the **Identifier** to `"AddEmoji"`.
   * From the **Prototype Cell**, control-drag to the same navigation controller and choose **Present Modally**.
     Set the **Identifier** to `"EditEmoji"`.

5.3 Prepare for Segue

   * In the `EmojiTableViewController` file:

   ```swift
   override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
       guard segue.identifier == "AddEmoji" || segue.identifier == "EditEmoji" else { return }
       let navController = segue.destination as! UINavigationController
       let addEditController = navController.topViewController as! AddEditEmojiTableViewController

       if segue.identifier == "EditEmoji", let indexPath = tableView.indexPathForSelectedRow {
           addEditController.emoji = emojis[indexPath.row]
       }
   }
   ```

5.4 Create the Unwind Action

   * In the `EmojiTableViewController` file:

   ```swift
   @IBAction func unwindToEmojiTableView(_ segue: UIStoryboardSegue) {
       guard segue.identifier == "saveUnwind",
             let sourceViewController = segue.source as? AddEditEmojiTableViewController,
             let emoji = sourceViewController.emoji else { return }

       if let selectedIndexPath = tableView.indexPathForSelectedRow {
           emojis[selectedIndexPath.row] = emoji
           tableView.reloadRows(at: [selectedIndexPath], with: .none)
       } else {
           let newIndexPath = IndexPath(row: emojis.count, section: 0)
           emojis.append(emoji)
           tableView.insertRows(at: [newIndexPath], with: .automatic)
       }
   }
   ```

5.5 Design the Add/Edit Screen

   * Set the table view **Content** to **Static Cells**.
   * Use **Inset Grouped** style.
   * Create four sections: **Symbol**, **Name**, **Description**, **Usage**.
   * Add a **Text Field** in each section and a **Save** button in the navigation bar.

5.6 Connect Outlets and Actions

   ```swift
   @IBOutlet weak var symbolTextField: UITextField!
   @IBOutlet weak var nameTextField: UITextField!
   @IBOutlet weak var descriptionTextField: UITextField!
   @IBOutlet weak var usageTextField: UITextField!
   @IBOutlet weak var saveButton: UIBarButtonItem!
   ```

   ```swift
   @IBAction func textEditingChanged(_ sender: UITextField) {
       updateSaveButtonState()
   }

   func updateSaveButtonState() {
       let symbol = symbolTextField.text ?? ""
       let name = nameTextField.text ?? ""
       let description = descriptionTextField.text ?? ""
       let usage = usageTextField.text ?? ""
       saveButton.isEnabled = !symbol.isEmpty && !name.isEmpty && !description.isEmpty && !usage.isEmpty
   }
   ```

   * Connect the **textEditingChanged** action to each of the Text Fields.

5.7 Configure View Did Load

   ```swift
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
   ```

5.8 Save the Emoji

   ```swift
   override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
       guard segue.identifier == "saveUnwind" else { return }

       let symbol = symbolTextField.text ?? ""
       let name = nameTextField.text ?? ""
       let description = descriptionTextField.text ?? ""
       let usage = usageTextField.text ?? ""
       emoji = Emoji(symbol: symbol, name: name, description: description, usage: usage)
   }
   ```

5.9 Configure the Unwind Segue in Storyboard

   * Control-drag from the **Save** button to the **Exit** icon of the Add/Edit scene.
   * Select `unwindToEmojiTableView`.
   * In the **Attributes Inspector**, set the **Identifier** to `"saveUnwind"`.

---

## To Learn More

* **Developing in Swift – Data Collections, Unit 1, Lessons 5–7**
