# Astronomer-Skills
This repository details my skills on Astronomer, for running Airflow

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
