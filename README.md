<p align="center">
  <img src="./assets/header.svg" alt="Guilherme Bernardes: AI Evaluation and Automation, Brazilian Portuguese" width="100%">
</p>

<p align="center">
  <a href="https://www.linkedin.com/in/guilherme-bernardes-192404263/"><img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge" alt="LinkedIn"></a>
  <a href="#featured-project"><img src="https://img.shields.io/badge/Featured-AI%20Workflow-1448D8?style=for-the-badge" alt="Featured project"></a>
  <a href="#portfolio"><img src="https://img.shields.io/badge/Portfolio-Projects-346CE6?style=for-the-badge" alt="Portfolio"></a>
  <a href="#professional-background"><img src="https://img.shields.io/badge/Background-Experience-0A1E64?style=for-the-badge" alt="Professional background"></a>
</p>

# Hi, I'm Guilherme Bernardes 👋

I am a Brazilian Portuguese professional focused on AI evaluation, LLM quality assurance, prompt testing, data quality and workflow automation.

My background combines Business Administration, data analysis, CRM operations and practical experience designing and testing AI-powered workflows for Portuguese-language use cases.

## What I work with

<table>
<tr>
<td valign="top" width="50%">

**AI evaluation and quality**

- Portuguese-language AI response evaluation
- Prompt and response testing
- Accuracy, clarity, tone and instruction-following review
- Fact-checking and unsupported-claim detection
- AI agent evaluation and quality documentation

</td>
<td valign="top" width="50%">

**Automation and data**

- Conversation design and request classification
- n8n workflow automation
- LLM, API, WhatsApp, calendar and CRM integrations
- Excel, dashboards, Power BI and operational data analysis

</td>
</tr>
</table>

## Featured project

### Vertex Digital — AI Automation Workflow

Designed and tested an AI-assisted workflow integrating an LLM, n8n, WhatsApp, conversation memory, request classification, calendar tools, CRM, APIs and human handoff.

The project included:

- Prompt and persona design
- Guardrails and fallback rules
- Portuguese-language response evaluation
- Detection of unsupported claims and inconsistent outputs
- Conversation-flow testing
- Workflow documentation and quality review

> [!NOTE]
> Vertex Digital is my own independent project. It is not related to Google Cloud Vertex AI.

**How the n8n workflow runs (15 nodes)**

```mermaid
flowchart LR
    subgraph IN["1. Receive"]
        direction TB
        A(["WhatsApp Trigger"]) --> B{{"Is it a message?"}}
        B -- yes --> C["Classify conversation<br/>new contact vs. follow-up"]
        C --> D["Wait: natural read delay"]
        D --> E["Mark as read<br/>WhatsApp Cloud API"]
        E --> F["Pause before typing"]
    end
    subgraph THINK["2. Think"]
        direction LR
        M[("Conversation memory<br/>last 10 messages")] -.-> G[["AI Agent"]]
        L["LLM: OpenAI gpt-4o-mini"] -.-> G
        T["Tool: consultar_agenda<br/>Google Calendar"] -.-> G
    end
    subgraph OUT["3. Reply like a person"]
        direction TB
        H["Split the reply into short messages<br/>and compute typing time"] --> I{{"Loop over messages"}}
        I --> J["Typing indicator<br/>WhatsApp Cloud API"]
        J --> K["Wait: typing time"]
        K --> S(["Send message"])
        S --> I
    end
    IN --> THINK --> OUT

    classDef ai fill:#1448D8,stroke:#0A1E64,color:#ffffff
    classDef sub fill:#EEF3FF,stroke:#1448D8,color:#0A1E64
    class G ai
    class M,L,T sub
```

**What testing changed: prompt evaluation log**

The first real use case was a Portuguese-language WhatsApp assistant for a 2026 state-legislature campaign in Espírito Santo, Brazil. Each version of the prompt came from reviewing test conversations:

| Version | What the test conversations showed | What changed in the prompt |
|:-:|---|---|
| v1 | Initial build | Four conversation paths (proposals, volunteering, community requests, talking to the candidate), deflection of sensitive topics and silent escalation to a human |
| v2 | The assistant asked for personal data by default, and greetings felt like an interrogation | Collect data only when there is a clear reason; light, varied greetings; shorter volunteer sign-up; updated few-shot examples |
| v3 | Answers added background nobody had asked for | Answer only what was asked; new calendar tool, with a rule to never invent dates, times or events |
| v4 | Scenarios the prompt did not cover | Full scenario map: community requests, agenda availability, donation questions with their legal limits, polarizing topics and suspicious interactions flagged to a human |
| v5 | Too many emojis and long single messages | At most one emoji per exchange; separate ideas sent as separate messages |
| v6 | Em dashes in replies and no rule for offensive messages | No em dashes; calm with mild offenses, a firm boundary with explicit insults |
| v7 | Duplicated rules and token cost | Removed duplicated instructions, prompt caching, compact data format for tool results, memory window review. No change in behavior |

## Portfolio

### 🤖 Multi-provider WhatsApp agent (Node.js)

The same assistant rebuilt as a Node.js server, with the natural-timing logic ported from the n8n flow.

```mermaid
flowchart TD
    U(["WhatsApp user"]) <--> W["Meta Cloud API"]
    W --> S["Node.js webhook<br/>on Render"]
    S <-.-> C["Chatwoot<br/>human handoff"]
    S --> R{{"Rules and<br/>intents"}}
    R -- "volunteer or idea" --> X["Name extraction<br/>small LLM"]
    R -- "chat" --> F["LLM fallback<br/>Claude, Groq, Gemini"]
    F -- "all fail" --> SAFE["Safe reply"]
    F --> O["Bubbles and<br/>natural timing"]
    X --> O
    SAFE --> O
    O --> W

    classDef ai fill:#1448D8,stroke:#0A1E64,color:#ffffff
    class F,X ai
```

- Automatic fallback across three LLM providers (Claude Haiku, Groq Llama 3.1, Gemini Flash), each with a 12-second timeout so no provider can stall a reply
- When every provider fails, a safe reply says the assistant would rather confirm than give a wrong answer
- A rule layer runs before the LLM: fixed replies, keyword triggers and intent detection; for volunteer sign-ups and idea suggestions, a small LLM extracts only the name (and the idea) from free text
- Tool calling (register a volunteer, register an idea, flag a conversation for a human) with one schema adapted to each provider's format
- Human handoff through Chatwoot: the bot goes silent when a person replies in the same thread
- Read receipts and typing indicators timed like a person typing

`Node.js` `Express` `Anthropic Claude` `Groq` `Google Gemini` `WhatsApp Cloud API` `Chatwoot` `Render`

### 🧩 Persona and prompt toolkit

- **WhatsApp persona builder** (a Claude Code skill I built): a branching intake form that produces a system prompt for n8n's AI Agent node plus a readable persona spec. It draws on reference libraries for conversation scenarios, guardrails, tones of voice, tools and token economy, has a test mode that simulates the WhatsApp conversation before deployment, and an edit mode that keeps a versioned changelog.
- **Demo persona template:** only verifiable public facts go into the prompt, and when in doubt the assistant deflects instead of guessing. It stays neutral on polarizing topics and never makes promises.
- **Review process:** a human checks every persona before use (name, role, city, people with the same name), and the demo questions and answers are approved before any video is recorded.

### 💬 WhatsApp conversation simulator

<img src="./assets/whatsapp-simulator.webp" alt="WhatsApp conversation simulator with a scripted demo conversation" width="100%">

A standalone HTML, CSS and JavaScript replica of the iOS WhatsApp interface that I use to script, play back and record demo conversations. It imports transcripts as plain text or JSON, splits replies into separate bubbles and simulates typing speed, read receipts and the keyboard.

`HTML` `CSS` `JavaScript`

### 📊 Campaign finance and accountability panel (prototype)

A multi-tenant panel for recording campaign income and expenses.

- Receipt and invoice reading in layers: free deterministic steps first (QR code, PDF text, access-key parsing, company lookup through BrasilAPI), then Google Gemini Flash as a fallback for photos and scans. The model must return strict JSON and is told to return null rather than invent a value.
- Access control with Supabase Auth and 22 PostgreSQL row-level security policies
- Dashboards for cash flow, daily movement and goals
- Spreadsheet export organized by the fields of SPCE, the Brazilian electoral accountability system

`Next.js` `TypeScript` `Supabase` `PostgreSQL` `Google Gemini` `Tailwind CSS`

### 🌐 Vertex Digital website · [vertexdigital.dev.br](https://vertexdigital.dev.br)

<a href="https://vertexdigital.dev.br"><img src="./assets/vertex-digital-site.webp" alt="Vertex Digital website" width="100%"></a>

- Scroll-driven animation (GSAP, Framer Motion, Lenis) and a service-area map (MapLibre)
- Contact form API that stores leads in Supabase and sends an email notification
- Technical SEO: JSON-LD structured data, sitemap, Open Graph images for every page and an `llms.txt` file for AI crawlers
- Daily keep-alive job and continuous deployment on Vercel

`Next.js` `React` `TypeScript` `Tailwind CSS` `Supabase` `Vercel`

### 🗳️ Campaign websites · [joaotita.com.br](https://www.joaotita.com.br)

<a href="https://www.joaotita.com.br"><img src="./assets/campaign-site.webp" alt="Campaign website built for a client" width="100%"></a>

- Client website for a 2026 state-deputy campaign, exported as a static site to meet the electoral rule of hosting in Brazil, with the official donation widget
- Reusable campaign-site template with colors set per candidate through CSS theme tokens
- Demo sites built from a research report on each candidate's public information and deployed on Vercel

`Next.js` `React` `TypeScript` `Tailwind CSS` `Vercel`

### 🎬 Motion graphics as code

<img src="./assets/motion-service-video.webp" alt="Frame from a looping service video made with Remotion" width="100%">

Looping service videos for the Vertex Digital website, written in React with Remotion. I also built a Claude Code skill that runs a structured briefing (format, colors, scenes, timing, messages) and outputs a Remotion project ready to render.

`Remotion` `React` `TypeScript`

## Professional background

Before focusing on AI automation, I worked with commercial data, CRM, performance indicators, administrative processes and customer support. This background helps me evaluate AI systems from both a technical and business perspective.

<table>
<tr>
<td align="center" width="25%"><h3>R$ 400k/year</h3>projected savings<br/><sub>Supergasbras</sub></td>
<td align="center" width="25%"><h3>680 → ~1,500</h3>new clients per year<br/><sub>SGI project, Supergasbras</sub></td>
<td align="center" width="25%"><h3>16 → 24</h3>vehicles sold per month<br/><sub>Giuliano O Vendedor</sub></td>
<td align="center" width="25%"><h3>8</h3>sellers supported<br/><sub>with reports and analysis</sub></td>
</tr>
</table>

**Founder / AI Automation Project Lead** · Vertex Digital · *June 2026 to present*<br/>
Portuguese-language AI assistants for WhatsApp, prompt and persona design, response evaluation, n8n workflows, websites and CRM integrations.

**Commercial Assistant** · Supergasbras, multinational LPG company · *2024 to 2026*
- Tracked commercial KPIs: contribution margin, EBIT, sales volume, retention and prospecting
- Analyzed a bulk-gas portfolio of about 1,900 clients
- SGI project: new clients per year went from 680 to about 1,500
- Savings project projected at R$ 400k per year by improving the customer-transfer registration process
- Automated reports in VBA for real-time tracking
- Lean methods (A3, Ishikawa, root-cause analysis), customer-experience initiatives and analytical support for a team of 8 sellers

**Commercial Assistant** · Giuliano O Vendedor, automotive retail · *2023 to 2024*
- Integrations with Webmotors, Mobiauto, iCarros and OLX, plus API and automation work between CRMs and listing platforms
- Lead management and distribution, income statements (DRE) and financial performance analysis
- After-sales, vehicle transfer documentation and invoicing
- Monthly sales grew from 16 to 24 vehicles and the store became the 3rd largest in Greater Vitória

**Receptionist** · Pink Elephant · *2023*<br/>
Customer service, entry control, ticket sales and cashier support.

## Tools and technologies

Used in my work and in the projects above.

**AI and automation**
<br/>
<img src="https://img.shields.io/badge/n8n-EA4B71?style=flat-square&logo=n8n&logoColor=white" alt="n8n">
<img src="https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=openai&logoColor=white" alt="OpenAI">
<img src="https://img.shields.io/badge/Anthropic%20Claude-D97757?style=flat-square&logo=anthropic&logoColor=white" alt="Anthropic Claude">
<img src="https://img.shields.io/badge/Claude%20Code-D97757?style=flat-square&logo=claude&logoColor=white" alt="Claude Code">
<img src="https://img.shields.io/badge/Google%20Gemini-8E75B2?style=flat-square&logo=googlegemini&logoColor=white" alt="Google Gemini">
<img src="https://img.shields.io/badge/Groq-F55036?style=flat-square" alt="Groq">
<img src="https://img.shields.io/badge/WhatsApp%20Cloud%20API-25D366?style=flat-square&logo=whatsapp&logoColor=white" alt="WhatsApp Cloud API">
<img src="https://img.shields.io/badge/Google%20Calendar-4285F4?style=flat-square&logo=googlecalendar&logoColor=white" alt="Google Calendar">
<img src="https://img.shields.io/badge/Chatwoot-1F93FF?style=flat-square" alt="Chatwoot">

**Web and data**
<br/>
<img src="https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white" alt="Next.js">
<img src="https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB" alt="React">
<img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white" alt="TypeScript">
<img src="https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white" alt="Node.js">
<img src="https://img.shields.io/badge/Tailwind%20CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white" alt="Tailwind CSS">
<img src="https://img.shields.io/badge/Supabase-3FCF8E?style=flat-square&logo=supabase&logoColor=white" alt="Supabase">
<img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white" alt="PostgreSQL">
<img src="https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white" alt="Vercel">
<img src="https://img.shields.io/badge/Render-46E3B7?style=flat-square&logo=render&logoColor=black" alt="Render">
<img src="https://img.shields.io/badge/Remotion-0B84F3?style=flat-square" alt="Remotion">

**Business and analytics**
<br/>
<img src="https://img.shields.io/badge/Excel-217346?style=flat-square" alt="Excel">
<img src="https://img.shields.io/badge/VBA-5C2D91?style=flat-square" alt="VBA">
<img src="https://img.shields.io/badge/Power%20BI-F2C811?style=flat-square" alt="Power BI">
<img src="https://img.shields.io/badge/Salesforce-00A1E0?style=flat-square" alt="Salesforce">
<img src="https://img.shields.io/badge/CRM-0A1E64?style=flat-square" alt="CRM">
<img src="https://img.shields.io/badge/Google%20Ads-4285F4?style=flat-square&logo=googleads&logoColor=white" alt="Google Ads">
<img src="https://img.shields.io/badge/Meta%20Ads-0467DF?style=flat-square&logo=meta&logoColor=white" alt="Meta Ads">
<img src="https://img.shields.io/badge/Lean%20A3%20%26%20Ishikawa-7891B9?style=flat-square" alt="Lean A3 and Ishikawa">

## Current interests

- AI training and evaluation
- LLM quality assurance
- Portuguese localization
- Data quality and annotation
- Prompt and response writing
- Agent evaluation
- Fact-checking
- Workflow automation

## Education and certifications

🎓 **Bachelor of Business Administration** · Rede de Ensino Doctum · *completed in 2025*

| Certificate | Institution |
|---|---|
| Excel: Dashboards | Kultivi |
| Time Management and Productivity | Kultivi |
| Team Building as a Strategy for Success | Sebrae |
| The Impact of IT on Business | Fundação Getulio Vargas |
| Power BI | Microsoft Power Platform |
| Customer Experience | Specialization |

## Languages

- Portuguese — Native
- English — B2

## Connect with me

[LinkedIn: Guilherme Bernardes](https://www.linkedin.com/in/guilherme-bernardes-192404263/)
