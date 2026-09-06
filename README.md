# requirements-review

A Claude skill that reviews a single requirement for readiness for test analysis, against 7 fixed quality criteria. Produces a structured, citation-based report instead of free-form commentary.

## When it triggers

- "перевір цю вимогу" / "review this requirement"
- "зроби рев'ю вимог"
- "чи готова вимога до тестування" / "is this ready for QA"
- User pastes a requirement and asks for feedback before writing test cases

## What it checks

| # | Criterion (UA) | Criterion (EN) |
|---|---|---|
| 1 | Атомарність | Atomicity — one requirement, one behavior |
| 2 | Однозначність | Unambiguity — no undefined vague terms ("швидко", "зручно", "коректно") |
| 3 | Повнота | Completeness — negative scenarios and boundaries covered |
| 4 | Несуперечність | Consistency — no internal or cross-requirement conflicts |
| 5 | Неповторюваність | Non-duplication — no overlap with other shared requirements |
| 6 | Тестованість | Testability — every statement is checkable |
| 7 | Реалізовність | Feasibility — nothing technically/logically impossible |

Criteria 4 and 5 are only evaluated against other requirements the user has actually shared in the conversation. If none were shared, they're marked "not checked" rather than failed or invented.

## Core rule

Every finding must be backed by an **exact quote** from the requirement. Nothing is flagged based on assumption. If something is unclear only because information is *missing* (not because of bad wording), it becomes a **question to the BA**, never a stated defect.

## Output format

1. **Verdict** (top of the report, bolded):
   - Готова (Ready) — no findings, or Minor only, no open questions
   - Потребує уточнень (Needs clarification) — Major findings and/or open BA questions, no Critical
   - Не готова (Not ready) — at least one Critical finding

2. **Findings table** — one row per criterion:

   | Критерій | Статус | Цитата | Проблема | Severity |
   |---|---|---|---|---|

   Passed criteria are included and marked ✅, not omitted.

3. **What's good** — written in English, each strength backed by a quote.

4. **Questions to BA** — only for genuine information gaps, each with:
   - **Питання:** the question
   - **Чому важливо:** why it affects test analysis
   - **Варіант відповіді:** a suggested answer, framed for the BA to confirm, not stated as fact

## Severity levels

- **Critical** — blocks test analysis (e.g. untestable core behavior, direct contradiction)
- **Major** — test analysis possible but likely to miss cases if unresolved
- **Minor** — clarity/style issue, low impact on coverage

## Multiple requirements

If several requirements are pasted at once, the skill asks whether to review them individually or check them against each other for consistency/duplication, unless already specified.

## Files

- `SKILL.md` — the skill definition and full instructions Claude follows
- `README.md` — this file
