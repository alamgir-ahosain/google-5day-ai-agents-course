## 1. Deployment Confidence Patterns 
Here are four proven patterns that help teams build confidence in their deployments:
* **Canary:** Release to 1% first; monitor and scale or roll back.
* **Blue-Green:** Two environments; switch traffic instantly for safe deploys.
* **A/B Testing:** Compare versions using real metrics.
* **Feature Flags:** Deploy code but enable features only for selected users.


## 2. Building Security from the Start 
Agents can be deployed safely, yet still cause harm if security isn’t built in from day one. Because agents reason, decide, and use tools autonomously, they introduce risks beyond traditional software:

* **Prompt Injection & Rogue Actions:** Attackers can manipulate agents into bypassing rules.
* **Data Leakage:** Agents may accidentally reveal sensitive information.
* **Memory Poisoning:** Incorrect or malicious data stored in memory can distort future behavior.

## 3. Secure Agent Framework

* **Policy & System Instructions (Constitution):** Define desired/forbidden behaviors and bake them into system instructions.
* **Enforcement (Guardrails):** Input filtering (block malicious prompts), output filtering (safety/PII checks), and human-in-the-loop for high-risk decisions.
* **Continuous Assurance:** Re-run evaluations on changes, dedicated RAI/neutrality tests, and proactive red-teaming.


## 4. Evolution Workflow

1. **Analyze:** Review production logs for behavior trends, success rates, and security issues.
2. **Update Tests:** Turn real-world failures into new evaluation cases; expand the golden dataset.
3. **Refine & Deploy:** Apply improvements—prompt fixes, new tools, stronger guardrails—and let the automated pipeline ship the updated agent.



## 5. Evolving Security: The Production Feedback Loop
Observe → Act → Evolve loop becomes critical for security.

1. **Observe:** Monitoring spots a new threat.
2. **Act:** Security team contains it.
3. **Evolve:**

   * **Add to Evaluation Set:** Add the attack to tests.
   * **Refine Guardrails:** Strengthen prompts/filters.
   * **Automate & Deploy:** Run CI/CD and deploy the fix.

---
# Part 1 :  Agent2Agent Communication  
## 1.1 Agent to Agent protocol   
The [Agent2Agent (A2A) Protocol](https://a2a-protocol.org/) is a **standard** that allows agents to:
-  **Communicate over networks** - Agents can be on different machines
-  **Use each other's capabilities** - One agent can call another agent like a tool
-  **Work across frameworks** - Language/framework agnostic
-  **Maintain formal contracts** - Agent cards describe capabilities


## 1.2 Common A2A Architecture Patterns

The A2A protocol is particularly useful in three scenarios:

![When to choose A2A?](https://storage.googleapis.com/github-repo/kaggle-5days-ai/day5/a2a_01.png)


1. **Cross-Framework Integration**: ADK agent communicating with other agent frameworks
2. **Cross-Language Communication**: Python agent calling Java or Node.js agent  
3. **Cross-Organization Boundaries**: Your internal agent integrating with external vendor services


## 1.3 A2A vs Local Sub-Agents: Decision Table

| Factor | Use A2A | Use Local Sub-Agents |
|--------|---------|---------------------|
| **Agent Location** | External service, different codebase | Same codebase, internal |
| **Ownership** | Different team/organization | Your team |
| **Network** | Agents on different machines | Same process/machine |
| **Performance** | Network latency acceptable | Need low latency |
| **Language/Framework** | Cross-language/framework needed | Same language |
| **Contract** | Formal API contract required | Internal interface |
| **Example** | External vendor product catalog | Internal order processing steps |


## 1.4 Expose the Product Catalog Agent via A2A 
use ADK's` to_a2a()` function to make our Product Catalog Agent accessible to other agents.What `to_a2a()` does:
* The `to_a2a()` function wraps an ADK agent in an A2A-compatible FastAPI server.
* It auto-generates an **agent card** containing the agent’s name, version, skills, protocol info, and endpoints.
* The agent card is served at `/.well-known/agent-card.json` following A2A standards.
* All A2A request/response formatting and task endpoints are handled automatically.

#### Agent Card
A JSON “business card” describing the agent’s purpose, capabilities, and communication endpoints for other agents.

##  1.5 RemoteA2aAgent 

>RemoteA2aAgent is a client-side proxy that lets one agent call another agent over the A2A protocol as if it were a local sub-agent.

##### How RemoteA2aAgent Works?
- It's a **client-side proxy** that reads the remote agent's card
- Translates sub-agent calls into A2A protocol requests (HTTP POST to `/tasks`)
- Handles all the protocol details so you just use it like a regular sub-agent


## 1.6 A2A Communication Flow



![](https://storage.googleapis.com/github-repo/kaggle-5days-ai/day5/a2a_03.png)




##  1.7 Summary - A2A Communication Patterns

### Key Takeaways


- **A2A Protocol**: Standardized protocol for agent-to-agent communication across networks and frameworks
- **Exposing Agents**: Use `to_a2a()` to make your agents accessible to others with auto-generated agent cards
- **Consuming Agents**: Use `RemoteA2aAgent` to integrate remote agents as if they were local sub-agents
- **Use Cases**: Best for microservices architectures, cross-team integrations, and third-party agent consumption

