# Dapr Agents Calculator Demo

This project demonstrates an issue with the Dapr Agents framework where the LLMOrchestrator service never stops and continuously shows an error message:
 
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

4. Configure your  OpenAI credentials in the `.env` file:

```
OPENAI_API_KEY=${OPENAI_API_KEY}

```

## Running the Application

Make sure Redis is running on your local machine (default port 6379).

### Running All Components with Dapr

1. Start the calculator agent:
  

1. Start the calculator agent:

```bash
dapr run --app-id CalculatorApp --app-port 8002 --resources-path ./components python calculator_agent.py
```

2. Start the LLM orchestrator:

```bash
dapr run --app-id OrchestratorApp --app-port 8004 --resources-path ./components python llm_orchestrator.py
```

3. Run the client:

```bash
dapr run --app-id ClientApp --dapr-http-port 3502 --resources-path ./components -- python client.py

```

## Reproducing the Error

The error occurs after the orchestrator finishes processing the calculation ("What is 1 + 1?"). Even though the workflow completes successfully, the orchestrator continues running and repeatedly shows the error:

```




== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:07:26.004 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating task history for AdditionExpert at step 2, substep None (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1)
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:07:26.019 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.utils.request:Structured Mode Activated! Mode=json.
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == INFO:dapr_agents.llm.utils.response:Structured output was successfully validated.
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Tracking Progress: {'verdict': 'continue', 'plan_needs_update': False, 'plan_status_update': [{'step': 2, 'substep': None, 'status': 'blocked'}], 'plan_restructure': None}
== APP == 2025-04-15 16:07:27.000 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating plan for instance 0f5590c94f6b4ceb844d0943b4d2c9d1
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updated status of step 2, substep None to 'blocked'
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Plan successfully updated for instance 0f5590c94f6b4ceb844d0943b4d2c9d1
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Workflow iteration 3 started (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:07:27.011 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == 2025-04-15 16:07:27.016 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.utils.request:Structured Mode Activated! Mode=json.
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == INFO:dapr_agents.llm.utils.response:Structured output was successfully validated.
== APP == 2025-04-15 16:07:28.971 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == 2025-04-15 16:07:28.977 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == WARNING:dapr_agents.workflow.agentic:No agents available for broadcast.
== APP == 2025-04-15 16:07:28.986 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Triggering agent BasicMathAgent for step 2, substep None (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1)
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Marked step 2, substep None as 'in_progress'
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == WARNING:dapr_agents.workflow.agentic:Target 'BasicMathAgent' is not registered as an agent. Skipping message send.
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Waiting for BasicMathAgent's response...
== APP == 2025-04-15 16:07:28.996 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 6e79db6cf9dc4aa18125ce9421c73364).
== APP == 2025-04-15 16:08:26.011 durabletask-worker INFO: 6e79db6cf9dc4aa18125ce9421c73364: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating task history for ArithmeticProcessor at step 2, substep 2.2 (Instance ID: 6e79db6cf9dc4aa18125ce9421c73364)
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 6e79db6cf9dc4aa18125ce9421c73364).
== APP == 2025-04-15 16:08:26.047 durabletask-worker INFO: 6e79db6cf9dc4aa18125ce9421c73364: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.utils.request:Structured Mode Activated! Mode=json.
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == INFO:dapr_agents.llm.utils.response:Structured output was successfully validated.
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 6e79db6cf9dc4aa18125ce9421c73364).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Tracking Progress: {'verdict': 'completed', 'plan_needs_update': False, 'plan_status_update': [{'step': 2, 'substep': 2.2, 'status': 'completed'}, {'step': 2, 'substep': None, 'status': 'completed'}, {'step': 3, 'substep': None, 'status': 'completed'}, {'step': 4, 'substep': 4.1, 'status': 'completed'}, {'step': 4, 'substep': 4.2, 'status': 'completed'}, {'step': 4, 'substep': None, 'status': 'completed'}, {'step': 5, 'substep': None, 'status': 'completed'}], 'plan_restructure': None}
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Workflow ending with verdict: completed
== APP == 2025-04-15 16:08:28.708 durabletask-worker INFO: 6e79db6cf9dc4aa18125ce9421c73364: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 6e79db6cf9dc4aa18125ce9421c73364).
== APP == 2025-04-15 16:08:33.768 durabletask-worker INFO: 6e79db6cf9dc4aa18125ce9421c73364: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating plan for instance 6e79db6cf9dc4aa18125ce9421c73364
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updated status of step 2, substep 2.2 to 'completed'
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updated status of step 2, substep None to 'completed'
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Plan successfully updated for instance 6e79db6cf9dc4aa18125ce9421c73364
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 2, Instance ID: 6e79db6cf9dc4aa18125ce9421c73364).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Workflow 6e79db6cf9dc4aa18125ce9421c73364 has been finalized with verdict: completed
== APP == 2025-04-15 16:08:33.781 durabletask-worker INFO: 6e79db6cf9dc4aa18125ce9421c73364: Orchestration completed with status: COMPLETED
INFO[0826] 6e79db6cf9dc4aa18125ce9421c73364: 'LLMWorkflow' completed with a COMPLETED status.  app_id=OrchestratorApp instance=b1.lan scope=dapr.wfengine.durabletask.backend type=log ver=1.15.3
INFO[0826] Workflow Actor '6e79db6cf9dc4aa18125ce9421c73364': workflow completed with status 'ORCHESTRATION_STATUS_COMPLETED' workflowName 'LLMWorkflow'  app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.targets.workflow type=log ver=1.15.3
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 3, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:12:28.033 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating task history for BasicMathAgent at step 2, substep None (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1)
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 3, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:12:28.066 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.utils.request:Structured Mode Activated! Mode=json.
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == INFO:dapr_agents.llm.utils.response:Structured output was successfully validated.
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 3, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Tracking Progress: {'verdict': 'continue', 'plan_needs_update': False, 'plan_status_update': [{'step': 2, 'substep': None, 'status': 'completed'}, {'step': 3, 'substep': None, 'status': 'not_started'}], 'plan_restructure': None}
== APP == 2025-04-15 16:12:29.731 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updating plan for instance 0f5590c94f6b4ceb844d0943b4d2c9d1
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updated status of step 2, substep None to 'completed'
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Updated status of step 3, substep None to 'not_started'
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Plan successfully updated for instance 0f5590c94f6b4ceb844d0943b4d2c9d1
== APP == WARNING:dapr_agents.workflow.orchestrators.llm.orchestrator:Agent response timed out (Iteration: 3, Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Workflow iteration 4 started (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1).
== APP == 2025-04-15 16:12:29.767 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == 2025-04-15 16:12:29.792 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Task with LLM...
== APP == INFO:dapr_agents.workflow.task:Retrieving conversation history...
== APP == INFO:dapr_agents.llm.utils.request:Structured Mode Activated! Mode=json.
== APP == INFO:dapr_agents.llm.openai.chat:Invoking ChatCompletion API.
== APP == INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
== APP == INFO:dapr_agents.llm.openai.chat:Chat completion retrieved successfully.
== APP == INFO:dapr_agents.llm.utils.response:Structured output was successfully validated.
== APP == 2025-04-15 16:12:31.437 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == 2025-04-15 16:12:31.458 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == WARNING:dapr_agents.workflow.agentic:No agents available for broadcast.
== APP == 2025-04-15 16:12:31.473 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 0 event(s) outstanding.
== APP == INFO:dapr_agents.workflow.task:Invoking Regular Task
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Triggering agent BasicMathAgent for step 4, substep 4.1 (Instance ID: 0f5590c94f6b4ceb844d0943b4d2c9d1)
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Marked step 4, substep 4.1 as 'in_progress'
== APP == INFO:dapr_agents.workflow.agentic:Agents found in 'agentstatestore' for key 'agents_registry'.
== APP == INFO:dapr_agents.workflow.agentic:No other agents found after filtering.
== APP == WARNING:dapr_agents.workflow.agentic:Target 'BasicMathAgent' is not registered as an agent. Skipping message send.
== APP == INFO:dapr_agents.workflow.orchestrators.llm.orchestrator:Waiting for BasicMathAgent's response...
== APP == 2025-04-15 16:12:31.490 durabletask-worker INFO: 0f5590c94f6b4ceb844d0943b4d2c9d1: Orchestrator yielded with 1 task(s) and 1 event(s) outstanding.
INFO[1239] Placement tables updated, version: 59         app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.placement type=log ver=1.15.3
INFO[1239] Running actor reminder migration from state store to scheduler  app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.reminders.migration type=log ver=1.15.3
INFO[1239] Skipping migration, no missing scheduler reminders found  app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.reminders.migration type=log ver=1.15.3
INFO[1239] Found 0 missing scheduler reminders from state store  app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.reminders.migration type=log ver=1.15.3
INFO[1239] Migrated 0 reminders from state store to scheduler successfully  app_id=OrchestratorApp instance=b1.lan scope=dapr.runtime.actors.reminders.migration type=log ver=1.15.3

```

### Expected Behavior

The orchestrator should cleanly complete the workflow without continuous retry attempts after the workflow has ended.
 