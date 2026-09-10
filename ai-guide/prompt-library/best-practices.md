---
type: explanation
title: "Prompt engineering best practices"
description: "Ten essential techniques for writing effective prompts that get better results from AI models."
resource: https://docs.aembit.io/ai-guide/prompt-library/best-practices/
tags: ["prompt-library"]
timestamp: 2026-02-24T15:57:13-08:00
---

# Prompt engineering best practices

These ten best practices help you write more effective prompts for AI models, including when using the [Aembit MCP Server](../mcp/mcp-server/overview.md).

> **Note**
>
> Examples in this guide use placeholder names like `payment-service` and `database-prod` for illustration. When adapting these prompts, use sanitized or generic names appropriate for your environment.

For the highest impact on basic prompts, start with [Section 1](#1-be-clear-and-specific) (clarity) and [Section 2](#2-provide-context) (context). Add [Section 7](#7-specify-output-format) (format) and [Section 8](#8-use-delimiters-and-structure) (delimiters) for structured output. Sections 3-6 and 9-10 refine results for complex analysis tasks.

## 1. Be clear and specific

Models process tokens sequentially, so ambiguous requests force them to guess your intent rather than follow it.

The most common prompt failures come from ambiguity, not model limitations. Clear structure and context matter more than clever wording.

**Instead of vague requests:**

```text
Tell me about authentication events.
```

**Be specific about what you need:**

```text
List the 5 most recent failed authentication events, including the timestamp,
source IP, and reason for failure.
```

## 2. Provide context

Background information narrows the model’s interpretation space, producing more relevant and targeted responses.

Give background information to help the model understand your goals and deliver targeted responses. Explain why you need the information and how you’ll use it.

```text
I'm investigating a potential security incident that occurred around 2pm EST.
Show me all authorization denials from 1pm to 3pm EST today, grouped by
client workload.
```

## 3. Use examples (few-shot prompting)

Examples anchor the model’s output format and style more reliably than instructions alone.

Show the model what you want by providing examples of ideal inputs and outputs. This is especially effective for establishing consistent formatting.

```text
Format authorization events like this example:


[2024-01-15 14:32:01] DENIED - Client: api-gateway → Server: database-prod
  Reason: Trust provider validation failed
  Policy: prod-db-access


Now show me the last 10 authorization denials in this format.
```

## 4. Break complex tasks into steps

Smaller sub-tasks reduce the chance of compounding errors in multi-step analysis.

For multi-step analysis, chain your prompts rather than asking for everything at once. Each prompt builds on the previous response.

**Step 1:**

```text
List all unique client workloads that had authorization failures in the last 24 hours.
```

**Step 2:**

```text
For the client workload "payment-service", show the timeline of all events
(successes and failures) from the last 24 hours.
```

**Step 3:**

```text
Based on this timeline, identify any patterns in when failures occur.
```

## 5. Encourage step-by-step reasoning

Explicit reasoning steps force the model to show its work, making errors visible and correctable.

For complex analysis, ask the model to think through the problem systematically. This improves accuracy for troubleshooting and multi-step reasoning.

```text
Analyze the authorization events from the last hour and identify any anomalies.
Think through this step by step:
1. First, establish what normal activity looks like
2. Then identify any deviations from normal patterns
3. Finally, assess the severity of each anomaly
```

## 6. Assign a role or persona

A persona constrains vocabulary, depth, and analytical lens to match your intended audience.

Setting a persona helps the model adopt the appropriate tone, vocabulary, and depth of analysis.

```text
As a security analyst reviewing audit logs for compliance, summarize all
administrative actions taken in the last 7 days. Flag any actions that
would require additional documentation for SOC 2 compliance.
```

## 7. Specify output format

Specifying structure prevents the model from choosing a format that doesn’t match your workflow.

Define the structure, length, and format you need. Be explicit about lists, tables, JSON, or prose.

```text
Provide a summary of workload events from the last 24 hours in this format:


## Summary
- Total events: [number]
- By severity: [breakdown]


## Critical Events
[Bulleted list of critical events with timestamps]


## Recommendations
[2-3 actionable recommendations]
```

## 8. Use delimiters and structure

Clear boundaries help the model distinguish between instructions, context, and data in your prompt.

Separate different parts of your prompt with clear delimiters. This helps the model understand which parts are instructions, context, and data.

```text
CONTEXT:
We're troubleshooting intermittent connection failures between our
payment-service and the external payment gateway.


TASK:
Search for authorization events involving payment-service from the last
6 hours.


OUTPUT REQUIREMENTS:
- Include timestamp, event type, and any error messages
- Group by success/failure
- Highlight any patterns in failure timing
```

## 9. Iterate and refine

Prompt engineering is empirical—small wording changes can markedly shift model behavior.

Start with a basic prompt and refine based on results. Small wording changes can improve outputs by a large margin.

**First attempt:**

```text
Show authentication events.
```

**Refined after reviewing output:**

```text
Show authentication events from the last 2 hours where the outcome was
"failure", sorted by timestamp descending. Include the authentication
method used and any error codes.
```

## 10. Provide output primers

Starting the response constrains the model’s first tokens, which strongly influence the rest of the output.

Start the response format for the model to guide how it completes the rest. This reduces randomness and improves consistency.

```text
Analyze authorization patterns for the last 24 hours. Begin your response with:


"Authorization Activity Summary (Last 24 Hours)


Total Requests: "
```

## Security considerations

Keep these practices in mind when writing prompts for your organization:

* **Never include credentials or secrets** - Don’t embed API keys, passwords, or tokens in prompts. If your prompt needs to reference an authenticated service, use the service name without credentials.
* **Sanitize real data** - Replace real service names, hostnames, and internal identifiers with generic placeholders before sharing prompts with others or storing them in shared repositories.
* **Use delimiters for untrusted input** - When building prompts that include user-provided content, use clear delimiters ([Section 8](#8-use-delimiters-and-structure)) to separate system instructions from external input.
* **Consider prompt visibility** - Other team members may log, store, or view your prompts. Avoid embedding sensitive operational details that shouldn’t appear outside your security tooling.

## Combining techniques

For best results, combine multiple techniques. Here’s an example that uses context, role assignment, specific formatting, and step-by-step reasoning:

```text
ROLE: You are a security operations analyst preparing a daily briefing.


CONTEXT:
The security team needs a summary of yesterday's Aembit activity for
the morning standup meeting.


TASK:
Create a briefing document covering:
1. Overall authorization health (success/failure rates)
2. Any anomalies or concerns
3. Recommended follow-up actions


FORMAT:
Use bullet points. Keep the total summary under 200 words.
Prioritize actionable insights over raw statistics.


Begin with: "Daily Security Briefing - Authorization Activity"
```

## Sources

These best practices are from industry-leading resources:

* [Anthropic Claude Prompt Engineering Guide](https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/overview)
* [Prompt Engineering Guide](https://www.promptingguide.ai/)
* [IBM Prompt Engineering Guide](https://www.ibm.com/think/prompt-engineering)
* [Lakera Prompt Engineering Guide](https://www.lakera.ai/blog/prompt-engineering-guide)
* [Palantir LLM Best Practices](https://www.palantir.com/docs/foundry/aip/best-practices-prompt-engineering)
* [SuperAnnotate 26 Prompting Tricks](https://www.superannotate.com/blog/llm-prompting-tricks)
* [Codesmith Mastering LLM Prompts](https://www.codesmith.io/blog/mastering-llm-prompts)
* [CodeSignal Prompt Engineering Best Practices](https://codesignal.com/blog/prompt-engineering-best-practices-2025/)
