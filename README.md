## iOS Practical Worksheet: Building an Emoji Dictionary App with Add, Edit, Delete, and Reorder Functionalities

In this worksheet, you will build an Emoji Dictionary app using a table view to display a list of emojis, each with a symbol, name, description, and usage.

---

### Part 1: Setting Up the Project and Navigation Controller

1. **Create a New Xcode Project**

   - Open Xcode and create a new iOS project with a **Single View App** template.
   - Name the project **EmojiDictionary** and select **Swift** as the language.
2. **Adding a Navigation Controller**

   - In **Main.storyboard**, drag a **Navigation Controller** from the **Object Library** onto the scene.
   - A **Table View Controller** is automatically added as the root view controller of this **Navigation Controller**. This **Table View Controller** will serve as the main screen displaying the list of emojis.
3. **Set the Navigation Controller as the Initial View Controller**

   - Select the **Navigation Controller** in the storyboard.
   - In the **Attributes Inspector**, check the **Is Initial View Controller** box.
   - This sets the navigation controller as the starting point of your app.
4. **Setting the Title and Adding Buttons**

   - Select the **Table View Controller**.
   - In the **Navigation Bar**, double-click the title and change it to **Emoji Dictionary**.
   - Add a **Bar Button Item** to the right side of the navigation bar, set its **System Item** to **Add**. This button will be used to add a new emoji.
   - The **Edit** button will appear automatically when the view controller enters edit mode, allowing you to delete or reorder emojis.
5. **Creating a Custom Table View Controller Class**

   - Go to **File > New > File**, select **Cocoa Touch Class**, and create a new class called **EmojiTableViewController** as a subclass of `UITableViewController`.
   - Back in the storyboard, select the **Table View Controller** and set its **Class** to `EmojiTableViewController` in the **Identity Inspector**.

### Part 2: Setting Up the Emoji Model and Sample Data

1. **Defining the Emoji Model**

   - Create a new **Swift file** named `Emoji.swift`.
   - Define the `Emoji` struct to store data for each emoji:
     ```swift
     struct Emoji {
         var symbol: String
         var name: String
         var description: String
         var usage: String
     }
     ```
2. **Setting Up Sample Emoji Data**

   - In `EmojiTableViewController.swift`, define an array of sample emojis:
     ```swift
     var emojis: [Emoji] = [
         Emoji(symbol: "😀", name: "Grinning Face", description: "A typical smiley face.", usage: "happiness"),
         Emoji(symbol: "😕", name: "Confused Face", description: "A confused, puzzled face.", usage: "unsure what to think; displeasure"),
         Emoji(symbol: "😍", name: "Heart Eyes", description: "A smiley face with hearts for eyes.", usage: "love of something; attractive"),
         Emoji(symbol: "🐢", name: "Turtle", description: "A cute turtle.", usage: "Something slow"),
         // Add more emojis as needed
     ]
     ```

### Part 3: Customizing the Table View Cell with Stack Views and Labels

1. **Adding a Prototype Cell**

   - Select the **Table View** in the **EmojiTableViewController** scene.
   - In the **Attributes Inspector**, set **Prototype Cells** to `1`.
   - Select the new prototype cell and set its **Identifier** to `EmojiCell`.
2. **Creating the Custom Cell Class in EmojiTableViewController.swift**

   - At the end of `EmojiTableViewController.swift`, add a separate `EmojiTableViewCell` class to define the custom cell:
     ```swift
     class EmojiTableViewCell: UITableViewCell {
         @IBOutlet var symbolLabel: UILabel!
         @IBOutlet var nameLabel: UILabel!
         @IBOutlet var descriptionLabel: UILabel!

         func update(with emoji: Emoji) {
             symbolLabel.text = emoji.symbol
             nameLabel.text = emoji.name
             descriptionLabel.text = emoji.description
         }
     }
     ```
3. **Configuring the Prototype Cell in the Storyboard**

   - Open **Main.storyboard** and select the **Prototype Cell** in your **EmojiTableViewController** scene.
   - In the **Identity Inspector**, set the **Class** of the prototype cell to `EmojiTableViewCell`.
   - Set the **Identifier** of the cell to `EmojiCell` in the **Attributes Inspector**.
4. **Connecting Outlets for EmojiTableViewCell in the Storyboard**

   - Open **Main.storyboard** and select the **Prototype Cell**.
   - In the **Assistant Editor**, ensure `EmojiTableViewController.swift` is open.
   - Control-drag from each label in the prototype cell to the corresponding `@IBOutlet` in `EmojiTableViewCell`:
     - Connect the emoji symbol label to `symbolLabel`.
     - Connect the name label to `nameLabel`.
     - Connect the description label to `descriptionLabel`.

### Part 4: Implementing the Table View Data Source and Editing Features

1. **Implementing the Data Source Methods in EmojiTableViewController**

   - In `EmojiTableViewController`, implement the table view data source methods to display the emoji data using your custom cell:
     ```swift
     override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
         return emojis.count
     }

     override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
         let cell = tableView.dequeueReusableCell(withIdentifier: "EmojiCell", for: indexPath) as! EmojiTableViewCell
         let emoji = emojis[indexPath.row]
         cell.update(with: emoji)
         cell.showsReorderControl = true // Enables the reorder control
         return cell
     }
     ```
2. **Setting Up the Add Button and Edit Mode**

   - In `viewDidLoad`, configure the **Add** button and enable editing mode:
     ```swift
     override func viewDidLoad() {
         super.viewDidLoad()
         navigationItem.leftBarButtonItem = editButtonItem
     }
     ```
3. **Supporting Deletion in Table View**

   - Add the following method to support deleting emojis:
     ```swift
     override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
         if editingStyle == .delete {
             emojis.remove(at: indexPath.row)
             tableView.deleteRows(at: [indexPath], with: .fade)
         }
     }
     ```
4. **Supporting Reordering in Table View**

   - Add methods to support reordering emojis in the table:
     ```swift
     override func tableView(_ tableView: UITableView, moveRowAt fromIndexPath: IndexPath, to: IndexPath) {
         let movedEmoji = emojis.remove(at: fromIndexPath.row)
         emojis.insert(movedEmoji, at: to.row)
     }
     ```

### Part 5: Adding and Editing Emojis with Modal Presentation and Unwind Segues

1. **Setting Up the Modal Presentation Segue**

   - In **Main.storyboard**, select the **Add** button.
   - Control-drag from the **Add** button to the **Navigation Controller** of the **Add/Edit Emoji Table View Controller**. Choose **Present Modally** as the segue type and set the segue identifier to `"AddEmoji"`.
   - To enable editing of existing emojis, also control-drag from the the **prototype cell** to the **Navigation Controller** of the **Add/Edit Emoji Table View Controller**, using `"EditEmoji"` as the identifier and **Present Modally** as the segue type.
2. **Configuring the Destination Controller in `prepare(for:)`**

   - In `EmojiTableViewController`, configure the destination controller in the `prepare(for:)` method to access `AddEditEmojiTableViewController` via the navigation controller:
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
3. **Creating a Custom Class for Add/Edit Emoji**

   - Go to **File > New > File**, select **Cocoa Touch Class**, and create a new class called **AddEditEmojiTableViewController** as a subclass of `UITableViewController`.
   - In the storyboard, select the new **Table View Controller** and set its **Class** to `AddEditEmojiTableViewController` in the **Identity Inspector**.
4. **Setting Up the Table View Style and Layout for Add/Edit Screen**

   - With the **Add/Edit Emoji Table View Controller** selected in the storyboard, go to the **Attributes Inspector**.
   - Change the **Style** of the table view to **Inset Grouped**.
   - Set the **Content** of the table view to **Static Cells**.
   - In **Sections**, set

 the number of sections to **4** (one for each property of the emoji).

5. **Adding Titles and Text Fields for Each Section**

   - Configure each section with a title and text field:
     - **Section 1 (Symbol)**: Set the **Title** to **Symbol** and add a text field.
     - **Section 2 (Name)**: Set the **Title** to **Name** and add a text field.
     - **Section 3 (Description)**: Set the **Title** to **Description** and add a text field.
     - **Section 4 (Usage)**: Set the **Title** to **Usage** and add a text field.
6. **Connecting Outlets and Configuring Save Logic**

   - Connect the text fields to `AddEditEmojiTableViewController` with `@IBOutlet` connections:

     ```swift
     @IBOutlet weak var symbolTextField: UITextField!
     @IBOutlet weak var nameTextField: UITextField!
     @IBOutlet weak var descriptionTextField: UITextField!
     @IBOutlet weak var usageTextField: UITextField!
     @IBOutlet weak var saveButton: UIBarButtonItem!
     ```
   - Add an `@IBAction` method for `textEditingChanged` to update the state of the **Save** button dynamically:

     ```swift
     @IBAction func textEditingChanged(_ sender: UITextField) {
         updateSaveButtonState()
     }

     func updateSaveButtonState() {
         let symbolText = symbolTextField.text ?? ""
         let nameText = nameTextField.text ?? ""
         let descriptionText = descriptionTextField.text ?? ""
         let usageText = usageTextField.text ?? ""
         saveButton.isEnabled = !symbolText.isEmpty && !nameText.isEmpty && !descriptionText.isEmpty && !usageText.isEmpty
     }
     ```
7. **Implementing the `prepare(for:)` Method in AddEditEmojiTableViewController**

   - Before performing the unwind segue, implement `prepare(for:)` in `AddEditEmojiTableViewController` to save the emoji with the current field values:
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
8. **Setting Up the Unwind Segue in the Storyboard**

   - To set up an unwind segue:
     - In **Main.storyboard**, control-drag from the **Save** button to the **Exit** icon and select `unwindToEmojiTableView`.
     - In the **Attributes Inspector**, set the identifier to `"saveUnwind"`.
9. **Implementing the Unwind Segue Action in EmojiTableViewController**

   - In `EmojiTableViewController.swift`, implement the `unwindToEmojiTableView` method:
     ```swift
     @IBAction func unwindToEmojiTableView(segue: UIStoryboardSegue) {
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

---

## To Learn More

1. Developing in Swift - Data Collections - Unit 1, Lessons 5, 6 and 7
