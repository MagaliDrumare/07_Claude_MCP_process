# MCP Architecture Flow Diagrams

Based on the MCP Server Primitives, here are three key interaction flows:
https://claude.ai/public/artifacts/f072bfcf-9757-4edc-ac0c-f7bed5d37299

---

## 1. Tool Flow: When Claude Needs to Use a Tool

**Model-controlled**: Claude decides when to call these. Results are used by Claude.

```
┌──────┐          ┌────────────┐          ┌────────────┐          ┌─────────────┐
│ User │          │   Claude   │          │ MCP Client │          │ MCP Server  │
└──┬───┘          └─────┬──────┘          └─────┬──────┘          └──────┬──────┘
   │                    │                       │                        │
   │  1. User prompt    │                       │                        │
   │───────────────────>│                       │                        │
   │                    │                       │                        │
   │                    │ 2. Analyzes request   │                        │
   │                    │    & decides tool     │                        │
   │                    │    is needed          │                        │
   │                    │                       │                        │
   │                    │ 3. Request tool call  │                        │
   │                    │──────────────────────>│                        │
   │                    │                       │                        │
   │                    │                       │ 4. Execute tool        │
   │                    │                       │───────────────────────>│
   │                    │                       │                        │
   │                    │                       │                        │ 5. Perform action
   │                    │                       │                        │    (e.g., file ops,
   │                    │                       │                        │     API calls, etc.)
   │                    │                       │                        │
   │                    │                       │ 6. Return result       │
   │                    │                       │<───────────────────────│
   │                    │                       │                        │
   │                    │ 7. Tool result        │                        │
   │                    │<──────────────────────│                        │
   │                    │                       │                        │
   │                    │ 8. Process result     │                        │
   │                    │    & generate response│                        │
   │                    │                       │                        │
   │  9. Response       │                       │                        │
   │<───────────────────│                       │                        │
   │                    │                       │                        │
```

**Key Points:**
- **Claude**: Autonomously decides when a tool is needed based on the task
- **MCP Client**: Acts as the bridge between Claude and MCP Server
- **MCP Server**: Provides tools (giving additional functionality to Claude)
- **Examples**: File operations, web searches, database queries, calculations

---

## 2. Resources Flow: When the MCP Client Accesses Resources

**App-controlled**: Our app decides when to call these. Results are used primarily by our app.

```
┌──────┐          ┌────────────┐          ┌────────────┐          ┌─────────────┐
│ User │          │   Claude   │          │ MCP Client │          │ MCP Server  │
└──┬───┘          └─────┬──────┘          └─────┬──────┘          └──────┬──────┘
   │                    │                       │                        │
   │  1. User prompt    │                       │                        │
   │───────────────────>│                       │                        │
   │                    │                       │                        │
   │                    │                       │ 2. App logic determines│
   │                    │                       │    context needed      │
   │                    │                       │                        │
   │                    │                       │ 3. Request resources   │
   │                    │                       │───────────────────────>│
   │                    │                       │    (e.g., list files,  │
   │                    │                       │     get project info)  │
   │                    │                       │                        │
   │                    │                       │                        │ 4. Fetch resources
   │                    │                       │                        │    (file contents,
   │                    │                       │                        │     metadata, etc.)
   │                    │                       │                        │
   │                    │                       │ 5. Return resource data│
   │                    │                       │<───────────────────────│
   │                    │                       │                        │
   │                    │ 6. Add context to     │                        │
   │                    │    conversation        │                        │
   │                    │<──────────────────────│                        │
   │                    │    (enriched with     │                        │
   │                    │     resource data)    │                        │
   │                    │                       │                        │
   │                    │ 7. Process with       │                        │
   │                    │    full context       │                        │
   │                    │                       │                        │
   │  8. Response       │                       │                        │
   │<───────────────────│                       │                        │
   │                    │                       │                        │
```

**Key Points:**
- **MCP Client**: Proactively fetches resources to enrich context
- **MCP Server**: Provides access to data sources (getting data into our app)
- **Claude**: Receives enriched messages with resource context
- **Examples**: Project files, documentation, database schemas, API specs
- **Purpose**: Adding context to messages so Claude has better information

---

## 3. Prompts Flow: When the User Triggers a Prompt

**User-controlled**: The user decides when to use these.

```
┌──────┐          ┌──���─────────┐          ┌────────────┐          ┌─────────────┐
│ User │          │   Claude   │          │ MCP Client │          │ MCP Server  │
└──┬───┘          └─────┬──────┘          └─────┬──────┘          └──────┬──────┘
   │                    │                       │                        │
   │  1. User triggers  │                       │                        │
   │     prompt action  │                       │                        │
   │     (slash command,│                       │                        │
   │      button, menu) │                       │                        │
   │────────────────────┼──────────────────────>│                        │
   │                    │                       │                        │
   │                    │                       │ 2. Request prompt      │
   │                    │                       │    template            │
   │                    │                       │───────────────────────>│
   │                    │                       │                        │
   │                    │                       │                        │ 3. Retrieve prompt
   │                    │                       │                        │    template with
   │                    │                       │                        │    instructions
   │                    │                       │                        │
   │                    │                       │ 4. Return prompt       │
   │                    │                       │    template            │
   │                    │                       │<───────────────────────│
   │                    │                       │                        │
   │                    │ 5. Formatted prompt   │                        │
   │                    │    with user input    │                        │
   │                    │<──────────────────────│                        │
   │                    │                       │                        │
   │                    │ 6. Execute prompt     │                        │
   │                    │    instructions       │                        │
   │                    │                       │                        │
   │                    │ 7. May use tools      │                        │
   │                    │    or resources       │                        │
   │                    │    as needed          │                        │
   │                    │ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─>│
   │                    │                       │                        │
   │  8. Response       │                       │                        │
   │<───────────────────│                       │                        │
   │                    │                       │                        │
```

**Key Points:**
- **User**: Explicitly triggers the prompt (full control)
- **MCP Client**: Retrieves prompt template from server
- **MCP Server**: Stores pre-defined workflows/prompt templates
- **Claude**: Executes the prompt instructions
- **Examples**: Slash commands (`/review-code`), button clicks, menu options
- **Purpose**: Workflows to run based on user input

---

## Summary: Role of Each Component

### MCP Client
- **Bridge** between Claude and MCP Server
- **Manages** tool calls from Claude
- **Fetches** resources to enrich context
- **Retrieves** prompt templates on user request
- **Part of**: Your application (e.g., Claude Desktop, VS Code extension)

### MCP Server
- **Provides** three primitives: Tools, Resources, and Prompts
- **Exposes** functionality and data to clients
- **Executes** tool operations
- **Serves** resource data
- **Stores** prompt templates
- **Can be**: Built by you or third-party developers

### Claude (LLM)
- **Analyzes** user requests
- **Decides** when to use tools autonomously
- **Processes** enriched context from resources
- **Executes** prompt instructions
- **Generates** responses to users
- **Does NOT**: Directly communicate with MCP Server (always through client)

---

## Control Flow Summary

| Primitive   | Who Decides | Primary Beneficiary | Purpose                          |
|-------------|-------------|---------------------|----------------------------------|
| **Tools**   | Claude      | Claude              | Give Claude additional abilities |
| **Resources**| App/Client | App/Client         | Add context to conversations     |
| **Prompts** | User        | User                | Trigger pre-defined workflows    |
