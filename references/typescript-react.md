# TypeScript and React
## 🦋 Prettier Formatting
* Make sure Prettier is installed for automatic code formatting
* Set it up such that we never use semicolons!
***
## ⚛️ React Components
- Use kebab-case for all React component filenames.
- Each component should live in its own file, except for components that are very related to each other. (For example a List with its ListRow component.)
- Eagerly and early break up bigger components into smaller ones.
***
## 🗂️ React Structure
* Have a folder called `ui` for components that aren't app-specific, such as Button, Dialog, Dropdown, etc.
* Have a folder called `screens` for components that represent one screen, such as MainScreen, LoginScreen, SettingsScreen, DashboardScreen, etc.
* Have a folder called `components`, for app-specific components such as Sidebar, Toolbar, or TaskRow or TaskDetailView. Feel free to create subfolders inside components/ folder.
