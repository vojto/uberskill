# iOS / Swift
## 🧰 Helper Files
1. The iOS project should have a Helpers/ folder somewhere. If not, create the Helpers/ folder.
2. The Helpers/ folder should have one file per type of helpers, for example Strings.swift, Audio.swift or DateTime.swift.
3. When writing code, always move generic helpers to appropriate file (feel free to create it) in this folder.
4. Proactively keep this folder well organized. Move code around, rename files, add new files.
***
## 🔑 User Defaults Keys
There must be a struct or enum listing all user defaults keys. Avoid using magic strings when accessing, always use a named constant.
***
## 📄 SwiftUI Components
Place each SwiftUI component in its own file unless it is extremely small and local.
***
## 🖼️ SwiftUI Previews
1. Preview pure presentational components using plain values and closures.
2. If the target view depends on SwiftData or environment objects, propose separating it into a stateful container and a pure presentational component. Explicitly ask the user to approve that refactor before implementing the preview.
3. Do not create in-memory SwiftData containers or instantiate app environment objects solely to support previews.
***
## 🧩 Reusable UI Components
When implementing or modifying UI, look for patterns that repeat or should stay visually consistent.
Reuse or extend an existing shared component when possible. Otherwise, extract the pattern and replace every duplicate.
Prefer one component with multiple styles over separate components that look or behave the same.
Examples:
- Buttons with shared styling or behavior.
- Badges, tags, and status indicators.
- Repeated cards, rows, and list items.
- Empty, loading, and error states.
- Form fields with shared labels or validation.
***
## 🔐 AuthenticatedView
If app uses authentication, it should have a separate `AuthenticatedView`, a thin wrapper around `MainView` or equivalent. Put callbacks that should run once the user is authenticated here.
***
## 🪟 Sheet Modals (Dialogs)
Any sheet modal UI should be implemented in its own separate file, not inline in an existing view file.
