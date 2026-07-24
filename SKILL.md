---
name: uberskill
description: Apply Vojto's programming preferences across projects. Use for implementing, changing, reviewing, or refactoring code in any language, especially when deciding code structure, helper extraction, naming, change scope, and verification.
---
# Uberskill
Read `references/general-programming.md` for every programming task. Then read only the relevant references:
- Object-oriented code: `references/object-oriented-programming.md`
- TypeScript or React: `references/typescript-react.md`
- iOS or Swift: `references/ios-swift.md`
- Ruby on Rails: `references/ruby-on-rails.md`
- LLM prompts: `references/writing-llm-prompts.md`
## 🧱 Structure and Refactoring
- Extract code when it is general, noisy, repeated, or distracts from the owning type's purpose. Do not extract merely to hide code.
- Prefer cohesive types and functions with clear responsibilities over large multipurpose units.
***
## 📦 Dependencies and Compatibility
- Reuse existing project utilities before adding new dependencies.
- Add a dependency only when its benefit clearly outweighs its maintenance cost.
- Preserve supported platforms, language versions, public APIs, and persisted data unless the request says otherwise.
***
## ✅ Verification
- Run the narrowest relevant formatter, build, lint, and tests after editing.
- Expand verification when the change affects shared code or broad behavior.
- Check the final diff for accidental changes, debug code, generated noise, and exposed secrets.
- Report what changed and what verification ran; state any verification that could not run.
***
## 🔍 Final Check
- Does each changed type read like the thing it is responsible for?
- Is setup, parsing, file, date, or network plumbing obscuring domain behavior?
- Would a topic-based reusable helper make the main code easier to understand?
- Is every new abstraction justified by current code?
- Is the change focused, compatible, and verified?
