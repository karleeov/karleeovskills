# Karleeov Skills

A curated collection of 50+ AI agent skills for Claude Code and other AI assistants. These skills extend AI capabilities with specialized knowledge, workflows, and tool integrations.

## Quick Start

Skills are automatically triggered based on file types and keywords in your requests. Just ask Claude to do something, and the appropriate skill will be used.

## Skills Overview

### Document Processing

| Skill | Description | Triggers |
|-------|-------------|----------|
| **pdf** | Read, merge, split, rotate PDFs; OCR; extract text/tables; fill forms | `.pdf` files |
| **docx** | Create, edit, analyze Word documents with formatting, tracked changes | `.docx`, "Word document", "report" |
| **xlsx** | Create spreadsheets, financial models, data analysis | `.xlsx`, `.csv`, spreadsheets |
| **pptx** | Create slide decks, presentations, edit templates | `.pptx`, "slides", "deck", "presentation" |

### Web Development

| Skill | Description | Triggers |
|-------|-------------|----------|
| **frontend-design** | Build distinctive, production-grade UI interfaces | "landing page", "dashboard", web components |
| **webapp-testing** | Test local web apps with Playwright | "test my app", UI testing |
| **web-artifacts-builder** | Build React + Tailwind + shadcn/ui artifacts | "React artifact", web apps |

### Creative & Design

| Skill | Description | Triggers |
|-------|-------------|----------|
| **algorithmic-art** | Generate p5.js algorithmic/generative art | "generative art", "algorithmic art" |
| **theme-factory** | Apply 10 pre-built professional themes | "apply theme", styling artifacts |
| **slack-gif-creator** | Create animated GIFs optimized for Slack | "GIF for Slack", animated GIF |
| **canvas-design** | Design with 50+ bundled fonts | Custom design work |
| **brand-guidelines** | Apply Anthropic brand styling | Brand consistency |

### Developer Tools

| Skill | Description | Triggers |
|-------|-------------|----------|
| **mcp-builder** | Build MCP servers (Python/TypeScript) | "MCP server", API integration |
| **skill-creator** | Create new agent skills | "create a skill" |

### Cloud & DevOps

| Skill | Description | Triggers |
|-------|-------------|----------|
| **azure-devops** | Azure DevOps operations (Boards, Repos, Pipelines) | "Azure DevOps", "ADO", work items, sprints |
| **azure-deploy** | Deploy apps and infrastructure to Azure | "deploy to Azure", "azd", "Azure CLI" |

### Collaboration

| Skill | Description | Triggers |
|-------|-------------|----------|
| **doc-coauthoring** | Structured collaborative document creation | "write a doc", "draft proposal" |
| **internal-comms** | Company communications (newsletters, FAQs) | "newsletter", "status report" |

### Curated Skills (35+ additional skills)

Located in `skills/.curated/`:

- **Deployment**: cloudflare-deploy, vercel-deploy, netlify-deploy, render-deploy
- **Development**: aspnet-core, chatgpt-apps, openai-docs, jupyter-notebook
- **Design**: figma, figma-implement-design, slides
- **Testing**: playwright, playwright-interactive, screenshot
- **Security**: security-best-practices, security-threat-model
- **Integrations**: linear, sentry, notion-*, gh-*
- **Media**: imagegen, sora, speech, transcribe

## Example Usage

```
# Documents
"Merge these PDFs into one"
"Create a Word document with a table of contents"
"Build a financial model for revenue projections"
"Make a 10-slide pitch deck"

# Web Development
"Build a landing page for a SaaS product"
"Test my React app's login flow"
"Create a dashboard with charts"

# Creative
"Generate algorithmic art with flow fields"
"Make a celebratory GIF for Slack"
"Apply the Ocean Depths theme to this presentation"

# Development
"Build an MCP server for the GitHub API"
"Create a new skill for my company's workflow"

# Cloud & DevOps
"Deploy my app to Azure using azd"
"Create a work item in Azure DevOps"
"List my Azure pipelines"
"Provision Azure infrastructure with Bicep"
```

## Skill Structure

Each skill follows this pattern:
```
skill-name/
├── SKILL.md          # Main skill definition (required)
├── scripts/          # Executable helper scripts
├── references/       # Documentation to load as needed
└── assets/           # Templates, fonts, images
```

## Dependencies

### Python
```bash
pip install pypdf pdfplumber reportlab openpyxl pandas pillow playwright
```

### Node.js
```bash
npm install -g docx pptxgenjs
```

### System Tools
- **LibreOffice** - Document conversion
- **Poppler** - PDF processing
- **pandoc** - Document conversion

## Creating Custom Skills

Use the skill-creator skill to build your own:

```bash
python skills/.system/skill-creator/scripts/init_skill.py my-skill --path ./skills
```

## Based On

This collection is built on the [Agent Skills Specification](https://agentskills.io/specification).

## License

Each skill contains its own license in the LICENSE.txt file.

---

Made with Claude Code
