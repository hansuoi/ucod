# UCOD Design Guide

## Overview
- This document describes the syntax, design principles, and usage guidelines for UCOD (UI Class Operation Diagram) written in PlantUML.

---

## 1. Classes
### Class Overview
- Each UCOD class is written in a three-layer structure:
    1. {Stereotype} {Class Name}
    2. {UI Elements Type} {UI Elements Name}
    3. {User Actions}
```plantuml
class "Login Page" <<Page>> {
  (TextBox) Password
  (Button) Log In
  ---
  Perform login
}
```

### Stereotypes
- `<<Page>>`, `<<Modal>>`, `<<Component>>`, `<<Overlay>>`, etc.
    - Additional stereotypes can be defined as needed to match your system’s architecture.

### UI Elements Type
- `(Button)`, `(TextBox)`, `(Link)`, `(CheckBox)`, `(RadioButton)`, `(Text)`, `(Img)`, etc.
    - Additional UI elements type can be defined as needed to match your system’s architecture.

### UI Elements
- There are two primary ways to represent child elements of a UI element:
    - Indentation(`\t`)
        - When the number of child elements is small, you can list them directly under the parent element using indentation.
        - The indentation level expresses the hierarchical structure (parent–child relationship).
        - Elements with the same indentation level are considered siblings.
    - Separate Class
        - When a UI element has many child elements, define it as a separate class.
        - Draw an arrow `-->` from the parent element to the new class to indicate the relationship.
        - Use a package to group related classes. You may also assign an alias with the as keyword.
        - If a UI element differs slightly on specific pages, prefix the modified child element with `+` or `-` to indicate additions or removals (diff-like notation).
```plantuml
package "Header" as HeaderPackage {
  class "Header" as HeaderClass <<Component>> {
    (Button) Share Button
      \t(Button) Twitter
      \t(Button) Copy Link
    (Button) Hamburger Menu
    ---
  }

  class "Hamburger Menu" <<Overlay>> {
    (Button) Log Out
    ---
    Log out from the system
  }
}

class "Settings Page" <<Page>> {
  <<Component>> Header
    \t+ (Text) Percentage of configured items
  ---
}

"HeaderClass" --> "Hamburger Menu"
```

### User Actions
- The third layer of the class represents user actions or use cases that can be performed on that screen.
- Single, self-contained operations (e.g., clicking or typing into a field) can be omitted. High-level actions that combine multiple UI operations should be written here.
    - For example, “Log in” combines entering an ID, entering a password, and clicking the login button — therefore, it belongs in the third layer.
    - In contrast, “Enter ID” is a single, explicit operation that can be inferred from `(TextBox) ID Input Field`, and can therefore be omitted.

---

## 2. Arrows (Transitions)
- UCOD uses arrows (-->) to represent transitions between screens, components, or overlays.

- `A --> B : (Button) exampleButton`
    - Indicates that clicking the exampleButton on screen A triggers a transition to screen B.
    - The trigger label `(Button) exampleButton` may be omitted when the action is self-explanatory (e.g., clicking the “Settings” button opens the settings page).
- `A --> B : (Button) exampleButton if {condition}`
    - Indicates that, under a specific condition, clicking the exampleButton on screen A transitions to screen B.
- `A <-> B`
    - Represents a bidirectional transition when the trigger is self-evident.
- (Omitted)
    - Implicit transitions (e.g., closing a modal returns to the previous page) may be omitted for simplicity.

---

## 3. UCOD as a Design Model for POM-Based Test Automation
- UCOD can serve as a design-level model for implementing automated tests using the Page Object Model (POM) pattern.

### Page Object Implementation
- (Assuming Playwright with TypeScript)

- Each UCOD class can be directly mapped to a corresponding POM class:
    - `<<Page>>` → PageObject
    - `<<Component>>` → Separate class, imported and composed into a Page Object
    - `<<Modal>>` / `<<Overlay>>` → Nested within the parent Page Object or Component
- Each UI element such as `(Button)` or `(TextBox)` is implemented as a Locator.
- The third layer (User Actions) in UCOD corresponds to high-level methods that combine multiple UI operations.
    - e.g. `login({ email, password })`, `changePassword({ old, new })`

### Test Code Implementation
- Each arrow in UCOD represents a normal flow — that is, “under certain conditions, performing an action triggers a transition to another screen or opens a new modal.”
- Therefore, following an arrow from one class to another corresponds directly to a valid end-to-end test case.