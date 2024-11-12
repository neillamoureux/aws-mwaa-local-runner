# About

This is a fork of the `aws-mwaa-local-runner` repo provided by Amazon
to mimic their MWAA environment.

It has been modified to work with Bazaarvoice's `insights-dashboard` repo.
This is done by
1. Mounting volumes via the
[docker/docker-compose-local.yml](docker/docker-compose-local.yml)
file,
to the `insights-dashboard` repo's dags, requirements, etc.
2.  Modifying the
[entrypoint.sh](docker/script/entrypoint.sh)
script to install the additional requirements.
3.  Modifying `PYTHONPATH` to enable loading folders as packages in the mounted dags folder


## Setup
Note after setup, there may be dags with import error.
Fix it if you need that dag.

### `aws-mwaa-local-runner` repo change
- Copy [docker/.env_template](./docker/.env_template) to `docker/.env`
- Update the first line to the absolute path to your copy of the `insights-dashboards` repo
```
INSIGHTS_REPO_ROOT_PATH="/path/to/the/repo/insights-dashboards"
```

### `insights-dashboard` repo changes
You need to change a file in the `insights-dashboard` repo.
Don't commit it to a shared branch!

- Modify the dbt paths in `data/dags/utils/vars.py` to work with this repo:
```python
DBT_EXECUTABLE_PATH = f"{AIRFLOW_HOME}/.local/bin/dbt"
# DBT_EXECUTABLE_PATH = f"{AIRFLOW_HOME}/dbt_venv/bin/dbt"
DBT_PROJECT_DIR = f"{AIRFLOW_HOME}/dags/insights/dbt/"
# DBT_PROJECT_DIR = f"{AIRFLOW_HOME}/dags/dbt/"
```
- You can tell git to ignore changes to this file in the `insights-dashboards` repo via: 
    ```
    git update-index --assume-unchanged data/dags/utils/vars.py
    ```
- To revert this:
    ```
    git update-index --no-assume-unchanged data/dags/utils/vars.py
    ```



### Airflow variables, connections, and pools.
You will need to set some of these up via your local Airflow.
You can tell from error messages in the Dag imports
or runs, or even possibly the logs in your terminal.

Look at the insights-dashboard MWAA settings in AWS
to get appropriate values

Adding the below will get you most of the way:

#### Variables
- `AWS_DEFAULT_REGION`
- `ENV`

#### Connections
- `redshift-serverless-qa`

#### Pools
- `redshift_2` (20 slots)

