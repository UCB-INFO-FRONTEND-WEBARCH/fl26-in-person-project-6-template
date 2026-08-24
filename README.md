# Week 9: React State & Interactive Components
## Breaking Down the Monolith with Component State

### Learning Objectives
By completing this project, you will:
- Use the useState hook to manage component-level state
- Update arrays and objects immutably using spread operator, filter, and map
- Implement controlled form components with real-time validation
- Lift state up to share data between sibling components
- Apply the principle of derived state vs stored state
- Break a monolithic component into a clean component hierarchy

### Prerequisites
Before starting, you should have:
- Completed Week 9 lecture on React State & Interactivity
- Reviewed prep work chapters: React Front To Back Chapters 3-4 (State & Forms)
- Your code from Week 8 React migration project (highly recommended)
- Understanding of the useState hook and event handling in React

### Project Overview
This week, you'll transform your Week 8 monolithic portfolio component into a proper React application with multiple components that manage and share state. You'll break down the giant App.jsx into logical components (Navbar, Hero, Skills, Projects, Contact) and use state to make features truly interactive - like filtering projects, validating forms in real-time, and managing UI interactions.

This project demonstrates the core state management patterns you'll use in every React application, preparing you for building complex interactive UIs.

### Getting Started

**First:** Accept the GitHub Classroom assignment link provided by your instructor. This will create a new repository with the Week 9 starter code.

1. **Clone your new repository:**
   ```bash
   git clone [your-github-classroom-repo-url]
   cd [your-repo-name]
   ```

2. **Choose your path:**

#### Option A: Continue from Week 8 (Recommended)
If you completed the Week 8 project and want to build upon it:

1. **Keep the starter files** - You'll need `package.json` and `vite.config.js`
2. **Copy your Week 8 code** into the starter folder:
   - Copy your `src/App.jsx` to use as reference
   - Copy your `src/App.css` to use as-is
   - You'll be breaking apart App.jsx into multiple components
3. **Install dependencies:**
   ```bash
   npm install
   ```
4. **Start the development server:**
   ```bash
   npm run dev
   ```

#### Option B: Fresh Start
If starting fresh, use the provided starter files:

1. **Install dependencies:**
   ```bash
   npm install
   ```
2. **Start the development server:**
   ```bash
   npm run dev
   ```
3. **Follow the step-by-step instructions** below to complete the project

### Step-by-Step Instructions

#### Part 1: Understanding Component Decomposition (10 minutes)

**The Week 8 Problem:**
Last week, you had ONE giant App.jsx component with ALL the HTML and ALL the JavaScript. While this worked, it's not how React applications are built in practice.

**The Week 9 Solution:**
Break the monolith into logical, reusable components:
- **Navbar** - Navigation with state for mobile menu
- **Hero** - Hero section (mostly presentational)
- **Skills** - Skills display (data from state)
- **Projects** - Projects with filtering state
- **Contact** - Form with validation state
- **Footer** - Footer (presentational)

**Key principle: Component-level state**
- Each component manages its own state
- State is lifted to parent when siblings need to share it
- Don't store what you can derive from existing state

**Review your Week 8 App.jsx:**
Look at all the event handlers and DOM manipulation. Ask yourself:
- What state does the navbar need? (mobile menu open/closed)
- What state do projects need? (active filter, filtered items)
- What state does the contact form need? (form data, validation errors)

#### Part 2: Creating the Navbar Component with State (15 minutes)

**Step 1: Create the Navbar component file**

Create `src/components/Navbar.jsx`:

```jsx
import { useState } from 'react'

function Navbar() {
  // State for mobile menu
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false)

  // Toggle mobile menu
  const handleMobileToggle = () => {
    setIsMobileMenuOpen(!isMobileMenuOpen)
  }

  // Smooth scroll navigation
  const handleNavClick = (e) => {
    e.preventDefault()
    const targetId = e.target.getAttribute('href')
    const targetSection = document.querySelector(targetId)

    if (targetSection) {
      targetSection.scrollIntoView({
        behavior: 'smooth',
        block: 'start'
      })
      // Close mobile menu after clicking
      setIsMobileMenuOpen(false)
    }
  }

  return (
    <nav id="navbar">
      <div className="nav-brand">
        <span>Alex Johnson</span>
      </div>
      <ul className={`nav-menu ${isMobileMenuOpen ? 'active' : ''}`}>
        <li><a href="#home" className="nav-link" onClick={handleNavClick}>Home</a></li>
        <li><a href="#about" className="nav-link" onClick={handleNavClick}>About</a></li>
        <li><a href="#skills" className="nav-link" onClick={handleNavClick}>Skills</a></li>
        <li><a href="#projects" className="nav-link" onClick={handleNavClick}>Projects</a></li>
        <li><a href="#contact" className="nav-link" onClick={handleNavClick}>Contact</a></li>
      </ul>
      <div
        className={`nav-toggle ${isMobileMenuOpen ? 'active' : ''}`}
        onClick={handleMobileToggle}
      >
        <span></span>
        <span></span>
        <span></span>
      </div>
    </nav>
  )
}

export default Navbar
```

What's happening here:
- `isMobileMenuOpen` is component-level state (only Navbar needs it)
- State replaces DOM manipulation (`classList.toggle` → useState)
- Conditional className uses template literals and state
- Menu auto-closes after navigation (better UX!)

**Step 2: Import and use Navbar in App.jsx**

Update your `src/App.jsx`:

```jsx
import './App.css'
import Navbar from './components/Navbar'

function App() {
  return (
    <div className="portfolio">
      <Navbar />
      {/* Other components will go here */}
    </div>
  )
}

export default App
```

**Step 3: Update CSS for state-based classes**

Make sure your `App.css` has these styles (you probably already have them from Week 8):

```css
/* Mobile menu hidden by default */
.nav-menu {
  display: flex;
  /* ...other styles... */
}

/* Mobile menu shown when active */
.nav-menu.active {
  display: flex;
  /* ...mobile menu styles... */
}

/* Mobile toggle icon animation */
.nav-toggle.active span:nth-child(1) {
  transform: rotate(45deg) translate(5px, 5px);
}
```

Common mistakes to avoid:
- Don't manipulate DOM directly - use state!
- Don't forget to update className conditionally
- Remember to preventDefault on navigation clicks

#### Part 3: Creating the Projects Component with Filter State (20 minutes)

**Step 1: Define the projects data and state**

Create `src/components/Projects.jsx`:

```jsx
import { useState } from 'react'

function Projects() {
  // State for all projects (would come from API in real app)
  const [projects] = useState([
    {
      id: 1,
      title: 'E-Commerce Platform',
      category: 'fullstack',
      description: 'A modern e-commerce platform built with React and Node.js, featuring shopping cart, payment integration, and admin dashboard.',
      image: 'https://via.placeholder.com/400x250/4f46e5/ffffff?text=E-Commerce+App',
      tags: ['React', 'Node.js', 'MongoDB', 'Stripe'],
      featured: true
    },
    {
      id: 2,
      title: 'Weather Dashboard',
      category: 'frontend',
      description: 'Clean and intuitive weather dashboard with location search, 7-day forecast, and beautiful animations.',
      image: 'https://via.placeholder.com/400x250/059669/ffffff?text=Weather+App',
      tags: ['JavaScript', 'CSS3', 'API'],
      featured: false
    },
    {
      id: 3,
      title: 'Task Management App',
      category: 'webapp',
      description: 'Productivity app with drag-and-drop functionality, project collaboration, and deadline tracking.',
      image: 'https://via.placeholder.com/400x250/dc2626/ffffff?text=Task+Manager',
      tags: ['React', 'TypeScript', 'Firebase'],
      featured: false
    },
    {
      id: 4,
      title: 'Creative Portfolio',
      category: 'design',
      description: 'Responsive portfolio website for a graphic designer with interactive galleries and smooth animations.',
      image: 'https://via.placeholder.com/400x250/7c3aed/ffffff?text=Portfolio+Site',
      tags: ['HTML5', 'CSS3', 'GSAP'],
      featured: false
    }
  ])

  // State for active filter
  const [activeFilter, setActiveFilter] = useState('all')

  // Derived state: filtered projects (don't store this in state!)
  const filteredProjects = activeFilter === 'all'
    ? projects
    : projects.filter(project => project.category === activeFilter)

  // Handle filter button click
  const handleFilterClick = (category) => {
    setActiveFilter(category)
  }

  return (
    <section id="projects" className="section">
      <div className="container">
        <h2>Featured Projects</h2>

        <div className="filter-buttons">
          <button
            className={`filter-btn ${activeFilter === 'all' ? 'active' : ''}`}
            onClick={() => handleFilterClick('all')}
          >
            All Projects
          </button>
          <button
            className={`filter-btn ${activeFilter === 'frontend' ? 'active' : ''}`}
            onClick={() => handleFilterClick('frontend')}
          >
            Frontend
          </button>
          <button
            className={`filter-btn ${activeFilter === 'fullstack' ? 'active' : ''}`}
            onClick={() => handleFilterClick('fullstack')}
          >
            Full-Stack
          </button>
          <button
            className={`filter-btn ${activeFilter === 'design' ? 'active' : ''}`}
            onClick={() => handleFilterClick('design')}
          >
            Design
          </button>
          <button
            className={`filter-btn ${activeFilter === 'webapp' ? 'active' : ''}`}
            onClick={() => handleFilterClick('webapp')}
          >
            Web App
          </button>
        </div>

        <div className="projects-grid">
          {filteredProjects.map(project => (
            <div
              key={project.id}
              className={`project-card ${project.featured ? 'featured' : ''}`}
            >
              <div className="project-image">
                <img src={project.image} alt={project.title} />
                <div className="project-overlay">
                  <div className="project-links">
                    <a href="#" className="project-link">
                      <span>🔗</span> Live Demo
                    </a>
                    <a href="#" className="project-link">
                      <span>💻</span> Code
                    </a>
                  </div>
                </div>
              </div>
              <div className="project-content">
                <div className="project-header">
                  <h3 className="project-title">{project.title}</h3>
                  <span className="project-category">{project.category}</span>
                </div>
                <p className="project-description">{project.description}</p>
                <div className="project-tags">
                  {project.tags.map((tag, index) => (
                    <span key={index} className="project-tag">{tag}</span>
                  ))}
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  )
}

export default Projects
```

What's happening here:
- Projects array is stored in state (in real app, would come from API)
- activeFilter tracks which filter button is clicked
- filteredProjects is DERIVED (computed from projects + activeFilter)
- No DOM manipulation! React re-renders when activeFilter changes
- map() renders projects dynamically from filtered array
- Conditional className for active filter button

**Key concept: Derived State**
Don't store `filteredProjects` in state! It can be calculated from `projects` and `activeFilter`. This prevents state sync issues.

**Step 2: Add Projects to App.jsx**

```jsx
import './App.css'
import Navbar from './components/Navbar'
import Projects from './components/Projects'

function App() {
  return (
    <div className="portfolio">
      <Navbar />
      {/* Other sections like Hero, About, Skills will go here */}
      <Projects />
      {/* Contact and Footer will go here */}
    </div>
  )
}

export default App
```

Common mistakes to avoid:
- Don't use querySelector to filter projects - use state and derived state!
- Don't forget the key prop when mapping
- Don't store derived data in state

#### Part 4: Creating the Contact Form with Validation State (25 minutes)

**Step 1: Create the Contact component with form state**

Create `src/components/Contact.jsx`:

```jsx
import { useState } from 'react'

function Contact() {
  // Form data state
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    subject: '',
    message: ''
  })

  // Validation errors state
  const [errors, setErrors] = useState({})

  // Success message state
  const [successMessage, setSuccessMessage] = useState('')

  // Email validation helper
  const isValidEmail = (email) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return emailRegex.test(email)
  }

  // Handle input changes
  const handleChange = (e) => {
    const { name, value } = e.target

    // Update form data
    setFormData(prev => ({
      ...prev,
      [name]: value
    }))

    // Clear error for this field
    if (errors[name]) {
      setErrors(prev => ({
        ...prev,
        [name]: ''
      }))
    }

    // Clear success message when user starts typing again
    if (successMessage) {
      setSuccessMessage('')
    }
  }

  // Real-time validation on blur
  const handleBlur = (e) => {
    const { name, value } = e.target
    let error = ''

    if (name === 'name' && value.trim().length < 2) {
      error = 'Name must be at least 2 characters'
    } else if (name === 'email' && !isValidEmail(value.trim())) {
      error = 'Please enter a valid email address'
    } else if (name === 'message' && value.trim().length < 10) {
      error = 'Message must be at least 10 characters'
    }

    if (error) {
      setErrors(prev => ({
        ...prev,
        [name]: error
      }))
    }
  }

  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault()

    // Validate all fields
    const newErrors = {}

    if (formData.name.trim().length < 2) {
      newErrors.name = 'Name must be at least 2 characters'
    }

    if (!isValidEmail(formData.email.trim())) {
      newErrors.email = 'Please enter a valid email address'
    }

    if (formData.message.trim().length < 10) {
      newErrors.message = 'Message must be at least 10 characters'
    }

    // If there are errors, set them and don't submit
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors)
      return
    }

    // Form is valid - submit it
    console.log('Form submitted:', formData)

    // Show success message
    setSuccessMessage('Thank you! Your message has been sent successfully.')

    // Clear form after 3 seconds
    setTimeout(() => {
      setFormData({
        name: '',
        email: '',
        subject: '',
        message: ''
      })
      setSuccessMessage('')
    }, 3000)
  }

  return (
    <section id="contact" className="section section-alt">
      <div className="container">
        <h2>Get In Touch</h2>
        <div className="contact-content">
          <p>I'm always interested in hearing about new opportunities and projects. Feel free to reach out!</p>

          <form className="contact-form" onSubmit={handleSubmit}>
            <div className="form-grid">
              <div className="form-group">
                <label htmlFor="name">Name *</label>
                <input
                  type="text"
                  id="name"
                  name="name"
                  value={formData.name}
                  onChange={handleChange}
                  onBlur={handleBlur}
                  className={errors.name ? 'error' : ''}
                  required
                />
                {errors.name && <span className="error-message">{errors.name}</span>}
              </div>

              <div className="form-group">
                <label htmlFor="email">Email *</label>
                <input
                  type="email"
                  id="email"
                  name="email"
                  value={formData.email}
                  onChange={handleChange}
                  onBlur={handleBlur}
                  className={errors.email ? 'error' : ''}
                  required
                />
                {errors.email && <span className="error-message">{errors.email}</span>}
              </div>

              <div className="form-group form-group-full">
                <label htmlFor="subject">Subject</label>
                <input
                  type="text"
                  id="subject"
                  name="subject"
                  value={formData.subject}
                  onChange={handleChange}
                />
              </div>

              <div className="form-group form-group-full">
                <label htmlFor="message">Message *</label>
                <textarea
                  id="message"
                  name="message"
                  rows="5"
                  value={formData.message}
                  onChange={handleChange}
                  onBlur={handleBlur}
                  className={errors.message ? 'error' : ''}
                  required
                ></textarea>
                {errors.message && <span className="error-message">{errors.message}</span>}
              </div>

              <div className="form-group form-group-full">
                <button type="submit" className="btn btn-primary">Send Message</button>
              </div>

              {successMessage && (
                <div className="success-message">{successMessage}</div>
              )}
            </div>
          </form>
        </div>
      </div>
    </section>
  )
}

export default Contact
```

What's happening here:
- **Controlled components**: input values come from state
- **Object state**: formData is an object, updated immutably with spread
- **onChange handler**: updates state as user types
- **onBlur handler**: validates when user leaves field
- **Validation state**: errors object tracks validation messages
- **Success state**: shows feedback after submission
- **Immutable updates**: `{...prev, [name]: value}` pattern

**Key pattern: Controlled Components**
Every input has:
1. `value` prop from state
2. `onChange` handler to update state
3. This creates a single source of truth (state)

**Key pattern: Immutable Object Updates**
```jsx
setFormData(prev => ({
  ...prev,        // Copy all existing fields
  [name]: value   // Update just one field
}))
```

**Step 2: Add Contact to App.jsx**

```jsx
import './App.css'
import Navbar from './components/Navbar'
import Projects from './components/Projects'
import Contact from './components/Contact'

function App() {
  return (
    <div className="portfolio">
      <Navbar />
      {/* Other sections */}
      <Projects />
      <Contact />
      {/* Footer */}
    </div>
  )
}

export default App
```

Common mistakes to avoid:
- Forgetting `value` prop (makes input uncontrolled)
- Mutating state directly (`formData.name = value` is wrong!)
- Not using computed property names `[name]: value`

#### Part 5: Adding the Remaining Components (15 minutes)

**Step 1: Create Hero component**

Create `src/components/Hero.jsx`:

```jsx
function Hero() {
  const handleNavClick = (e) => {
    e.preventDefault()
    const targetId = e.target.getAttribute('href')
    const targetSection = document.querySelector(targetId)

    if (targetSection) {
      targetSection.scrollIntoView({
        behavior: 'smooth',
        block: 'start'
      })
    }
  }

  return (
    <section id="home" className="hero">
      <div className="hero-content">
        <h1 className="hero-title">Alex Johnson</h1>
        <p className="hero-subtitle">Frontend Developer & UI Designer</p>
        <p className="hero-tagline">Creating beautiful, responsive web experiences with modern technologies</p>
        <div className="hero-buttons">
          <a href="#projects" className="btn btn-primary" onClick={handleNavClick}>View Projects</a>
          <a href="#contact" className="btn btn-secondary" onClick={handleNavClick}>Get In Touch</a>
        </div>
      </div>
    </section>
  )
}

export default Hero
```

**Step 2: Create Skills component**

Create `src/components/Skills.jsx`:

```jsx
import { useState } from 'react'

function Skills() {
  const [skills] = useState([
    { id: 1, name: 'React', icon: '⚛️', category: 'Frontend Framework', level: 90 },
    { id: 2, name: 'CSS3', icon: '🎨', category: 'Styling', level: 95 },
    { id: 3, name: 'Responsive Design', icon: '📱', category: 'Design', level: 88 },
    { id: 4, name: 'JavaScript', icon: '🚀', category: 'Programming', level: 85 },
    { id: 5, name: 'Figma', icon: '🎯', category: 'Design Tools', level: 80 },
    { id: 6, name: 'Node.js', icon: '🌐', category: 'Backend', level: 75 }
  ])

  return (
    <section id="skills" className="section section-alt">
      <div className="container">
        <h2>Skills & Technologies</h2>
        <div className="skills-grid">
          {skills.map(skill => (
            <div key={skill.id} className="skill-card">
              <div className="skill-header">
                <span className="skill-icon">{skill.icon}</span>
                <h3 className="skill-name">{skill.name}</h3>
              </div>
              <div className="skill-category">{skill.category}</div>
              <div className="skill-level">
                <div className="skill-progress" style={{width: `${skill.level}%`}}></div>
              </div>
              <span className="skill-percentage">{skill.level}%</span>
            </div>
          ))}
        </div>
      </div>
    </section>
  )
}

export default Skills
```

**Step 3: Create Footer component**

Create `src/components/Footer.jsx`:

```jsx
function Footer() {
  return (
    <footer className="footer">
      <div className="container">
        <div className="footer-content">
          <p>&copy; 2024 Alex Johnson. All rights reserved.</p>
          <div className="social-links">
            <a href="#" className="social-link">GitHub</a>
            <a href="#" className="social-link">LinkedIn</a>
            <a href="#" className="social-link">Twitter</a>
            <a href="#" className="social-link">Dribbble</a>
          </div>
        </div>
      </div>
    </footer>
  )
}

export default Footer
```

**Step 4: Create About component**

Create `src/components/About.jsx`:

```jsx
function About() {
  return (
    <section id="about" className="section">
      <div className="container">
        <h2>About Me</h2>
        <div className="about-grid">
          <div className="about-text">
            <p>I'm a passionate frontend developer with 3 years of experience creating engaging digital experiences. I specialize in React, modern CSS, and responsive design patterns that work beautifully across all devices.</p>
            <div className="about-details">
              <div className="detail-item">
                <span className="detail-icon">📍</span>
                <span>San Francisco, CA</span>
              </div>
              <div className="detail-item">
                <span className="detail-icon">✉️</span>
                <a href="mailto:alex@example.com">alex@example.com</a>
              </div>
              <div className="detail-item">
                <span className="detail-icon">📱</span>
                <span>(555) 123-4567</span>
              </div>
            </div>
          </div>
          <div className="about-availability">
            <h3>Currently Available For:</h3>
            <div className="availability-tags">
              <span className="tag tag-available">Freelance</span>
              <span className="tag tag-available">Full-Time</span>
              <span className="tag tag-available">Remote</span>
            </div>
          </div>
        </div>
      </div>
    </section>
  )
}

export default About
```

**Step 5: Update App.jsx with all components**

```jsx
import './App.css'
import Navbar from './components/Navbar'
import Hero from './components/Hero'
import About from './components/About'
import Skills from './components/Skills'
import Projects from './components/Projects'
import Contact from './components/Contact'
import Footer from './components/Footer'

function App() {
  return (
    <div className="portfolio">
      <Navbar />
      <Hero />
      <About />
      <Skills />
      <Projects />
      <Contact />
      <Footer />
    </div>
  )
}

export default App
```

What's happening here:
- App.jsx is now clean and simple
- Each section is a separate component
- Components are composable and reusable
- Each component manages its own state
- No more 500+ line monolithic component!

### Understanding State Management Patterns

**Component-Level State:**
- Used when only one component needs the data
- Examples: mobile menu open/closed, active filter, form data

**App-Level State (Coming in Week 10):**
- Used when multiple components need the same data
- Will learn Context API and state lifting patterns

**Derived State:**
- Don't store what you can calculate
- `filteredProjects` is derived from `projects` + `activeFilter`
- Prevents state synchronization bugs

**Immutable Updates:**
- Arrays: use spread, filter, map (never push, splice, pop directly)
- Objects: use spread `{...prev, key: value}` (never mutate directly)
- React needs new references to detect changes

### Customization Ideas

Once everything works, try these enhancements:

1. **Add a Skills Filter**
   - Add filter buttons to Skills section
   - Filter skills by category
   - Practice the same filter pattern as Projects

2. **Add Form Character Counter**
   - Show remaining characters for message field
   - Calculate from `formData.message.length`
   - Example of derived state

3. **Add Dark Mode Toggle**
   - Create state in App.jsx
   - Pass down as prop to all components
   - Toggle CSS class on body element

### Challenge Extensions

**Intermediate:**
- Add a "Load More" button to Projects that shows 4 at a time
- Implement a search bar for projects (filter by title/description)
- Add a rating system (stars) to skills using state

**Advanced:**
- Create a custom useForm hook to handle form logic
- Add animation when projects filter (fade in/out)
- Implement a "Featured Projects" toggle button

**Graduate (253A):**
- Refactor Projects to use a reducer instead of useState
- Create a context for theme (dark/light mode)
- Build a custom useLocalStorage hook to persist form drafts

### Troubleshooting Guide

**Problem:** State updates but UI doesn't re-render

**Solution:**
- Make sure you're creating new references with spread operator
- Check that you're not mutating state directly
- Verify useState is imported from 'react'

---

**Problem:** Form inputs don't update when typing

**Solution:**
- Check that input has both `value` and `onChange`
- Verify onChange calls setState
- Make sure you're using controlled component pattern

---

**Problem:** Getting "key" prop warning in console

**Solution:**
- Add unique `key` prop when mapping arrays
- Use stable IDs (project.id) not array indices
- Every mapped element needs a key

---

**Problem:** Filter buttons don't update active class

**Solution:**
- Check conditional className syntax: `${condition ? 'active' : ''}`
- Verify activeFilter state is updating
- Make sure button onClick calls setActiveFilter

---

**Problem:** Form validation errors don't clear

**Solution:**
- Clear errors in onChange handler when user starts typing
- Check that you're updating errors object immutably
- Verify error clearing logic in handleChange

### Resources & References

**Official Documentation:**
- [React useState Hook](https://react.dev/reference/react/useState)
- [Controlled Components](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)
- [State as a Snapshot](https://react.dev/learn/state-as-a-snapshot)

**Week 9 Prep Work:**
- React Front To Back Chapter 3 - Components, Props, and State
- React Front To Back Chapter 4 - Forms, Validation, and Simple Animation

**Related Week Projects:**
- Week 7: Interactive Portfolio (JavaScript functionality)
- Week 8: React Migration (monolithic component you're breaking down)
- Week 10: Advanced State Management (Context API, useEffect)

**Patterns Learned:**
- Component decomposition
- Component-level vs app-level state
- Controlled components
- Immutable state updates
- Derived state

### Project Checklist

Before submitting, ensure you have:

**Component Structure:**
- [ ] Created separate component files for Navbar, Hero, About, Skills, Projects, Contact, Footer
- [ ] App.jsx imports and uses all components
- [ ] No 500+ line monolithic component
- [ ] All components in `src/components/` folder

**State Management:**
- [ ] Navbar uses useState for mobile menu
- [ ] Projects uses useState for activeFilter
- [ ] Projects derives filteredProjects (not stored in state)
- [ ] Contact uses useState for formData object
- [ ] Contact uses useState for errors object
- [ ] Skills uses useState for skills array

**Controlled Components:**
- [ ] All form inputs have `value` from state
- [ ] All form inputs have `onChange` handler
- [ ] Form updates formData state as user types
- [ ] No uncontrolled inputs

**Immutable Updates:**
- [ ] Object state updated with spread operator
- [ ] Array filtering uses .filter() method
- [ ] No direct mutation of state
- [ ] Creating new references for React to detect changes

**Functionality:**
- [ ] Mobile menu toggles open/closed
- [ ] Project filter buttons work correctly
- [ ] Active filter button shows active class
- [ ] Form validates on blur and submit
- [ ] Form shows success message after valid submission
- [ ] Form clears after submission

**Code Quality:**
- [ ] No console errors
- [ ] All map() operations have key prop
- [ ] Proper conditional className syntax
- [ ] Event handlers use arrow functions or proper binding
- [ ] No DOM manipulation with querySelector

**Bonus (Optional):**
- [ ] Added custom features or styling
- [ ] Implemented challenge extensions
- [ ] Created reusable custom hooks

### Reflection Questions

After completing the project, consider:

1. **How does state management differ from DOM manipulation?**
   - Compare Week 7 (querySelector) vs Week 9 (useState)
   - Which is more predictable? Why?

2. **When should state be component-level vs app-level?**
   - What makes mobile menu component-level?
   - Why might projects need to be app-level later?

3. **Why are immutable updates important?**
   - What happens if you mutate state directly?
   - How does React detect changes?

4. **What is derived state and why use it?**
   - Why not store filteredProjects in state?
   - What problems does this prevent?

Keep these patterns in mind - you'll use them in every React application you build!

---

**Congratulations!** You've transformed your monolithic component into a clean, state-managed React application! You now understand the core patterns of React development: component decomposition, state management, controlled components, and immutable updates. In Week 10, you'll learn how to share state across components with Context API and fetch data with useEffect.
