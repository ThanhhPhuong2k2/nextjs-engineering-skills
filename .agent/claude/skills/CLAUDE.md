# Project: fintech

Stack: Next.js (App Router), TypeScript strict, Tailwind CSS.
Project skills live in `.claude/skills/` (auto-discovered).

## Skills-first policy

Before any task that creates, changes, reviews, or fixes UI (including work
from screenshots, Figma designs, wireframes, or written requirements):

1. Pick every matching skill in `.claude/skills/`.
2. Read the whole `SKILL.md` of each matching skill before applying it.
3. State which skills you are applying and in what order, then follow the phased workflow below.

If a skill conflicts with your default habits, the skill wins.

## Phased workflow (one skill per phase)

Apply skills one at a time, never all at once:

1. **Phase 0 (Plan)**: list the matching skills in the order you will apply
   them, the files you will touch, and any missing capability or open
   question. Then ask for confirmation (see "Confirmation prompt" below).
2. **Phase 1..N**: each phase applies exactly ONE skill:
   - Read that skill's `SKILL.md` fully.
   - Make only the changes that skill is responsible for.
   - Summarize what you changed and which rules of the skill you applied.
   - Then ask for confirmation (see "Confirmation prompt" below).
3. Never start the next phase, or apply another skill, before the developer confirms.

Default order when several skills apply:

1. `nextjs-component-engineering` (structure, Server/Client Components,
   logic, states, accessibility)
2. `design-taste-frontend` (layout, color, typography, spacing, polish)

Other skills go after these, unless the developer sets another order.

### Confirmation prompt

At the end of every phase (including Phase 0), you MUST ask the developer
whether to continue, in Vietnamese, using this format:

> **Phase [N] đã xong.**
> **Đề xuất cho phase tiếp theo** (chọn hoặc bỏ qua):
>
> - S1: [đề xuất] – [lý do], ảnh hưởng: [file], đánh đổi: [...]
> - S2: [đề xuất] – [lý do], ảnh hưởng: [file], đánh đổi: [...]
>   **Phase tiếp theo ([N+1])**: áp dụng skill `[skill-name]` để [mô tả 1–2 câu
>   > việc sẽ làm], dự kiến sửa các file: [danh sách file].
>   Bạn chọn:
>
> 1. Tiếp tục phase [N+1], kèm các đề xuất: [ghi S1, S2... hoặc "không"]
> 2. Chỉnh lại phase [N] (nói rõ cần sửa gì)
> 3. Bỏ qua phase [N+1]
> 4. Dừng tại đây

If the AskUserQuestion tool is available, use it to present these options.
If there is no next phase, say the work is complete and ask whether the
developer wants any final adjustments.
If you have open questions for the next phase, put them in the same message
so the developer can answer everything at once.

## Suggestions (propose, don't apply)

At the end of every phase, if you see improvements or alternatives worth
considering, propose them to the developer. Never apply a suggestion on your
own.

What to propose (only if genuinely valuable, max 3 per phase):

- Better UX, accessibility, or performance for what was just built.
- A simpler or more maintainable approach.
- Something missing that the next phase could cover.
- A risk or edge case you noticed.

How to propose:

- Put suggestions in the same message as the "Confirmation prompt", after the
  phase summary.
- Number each suggestion (S1, S2, S3). For each, give: what it is, why it
  helps, which files it affects, and a one-line cost or trade-off.
- Let the developer choose: any of them, several, all, or none. Include
  "none" as an explicit option.
- Apply only the suggestions the developer selects, and only in the phase
  the developer approves. If there are no valuable suggestions, say
  "Không có đề xuất thêm" and do not invent any.

## Ask, don't assume

If anything is unclear or uncertain, at any phase (including before starting
the next phase), you MUST ask the developer instead of deciding yourself.

Ask when:

- A requirement, behavior, or design detail is ambiguous or missing.
- There are two or more reasonable ways to implement something.
- You are unsure which skill applies, or two skills give conflicting rules.
- You need a decision about data, naming, API shape, or UI copy that the
  developer has not specified.
- The change would touch files outside the agreed plan.

Do not ask about trivial details that existing code conventions already
answer; follow the conventions instead.

How to ask:

- Ask before doing the uncertain part, not after.
- State the question briefly, list the options with a one-line trade-off each,
  and say which option you would pick and why.
- If the AskUserQuestion tool is available, use it to present the options.
- Do not continue the uncertain part until the developer answers. You may
  continue with parts that are clearly unaffected.

Never guess, never pick a default silently, and never fill in a gap with an
invented assumption. If you did make a small assumption, say so explicitly in
the phase summary and ask the developer to confirm it.

## Component priority order

1. Reuse an existing component in `src/components/`.
2. Compose existing components.
3. Create only the missing UI or behavior, and state what capability is missing.

Do not recreate standard controls that already exist (Button, Input, Select,
Checkbox, Table, Toast, Tabs, ...). Semantic layout elements (`main`,
`section`, `form`, `div`) are fine.

Before creating custom UI:

- Inspect the existing component API and its usage in this repository.
- Keep custom code limited to the missing capability.

## Quality bar

- Always handle loading, empty, and error states.
- Preserve accessibility and TypeScript type safety. Never use `any`.

## Dependency boundaries

Do not add a UI library as a substitute for a component the project already has.

## Git

Do not run `git commit`. Leave changes as modified so the developer can
review and commit.

## Language

Reply in Vietnamese. Write code and code comments in English.
