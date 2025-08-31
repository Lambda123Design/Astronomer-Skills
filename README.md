# Astronomer and Airflow -Skills
This repository details my skills on Astronomer, for running Airflow

**A) Setting up Airflow with Astro**

**B) Building Your First DAG With Airflow**

**C) Designing Mathematical Calculation DAG With Airflow**

**D) Getting Started With TaskFlow API Using Apache Airflow**


**A) Setting up Airflow with Astro**

1. To Initialize Project - "astro dev init".

2. To start Airflow - "astro dev start"

3. To stop Airflow - "astro dev stop"

🔹 Components

Astronomer (Astro): A managed platform for Apache Airflow that simplifies running, scaling, monitoring, and securing Airflow.

Docker: Required since Astronomer runs Airflow within Docker containers.

Docker Compose: Used to enable multiple services (e.g., Airflow webserver, scheduler, database) to interact.

Project Structure (auto-created by astro dev init):

.astro → Astro project config.

dags/ → Folder for DAGs.

Dockerfile → Image definition for running Airflow.

include/ → For additional files.

plugins/ → For Airflow plugins.

tests/ → For test cases.

requirements.txt → For extra Python dependencies.

README.md → Project info.

Airflow UI (localhost:8080): Web interface for managing DAGs.

XCom: Mechanism for passing information between tasks.

🔹 Important Astronomer Commands

Initialize project:

astro dev init


→ Creates the Astro project structure with DAGs, Dockerfile, configs.

Start Airflow:

astro dev start


→ Builds Docker image, runs Airflow containers (webserver, scheduler, Postgres, triggerer).

Stop Airflow:

astro dev stop


→ Stops all running Airflow containers.

🔹 Example DAG (default)

example_dag.py provided in dags/.

Demonstrates an ETL workflow:

Task 1 – Get Astronauts → Fetch astronauts from API.

Task 2 – Print Astronauts → Print list of astronauts.

🔹 Steps to Set Up and Run Airflow with Astro

Install Docker (prerequisite).

Create project folder (e.g., airflow-astro).

Open terminal / VS Code and navigate to folder.

Initialize project → astro dev init.

Inspect structure → dags/, Dockerfile, .astro, etc.

Start Airflow → astro dev start.

Containers for webserver, scheduler, Postgres created.

Airflow UI accessible at http://localhost:8080.

Default credentials: admin / admin.

Check DAGs in UI → example_astronauts.

View tasks (get_astronauts, print_astronauts).

View Graph, Gantt, Logs, Code, XCom, etc.

Trigger DAG manually or let it follow its schedule.

Monitor logs & XComs for task details.

Stop Airflow → astro dev stop.



**B) Building Your First DAG With Airflow**

Components Used

DAG (Directed Acyclic Graph) → Defines the workflow.

PythonOperator → Runs Python functions as tasks.

datetime → Used for scheduling (start date).

Tasks Defined:

preprocess_data() → Pre-processing step.

train_model() → Training step.

evaluate_model() → Evaluation step.

🔹 DAG Definition
with DAG(
    dag_id="ml_pipeline",
    start_date=datetime(2024, 1, 1),
    schedule_interval="@weekly",
    catchup=False
) as dag:


dag_id = "ml_pipeline"

start_date = 2024-01-01

schedule_interval = "@weekly" (runs once a week).

catchup=False → Prevents backfilling past runs.

🔹 Tasks Setup (PythonOperator)
preprocess = PythonOperator(
    task_id="preprocess_task",
    python_callable=preprocess_data
)

train = PythonOperator(
    task_id="train_task",
    python_callable=train_model
)

evaluate = PythonOperator(
    task_id="evaluate_task",
    python_callable=evaluate_model
)


Each task has a unique task_id.

python_callable points to the defined Python function.

🔹 Dependencies
preprocess >> train >> evaluate


Execution order: Preprocessing → Training → Evaluation.

If one task fails, downstream tasks won’t run.

🔹 Full DAG File → dags/ml_pipeline.py

Contains imports, task functions, DAG definition, operators, and dependencies.

Saved in dags/ folder so Airflow can auto-detect it.

🔹 Running the Pipeline

Start Airflow:

astro dev start


→ Brings up Docker containers (Airflow webserver, scheduler, Postgres).

Access UI:

Open: http://localhost:8080

Login: admin / admin (default).

View DAGs:

example_dag (default).

ml_pipeline (new DAG).

Inside ml_pipeline DAG → see tasks:

preprocess_task → Logs: “Pre-processing data...”

train_task → Logs: “Training model...”

evaluate_task → Logs: “Evaluating model...”

Graph view shows arrows → Preprocess → Train → Evaluate.

Successful runs show green tasks in UI.

🔹 Managing Airflow

Stop containers:

astro dev stop


Restart (reload code changes):

astro dev restart

**C) Designing Mathematical Calculation DAG With Airflow**

Key Concept: XCom (Cross Communication)

Mechanism for tasks to share data in Airflow.

Uses:

xcom_push(key, value) → Push value into XCom.

xcom_pull(key, task_ids) → Retrieve value from another task.

Enables data flow between tasks.

🔹 DAG File → dags/maths_operation.py
Imports
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

🔹 Task Functions

Start with 10

def start_number(**context):
    value = 10
    context['ti'].xcom_push(key='current_value', value=value)
    print(f"Starting number is {value}")


Add 5

def add_five(**context):
    current_value = context['ti'].xcom_pull(key='current_value', task_ids='start_task')
    new_value = current_value + 5
    context['ti'].xcom_push(key='current_value', value=new_value)
    print(f"{current_value} + 5 = {new_value}")


Multiply by 2

def multiply_by_two(**context):
    current_value = context['ti'].xcom_pull(key='current_value', task_ids='add_five_task')
    new_value = current_value * 2
    context['ti'].xcom_push(key='current_value', value=new_value)
    print(f"{current_value} * 2 = {new_value}")


Subtract 3

def subtract_three(**context):
    current_value = context['ti'].xcom_pull(key='current_value', task_ids='multiply_by_two_task')
    new_value = current_value - 3
    context['ti'].xcom_push(key='current_value', value=new_value)
    print(f"{current_value} - 3 = {new_value}")


Square the number

def square_number(**context):
    current_value = context['ti'].xcom_pull(key='current_value', task_ids='subtract_three_task')
    new_value = current_value ** 2
    print(f"{current_value}^2 = {new_value}")

🔹 DAG Definition
with DAG(
    dag_id="maths_sequence_dag",
    start_date=datetime(2023, 1, 1),
    schedule_interval="@once",
    catchup=False
) as dag:


dag_id = "maths_sequence_dag"

start_date = 2023-01-01

schedule_interval = "@once" → Runs only when manually triggered.

catchup=False → No backfilling.

🔹 Tasks Setup (with XCom context)
start_task = PythonOperator(
    task_id="start_task",
    python_callable=start_number,
    provide_context=True
)
# Similar for add_five_task, multiply_by_two_task, subtract_three_task, square_task


Each task has provide_context=True → makes context available for XCom push/pull.

🔹 Dependencies
start_task >> add_five_task >> multiply_by_two_task >> subtract_three_task >> square_task


Execution order:
Start (10) → Add Five (15) → Multiply (30) → Subtract (27) → Square (729)

🔹 Running the DAG

Start Airflow:

astro dev start


Open UI: http://localhost:8080 → login admin/admin.

See new DAG → maths_sequence_dag.

Trigger DAG manually.

🔹 Monitoring Results

Graph view → tasks connected in sequence.

Logs:

Start: Starting number is 10

Add: 10 + 5 = 15

Multiply: 15 * 2 = 30

Subtract: 30 - 3 = 27

Square: 27^2 = 729

XCom tab → shows values passed: 10 → 15 → 30 → 27.

This example introduces XComs for passing data between tasks.

Next step: TaskFlow API (with @task decorator) → makes this simpler without manually handling xcom_push / xcom_pull.

**D) Getting Started With TaskFlow API Using Apache Airflow**

Key Concept: TaskFlow API (introduced in Airflow 2.0)

Lets you define tasks with @task decorator.

Return values from tasks are automatically stored in XCom.

No need to write xcom_push / xcom_pull.

Task dependencies look like normal Python function calls.

Makes DAGs cleaner, simpler, and more Pythonic.

🔹 DAG File → dags/taskflow_api.py
Imports
from airflow import DAG
from airflow.decorators import task
from datetime import datetime

🔹 DAG Definition
with DAG(
    dag_id="maths_sequence_taskflow",
    start_date=datetime(2023, 1, 1),
    schedule_interval="@once",
    catchup=False
) as dag:


dag_id = "maths_sequence_taskflow"

schedule_interval = "@once" → run only when triggered manually.

catchup=False → no backfilling.

🔹 Task Functions with @task

Start with 10

@task
def start_number():
    initial_value = 10
    print(f"Starting number is {initial_value}")
    return initial_value


Add 5

@task
def add_five(number):
    new_value = number + 5
    print(f"{number} + 5 = {new_value}")
    return new_value


Multiply by 2

@task
def multiply_by_two(number):
    new_value = number * 2
    print(f"{number} * 2 = {new_value}")
    return new_value


Subtract 3

@task
def subtract_three(number):
    new_value = number - 3
    print(f"{number} - 3 = {new_value}")
    return new_value


Square

@task
def square_number(number):
    new_value = number ** 2
    print(f"{number}^2 = {new_value}")
    return new_value

🔹 Task Dependencies (looks like normal Python calls)
start_val = start_number()
added_val = add_five(start_val)
multiplied_val = multiply_by_two(added_val)
subtracted_val = subtract_three(multiplied_val)
squared_val = square_number(subtracted_val)


Behind the scenes → Airflow builds DAG structure.

Dependencies:
start → add_five → multiply → subtract → square

🔹 Running the DAG

Start Airflow:

astro dev start


Open Airflow UI → http://localhost:8080

Trigger maths_sequence_taskflow DAG.

🔹 Monitoring Results

Graph View → tasks connected in sequence.

Logs:

start_number: Starting number is 10

add_five: 10 + 5 = 15

multiply_by_two: 15 * 2 = 30

subtract_three: 30 - 3 = 27

square_number: 27^2 = 729

XCom tab: return values (10 → 15 → 30 → 27 → 729) are auto-stored.

**Conclusion**

Compared to PythonOperator + manual XComs, TaskFlow API:

Is cleaner and requires less boilerplate.

Automatically handles XComs & dependencies.

Feels like writing normal Python code, but produces a DAG.

Next, we can dive into advanced TaskFlow features (like retries, retries with exponential backoff, custom XCom backends, branching).


Is cleaner and requires less boilerplate.

Automatically handles XComs & dependencies.

Feels like writing normal Python code, but produces a DAG.

Next, we can dive into advanced TaskFlow features (like retries, retries with exponential backoff, custom XCom backends, branching).
