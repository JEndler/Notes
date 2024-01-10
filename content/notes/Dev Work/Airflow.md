---
title: Some Notes on learning Airflow.
---


*Apache Airflow is an open source platform to programmatically author, schedule, and monitor workflows*

### Components

Webserver - flask-based running via gunicorn.
Schduler - manages and schedules tasks.
Database - stores Metadata about Users, tasks ect. runs via SQLAlchemy
Executor - defines how tasks are executed.
Worker - subprocess that runs the task


### Example Architectures

**Single Node Architecture**
	-	Everything runs with on one Machine
	
### Concepts

**DAGs** -> Directed Acyclic Graph
 - Basic Representation of a Data Pipeline
 - DAGs define Relationships between Operators

**Operators** -> Run Operations/Tasks
- Action Operator - used to execute python, bash, sql ect
- Transfer Operator - send Data from Source to Destination (ex. SQL to ES)
- Sensor Operator - waiting for something to happen (ex. file to load)

**Tasks** -> Instance of an Operator
**Task Instance** -> Represents one run of a task: DAG + Task + timestamp

**Dependencies** -> Defined by DAG
- Task 1 >> Task 2 >> Task 3

**Workflow** -> Combines all the Concepts, one full Pipeline = Workflow

### Task Lifecycle

[Look at this](https://academy.astronomer.io/astronomer-certification-apache-airflow-fundamentals-preparation/725487)

### Usage on My Linux Box

Airflow runs with poetry ^^

airflow config directory is in ~/airflow

``` bash
$ poetry shell
$ airflow standalone
```

in **airflow-config** 

web_server_port läuft auf 8869

auf der subdomain airflow.endler.tech

**Nutzer erstellen**
```bash
airflow users  create --role Admin --username Jakob --email jakob@endler.tech --firstname Jakob --lastname Endler --password xxx
```


## Views

**Graph View**
 - Useful to see dependencies between the DAGs.
 - See the status of the task for the latest DAG run.
 - Color denominates the operator used per task.

**Gantt View**
- Used to analyze task duration.
- visualizes overlap between tasks.
- quickly identify bugs & bottlenecks.


## Interacting with tasks

- Clicking on a task opens context menu.
- the logs are accessible inside the context menu

Tasks can be filtered by instance, upstream etc.
**Clearing** the task is used after a crash and fix, to restart the task.

## Important Commands

*first connect to the docker instance containing our airflow instance.*
```bash
docker exec -it <container-id> /bin/bash
```
 
*initialize the Airflow Database* 
```bash
airflow db init
```

*upgrade Airflow Database to new schema*
```bash
airflow db upgrade
```

*start webserver, scheduler, celery worker( to run task on parallel machines)*
```bash
airflow webserver
airflow scheduler
airflow celery worker
```

*pause and unpause Airflow Dags*
```bash
airflow dags pause
airflow dags unpause
```

*trigger a dag*
```bash
airflow dags trigger
```

*list dags or tasks of a dag*
```bash
airflow dags list
airflow tasks list example_dag
```

*test your dags before you run them*
```bash
airflow task test <dag_name> <task_name> <run_date>
```

# Airflow

Implement Dags like this:

```python
with DAG(dag_id='simple_dag', default_args=default_args, schedule_interval='@daily', start_date=days_ago(3), catchup=False) as dag:
	downloading_data = PythonOperator(
		task_id = 'downloading_data',
		python_callable=_downloading_data
	)

	waiting_for_data = FileSensor(
		task_id = 'waiting_for_data',
		fs_conn_id = 'fs_default',
		filepath = 'my_file.txt'
	)

	downloading_data >> waiting_for_data
```

#### Exchange data between tasks

The way of sharing data between tasks in a dag is called **XComs**.

The easiest way to create an XCom is to return a value at the end of a task definition.

Pulling the data from a Method:

```python
def _checking_data(ti):
	ti.xcom_pull(key='return_value', task_ids=['downloading_data'])
```

One can also use the same mechanism to push data:

```python
def _checking_data(ti):
	ti.xcom_push(key='some_key', value=42)
```

XComs are stored inside the metadatabase, so there is a limit of 1-2 GB of storage, depending on the type of database used.

## What happens when tasks fail

First, Airflow ties to rerun the task after a specified delay.

This is configured in the default args for your DAG:

```python
default_args = {
	'retries':5
	'retry_delay': timedelta(minutes=5)
}
```

It is also possible to specify an Email address to notify on failure.

**Executors**

Define how tasks are run (or on which system). Locally, Kubernetes, Celery or more.

Always queue-based.

#### A little about parameters

Parallelism = 32, allows to execute at most 32 talks in a parallel manner.
DAG_concurrency = 16, same as parallelism, but for one dag, so 16 tasks per dag.
max_active_runs_per_dag = 16


---

[Avyy we did it](https://www.credly.com/badges/99dcaf45-29e9-4210-844e-1ac3362106fc/public_url)

