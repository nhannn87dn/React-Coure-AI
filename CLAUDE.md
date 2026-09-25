# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is course content (not an application) for a 12-lesson React JS training program taught in Vietnamese. There is no build system, package.json, or runnable app anywhere in the repo — all content is Markdown lesson material, homework assignments, and (currently empty) placeholder folders for ebooks and example projects. There are no lint/test/build commands to run.

## Structure

- `outline-claude.md` — the full course outline (all 12 lessons, topic-by-topic breakdown) that every lesson README is derived from. Check this first when asked to write or revise a lesson, to keep topic coverage and ordering consistent with the plan.
- `01.Lessons/NN.Lesson-NN-<Topic>/` — one folder per lesson (01–12), each with:
  - `README.md` — the lesson content itself
  - optional `homeworks.md` / `homework-*.md` — student assignments for that lesson
  - optional `assets/` — images referenced by the README (e.g. `![...](assets/xxx.png)`)
- `02.Lessons-Bonus/NN.Lesson-NN-<Topic>/` — optional **self-study** bonus lessons 13–15 (Tailwind CSS, StyleX, React performance). Their outline lives in the "Phần Bonus" section at the end of `outline-claude.md`; each lists which core lesson it should follow, and may only use concepts taught up to that point. Unlike core lessons they are not taught in class: each has only a `README.md` (no `homeworks.md`, no `## 🧪 Bài tập thực hành cuối buổi`), written as a step-by-step guide the learner reads alone, ending with a fully worked "Ví dụ tổng hợp" and a "📚 Tài liệu tham khảo" list. The session-oriented parts of the authoring structure (quick review of the previous session, in-class exercise, homework) don't apply; heading/code conventions below still do.
- `03.Example-Projects/` — placeholder, currently empty.
- `04.eBooks/` — placeholder, currently empty.

## Course context

- Course: "React cho người mới bắt đầu" (React for beginners).
- Audience: students / working professionals / career-switchers who already know HTML, CSS, and ES6+ JavaScript, but have **not** used React yet.
- Format: 12 sessions × 4 hours, 3 sessions/week (4 weeks total, 48 hours).
- Tech stack: React 19 + **TypeScript** (taught from Lesson 1) + Vite + pnpm, React Router v7, Zustand v5, TanStack Query v5, Axios, Vercel.
- Assessment: in-class exercise right after each session's theory, homework for every session, one mini project (assigned at the end of Lesson 7, done at home), one capstone project (assigned at the end of Lesson 12, done at home and submitted after the course).
- End goal: learners can independently build a complete, modern frontend app (components, state, routing, API calls, form handling...).

## Workflow for authoring lesson content

When asked to design or expand course content, follow this two-step process and don't skip ahead:

1. **Outline first.** For all 12 lessons, draft: lesson title; measurable learning objectives (verbs like "giải thích", "viết được", "xây dựng được"); bullet list of subtopics; prerequisite review needed at the start of the session (if any); a short description of the in-class exercise; a short description of the homework (difficulty should increase as the course progresses). Get this outline approved before writing detailed content for any lesson.
2. **Detailed content** (only after the outline is approved) — write each lesson in full using this structure:
   1. Lesson objective (brief restatement)
   2. Quick review of the previous session (if session > 1)
   3. Theory — explain concepts in plain language with a real-world analogy before introducing code
   4. Annotated code example — functional components + hooks only (don't teach class components except as an optional aside)
   5. Common mistakes — 2-3 pitfalls beginners typically hit with this topic
   6. In-class exercise — problem statement + hints, not a full solution
   7. Homework — clear requirements with a completion checklist
   8. Short review questions (3-5, multiple choice or code-trace)

Always ask before elaborating a lesson's full detail if key information is missing. Never merge or split lessons relative to the approved outline unless explicitly asked to.

## Lesson README conventions

Follow the existing style exactly when writing or editing lesson content — it is consistent across all 12 lessons:

- Title line: `# ⭐ Bài N: <Tên bài>`, followed by a `> 🎯 Mục tiêu:` blockquote stating the lesson's learning goal.
- Body organized as `## Phần N: <Chủ đề>` sections, each broken into `### N.M <Tiêu đề con>` subsections, matching the outline's bullet hierarchy in `outline-claude.md`.
- Concept definitions are pulled out into `> **Bold definition sentence.**` blockquotes.
- Tips/warnings use emoji-prefixed blockquotes: `> 💡 ...` for tips, `> ⚠️ ...` for warnings/gotchas.
- Code samples use fenced ` ```tsx ` blocks (` ```javascript ` only for plain JS review in Lesson 1); file names are `.tsx`/`.ts` (`main.tsx`, `App.tsx`, `vite.config.ts`), and Props/data are typed with `type` (course convention — `interface` is only introduced for reading other people's code). Don't use PropTypes or `defaultProps` (removed for function components in React 19) — use TypeScript types and default values in destructuring instead.
- Wrong/right contrasts use `// ❌ Sai` and `// ✅ Đúng` comments inside code blocks.
- Sections are separated by `---` horizontal rules.
- Each lesson ends with a `## 🧪 Bài tập thực hành cuối buổi` (in-class exercises) section; homework lives in a separate `homeworks.md` file starting with `# 📝 Bài Tập Về Nhà — Bài N: <Topic>`.
- Homework problems are numbered `## Bài N — <task name>`, each with a "Yêu cầu" (requirement) description and, where relevant, a starter data snippet in a fenced code block.

## Content requirements

- All prose is written in Vietnamese; keep new content in Vietnamese and match the existing tone — approachable and direct, not academic. Keep technical terms in English (component, state, props, hook...) but explain them in Vietnamese.
- Never assume a React concept has been taught before the lesson that formally introduces it — only use concepts already covered in that lesson or earlier ones (e.g. don't use `useEffect` in a Lesson 3 example). Forward references to later lessons must be called out explicitly (see e.g. `01.Lessons/02.Lesson-02-JSX-Component/README.md`'s note on `&&` conditional rendering, which is formally taught in Lesson 5).
- A fully detailed lesson runs roughly 1500-2500 words (enough to fill a 4-hour session of theory + practice).
- Code samples use modern React (functional components + hooks) and TypeScript with a consistent style throughout the course (arrow functions, camelCase naming, no `any`).
- Formatting: clear Markdown headings (H2 for major sections, H3 for subsections), syntax-highlighted code blocks (` ```tsx `).

## Mini project & capstone project

Both are done at home; their topic options, minimum requirements, and rubrics are defined in `outline-claude.md`.

- **Mini project** (assigned at the end of Lesson 7, brief lives in `01.Lessons/07.Lesson-07-Forms-Custom-Hooks/mini-project.md`): covers lessons 1-7. 2-3 topic options, each with minimum required features and grading criteria.
- **Capstone project** (assigned at the end of Lesson 12, brief lives in `01.Lessons/12.Lesson-12-Deploy-Production/capstone-project.md`, submitted after the course): covers the whole course, including real API calls and deployment. 2-3 topic options with a more detailed rubric (functionality, code quality, UI/UX, deploy).
