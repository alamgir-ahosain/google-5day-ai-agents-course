<h1 id="top">Day 2 : Agent Tools & Interoperability with Model Context Protocol (MCP)</h1>

---

## 1. Why Do Agents Need Tools?

* **Without tools:** Agents are stuck with old knowledge — they can’t access real-time data or act in the real world.
* **With tools:** Agents can fetch live info, connect to systems, and take actions — turning an LLM into a truly capable AI assistant.


## 2. What Are Custom Tools?

*  Custom Tools are built with custom code and business logic.
*  Unlike built-in ADK tools, they offer full flexibility and control.
*  Used when:
    * **Custom business logic** is required.
    * **Internal system connections** are needed.
    * **Domain-specific problems** must be solved.
* ADK provides multiple custom tool types to handle these scenarios.

## 3.  Building Custom Function Tools

#### Example: Currency Converter Agent

This agent can convert currency from one denomination to another and calculates the fees to do the conversion. The agent has two custom tools and follows the workflow:

1. **Fee Lookup Tool** - Finds transaction fees for the conversion (mock)
2. **Exchange Rate Tool** - Gets currency conversion rates (mock)
3. **Calculation Step** - Calculates the total conversion cost including the fees

<img src="https://storage.googleapis.com/github-repo/kaggle-5days-ai/day2/currency-agent.png" width="600" alt="Currency Converter Agent">


## 4. How to define a Tool?

**Any Python function can become an agent tool** by following these simple guidelines:

1. Create a Python function
2. Follow the best practices listed below
3. Add your function to the agent's `tools=[]` list and ADK handles the rest automatically.

## 5. Agent Tools vs Sub-Agents: What's the Difference?

**Agent Tools (what we're using):**
- Agent A calls Agent B as a tool
- Agent B's response goes **back to Agent A**
- Agent A stays in control and continues the conversation
- **Use case**: Delegation for specific tasks (like calculations)

**Sub-Agents (different pattern):**
- Agent A transfers control **completely to Agent B**
- Agent B takes over and handles all future user input
- Agent A is out of the loop
- **Use case**: Handoff to specialists (like customer support tiers)



<br>

[↑ Back to top](#top)

# 6. ADK Tool Types
1. Custom Tools
2. Built in Tools

## 6.1 Custom  Tools

>Tools built with custom code for specific needs — giving full control and flexibility.

<img src="https://storage.googleapis.com/github-repo/kaggle-5days-ai/day2/custom-tools.png" width="800" alt="Custom Tools">



| **Tool Type**          | **What**                                    | **Advantage**                                  | **Example**                          |
| ---------------------- | ------------------------------------------- | ---------------------------------------------- | ------------------------------------ |
| **Function Tools**     | Python functions converted into agent tools | Instantly turn any Python function into a tool | `get_exchange_rate`                  |
| **Long Running Tools** | Handle time-consuming tasks               | Agent continues working while task runs        | File processing, approvals           |
| **Agent Tools**        | Use one agent as a tool for another                   | Reuse specialist agents across systems         | `AgentTool(agent=calculation_agent)` |
| **MCP Tools**          | From Model Context Protocol servers         | Connect to MCP services easily                 | Google Maps, Databases               |
| **OpenAPI Tools**      | Auto-generated from API specs               | No coding — just provide API spec              | REST API endpoints                   |







## 6.2 Built in tools 

>Ready-to-use tools provided by ADK — no setup required.

<img src="https://storage.googleapis.com/github-repo/kaggle-5days-ai/day2/built-in-tools.png" width="1200" alt="Built-in Tools">

| **Tool Type**          | **What**                                                 | **Advantage**                                  | **Example**                                          |
| ---------------------- | -------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------- |
| **Gemini Tools**       | Tools leveraging Gemini’s capabilities                   | Reliable, tested, ready to use                 | `google_search`, `BuiltInCodeExecutor`               |
| **Google Cloud Tools** | Tools for Google Cloud services & enterprise integration | Secure, enterprise-grade database & API access | `BigQueryToolset`, `SpannerToolset`, `APIHubToolset` |
| **Third-party Tools**  | Wrappers for existing tool ecosystems                    | Reuse existing tools without rebuilding        | Hugging Face, Firecrawl, GitHub Tools                |

<br>

[↑ Back to top](#top)
---

# Part - 2 : Agent Tool Patterns and Best Practices

## 1. Model Context Protocol (MCP)

* **What is MCP?**
  An open standard that lets agents use community-built integrations without writing custom API clients.

* **Benefits:**

  *  Access live external data from databases, APIs, and services.
  *  Use community-built tools with standardized interfaces.
  *  Scale capabilities by connecting to multiple specialized MCP servers.

## 2. How MCP Works

MCP connects  agent  to external **MCP servers** that provide tools:

- **MCP Server**: Provides specific tools (like image generation, database access)
- **MCP Client**: agent  uses those tools
- **All servers work the same way** - standardized interface

**Architecture:**
```
┌──────────────────┐
│   Your Agent     │
│   (MCP Client)   │
└────────┬─────────┘
         │
         │ Standard MCP Protocol
         │
    ┌────┴────┬────────┬────────┐
    │         │        │        │
    ▼         ▼        ▼        ▼
┌────────┐ ┌─────┐ ┌──────┐ ┌─────┐
│ GitHub │ │Slack│ │ Maps │ │ ... │
│ Server │ │ MCP │ │ MCP  │ │     │
└────────┘ └─────┘ └──────┘ └─────┘
```
## 3. Using MCP with  Agent

The workflow is simple:

1. Choose an MCP Server and tool
2. Create the MCP Toolset (configure connection)
3. Add it to the agent
4. Run and test the agent

## 4. Long-Running Operations (Human-in-the-Loop)

* **What are LROs?**
  Tools that **pause** for external input or human approval before completing an action.

* **Flow:** 
> ```User asks → Agent calls tool → Tool PAUSES and asks human → Human approves → Tool completes → Agent responds```


* **Use case:** Shipping agent waiting for approval before placing a large order.


## 4.1 When to Use Long-Running Operations

*  **Financial transactions** requiring approval (e.g., transfers, purchases)
*  **Bulk operations** (e.g., deleting 1000 records)
*  **Compliance checkpoints** (e.g., regulatory approval)
*  **High-cost actions** (e.g., spinning up 50 servers)
*  **Irreversible operations** (e.g., permanently deleting an account)


## 5. Summary - Key Patterns for Advanced Tools


| Pattern | When to Use It | Key ADK Components |
| :--- | :--- | :--- |
| **MCP Integration** | You need to connect to **external, standardized services** (like time, databases, or file systems) without writing custom integration code. | `McpToolset` |
| **Long-Running Operations** | You need to **pause a workflow** to wait for an external event, most commonly for **human-in-the-loop** approvals or long background tasks or for compliance/security checkpoints. | `ToolContext`, `request_confirmation`, `App`, `ResumabilityConfig` |

## 6. Production Ready Concepts

-  **Scale**: Leverage community tools instead of building everything
-  **Handle Time**: Manage operations that span minutes, hours, or days  
-  **Ensure Compliance**: Add human oversight to critical operations
-  **Maintain State**: Resume conversations exactly where they paused

<br>

[↑ Back to top](#top)