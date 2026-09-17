# Aembit Documentation — OKF Bundle

The Aembit product documentation, packaged for AI agents.

[Aembit](https://aembit.io) is a workload identity and access management platform: it issues credentials to workloads, AI agents, and MCP clients under access policies instead of leaving secrets in code and config. The docs at <https://docs.aembit.io> explain how to set that up. This repository holds the same documentation as an [Open Knowledge Format](https://openknowledgeformat.org) (OKF v0.1) bundle: a tree of small Markdown files with frontmatter and folder indexes that an agent clones once and navigates with file tools, reading only the files each question needs.

## Who this is for

- **AI coding agents and assistants with file tools** (Claude Code, Cursor, Copilot agents) that are configuring, integrating with, or troubleshooting Aembit.
- **Developers who run those agents** and want them working from Aembit's documentation instead of guessing or searching the web.

It is not meant to be read in a browser. For the human-readable documentation, go to <https://docs.aembit.io>. For a one-shot context paste instead of a file tree, use the `llms.txt` files listed at <https://docs.aembit.io/llm-resources/>.

## How to use it

**1. Get the bundle** into your project or workspace:

```bash
git clone https://github.com/Aembit/aembit_docs_okf aembit-docs
# no git? download and extract the tarball instead:
# curl -fsSL "https://docs.aembit.io/okf.tar.gz" | tar -xz
```

**2. Tell your agent it exists.** Add a note to your project's `CLAUDE.md`, `AGENTS.md`, or equivalent that says to consult `aembit-docs/` for Aembit questions and to read `aembit-docs/index.md` first. A ready-to-paste snippet is at <https://docs.aembit.io/llm-resources/>.

**3. Let the agent navigate.** The bundle is built for this pattern:

1. Read `index.md` at the root. It explains the layout and maps the guides.
2. Open a folder's `index.md` before the files in it, and filter by each file's `type` (`how-to`, `reference`, `explanation`, `tutorial`, `troubleshooting`, `definition`), `interface` (`web-ui`, `cli`, `api`, `mcp`, `sdk`), and `tags` frontmatter.
3. Follow the bundle-relative links between files, including each file's `## Related` section.

A typical question resolves in the root index, one folder index, and two or three files.

## What is in it

| Path | Contents |
|---|---|
| `index.md` | Navigation guide and guide map. Start here. |
| `get-started/` | What Aembit is, core concepts, quickstarts, tutorials, and use cases. |
| `user-guide/` | Configuring Aembit in the web UI: access policies, workloads, trust and credential providers, blended identities, Edge deployment, the MCP Identity Gateway and Authorization Server, the MCP Server and its prompt library, administration, audit, discovery, and troubleshooting. |
| `dev-guide/` | Integrating with Aembit from code: the CLI, the Cloud and Edge APIs, the SDKs, and integration patterns. |
| `reference/` | The identifier reference, the support matrix, and Edge Component compatibility. |
| `glossary/` | One file per term. |
| `log.md` | The documentation changelog. |

## Keeping it current

This repository is a generated artifact. Every documentation release rebuilds the bundle from the source docs and force-syncs it here, so **manual edits are overwritten on the next release**. Do not open pull requests against this repository. To report a problem with the documentation itself, use the support channels listed at <https://docs.aembit.io/support-overview/>.

## Provenance

- **Source:** `aembit/aembit_docs_astro` @ [`4f418d36a7688a9af19bb0066d2743463d6d3ca4`](https://github.com/aembit/aembit_docs_astro/commit/4f418d36a7688a9af19bb0066d2743463d6d3ca4)
- **Synced:** 2026-09-16
