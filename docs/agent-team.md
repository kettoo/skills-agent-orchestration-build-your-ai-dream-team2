# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard:

- **Planner** uses **Claude Opus 5.5 (copilot)** to research the repository and produce an implementation plan, including file assignments, dependencies, risks, and validation expectations. Definition: `.github/agents/planner.agent.md`.
- **Designer** uses **Gemini 3.7 Flash (copilot)** to shape the dashboard's user experience, visual design, accessibility, interaction flow, and responsive behavior. Definition: `.github/agents/designer.agent.md`.
- **Coder** uses **GPT-5.5 (copilot)** to implement assigned code, fix bugs, and validate changes, including Project Pulse app and launch support when assigned. Definition: `.github/agents/coder.agent.md`.
- **Orchestrator** uses **Claude Opus 5.5 (copilot)** to coordinate the specialists, assign work by phase and file scope, manage dependencies, and verify the integrated result. Definition: `.github/agents/orchestrator.agent.md`.

I am using GitHub Copilot CLI in a Codespace to orchestrate the work.
