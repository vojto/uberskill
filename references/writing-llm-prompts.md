# Writing LLM prompts
## 💬 Language
Write prompts so a person can understand them quickly. Use short sentences and common words.
Prefer simple wording:
- "Use" instead of "utilize."
- "Keep the original entries separate" instead of "preserve the entry boundaries."
- "Handle both tasks here" instead of "completely resolve both responsibilities here."
Avoid being overprescriptive. Require a specific method only when it matters; otherwise, leave room for the model to choose a good approach.
Start with the smallest prompt that expresses the required behavior. Add constraints and edge-case instructions only after observing failures.
***
## 🧭 Logical Flow
Introduce an idea before giving instructions about it.
Order instructions as a person would perform them: identify the situation, check existing options, act, then clean up or verify.
Make each sentence follow naturally from the previous one.
***
## 🛠️ Tools
- List every available tool near the top of the prompt so people can see them while reading it.
- Wrap every tool name referenced in prompt text in backticks.
- When one tool submits the agent's final result, name it `submit_<result>`, for example `submit_food_inputs`.
***
## 📝 Markdown Spacing
- Remove blank lines from any prompt Markdown you encounter, my editor doesn't like them.
***
## 🪁 Providing Examples
If you're updating prompt because of eval failure, don't provide the same examples to prompt that were used in the eval.
That would beat the purpose of evals - evals' test cases should differ from examples provided in the prompt.
***
## ✅ Final Check
Read the prompt once as a busy person. Remove repetition and anything that does not help the model decide or act.
