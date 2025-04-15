# Dapr Agents Calculator Demo

This project demonstrates an issue with the Dapr Agents framework where the LLMOrchestrator service never stops and continuously shows an error message:

```
failed to invoke scheduled actor reminder named: run-activity due to: failed to invoke 'AddWorkflowEvent' method on workflow actor: error from worfklow actor: no such instance exists
```

## Prerequisites

- Python 3.10 or later
- Dapr CLI (v1.15.x)
- Redis (for state storage and pub/sub)
- Azure OpenAI API key

## Setup

1. Clone this repository

2. Create and activate a virtual environment:

```bash
# Create a virtual environment
python3.10 -m venv .venv

# Activate the virtual environment 
# On Windows:
.venv\Scripts\activate
# On macOS/Linux:
source .venv/bin/activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

4. Configure your Azure OpenAI credentials in the `.env` file:

```
AZURE_OPENAI_DEPLOYMENT=your-azure-deployment-name
AZURE_OPENAI_ENDPOINT=https://your-endpoint.openai.azure.com
AZURE_OPENAI_API_KEY=your-api-key
AZURE_OPENAI_API_VERSION=2025-01-01-preview
```

## Running the Application

Make sure Redis is running on your local machine (default port 6379).

### Running All Components with Dapr

1. Start the calculator agent:

```bash
dapr run --app-id CalculatorApp --app-port 8002 --resources-path ./components python calculator_agent.py
```

2. In a separate terminal, start the LLM orchestrator:

```bash
dapr run --app-id OrchestratorApp --app-port 8004 --resources-path ./components python llm_orchestrator.py
```

3. In a third terminal, run the client with Dapr:

```bash
dapr run --app-id ClientApp --resources-path ./components python client.py
```

### Alternative Sidecar Configuration

If you prefer to specify HTTP ports explicitly:

1. Start the calculator agent:

```bash
dapr run --app-id CalculatorApp --app-port 8002 --dapr-http-port 3500 --resources-path ./components -- python calculator_agent.py
```

2. Start the LLM orchestrator:

```bash
dapr run --app-id OrchestratorApp --app-port 8004 --dapr-http-port 3501 --resources-path ./components -- python llm_orchestrator.py
```

3. Run the client:

```bash
dapr run --app-id ClientApp --dapr-http-port 3502 --resources-path ./components -- python client.py
```

## Reproducing the Error

The error occurs after the orchestrator finishes processing the calculation ("What is 1 + 1?"). Even though the workflow completes successfully, the orchestrator continues running and repeatedly shows the error:

```
WARN: execution failed with a recoverable error and will be retried later: failed to invoke 'AddWorkflowEvent' method on workflow actor: error from worfklow actor: no such instance exists
ERRO: failed to invoke scheduled actor reminder named: run-activity due to: failed to invoke 'AddWorkflowEvent' method on workflow actor: error from worfklow actor: no such instance exists
```

### Expected Behavior

The orchestrator should cleanly complete the workflow without continuous retry attempts after the workflow has ended.

### Actual Behavior

The orchestrator keeps trying to execute an activity for a workflow instance that no longer exists.

## Troubleshooting

If you can't see the error right away, check that:

1. All three services (calculator agent, orchestrator, and client) are running
2. The client successfully sent the message (you should see "✅ Successfully published request" in the client terminal)
3. Watch the logs in the orchestrator terminal for the error message 