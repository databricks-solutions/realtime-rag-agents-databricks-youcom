# Setup Guide

This guide provides additional setup details beyond the quick start in [README.md](README.md).

## Configuration

The notebooks use **Databricks widgets** to accept parameters, which are automatically populated when running as a job via Databricks Asset Bundles.

### For Job Execution (Recommended)

When deploying with `databricks bundle deploy`:

1. Edit variables in [databricks.yml](databricks.yml) if needed
2. Deploy: `databricks bundle deploy -t dev`
3. Run: `databricks bundle run realtime_rag_agent_pipeline -t dev`

Parameters flow automatically: `databricks.yml` → `resources/jobs.yml` → notebook widgets

### For Interactive Development

When running notebooks manually in the Databricks workspace:

**Option A: Use widget UI**
- Run the configuration cell to create widgets
- Set values in the widget UI at the top of the notebook

**Option B: Use `common.yaml` fallback**
- Create `src/common.yaml`:
  ```yaml
  catalog: main
  schema: realtime_rag_demo
  secret_scope: demo_secrets
  secret_key: you_com_api_key
  llm_endpoint_name: databricks-claude-3-7-sonnet
  ```
- Widgets will use these as defaults when not explicitly set

**Priority**: Widget values → `common.yaml` → Built-in defaults

⚠️ **Important**: Add `src/common.yaml` to `.gitignore` - never commit secrets!

## Notebook Parameters

### [01-agent-configs.ipynb](src/01-agent-configs.ipynb)
Creates UC Connection and Function for You.com API.

Parameters:
- `catalog`: Unity Catalog name
- `schema`: Schema name
- `secret_scope`: Secret scope with You.com API key
- `secret_key`: Secret key name

### [02-define-agent.ipynb](src/02-define-agent.ipynb)
Generates `agent.py` with LangGraph agent definition.

Parameters:
- `catalog`: Unity Catalog name
- `schema`: Schema name
- `llm_endpoint_name`: LLM endpoint (e.g., `databricks-claude-3-7-sonnet`)

### [03-create-agent.ipynb](src/03-create-agent.ipynb)
Tests agent and creates evaluation dataset.

Parameters:
- `catalog`: Unity Catalog name
- `schema`: Schema name

### [04-evaluate-agent.ipynb](src/04-evaluate-agent.ipynb)
Evaluates agent performance with MLflow metrics.

Parameters:
- `catalog`: Unity Catalog name
- `schema`: Schema name

## Troubleshooting

### "Missing secret_scope" error
**Solution**: Set widget value or create `src/common.yaml` with required fields

### Widget values not updating
**Solution**: Clear widgets with `dbutils.widgets.removeAll()` or manually update in UI

### agent.py using wrong configuration
**Solution**: Environment variables are set in configuration cells. If already imported, restart Python: `dbutils.library.restartPython()`

### Job parameters not flowing to notebooks
**Solution**: Ensure parameter names in `resources/jobs.yml` match widget names exactly

## Advanced: CI/CD Integration

### GitHub Actions Example

```yaml
name: Deploy Bundle
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install Databricks CLI
        run: pip install databricks-cli
      - name: Deploy
        env:
          DATABRICKS_HOST: ${{ secrets.DATABRICKS_HOST }}
          DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN }}
        run: databricks bundle deploy -t prod
```

## Security Best Practices

1. ✅ Store secrets in Databricks Secrets, not YAML files
2. ✅ Add `src/common.yaml` to `.gitignore`
3. ✅ Use service principals for production deployments
4. ✅ Grant minimal required permissions (principle of least privilege)
5. ✅ Regularly rotate API keys
6. ❌ Never commit secrets to version control

## Resources

- [Databricks Asset Bundles](https://docs.databricks.com/dev-tools/bundles/)
- [Job Parameters](https://docs.databricks.com/jobs/parameter-use.html)
- [Databricks Widgets](https://docs.databricks.com/notebooks/widgets.html)
- [You.com API Documentation](https://documentation.you.com/)
