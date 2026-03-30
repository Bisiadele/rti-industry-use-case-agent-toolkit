# RTI Industry Use Case Agent Toolkit

A GitHub Copilot agent for generating industry-specific use cases for **Microsoft Fabric Real-Time Intelligence (RTI)**.

## Overview

The Industry Use Case Creation Agent is an AI‑driven toolkit for generating industry use case assets for Microsoft Fabric Real‑Time Intelligence (RTI) and other Microsoft data and AI products.
It helps teams identify both known and emerging industry challenges—including issues customers may not yet be explicitly asking for—and turns them into customer‑ready storylines, value narratives, and reference architectures that support product strategy, go‑to‑market motions, and customer engagements.

## Who This Is For
This agent is designed to be used across Microsoft teams, including:

Product Marketing — to develop consistent, repeatable use case messaging grounded in real industry problems
Field Enablement — to equip sellers and specialists with structured, credible use cases
Customer Engagement & Solutions Teams — to frame conversations around value, outcomes, and architecture
Product Groups — to explore and validate industry scenarios that inform roadmap and feature prioritization
Customer Advocacy Teams (CAT) — to translate product capabilities into industry‑specific customer narratives

## The agent generates the following industry use case assets. Outputs are intended to be shared, reviewed, and adapted across teams.
Key outputs include:

- Use case research and summary tables
- Curated industry use case summaries grounded in external research and Microsoft documentation
- Use case architecture explanation, diagram, and storyline
  - - Customer‑ready storylines that connect business problems to outcomes
  - - Value narratives framed around AI, efficiency, cost avoidance, risk reduction, and growth
  - - Reference architecture explanations aligned to Microsoft Fabric RTI documentation patterns
- Supporting documentation suitable for internal reviews and customer conversations
  - - Presentation decks
- Industry usecase specific Fabric notebook with synthetic data

## How It Works (High‑Level)
The agent follows a structured, multi‑phase workflow:

1. Industry Discovery
Gathers input on the target industry and business context.

2. Research & Signal Gathering
Synthesizes information from Microsoft documentation and publicly available industry sources (e.g., architecture centers, analyst research, market insights).

3. Use Case Structuring
Translates research into clear, structured use cases with defined problems, AI roles, data sources, and outcomes.

4. Narrative & Architecture Development
Expands selected use cases into detailed, enterprise‑ready narratives and architectural explanations tied to Microsoft Fabric RTI.

5. Review & Refinement
Supports iteration based on feedback to ensure relevance, clarity, and consistency.


## Prerequisites

- **VS Code** with GitHub Copilot extension
- **GitHub Copilot Chat** enabled
- Recommended extensions:
  - [Excalidraw](https://marketplace.visualstudio.com/items?itemName=pomdtr.excalidraw-editor) - for viewing/editing architecture diagrams
  - [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) - for presentations

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/BisiAdele/rti-industry-use-case-agent-toolkit.git
   ```

2. Open the folder in VS Code

3. The agent is automatically available in GitHub Copilot Chat

## Usage

1. Open GitHub Copilot Chat in VS Code
2. Select the **"Industry Use Case Creation agent"** from the agent picker (or type `@industryusecase`)
3. Start by specifying an industry:
   ```
   I want to create use cases for the Healthcare industry
   ```

4. Follow the guided workflow through all phases

## Workflow Phases

| Phase | Description |
|-------|-------------|
| 1 | Gather Industry Input |
| 2 | Research Use Cases |
| 3 | Summarize Use Cases (creates summary table) |
| 4 | Review & Select Use Cases |
| 5 | Create Written Architecture Explanations |
| 6 | Enforce Style Guidelines |
| 7 | Generate Architecture Diagrams (Excalidraw) |
| 8 | Create Architecture Storylines |
| 9 | Export to PowerPoint |
| 10 | Create Marp Presentation Deck |
| 11 | Generate Fabric Notebook + Synthetic Data |

## Output Structure

Generated documents are placed in `staging-documentation/`:

```
staging-documentation/
├── use-case-summary-table-[industry].md
├── use-case-[name]-architecture-explanation.md
├── use-case-[name]-architecture-diagram.excalidraw
├── use-case-[name]-architecture-storyline.md
├── use-case-[name]-presentation.md
├── use-case-[name]-presentation.pptx
├── use-case-[name]-notebook.ipynb
└── media/
    └── use-case-[name]-architecture-diagram.png
```

## Supported Industries

- Healthcare
- Finance / Banking
- Retail / E-commerce
- Manufacturing
- Energy & Utilities
- Transportation & Logistics
- Telecommunications
- And more...

## License

MIT License - see [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome! Please open an issue or submit a pull request.
