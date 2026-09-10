---
okf_version: "0.1"
---

This is an Open Knowledge Format (OKF) bundle of the Aembit documentation, structured so an AI agent can navigate it with filesystem tools instead of loading the whole corpus into context.

## How to navigate this bundle

1. **Read indexes first.** Every folder has an `index.md` listing its contents — read it before opening the files inside. The guide map below is this bundle's top-level index.
2. **Filter before you read.** Each concept file's frontmatter carries `type` (`how-to`, `reference`, `explanation`, `tutorial`, `troubleshooting`, `definition`), `interface` (`web-ui`, `cli`, `api`, `mcp`, `sdk`), and `tags`. Match them to your task — a CLI question wants `interface: cli`; a conceptual question wants `type: explanation` — and open only the files that fit.
3. **Follow the links.** Concept files cross-reference related concepts with bundle-relative links, and many end with a `## Related` section pointing to their counterparts in other guides. Follow those instead of re-searching.
4. **Stop early.** A typical question resolves in this root index → one folder `index.md` → two or three concept files. You should never need to read a guide-sized document to answer one question.

## Guide map

* [Aembit Docs](overview.md) - Attest. Authenticate. Accelerate.
* [AI Assistant Integration Resources](llm-resources.md) - Machine-readable documentation resources for AI coding assistants and LLMs integrating with Aembit.
* [Getting support for Aembit](support-overview.md) - Overview of Aembit's support process
* [AI Guide](ai-guide/index.md) - Aembit's AI and MCP ecosystem documentation
* [Developer Guide](dev-guide/index.md) - Every path for integrating with Aembit. Choose how your workload gets credentials at runtime and how you manage configuration.
* [What is Aembit?](get-started/index.md) - An overview of Aembit, its core principles, and key capabilities
* [Glossary](glossary/index.md)
* [Aembit reference documentation](reference/index.md) - Reference documentation for Aembit features and functionality
* [Aembit User Guide Overview](user-guide/index.md) - How to set up and use Aembit
