# AI Workplace Productivity Assistant

> Built as part of the **CAPACITI AI Skill Accelerator Programme**

A multi-page web application that uses the **Anthropic Claude API** to automate three common workplace tasks: generating professional emails, summarizing meeting notes, and creating AI-powered task schedules. The project demonstrates practical prompt engineering, responsible AI usage, and real-world integration of a large language model into a clean, usable interface.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Prompt Engineering](#prompt-engineering)
- [Responsible AI Practices](#responsible-ai-practices)
- [Getting Started](#getting-started)
- [Usage Guide](#usage-guide)
- [Challenges and Solutions](#challenges-and-solutions)
- [Future Improvements](#future-improvements)
- [Industry Relevance](#industry-relevance)
- [Author](#author)

---

## Project Overview

Professionals across industries lose significant time to repetitive communication and planning tasks — drafting emails, writing up meeting notes, and figuring out how to structure their day. This project solves that problem by providing an AI-powered assistant that handles those tasks in seconds.

The application is built entirely with vanilla HTML, CSS, and JavaScript — no frameworks or build tools required. It connects directly to the Anthropic Claude API (`claude-sonnet-4-20250514`) to generate intelligent, context-aware outputs based on user inputs.

---

## Features

### 1. ✉️ Smart Email Generator (`email-generator.html`)
Generates complete, professional emails based on user-provided context.

- Select the **recipient type** — Client, Manager, Team member, Colleague, Vendor, or HR
- Choose the **email tone** — Formal, Informal, Persuasive, Apologetic, Assertive, or Grateful
- Describe the **purpose** of the email in plain language
- Optionally provide the **sender name/role** and **recipient name** for personalisation
- Claude returns a fully written email including Subject line, greeting, body, and sign-off
- One-click **Copy** button to grab the output

### 2. 📋 Meeting Notes Summarizer (`meeting-summarizer.html`)
Converts raw, messy meeting notes into a clean structured summary.

- Paste any format of notes — bullet points, paragraphs, voice-to-text transcripts
- Select the **meeting type** — standup, client meeting, strategy session, etc.
- Choose the **summary style** — Concise bullet points, Detailed paragraphs, or Executive one-pager
- Optionally list **attendees** for context
- Claude returns a structured output with four sections:
  - 📋 Summary
  - ✅ Key Decisions
  - 📌 Action Items (with responsibilities and deadlines)
  - ⏭️ Follow-ups / Next Steps
- Live **character counter** to track input length
- One-click **Copy** button

### 3. 📅 AI Task Planner (`task-planner.html`)
Turns a list of tasks into a prioritized, time-blocked daily or weekly schedule.

- Enter tasks one per line — as specific or brief as needed
- Choose the **plan type** — Daily or Weekly
- Set your **work hours** — multiple time range options
- Select a **focus style** — Deep work first, Quick wins first, Deadline-driven, Balanced mix, or Energy-based
- Add **extra context** such as fixed meetings or personal preferences
- Claude returns a time-blocked schedule with realistic time slots and 3 productivity tips tailored to the task list
- Live **task counter** showing how many tasks have been entered
- One-click **Copy** button

---

## Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | CSS3 (custom properties, grid, flexbox) |
| Logic | Vanilla JavaScript (ES6+) |
| AI Model | Anthropic Claude (`claude-sonnet-4-20250514`) |
| API | Anthropic Messages API (`/v1/messages`) |
| Fonts | Google Fonts — DM Sans & DM Mono |
| Hosting | Any static file server or opened directly in browser |

No npm, no build step, no dependencies to install. Just open the HTML files.

---

## Project Structure

```
ai-assistant/
│
├── index.html               # Home page — links to all three tools
├── email-generator.html     # Tool 1 — Smart Email Generator
├── meeting-summarizer.html  # Tool 2 — Meeting Notes Summarizer
└── task-planner.html        # Tool 3 — AI Task Planner
```

All four files must be kept in the **same folder** for the navigation links between pages to work correctly.

---

## How It Works

Each tool follows the same flow:

1. The user fills in a form with their inputs (context, preferences, raw content)
2. On clicking the generate button, JavaScript collects the form values
3. A structured prompt is constructed using those values
4. A `fetch()` call sends the prompt to the Anthropic API via a `POST` request to `https://api.anthropic.com/v1/messages`
5. The API returns a response from Claude, which is extracted from `data.content[0].text`
6. The result is displayed in the output box on the page
7. The user can copy the output with a single click

### API Call Structure

```javascript
const response = await fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 1000,
    messages: [{ role: 'user', content: prompt }]
  })
});
const data = await response.json();
const result = data.content[0].text;
```

---

## Prompt Engineering

A core focus of this project is the quality and structure of the prompts sent to Claude. Each prompt is carefully constructed to:

- **Set a role** — Claude is told what type of assistant it is (email writer, summarizer, scheduler)
- **Provide structured context** — user inputs are injected cleanly into the prompt using template literals
- **Define the output format** — Claude is instructed on exactly how to structure its response
- **Suppress unwanted content** — prompts explicitly say "no preamble, no commentary, no markdown code blocks" to keep outputs clean and ready to use
- **Handle optional fields gracefully** — conditional template logic ensures the prompt stays clean when optional inputs are left blank

### Example — Email Generator Prompt Pattern

```
You are a professional email writing assistant. Write a complete, ready-to-send workplace email.

Recipient type: [Client / Manager / etc.]
Tone: [Formal / Persuasive / etc.]
Sender name and role: [optional]
Recipient name: [optional]
Purpose / context: [user input]

Write the full email including Subject line, greeting, body paragraphs, and sign-off.
Output ONLY the email — no explanations, no commentary, no markdown formatting.
```

### Example — Task Planner Prompt Pattern

```
You are a productivity coach. Create a structured daily plan.

Work hours: 8am – 5pm
Scheduling approach: Deep work first
Tasks: [user task list]

Assign realistic time blocks. Include breaks. End with 3 productivity tips.
Output ONLY the schedule and tips.
```

---

## Responsible AI Practices

This project applies the following responsible AI principles:

- **Disclaimer notices** — every tool page includes a visible disclaimer reminding users to review AI outputs before using them professionally
- **Input validation** — users are prompted if they try to generate output without providing the required information, preventing empty or useless API calls
- **Transparency** — the interface makes it clear that all content is AI-generated, not human-authored
- **No over-automation** — the tools assist and draft, but always leave the final decision and review to the human user
- **Bias awareness** — prompts are designed to be neutral and professional, avoiding language that could introduce cultural or linguistic bias into outputs
- **Scope limitation** — each tool is scoped to a specific, low-risk productivity task, avoiding domains where AI errors could cause serious harm

---

## Getting Started

### Prerequisites
- A modern web browser (Chrome, Firefox, Edge, Safari)
- An Anthropic API key — get one at [console.anthropic.com](https://console.anthropic.com)

### Setup

1. Download or clone all four HTML files into the same folder

2. Open each HTML file and locate the API call. The API key handling depends on your setup:

   **Option A — For demo/testing:** The app uses the Anthropic API directly from the browser. Ensure your API key is configured at the platform level or injected via a proxy.

   **Option B — Add your key directly (not recommended for production):** In each HTML file's `<script>` section, add your key to the headers:
   ```javascript
   headers: {
     'Content-Type': 'application/json',
     'x-api-key': 'YOUR_API_KEY_HERE',
     'anthropic-version': '2023-06-01'
   }
   ```

3. Open `index.html` in your browser

4. Click any tool card to start using the assistant

> ⚠️ **Security note:** Never expose your API key in client-side code in a production environment. For production use, route API calls through a backend server or serverless function that keeps the key secure.

---

## Usage Guide

### Email Generator
1. Select who you are writing to (e.g. Client)
2. Choose the tone that fits (e.g. Formal)
3. In the text area, describe what the email is about in plain language
4. Optionally add your name/role and the recipient's name
5. Click **Generate email**
6. Review the output and click **Copy** to use it

### Meeting Notes Summarizer
1. Paste your raw meeting notes into the text area (any format)
2. Select the meeting type and preferred summary style
3. Optionally list the attendees
4. Click **Summarize notes**
5. Review the structured summary and copy it to share

### Task Planner
1. Type each task on its own line
2. Choose your plan type, work hours, and focus style
3. Add any fixed commitments or preferences in the context field
4. Click **Generate my plan**
5. Review the time-blocked schedule and copy it

---

## Challenges and Solutions

| Challenge | Solution |
|---|---|
| Getting Claude to return clean, ready-to-use text without extra commentary | Added explicit instructions in every prompt: "Output ONLY the [content]. No preamble, no commentary." |
| Handling optional form fields without breaking the prompt | Used conditional template literals — optional fields are only injected into the prompt if they have a value |
| Keeping the UI responsive while waiting for the API | Disabled the button and showed an animated loading indicator during the fetch call, re-enabled on completion |
| Making outputs immediately usable | Added a one-click Copy button on every output panel |
| Preventing empty API calls | Added input validation that alerts the user and stops execution if required fields are empty |

---

## Future Improvements

- Add a **backend proxy** to secure the API key server-side
- Implement **local storage** to save and revisit previous outputs
- Add an **export to PDF** feature for meeting summaries and schedules
- Build a **history panel** showing the last 5 generated outputs per tool
- Add **multi-language support** for non-English speaking users
- Integrate with **Google Calendar API** to auto-populate the task planner with real calendar events
- Add **dark mode** toggle

---

## Industry Relevance

This project prepares candidates for roles including:

- **AI Prompt Engineer** — designing and refining prompts for specific business outcomes
- **AI Productivity Specialist** — implementing AI tools to streamline workplace operations
- **Digital Transformation Analyst** — identifying processes that can be automated with AI
- **Business Analyst (AI-enabled)** — using AI tools to improve reporting and communication workflows
- **Operations / Admin roles** — leveraging AI to reduce time spent on repetitive tasks

---

## Author

Built for the **CAPACITI AI Skill Accelerator Programme**  
Powered by [Anthropic Claude API](https://www.anthropic.com)

---

*AI-generated content should always be reviewed by a human before use in professional or client-facing settings.*
