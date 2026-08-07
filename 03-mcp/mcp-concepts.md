## Model Context Protocol
MCP is a standard standardize how AI model talks to external tools and data. It is like an USB-C for AI. Split LLM layer from service layer so that any host like Claude desktop, copilot CLI, Cursor, IDE agent could discover your capability.

| Layers   | Verb     | Definition                                     | cover LLM weakness          |
|----------|----------|------------------------------------------------|-----------------------------|
| Tool     | Do sth   | call a function/ask an expert to run some task | cannot act                  |
| Resource | Show sth | give the model a ref doc to read               | does not know your data     |
| Prompt   | Guide me | a pre-written recipe tell model how to work    | does not know your strategy |

### MCP Monitor
MCP monitor splits into 2 parts:
- MCP monitor, for every invocation, capture tool name, result status, latency, error type, infra cost.
- Model, capture the token cost

### How to handle sessions when MCP server scales horizontally
MCP does not store session in a single pod so that when session sent to on pod and another session to a different pod it still works. If shared session needed, store in a shared cache like redis instead of local memory. Take a example:

If the first request goes to podA and second goes to podB, B will not know anything if the memory only exists in podA's memory.

### How should a timeout returned and retried
Return a clean timeout error to the client, and only set retry if it is safe to operate. Give an exponential backoff with a small retry limit.

### How to prevent duplicate calls to a side-effecting tool?
For side-effecting tool, better design an idempotent key and stored the execution result in a shared storage. If same request came, return from the storage rather than run it again.

