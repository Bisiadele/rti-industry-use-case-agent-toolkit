# RTI Industry Use Case Agent Toolkit

A GitHub Copilot agent for generating industry-specific use cases for **Microsoft Fabric Real-Time Intelligence (RTI)**.

## Overview

This agent automates the creation of comprehensive industry use case documentation, including:
- Use case research and summary tables
- Architecture explanations
- Excalidraw architecture diagrams
- Architecture storylines
- Marp presentation decks
- Fabric notebooks with synthetic data

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
