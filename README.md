# Real Time Intelligence for Agents on Databricks with You.com

Retrieval Augmented Generation (RAG) systems operate by retrieving content related to an input query, incorporating the retrieved content into a prompt, then using an LLM generate a final response relative to the user's query and retrieved content. Having high-quality content retrieval is a critical piece toward improving the overall quality of any RAG system.


[You.com](https://you.com)’s developer-friendly APIs deliver real-time, low-latency web and news data, enabling developers to build applications that seamlessly integrate real-time public data from across the entire web at enterprise scale. Whether you’re enhancing large language models (LLMs) or building custom agents, anyone can have access to the entire web with just a few lines of code. 


In this solution accelerator, we show how Databricks and You.com makes it possible to:

* **Access real-time knowledge** from the web and news

* **Empower AI agents** with fresh context for better responses

* **Evaluate agent performances** using Databricks-native MLflow tools.

Reference Blog:
[Unlocking Real-Time Intelligence for AI Agents with You.com and Databricks Blog](https://you.com/articles/unlocking-real-time-intelligence-for-ai-agents-with-you.com-and-databricks)

# Architecture Diagram

![Architecture Diagram](./assets/workflow.png)

# Getting Started

## Prerequisites

Before running this demo, you'll need:
- A Databricks workspace with Unity Catalog enabled
- Databricks CLI installed and configured (`databricks --version` >= 0.200.0)
- A You.com API key ([Sign up here](https://you.com/api))
- Access to a Databricks model serving endpoint (default: `databricks-claude-3-7-sonnet`)
- Permissions to create UC functions, secrets, jobs, and MLflow experiments

## Deployment with Databricks Asset Bundles (Recommended)

This project uses **Databricks Asset Bundles (DAB)** for streamlined deployment and lifecycle management.

### Quick Setup

1. **Clone this repository**
   ```bash
   git clone https://github.com/databricks-solutions/realtime-rag-agents-databricks-youcom.git
   cd realtime-rag-agents-databricks-youcom
   ```

2. **Install/upgrade Databricks CLI**
   ```bash
   pip install --upgrade databricks-cli
   databricks --version  # Should be >= 0.200.0
   ```

3. **Authenticate with your Databricks workspace**
   ```bash
   databricks auth login --host https://your-workspace.cloud.databricks.com
   ```

4. **Store your You.com API key in Databricks Secrets**
   ```bash
   databricks secrets create-scope demo_secrets
   databricks secrets put --scope demo_secrets --key you_com_api_key --string-value "YOUR_API_KEY"
   ```

5. **Deploy the bundle**
   ```bash
   # Deploy to dev environment (default)
   databricks bundle deploy -t dev

   # Or deploy to production
   databricks bundle deploy -t prod
   ```

6. **Run the pipeline**
   ```bash
   # Run the full agent pipeline
   databricks bundle run realtime_rag_agent_pipeline -t dev

   # Or run evaluation only
   databricks bundle run evaluate_agent_only -t dev
   ```

### Configure Your Environment

You can customize the deployment by setting variables in the bundle. Create a `databricks.yml` override or use the CLI:

```bash
# Example: Deploy with custom catalog and schema
databricks bundle deploy -t dev \
  --var catalog=my_catalog \
  --var schema=my_schema \
  --var secret_scope=my_secrets
```

Available variables:
- `catalog`: Unity Catalog name (default: `main`)
- `schema`: Schema name (default: `realtime_rag_demo_dev` for dev, `realtime_rag_demo_prod` for prod)
- `secret_scope`: Secret scope containing You.com API key (required)
- `secret_key`: Secret key name (default: `you_com_api_key`)
- `cluster_node_type`: Cluster node type (default: `i3.xlarge`)
- `llm_endpoint_name`: LLM endpoint name (default: `databricks-claude-3-7-sonnet`)

## Manual Notebook Execution (Alternative)

If you prefer to run notebooks manually instead of using the asset bundle:

1. **Set up secrets** (same as step 4 above)
2. **Run notebooks in sequence** - Use the widget UI at the top of each notebook to set parameters, or create `src/common.yaml` with your configuration values
3. See **[SETUP.md](./SETUP.md)** for configuration details

# Notebooks

Execute these notebooks in order:

1. [`01-agent-configs`](./src/01-agent-configs.ipynb): Establishes a You.com Connection via a Unity Catalog Function.
2. [`02-define-agent`](./src/02-define-agent.ipynb): Defines the agentic logic that integrates the UC function with a base LLM.
3. [`03-create-agent`](./src/03-create-agent.ipynb): Creates and tests the agent using MLflow tracing.
4. [`04-evaluate-agent`](./src/04-evaluate-agent.ipynb): Evaluates agent performance using multiple scorers.

## Example Queries

This demo showcases real-time information retrieval. Try queries like:
- "What is the current status of United Airlines flight UA2749?"
- "What are the current delays at JFK airport?"
- "What are the latest developments in today's tech news?"
- "What is the current stock price of NVIDIA?"

## How to get help

Databricks support doesn't cover this content. For questions or bugs, please open a GitHub issue and the team will help on a best effort basis.


## License

&copy; 2025 Databricks, Inc. All rights reserved. The source in this notebook is provided subject to the Databricks License [https://databricks.com/db-license-source].  All included or referenced third party libraries are subject to the licenses set forth below.

| library                                | description             | license    | source                                              |
|----------------------------------------|-------------------------|------------|-----------------------------------------------------|
