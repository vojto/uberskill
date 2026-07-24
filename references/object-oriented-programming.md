# OOP
## 🗂️ Grouping and Ordering Methods in a Class
1. Methods in a class should be grouped by purpose, separated with a comment, such as `// MARK:` in Swift, but depending on language.
2. Most classes should have "Lifecycle" as the first group with things such as constructor and any initialization code.
3. Public methods should go first, and private after.
4. Methods that call other, helper methods, within the same class should go first, and the helper methods should go after.
***
## ✂️ Refactoring Long Methods in Classes
1. Whenever methods span over 10 lines or so, consider refactoring.
2. If you're finding generic code (applicable across domains), refactor out into pure, helper methods. Place them in a generic location, such as helpers/ folder.
3. If the code isn't generic plumbing, consider introducing a private helper method in the same class to break the long method into shorter methods.
