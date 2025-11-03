# UCOD

## ABOUT
- UCOD(/juːkɒd/, UI Class Operation Diagram) is a UML-based diagram format. Designed for AI-assisted testing, documentation, and system analysis.
- Each class block describes a single Page, Modal, Component, etc., and is organized into three layers:
    - Class name: the name of the screen, modal, or UI component
    - UI elements: the interactive or visual elements within that class (e.g., buttons, text box)
    - User Actions: the purpose or goal of the user, described after a horizontal divider (---)
![UCOD Sample](./samples/ucod-sample.png)

---

## How to Write UCOD
- Inside the class block, list UI elements with prefixes such as (Button), (TextBox), (Link), etc.
- Nested elements (such as icons inside a button group or items inside a dropdown) can be represented by indentation, or—if the element is large or reused—can be extracted into a separate class.
    - If there are separate classes, you can use the “package” keyword to group them.
- If an extracted component has minor visual differences depending on the page, use + or - at the start of the line to indicate additions or removals (a diff-like notation).
- Transitions between classes are expressed with arrows (--> or <->), optionally annotated with the triggering UI element.
    - When the trigger is self-explanatory (e.g., pressing the “Settings” button opens the settings page), the label can be omitted.
- You can freely add or consolidate class types such as Page, Modal, Component and element types such as (Button), (TextBox) according to your organization’s standards.
- Likewise, color schemes under skinparam class can be customized for readability or team preference.

---

## Directory Structure
```
.
├─ README.md                 # this file
├─ docs/
│  ├─ ucod-design-guide-en.md
│  └─ ucod-design-guide-ja.md
├─ prompts/
│  ├─ generate-pom.md        # Example prompt for generating automated tests
│  └─ generate-testcases.md  # Example prompt for generating test cases
└─ samples/
   ├─ ucod-sample.puml
   └─ test-viewpoint-sample.puml
```