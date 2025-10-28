# Databricks Asset Bundle Resources

This directory contains modular YAML configuration files for the Databricks Asset Bundle.

## Files

- **[jobs.yml](jobs.yml)**: Job definitions for the Real-Time RAG Agent pipeline
  - `realtime_rag_agent_pipeline`: Main pipeline job that runs all notebooks in sequence
  - `evaluate_agent_only`: Evaluation-only job for testing agent performance

## Customization

You can add additional resource files here for:
- **Experiments**: MLflow experiment configurations
- **Models**: Model registry configurations
- **Permissions**: Access control lists
- **Schedules**: Scheduled job triggers

Example structure:
```
resources/
├── jobs.yml          # Job definitions
├── experiments.yml   # MLflow experiments (optional)
├── models.yml        # Model registry (optional)
└── permissions.yml   # Access controls (optional)
```

All YAML files in this directory are automatically included by the main `databricks.yml` via the `include` directive.

## Variables

These files reference variables defined in the main `databricks.yml`:
- `${bundle.target}`: Current deployment target (dev/staging/prod)
- `${var.catalog}`: Unity Catalog name
- `${var.schema}`: Schema name within the catalog
- `${var.secret_scope}`: Secret scope containing API keys
- `${var.secret_key}`: Secret key name for API keys
- `${var.cluster_node_type}`: Cluster node type for job execution
- `${var.llm_endpoint_name}`: LLM model serving endpoint name

## Adding New Jobs

To add a new job:

1. Edit `jobs.yml` and add a new job definition under `resources.jobs`
2. Define tasks with dependencies using `depends_on`
3. Reference notebook paths relative to the bundle root
4. Use variables for environment-specific values

Example:
```yaml
resources:
  jobs:
    my_new_job:
      name: "[${bundle.target}] My New Job"
      tasks:
        - task_key: my_task
          notebook_task:
            notebook_path: ./src/my-notebook.ipynb
```

## Learn More

- [Databricks Asset Bundles Documentation](https://docs.databricks.com/dev-tools/bundles/index.html)
- [Bundle YAML Reference](https://docs.databricks.com/dev-tools/bundles/reference.html)
- [Job Configuration Reference](https://docs.databricks.com/dev-tools/bundles/jobs-reference.html)
