# Firebase
## 🔥 Project conventions
- For Whisper Memos, use Firebase project `whisper-day`.
- Treat the iOS app as the reference implementation for business logic shared with the website or backend.
- Before changing cross-project flows, Firestore rules, uploads, or offline sync, check relevant `llm-docs/`.
- When changing Firestore fields, update the maintained schema documentation alongside the code.
***
## 🧩 Feature organization
- Keep framework and responsibility-specific files in their established top-level folders, including `src/api/`, `src/hooks/`, `src/db/`, `src/helpers/`, and `src/schemas/`.
- Put the remaining code that belongs to one feature in `src/lib/<feature>/`. This includes feature-specific orchestration, integrations, and implementation details that are not shared infrastructure.
- Reserve broad infrastructure folders such as `src/backends/` for implementations that participate in the standard shared backend flow. Keep special-case implementations inside their owning feature folder.
***
## 🗃️ Firestore modeling
- Group three or more related user settings in one nested object.
- When migrating flat fields into an object, read the object first and fall back to legacy fields until migration is complete.
- Use `Date()` for iOS-written timestamps needed immediately by local UI. A server timestamp is locally unresolved until the round trip. Prefer server timestamps for backend writes where server-clock accuracy matters.
- Omit `includeMetadataChanges` when false; false is the default.
***
## 🛠️ Admin and Storage
- In Whisper Memos functions, import Admin instances from `src/firebase-setup`.
- Use the explicit Storage bucket `whisper-day.appspot.com`.
- Memo audio paths use `{userId}/memos/{filename}`.
- Run local production-access scripts with the checked-in Admin credential and the repository's mise-managed runtime.
***
## ✅ Rules and deployment
- Firestore rules live at `firebase/firestore.rules` and are configured by `firebase/firebase.json`.
- Validate rule changes with a dry run before deploying them.
- Production deployment is an external mutation and requires the user's request or confirmation.
