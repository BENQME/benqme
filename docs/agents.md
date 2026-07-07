# Agents

> Guidelines for designing, configuring, and running AI agents in this project.

---

## Table of Contents

1. [What Are Agents](#what-are-agents)
2. [Agent Types](#agent-types)
3. [Agent Design Principles](#agent-design-principles)
4. [Tool Definitions](#tool-definitions)
5. [Prompt Engineering for Agents](#prompt-engineering-for-agents)
6. [Memory & State](#memory--state)
7. [Error Handling](#error-handling)
8. [Evaluation](#evaluation)
9. [Security Considerations](#security-considerations)
10. [Example Agent Configurations](#example-agent-configurations)

---

## What Are Agents

An **agent** in this project is an AI-powered system that:
- Receives a goal or instruction
- Plans a sequence of steps
- Uses tools (functions, APIs, file I/O) to accomplish the goal
- Returns a result or takes an action

Agents differ from simple LLM calls in that they can iterate, branch, and use tools autonomously.

---

## Agent Types

### 1. Task Agents
Run a specific bounded task (e.g., summarize a document, generate a component). Single-turn or short-lived.

### 2. Workflow Agents
Orchestrate multi-step workflows (e.g., code review → test generation → PR creation). May run multiple sub-agents.

### 3. Assistant Agents
Long-running, conversational agents with persistent context. Used in chat interfaces.

### 4. Monitoring Agents
Scheduled agents that watch for changes, anomalies, or events and trigger actions.

---

## Agent Design Principles

1. **Single Responsibility** — each agent has one clear purpose
2. **Deterministic Tooling** — prefer tools with predictable, idempotent behavior
3. **Minimal Permissions** — agents only get access to what they need
4. **Transparent Reasoning** — capture reasoning steps for debugging
5. **Graceful Degradation** — agents should fail safely and communicate errors clearly
6. **Human in the Loop** — high-stakes actions require human confirmation

---

## Tool Definitions

Tools are functions the agent can call. Define them with clear schemas:

```typescript
const tools = [
  {
    name: "read_file",
    description: "Read the contents of a file at the given path.",
    parameters: {
      type: "object",
      properties: {
        path: {
          type: "string",
          description: "Relative path to the file from project root.",
        },
      },
      required: ["path"],
    },
  },
  {
    name: "write_file",
    description: "Write content to a file, creating it if it doesn't exist.",
    parameters: {
      type: "object",
      properties: {
        path: { type: "string" },
        content: { type: "string" },
      },
      required: ["path", "content"],
    },
  },
  {
    name: "run_command",
    description: "Run a shell command and return stdout/stderr.",
    parameters: {
      type: "object",
      properties: {
        command: { type: "string" },
      },
      required: ["command"],
    },
  },
];
```

---

## Prompt Engineering for Agents

### System Prompt Template

```
You are a [role] agent for the [project name] project.

## Context
[Relevant project context, conventions, and constraints]

## Capabilities
You have access to the following tools:
[List tools with short descriptions]

## Rules
- Always verify before destructive actions
- Prefer minimal changes
- Report your reasoning at each step
- If uncertain, ask for clarification instead of guessing

## Output Format
[Specify expected output format]
```

### Task Prompt Template

```
Task: [Clear, specific task description]

Constraints:
- [Constraint 1]
- [Constraint 2]

Success criteria:
- [Criterion 1]
- [Criterion 2]

Start by outlining your plan, then execute it step by step.
```

---

## Memory & State

### Short-term Memory
- In-context: messages within the current conversation window
- Suitable for: single session tasks

### Long-term Memory
- External vector store (e.g., Pinecone, Weaviate, pgvector)
- Suitable for: knowledge bases, user preferences, project history

### Working Memory
- Structured scratch pad (JSON object) injected into the system prompt
- Suitable for: tracking task state, partial results, visited items

```typescript
interface AgentWorkingMemory {
  currentTask: string;
  completedSteps: string[];
  pendingSteps: string[];
  artifacts: Record<string, unknown>;
  errors: string[];
}
```

---

## Error Handling

```typescript
try {
  const result = await agent.run(task);
} catch (error) {
  if (error instanceof ToolCallError) {
    // Retry with exponential backoff
  } else if (error instanceof ContextLengthError) {
    // Summarize context and retry
  } else if (error instanceof RateLimitError) {
    // Queue and retry after delay
  } else {
    // Escalate to human
    await notifyHuman(error);
  }
}
```

### Retry Strategy
- Max retries: 3
- Backoff: exponential (1s, 2s, 4s)
- Jitter: ±200ms
- Dead letter queue for persistent failures

---

## Evaluation

### Metrics
| Metric | Description |
|--------|-------------|
| Task success rate | % of tasks completed correctly |
| Tool call accuracy | % of tool calls with valid arguments |
| Hallucination rate | % of fabricated facts/code |
| Cost per task | Average token cost per completion |
| Latency P95 | 95th percentile task duration |

### Evaluation Set
Maintain a set of golden examples with known correct outputs. Run evals before and after prompt or model changes.

---

## Security Considerations

- **Prompt injection**: Sanitize user-provided content before including in agent prompts
- **Tool sandboxing**: Run code execution tools in isolated environments (Docker/WASM)
- **Least privilege**: Scope API keys and file access to minimum required
- **Output validation**: Validate and sanitize agent outputs before using in UI
- **Audit logging**: Log all tool calls with timestamps and user context
- **PII handling**: Never include personal data in prompts without consent

---

## Example Agent Configurations

### Code Review Agent
```typescript
const codeReviewAgent = {
  role: "Senior code reviewer",
  tools: ["read_file", "search_codebase", "post_comment"],
  maxIterations: 10,
  temperature: 0.2,
  systemPrompt: `Review the provided code changes for correctness, 
    security, performance, and adherence to project conventions.`,
};
```

### Documentation Agent
```typescript
const docsAgent = {
  role: "Technical writer",
  tools: ["read_file", "write_file", "search_codebase"],
  maxIterations: 20,
  temperature: 0.4,
  systemPrompt: `Generate or update documentation for the provided 
    code. Follow the style in docs/instructions.md.`,
};
```
