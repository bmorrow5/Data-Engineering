This project was created as part of a Data Engineering group project to consolidate COVID data from four sources into a database which will automatically update weekly. It will then store this data in a database and generate a report that can be accessed via API. It was done with Dana Andrea, Jackie Huang, Twesha Mitra, Brandon Morrow. 

I (Brandon Morrow) completed the airflow scripts, and the get_emissions_data from the data processing folder, and I also created the modified covid schema file. I assisted with the API creation and other variables. To create the database and run the DAG:


Download all files in this folder and move to the home directory inside an ubuntu VM "~/EN.685.648.81.FA23-main"
Navigate to this folder in the terminal.

1. cd EN.685.648.81.FA23-main

--------------Create the COVID database---------------
Run the following commands one after the other:

1. psql -U jhu -d postgres
2. \i modified_covid_schema.sql
3. \copy covid.economic_indicators FROM 'economic_indicators.csv' WITH (FORMAT csv, HEADER, NULL '');
4. \copy covid.emissions_data FROM 'emissions_data_2017_2023.csv' WITH (FORMAT csv, HEADER, NULL 'NA');
5. \copy covid.covid_data FROM 'COVID-19_Case_Surveillance_Public_clean.csv' WITH (FORMAT csv, HEADER, NULL 'NA');
6. \copy covid.stock_data FROM 'spy_data.csv' WITH (FORMAT csv, HEADER, NULL 'NA');

7. ctrl + d to exit postgres
--------------If airflow is installed---------------
Move the file DAG.py inside the "EN.685.648.81.FA23-main/airflow_scripts" to the "~/airflow/dags" folder

Run the following commands:
1. conda activate airflow_env
2. pip install pandas
3. pip install yfinance
4. pip install pandas_datareader
5. pip instapp matplotlib
6. pip install psycopg2-binary
7. export AIRFLOW_HOME=~/airflow
8. airflow webserver -p 8080

Open a second terminal tab
9. conda activate airflow_env
10. export AIRFLOW_HOME=~/airflow
11. airflow scheduler

Open a web browser to localhost:8080


--------------If airflow is not installed---------------
Execute the following commands one at a time, except the airflow users create can all be executed at once:

1. conda create --name airflow_env python=3.9 -y
2. conda activate airflow_env
3. pip install pandas
4. pip install yfinance
5. pip install psycopg2-binary
6. pip install pandas_datareader
7. pip install matplotlib
8. export AIRFLOW_HOME=~/airflow
9. conda install -c conda-forge airflow
10. airflow db init
11. cd ~/airflow

Using a text editor (nano in this case), change the line in the “airflow.cfg” file where it says load_examples from True to False. This will prevent the airflow UI from loading the example DAGs. These DAG examples can be helpful, but they also make it more difficult to find the DAGs you are creating for the assignment.

12. mkdir dags
13. airflow users create \
--username admin \
--password admin \
--firstname Brandon \
--lastname Morrow \
--role Admin \
--email bmorrow5@jh.edu

Move the file DAG.py inside the "EN.685.648.81.FA23-main/airflow_scripts" to the "~/airflow/dags" folder

14. export AIRFLOW_HOME=~/airflow
15. airflow webserver -p 8080

Open a second terminal tab and run these commands:
16. conda activate airflow_env
17. export AIRFLOW_HOME=~/airflow
18. airflow scheduler

Open a web browser to localhost:8080 
Login is admin admin

--------------Run the Airflow DAG---------------
Open localhost:8080 in a web browser to view the DAG


--------------Run the flask app---------------
To run the flask app, run the following command to start the web server. This is necessary to make API calls.
python api/app.py

--------------View Reports---------------
To view or download reports on the data for further processing they can be accessed via flask API at:
stock_api = http://localhost:8001/api/spy
economics_api = http://localhost:8001/api/economics
emissions_api = http://localhost:8001/api/emissions
covid_api = http://localhost:8001/api/covid

--------------Generate Charts---------------
The report.py script will call the APIs and generate some charts and save them in the api/ folder.
To generate those, run the following:
python api/report.py