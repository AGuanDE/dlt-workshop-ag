# dlt-workshop-ag
How to build robust, scalable, and self-maintaining pipelines using dlt

🔹 **Base API URL:**

```
<https://us-central1-dlthub-analytics.cloudfunctions.net/data_engineering_zoomcamp_api>

```

🔹 **Data format:** Paginated JSON (1,000 records per page).

🔹 **API Pagination:** Stop when an empty page is returned.

## **Question 1: dlt Version**

Provide the **version** you see in the output

**Answer**: dlt 1.6.1

## **Question 2: Define & Run the Pipeline (NYC Taxi API)**

```
import dlt
from dlt.sources.helpers.rest_client import RESTClient
from dlt.sources.helpers.rest_client.paginators import PageNumberPaginator

# Define the API source

@dlt.resource(name="taxi_rides")
def ny_taxi():
    client = RESTClient(
        base_url="https://us-central1-dlthub-analytics.cloudfunctions.net",
        paginator=PageNumberPaginator(
            base_page=1,
            total_path=None
        )
    )

    for page in client.paginate("data_engineering_zoomcamp_api"):
        yield page

# define pipepine

pipeline = dlt.pipeline(
    pipeline_name="ny_taxi_pipeline",
    destination="duckdb",
    dataset_name="ny_taxi_data"
)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>database</th>
      <th>schema</th>
      <th>name</th>
      <th>column_names</th>
      <th>column_types</th>
      <th>temporary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ny_taxi_pipeline</td>
      <td>ny_taxi_data</td>
      <td>_dlt_loads</td>
      <td>[load_id, schema_name, status, inserted_at, sc...</td>
      <td>[VARCHAR, VARCHAR, BIGINT, TIMESTAMP WITH TIME...</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ny_taxi_pipeline</td>
      <td>ny_taxi_data</td>
      <td>_dlt_pipeline_state</td>
      <td>[version, engine_version, pipeline_name, state...</td>
      <td>[BIGINT, BIGINT, VARCHAR, VARCHAR, TIMESTAMP W...</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ny_taxi_pipeline</td>
      <td>ny_taxi_data</td>
      <td>_dlt_version</td>
      <td>[version, engine_version, inserted_at, schema_...</td>
      <td>[BIGINT, BIGINT, TIMESTAMP WITH TIME ZONE, VAR...</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ny_taxi_pipeline</td>
      <td>ny_taxi_data</td>
      <td>taxi_rides</td>
      <td>[end_lat, end_lon, fare_amt, passenger_count, ...</td>
      <td>[DOUBLE, DOUBLE, DOUBLE, BIGINT, VARCHAR, DOUB...</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>

How many tables were created?

- 2
- 4 (answer)
- 6
- 8

## **Question 3: Explore the loaded data**

Inspect the table `taxi_rides`:

```
df = pipeline.dataset(dataset_type="default").taxi_rides.df()
df

```

What is the total number of records extracted?

- 2500
- 5000
- 7500
- 10000 (answer)

## **Question 4: Trip Duration Analysis**

Run the SQL query below to:

- Calculate the average trip duration in minutes.

```
with pipeline.sql_client() as client:
    res = client.execute_sql(
            """
            SELECT
            AVG(date_diff('minute', trip_pickup_date_time, trip_dropoff_date_time))
            FROM rides;
            """
        )
    # Prints column values of the first row
    print(res)

```

What is the average trip duration?

- 12.3049 (answer)
- 22.3049
- 32.3049
- 42.3049
