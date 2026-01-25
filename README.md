# UCOD

## ABOUT
- UCOD (/ˈjuːkɒd/, UI Class Operation Diagram) is a modeling notation based on class diagrams and screen transition diagrams, designed to visualize how users use a product.
- It is designed for documentation, system analysis, and AI-assisted test design and implementation.

![UCOD Sample](./samples/ucod-sample.png)

---

## How to Describe UCOD
- Each UI class represents a single Page, Modal, Component, or similar UI unit, and is described using the following three-layer structure:
    - UI class name: the name of the screen, modal, or UI component
    - UI elements: the interactive or visual elements contained in the class (e.g. buttons, text fields)
    - User actions: the actions performed by the user on the screen, described below a horizontal divider (`---`)
- UI elements are listed with prefixes such as `(Button)`, `(TextBox)`, `(Link)`, etc.
- If UI classes differ slightly in their contained UI elements, additions or removals can be expressed using `+` or `-` at the beginning of the line.
- Transitions between UI classes are expressed using arrows (`-->` or `<->`), optionally annotated with the triggering UI element.
    - If the trigger is self-explanatory (e.g. pressing the “Settings” button opens the settings page), the label may be omitted.
- [Design guide](./docs/ucod-design-guide.en.md)

---

## Use Cases for UCOD
- Organizing how to use products:
    - Specifications for web-based products in particular can be organized in a way that is friendly to both humans and generative AI
    - Also useful for identifying improvement points in the system, such as whether UI elements are concentrated on a specific page, or whether there are too many UI element additions/deletions per UI class
- AI-assisted test case design:
    - By providing UCOD to generative AI and covering the UI elements and page transitions described in UCOD, you can design a test suite
    - Test viewpoint diagrams may also be provided to generative AI
    - [Sample prompt](./prompts/generate-testcases-by-ucod.md)
- AI-assisted automated test implementation:
    - By providing UCOD to generative AI and having it interpret UCOD as a design model for POM (Page Object Model), Page Objects, Assertions, and Test Code can be automatically generated
    - [Sample prompt](./prompts/generate-pom.md)

---

## Directory Structure
```
.
├─ README.md                 # this file
├─ docs/
│  ├─ ucod-design-guide.en.md
│  └─ ucod-design-guide.ja.md
├─ prompts/
│  ├─ generate-pom.md             # Sample prompt for AI-assisted implementation of automated tests using POM
│  └─ generate-testcases-by-ucod.md  # Sample prompt for AI-assisted test case design
└─ samples/
   ├─ ucod-sample.puml
   └─ test-viewpoint-sample.puml
```