# requirements-review-skill


A Claude skill for QA analysis of requirements before test design. It has two modes:

Review — checks a requirement, user story, task, or acceptance criteria for readiness for test analysis and produces a structured, quote-backed report.
Atomic decomposition — breaks vague requirements into atomic requirements and lists the open questions for each.

The skill instructions are in English; the report it returns is always in Ukrainian, regardless of the language of the request or the requirement. Quotes from the requirement stay in their original language.

When it triggers
"перевір цю вимогу" / "review this requirement"
"зроби рев'ю вимог"
"чи готова вимога до тестування" / "is this ready for QA"
"напиши критерії прийняття"
"розклади на атомарні вимоги" / "що тут треба тестувати" (decomposition mode)
The user pastes a requirement and asks for feedback before writing test cases
Inputs

Provided directly in the conversation (pasted text, uploaded file, or design link). The skill uses whatever is available: title, description, acceptance criteria, design links, comments, and other related requirements. If the input is too thin to review (e.g. only a title), it asks for more before analyzing.

What it checks
#	Criterion (UA)	Criterion (EN)
1	Атомарність	Atomicity — one requirement, one behavior
2	Однозначність	Unambiguity — no undefined vague terms ("швидко", "зручно", "коректно"); clear AC
3	Повнота	Completeness — expected behavior, negative scenarios, boundaries, error handling
4	Несуперечність	Consistency — no conflicts within the requirement, between title / description / AC / comments, or with other shared requirements
5	Неповторюваність	Non-duplication — no overlap with other shared requirements
6	Тестованість	Testability — every statement is checkable
7	Реалізовність	Feasibility — nothing technically or logically impossible

Criteria 4 and 5 are evaluated against other requirements only if the user actually shared them. Otherwise #4 covers internal consistency only and #5 is marked "Не перевірено".

The skill also lists regression risks: existing functionality the change could affect.

Core rules
Every finding must be backed by an exact quote from the requirement. Nothing is flagged on assumption.
Missing information becomes a question to the BA, never a stated defect.
No invented behavior, business logic, limits, statuses, roles, file types, error messages, or API behavior.
No questions already answered in the provided text.
No test cases unless explicitly requested.
Output format (Ukrainian)
Вердикт — at the top, bolded:
Готова — no findings, or Minor only, no open questions
Потребує уточнень — Major findings and/or open BA questions, no Critical
Не готова — at least one Critical finding
Знахідки — table, one row per criterion (extra rows for multiple issues). Passed criteria are shown as ✅, not omitted.
Критерій	Статус	Цитата	Проблема	Severity
Що добре — strengths, each backed by a quote.
Питання до BA — blocking questions first, each with:
Питання — the question
Чому важливо — why it affects test analysis
Варіант відповіді — a suggested option for the BA to confirm, not stated as fact
Регресійні ризики — omitted if none.
Пропоновані критерії прийняття — only when AC are missing, need rework, or the user asks. Confirmed behavior only; unknowns use [TBD: ...] placeholders.
Severity levels
Critical — blocks test analysis (e.g. untestable core behavior, direct contradiction)
Major — test analysis possible but likely to miss cases if unresolved
Minor — clarity/style issue, low impact on coverage
Multiple requirements

If several requirements are pasted at once, the skill asks whether to review them individually or check them against each other for consistency and duplication, unless already specified.

Atomic decomposition mode

Splits each source statement into atomic requirements using three techniques:

Input / Processor / Action / Output — inputs, processing, action, and output of each statement become separate requirements where they carry their own behavior.
І / АБО / ЯКЩО — split on "and", "or", and "if" (including the case where the condition does not hold).
«А що, якщо…» — invalid input, boundaries, interruptions, repeats, timeouts, concurrency.

The output is mostly questions, not assumed answers. Each atomic requirement is traced to its source statement, possible duplicates are raised as questions, and non-requirements (motivation, context) are listed separately with their impact on test priority.

№	Вихідна вимога	Атомарна вимога	Питання / невизначеність

Followed by «Не є вимогою» (if any) and «Ключові блокувальні питання».

See examples/wallet-top-up.md for a worked example (wallet balance top-up).

Installation

Download SKILL.md and the examples/ folder into a folder named requirement-quality-assistant/ and add it as a skill in Claude (Settings → Capabilities → Skills), or place the folder in your agent's skills directory (e.g. .agents/skills/ or .claude/skills/).

Files
SKILL.md — the skill definition and full instructions Claude follows
examples/wallet-top-up.md — worked example for the decomposition mode
README.md — this file
