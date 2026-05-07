# AI Workplace Productivity Assistant

> Built as part of the **CAPACITI AI Skill Accelerator Programme**

A multi-page web application that uses the **Google Gemini API** to automate three common workplace tasks: generating professional emails, summarizing meeting notes, and creating AI-powered task schedules. The project demonstrates practical prompt engineering, responsible AI usage, and real-world integration of a large language model into a clean, usable interface — using a completely **free API** with no credit card required.

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

The application is built entirely with vanilla HTML, CSS, and JavaScript — no frameworks or build tools required. It connects directly to the **Google Gemini API** (`gemini-2.0-flash`) to generate intelligent, context-aware outputs based on user inputs. The Gemini API was chosen because it offers a **free tier with no credit card required**, making this project fully accessible for development, testing, and demonstration.

---

## Features

### 1. ✉️ Smart Email Generator (`email-generator.html`)
Generates complete, professional emails based on user-provided context.

- Select the **recipient type** — Client, Manager, Team member, Colleague, Vendor, or HR
- Choose the **email tone** — Formal, Informal, Persuasive, Apologetic, Assertive, or Grateful
- Describe the **purpose** of the email in plain language
- Optionally provide the **sender name/role** and **recipient name** for personalisation
- Gemini returns a fully written email including Subject line, greeting, body, and sign-off
- One-click **Copy** button to grab the output

### 2. 📋 Meeting Notes Summarizer (`meeting-summarizer.html`)
Converts raw, messy meeting notes into a clean structured summary.

- Paste any format of notes — bullet points, paragraphs, voice-to-text transcripts
- Select the **meeting type** — standup, client meeting, strategy session, etc.
- Choose the **summary style** — Concise bullet points, Detailed paragraphs, or Executive one-pager
- Optionally list **attendees** for context
- Gemini returns a structured output with four sections:
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
- Gemini returns a time-blocked schedule with realistic time slots and 3 productivity tips tailored to the task list
- Live **task counter** showing how many tasks have been entered
- One-click **Copy** button

---

## Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | CSS3 (custom properties, grid, flexbox) |
| Logic | Vanilla JavaScript (ES6+) |
| AI Model | Google Gemini (`gemini-2.0-flash`) |
| API | Google Gemini API (`/v1beta/models/gemini-2.0-flash:generateContent`) |
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

1. The user pastes their free Gemini API key into the key field at the top of the page
2. The user fills in the form with their inputs (context, preferences, raw content)
3. On clicking the generate button, JavaScript collects the form values
4. A structured prompt is constructed using those values
5. A `fetch()` call sends the prompt to the Gemini API via a `POST` request
6. The API returns a response from Gemini, extracted from `data.candidates[0].content.parts[0].text`
7. The result is displayed in the output box on the page
8. The user can copy the output with a single click

### API Call Structure

```javascript
const MODEL = 'gemini-2.0-flash';
const API_KEY = document.getElementById('api-key').value.trim();
const URL = `https://generativelanguage.googleapis.com/v1beta/models/${MODEL}:generateContent?key=${API_KEY}`;

const response = await fetch(URL, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    contents: [{ parts: [{ text: prompt }] }],
    generationConfig: { maxOutputTokens: 1000, temperature: 0.7 }
  })
});

const data = await response.json();
const result = data.candidates[0].content.parts[0].text;
```

### Why Gemini Works in the Browser

Unlike some AI APIs that block direct browser requests due to CORS restrictions, the Gemini API accepts the key as a **URL query parameter** (`?key=YOUR_KEY`). This means it works natively from plain HTML/JS without needing a backend server or proxy — making it the ideal choice for a student project opened directly in a browser.

---

## Prompt Engineering

A core focus of this project is the quality and structure of the prompts sent to Gemini. Each prompt is carefully constructed to:

- **Set a role** — Gemini is told what type of assistant it is (email writer, summarizer, scheduler)
- **Provide structured context** — user inputs are injected cleanly into the prompt using JavaScript template literals
- **Define the output format** — Gemini is instructed on exactly how to structure its response
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

### Example — Meeting Summarizer Prompt Pattern

```
You are a professional meeting notes summarizer. Summarize the following notes clearly.

Summary style: Concise bullet points
Attendees: [optional]

Structure your output with:
📋 SUMMARY
✅ KEY DECISIONS
📌 ACTION ITEMS
⏭️ FOLLOW-UPS / NEXT STEPS

Raw meeting notes: [user input]

Output ONLY the structured summary — no preamble, no commentary.
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
- **Input validation** — users are prompted if they try to generate output without providing required information or an API key, preventing empty or useless API calls
- **Specific error messages** — the app distinguishes between a missing key, an invalid key, and a connection error, helping users self-correct without confusion
- **Transparency** — the interface makes it clear that all content is AI-generated, not human-authored
- **No over-automation** — the tools assist and draft, but always leave the final decision and review to the human user
- **Bias awareness** — prompts are designed to be neutral and professional, avoiding language that could introduce cultural or linguistic bias into outputs
- **Scope limitation** — each tool is scoped to a specific, low-risk productivity task, avoiding domains where AI errors could cause serious harm
- **Key privacy** — the API key is entered by the user at runtime, never hardcoded into the source files, and never leaves the browser

---

## Getting Started

### Prerequisites
- A modern web browser (Chrome, Firefox, Edge, Safari)
- A **free** Google Gemini API key — no credit card required

### Step 1 — Get your free Gemini API key

1. Go to [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
2. Sign in with your Google account
3. Click **Create API key**
4. Copy the key — you will paste it into each tool page when using it

### Step 2 — Set up the project

1. Download all four HTML files into the same folder on your computer
2. Open `index.html` in your browser
3. Click any tool card to open it
4. Paste your Gemini API key into the key field at the top of the page
5. Fill in the form and click the generate button

That's it — no installation, no terminal, no dependencies.

> ⚠️ **Security note:** Your API key is entered at runtime and stays only in your browser session. Never hardcode your API key directly into source files that will be pushed to a public GitHub repository. For production use, route API calls through a backend server or serverless function.

---

## Usage Guide

### Email Generator
1. Paste your Gemini API key into the key field
2. Select who you are writing to (e.g. Client)
3. Choose the tone that fits (e.g. Formal)
4. Describe what the email is about in the text area
5. Optionally add your name/role and the recipient's name
6. Click **Generate email**
7. Review the output and click **Copy** to use it

### Meeting Notes Summarizer
1. Paste your Gemini API key into the key field
2. Paste your raw meeting notes into the text area (any format)
3. Select the meeting type and preferred summary style
4. Optionally list the attendees
5. Click **Summarize notes**
6. Review the structured summary and copy it to share

### Task Planner
1. Paste your Gemini API key into the key field
2. Type each task on its own line
3. Choose your plan type, work hours, and focus style
4. Add any fixed commitments or preferences in the context field
5. Click **Generate my plan**
6. Review the time-blocked schedule and copy it

---

## Challenges and Solutions

| Challenge | Solution |
|---|---|
| Browser CORS errors when calling AI APIs directly | Switched to the Google Gemini API, which accepts the key as a URL query parameter and supports browser-based requests natively — no CORS issues, no proxy needed |
| Keeping the API key secure without a backend | Added an in-page API key input field — the key is entered at runtime by the user and never stored or hardcoded in source files |
| Getting Gemini to return clean, ready-to-use text | Added explicit prompt instructions: "Output ONLY the [content]. No preamble, no commentary, no markdown code blocks." |
| Handling optional form fields without breaking the prompt | Used conditional template literals — optional fields are only injected into the prompt if they have a value |
| Keeping the UI responsive while waiting for the API | Disabled the button and showed an animated loading indicator during the fetch call, re-enabled on completion |
| Giving users clear feedback when something goes wrong | Added specific error handling that differentiates between a missing key, an invalid key, and a network error |
| Making outputs immediately usable | Added a one-click Copy button on every output panel with a visual confirmation state |

---

## Future Improvements

- Add a **backend proxy** to secure the API key server-side for production deployments
- Implement **session storage** to save and revisit previous outputs within a session
- Add an **export to PDF** feature for meeting summaries and schedules
- Build a **history panel** showing the last 5 generated outputs per tool
- Add **multi-language support** for non-English speaking users
- Integrate with **Google Calendar API** to auto-populate the task planner with real calendar events
- Add **dark mode** toggle
- Explore **Gemini's multimodal capabilities** to allow users to upload documents or images as additional context

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
Powered by [Google Gemini API](https://aistudio.google.com)

---

*AI-generated content should always be reviewed by a human before use in professional or client-facing settings.*
