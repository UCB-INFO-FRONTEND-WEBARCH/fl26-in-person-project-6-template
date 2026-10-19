# Week 8: React State & Interactive Components
## Breaking Down the Monolith with Component State

### Learning Objectives
By completing this project, you will:
- Use the `useState` hook to manage component-level state
- Update arrays and objects immutably using the spread operator, `filter` and `map`
- Implement controlled form components with validation
- Lift state up to share data between sibling components
- Apply the principle of derived state versus stored state
- Break a monolithic component into a clean component hierarchy

### Prerequisites
Before starting, you should have:
- Completed the Week 8 lecture on React State & Interactivity
- Reviewed the prep work chapters: React Front To Back Chapters 3-4 (State & Forms)
- Completed In-Person Project 5 on October 12 (your one-component React portfolio); highly recommended, but Option B below works without it
- An understanding of `useState` and event handling in React from today's lecture

### Project Overview
Last week you moved your whole portfolio into ONE `App.jsx` component. Today you break that monolith into logical components (Navbar, Hero, About, Skills, Projects, Contact, Footer) and use state to make the features truly interactive: a mobile menu driven by state instead of `classList`, project filtering computed from state, and a contact form whose inputs are controlled by state and validated as you type.

These are the patterns Assignment 2 (the React Task Manager, due Friday October 30, 9:00 AM PT) is built on: components in their own files, `useState` in the right component, immutable updates, a controlled form, and a derived list. Everything you do today is a rehearsal for that assignment.

### Getting Started

#### First: Create your own copy of this template
This repository is a GitHub **template** (GitHub Classroom is not used this term). Make your own copy of it, then clone that copy.

1. On this repository's page, click the green **Use this template** button and choose **Create a new repository**. On the next screen: Owner = your own GitHub account, Repository name = `in-person-project-6-<your-github-username>`, visibility **Public**. Click **Create repository**. (This makes your own copy of the starter, not a fork.)

2. **Clone your new repository:**
   ```bash
   git clone https://github.com/<your-github-username>/in-person-project-6-<your-github-username>.git
   cd in-person-project-6-<your-github-username>
   ```

#### Second: Install and run (start this now)
This project needs Node and an install step before anything shows in the browser. Start the install first; on classroom wifi it can take a few minutes, and you can read Part 1 while it runs.

1. **Check Node:** `node -v` must print `v18` or newer. If it prints an older version or "command not found", install the current LTS from https://nodejs.org, then open a new terminal and check again.
2. **Install dependencies** (once per clone):
   ```bash
   npm install
   ```
3. **Start the development server:**
   ```bash
   npm run dev
   ```
4. **Open your browser** to the URL shown in the terminal (usually `http://localhost:5173`). You should see a one-line starter page with TODO comments in `src/App.jsx` and no portfolio content yet. Keep this terminal running all session.

**What the starter gives you:** `src/App.jsx` is a stub whose return statement is a heading, a sentence and a TODO comment listing the seven components you will create. `src/App.css` is a complete stylesheet for the whole portfolio (615 lines), including the classes the state-driven features need (`.nav-menu.active`, `.filter-btn.active`, `.error-message`, `.success-message`). `src/index.css` holds base variables and is loaded by `main.jsx`; leave it alone. There is no finished week 7 component in this repository; your own code, or the week 6 reference described under Option B, is where the content comes from.

#### Third: Choose your path

#### Option A: Continue from Week 7 (Recommended)
If you completed In-Person Project 5 and want to break apart your own component:

1. **Keep the starter files.** Do not delete `package.json`, `vite.config.js`, `index.html` or anything in `src/`.
2. **Copy your week 7 component into this repository as a reference** so you can open it beside your new components:
   ```bash
   # from inside your cloned repository
   mkdir my-week7
   cp /path/to/in-person-project-5-<you>/src/App.jsx my-week7/App.jsx
   ```
   Vite ignores this folder; it is only for copying from. Everywhere a step says "your week 7 `App.jsx`", use `my-week7/App.jsx`.
3. **Decide on your CSS.** If your week 7 `App.css` is the stock file, keep the starter's `App.css`; it is the same stylesheet plus the state-driven classes. If you customized your CSS last week, copy your `App.css` over the starter's and make sure the four classes named above exist in it.

#### Option B: Fresh Start
If you did not finish In-Person Project 5, decompose the class solution instead. It is already in this repository under `week7-reference/` (`App.jsx`, one 544-line component, and `App.css`): the finished week 7 portfolio with the navbar toggle, the project filter and the contact form all inside a single `App` function. Everywhere a step says "your week 7 `App.jsx`", use `week7-reference/App.jsx`. Vite ignores that folder; your work goes in `src/`. The content (name, skills, projects) is the reference's; change it to yours as you go if you like, but the state work comes first.

### Step-by-Step Instructions

**Today's core is Parts 1 to 3** (decomposition, the Navbar with state, the Projects with filter state): finish those first. Parts 4 and 5 (the controlled Contact form, the remaining components) are next if you have time, and the Extension Activities come after that. Each part ends with a checkpoint that says what you should see.

#### Part 1: Understanding Component Decomposition (10 minutes)

**The week 7 problem:** one `App.jsx` holds all the markup and all the logic. It works, but every change touches one huge file, and nothing can be reused.

**The week 8 solution:** split it into components that each own one job and, where needed, one piece of state:
- **Navbar**: navigation, with state for whether the mobile menu is open
- **Hero**: the opening section (presentational, no state)
- **About**: the about section (presentational)
- **Skills**: the skill bars (rendered from an array)
- **Projects**: the project cards, with state for the active filter
- **Contact**: the form, with state for the field values and the validation errors
- **Footer**: presentational

**Key principle, component-level state:** each component manages its own state. State is lifted to the parent only when siblings need to share it. Do not store what you can derive from existing state.

**Do this now:** create the folder `src/components/`. Open your week 7 `App.jsx` (or the week 6 reference) and, for each of the three interactive features, write one line in a comment at the top of the matching new file saying what state it needs:
- Navbar: is the mobile menu open? (a boolean)
- Projects: which filter is active? (a string such as `'all'`)
- Contact: what has the user typed, and which fields have errors? (two objects)

**Checkpoint (end of Part 1):** `src/components/` exists, and you can say in one sentence per feature which component owns which state. Nothing on screen has changed yet.

#### Part 2: Creating the Navbar Component with State (15 minutes)

**Step 1: Create `src/components/Navbar.jsx`**

Requirements:
- The file exports a `Navbar` function component. Move the `<nav>` markup from your week 7 `App.jsx` into its return statement.
- Replace last week's `classList.toggle` mobile menu with one piece of state: `const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false)` (import `useState` from `'react'`).
- The hamburger button's `onClick` flips that boolean.
- The menu's `className` is computed from the state, for example `` className={`nav-menu ${isMobileMenuOpen ? 'active' : ''}`} ``.
- Each nav link's `onClick` still prevents the default, scrolls smoothly to its section, and then closes the menu by setting the state to `false`.

What is happening: the open/closed fact lives in state, and React re-renders the `className` from it. No `querySelector`, no `classList`.

**Step 2: Use it in `App.jsx`**

Import the component and render `<Navbar />` where the `<nav>` used to be. Delete the old nav markup and its handlers from `App.jsx`.

**Step 3: Check the CSS**

`App.css` must contain `.nav-menu.active` (it does in the starter). If you brought your own CSS, confirm the class is there.

Common mistakes:
- Toggling a class on the DOM instead of changing state
- Forgetting the conditional `className`
- Forgetting `e.preventDefault()` on the navigation links

**Checkpoint (end of Part 2):** at a narrow browser width the hamburger opens and closes the menu, clicking a link scrolls to the section and closes the menu, and `App.jsx` no longer contains any nav markup.

#### Part 3: Creating the Projects Component with Filter State (20 minutes)

**Step 1: Create `src/components/Projects.jsx`**

Requirements:
- A `projects` array of objects at the top of the file (or in state), one entry per card: `id`, `title`, `description`, `category` (for example `'web'`, `'mobile'`, `'design'`) and whatever else your card shows.
- One piece of state: `const [activeFilter, setActiveFilter] = useState('all')`.
- The filtered list is DERIVED, not stored: `const filteredProjects = activeFilter === 'all' ? projects : projects.filter(p => p.category === activeFilter)`.
- Filter buttons whose `onClick` calls `setActiveFilter(...)`, and whose `className` adds `active` when the button's category equals `activeFilter`.
- The cards are rendered with `filteredProjects.map(...)`, each with `key={project.id}`.

**Key concept, derived state:** do not put `filteredProjects` in state. It can always be computed from `projects` and `activeFilter`, and storing it would let the two fall out of sync.

**Step 2: Use it in `App.jsx`**

Import and render `<Projects />` in place of the old projects markup, and delete the old filter handlers.

Common mistakes:
- Using `querySelector` to show and hide cards instead of filtering the array
- Forgetting the `key` prop on mapped elements
- Storing derived data in state

**Checkpoint (end of Part 3):** clicking a filter button shows only that category, the active button is highlighted, "All" shows every card, and the console shows no `key` warning. Commit and push now (see Submit below), then continue.

#### Part 4: Creating the Contact Form with Validation State (25 minutes)

**Step 1: Create `src/components/Contact.jsx`**

Requirements:
- Two objects in state: `formData` (`{ name: '', email: '', message: '' }`) and `errors` (`{}`), plus a boolean `isSubmitted` (`false`).
- Every input is controlled: its `value` comes from `formData`, and its `onChange` updates the matching key immutably. The one handler for all three fields reads `e.target.name` and `e.target.value` and updates with a computed property name:
  ```jsx
  setFormData(prev => ({ ...prev, [name]: value }))
  ```
- Validation: a `validateField(name, value)` function returns an error message or an empty string (name required; email required and must contain `@`; message required, at least 10 characters). `onBlur` validates the one field the user just left; the submit handler validates all three.
- The submit handler calls `e.preventDefault()`, validates everything, and if there are no errors sets `isSubmitted` to `true` and resets `formData` to empty strings. If there are errors, it stores them in `errors` and does not submit.
- Each field shows its error (if any) in an element with the `error-message` class; after a successful submit the form shows a `success-message`.
- When the user types into a field that has an error, clear that field's error (an immutable update to `errors`).

**Key pattern, controlled components:** every input has a `value` from state and an `onChange` that updates state, so state is the single source of truth.

**Key pattern, immutable updates:** `{ ...prev, [name]: value }` builds a new object; `formData.name = value` mutates the old one and React will not re-render.

**Step 2: Use it in `App.jsx`**

Import and render `<Contact />` in place of the old form markup; delete the old validation code from `App.jsx`.

Common mistakes:
- Leaving out `value` (the input becomes uncontrolled and the console warns)
- Mutating state directly
- Forgetting `[name]: value` and writing the literal key `name` instead

**Checkpoint (end of Part 4):** typing updates the fields; leaving an empty field shows its error; typing again clears it; submitting valid data shows the success message and empties the fields.

#### Part 5: Adding the Remaining Components (15 minutes)

Create `src/components/Hero.jsx`, `About.jsx`, `Skills.jsx` and `Footer.jsx`, each exporting one function component with the matching markup from your week 7 `App.jsx` (or the week 6 reference).

- **Skills:** put the six skills in a `const skills = [...]` array (name and level) and render them with `.map()`, each bar with `key={skill.name}` and its width taken from the level. No state is needed here.
- **Hero, About, Footer:** presentational, no state.

Then update `App.jsx` so its return statement is only the seven components in page order inside `<div className="portfolio">`, and `import './App.css'` at the top. Delete every leftover handler.

**Checkpoint (end of Part 5):** `App.jsx` is under 40 lines and contains no markup beyond the seven component tags; every section renders as it did last week; the console is clean. Commit and push.

### Extension Activities (requirements and hints only, no code given)

If you finish the five parts early, these are not bonus busywork. They are the three state patterns Assignment 2 grades, done once today on your own portfolio before you do them on the task manager.

#### Tier 1 (about 10 to 15 minutes): do this one

**1. A derived count**
- **Goal:** Above the project cards show a line such as "Showing 3 of 6 projects" that updates when the filter changes.
- **Done when:** the numbers are right for every filter, and you did not add any new `useState` to do it.
- **Hint:** both numbers are lengths of arrays you already have in `Projects.jsx`.
- **Why it matters:** Assignment 2's header shows "total / completed", a derived count of exactly this kind. Students who store it in state get sync bugs.

#### Tier 2 (about 15 to 20 minutes): for the student who finished Tier 1

**2. Add a project from a form**
- **Goal:** In `Projects.jsx`, add a small controlled form (title, category) that appends a new project to the list on submit, and rejects an empty or whitespace-only title.
- **Done when:** a new card appears under the right filter immediately, an empty title adds nothing, and the `projects` array is never mutated (the update uses spread or `concat`).
- **Ceiling, stop here:** no delete, no edit, no persistence.
- **Hint:** the `projects` array has to move into `useState` for this to work; the new card needs a unique `id` (`Date.now()` is enough today). `title.trim() === ''` is the empty check.
- **Why it matters:** this is Assignment 2's Add Task requirement (25 points on task management, including "empty or whitespace-only input adds nothing").

#### Tier 3 (about 15 to 25 minutes, a bounded task): an Assignment 2 rehearsal

**3. State lifted to the parent, with delete and an empty state**
- **Goal:** In a scratch folder outside this repository, run `npm create vite@latest rehearsal -- --template react` and build three components: `App` owns a `tasks` array in state, `TaskList` receives it as a prop and renders a `TaskItem` per task, and each `TaskItem` has a delete button that removes it. When the array is empty, `TaskList` shows a "No tasks yet" message.
- **Done when:** deleting works from inside `TaskItem` even though the state lives in `App`; the empty message appears after the last delete; no `key` warning.
- **Ceiling, stop here:** no add form, no completion checkbox, no styling.
- **Hint:** `App` defines `handleDelete(id)` with `setTasks(prev => prev.filter(t => t.id !== id))` and passes it down as a prop; the button calls it with its own task's `id`.
- **Why it matters:** this is the component hierarchy Assignment 2 requires ("App owns the data; TaskList renders TaskItems") and two of its graded behaviors (delete, empty state).

### Troubleshooting Guide

**Problem:** `node -v` prints an old version, or "command not found"

**Solution:**
- Vite needs Node 18 or newer. Install the current LTS from https://nodejs.org, close the terminal, open a new one, run `node -v` again.
- On a lab machine where you cannot install, ask for help before class ends rather than losing the hour.

---

**Problem:** `npm install` is taking forever

**Solution:**
- On classroom wifi it can take several minutes. Leave it running and read Part 1 while you wait.
- If it stops with a network error, run `npm install` again; it resumes from the cache.

---

**Problem:** Vite dev server won't start

**Solution:**
```bash
# macOS / Linux / Git Bash
rm -rf node_modules package-lock.json
npm install
npm run dev
```
```powershell
# Windows PowerShell
Remove-Item -Recurse -Force node_modules, package-lock.json
npm install
npm run dev
```

---

**Problem:** "Port 5173 is in use"

**Solution:**
- You already have a dev server running in another terminal (often the one you forgot about). Stop it with Ctrl+C there, or let Vite use the next port it prints (5174) and open that URL instead.

---

**Problem:** The page is blank and the console shows a JSX error

**Solution:**
- Read the first error line; it names the file and line. The usual causes: an HTML comment `<!-- -->` left in pasted markup, `class` instead of `className`, `for` instead of `htmlFor`, an unclosed `<img>`, `<input>` or `<br>`, or a component file that forgot `export default`.
- Fix the first error only, save, read the next one.

---

**Problem:** State updates but the UI does not re-render

**Solution:**
- You mutated the state (`formData.name = value`, `projects.push(...)`). Build a new object or array with spread, `filter` or `map` and pass that to the setter.
- Check that `useState` is imported from `'react'` in that file.

---

**Problem:** Form inputs do not update when typing

**Solution:**
- Each input needs both `value` and `onChange`. Check that the handler reads `e.target.name` and that the input's `name` attribute matches the key in `formData`.

---

**Problem:** "key" prop warning in the console

**Solution:**
- Every element returned from `.map()` needs a `key`; use a stable id (`project.id`, `skill.name`), not the array index.

---

**Problem:** Filter buttons do not show the active class

**Solution:**
- Check the conditional `className` expression and that the button's `onClick` calls `setActiveFilter` with the same string the comparison uses (`'all'` versus `'All'` is a common mismatch).

---

**Problem:** Form validation errors do not clear

**Solution:**
- Clear the one field's error inside the `onChange` handler with an immutable update (`setErrors(prev => ({ ...prev, [name]: '' }))`).

### Resources & References

**Official Documentation:**
- [React useState Hook](https://react.dev/reference/react/useState)
- [Controlling an input with a state variable](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)
- [State as a Snapshot](https://react.dev/learn/state-as-a-snapshot)
- [Updating Objects in State](https://react.dev/learn/updating-objects-in-state) and [Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)

**Week 8 Prep Work:**
- React Front To Back Chapter 3 - Components, Props, and State
- React Front To Back Chapter 4 - Forms, Validation, and Simple Animation

### Submit

1. Commit and push your work before you leave (the `.gitignore` in this repository keeps `node_modules` out of the push, so it is quick):
   ```bash
   git add .
   git commit -m "In-person project 6"
   git push
   ```
2. On the bCourses assignment page click **Submit Assignment** and paste the URL of your repository (the repository page, not a file) as the Website URL.
3. Grading is complete/incomplete: a pushed repository that runs with `npm run dev`, shows the styled portfolio split into components, and whose mobile menu and project filter are driven by state (Parts 1 to 3) earns complete.
4. This is an individual project. No deployment is required.

### Project Checklist

Before submitting:
- [ ] `npm run dev` runs and the console is clean on load
- [ ] Separate files under `src/components/` for Navbar, Hero, About, Skills, Projects, Contact and Footer; `App.jsx` only imports and renders them
- [ ] Navbar's mobile menu is driven by `useState`, with no `classList` or `querySelector`
- [ ] Projects stores `activeFilter` in state and derives the filtered list (not stored in state); every mapped card has a `key`
- [ ] Contact's inputs are controlled (`value` plus `onChange`), state is updated immutably, errors show on blur and on submit, a success message shows after a valid submit
- [ ] Skills renders from an array with `.map()`
- [ ] Optional: Tier 1 of the Extension Activities (the derived count), then Tier 2 or 3

### Reflection Questions

1. How is managing the menu with state different from toggling a class on the DOM, and which was easier to reason about?
2. Why is `filteredProjects` derived rather than stored, and what bug would storing it invite?
3. What happens when you mutate state directly, and how does React decide to re-render?
4. Which state in your portfolio would have to move up to `App` if two components needed it, and why?

Keep these in mind for Week 9, when you share state across components with the Context API and fetch data with `useEffect`.

---

Your portfolio is now a set of components that own their state. Assignment 2 asks you to do the same thing to a task manager.
