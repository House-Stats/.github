# House Stats
> Unfortunately, the website is no longer hosted as I have had to move my servers around my garage to make room for the Landy, but I hope to get it back up and running soon.
> Currently, while the house-stats original site is down, I am developing house stats 2.0. This will involve a full re-write of the backend to a port-adapter architecture, which will allow full end-to-end testing and enable future data to be easily incorporated as EPC-certified, local planning permissions, and land polygons from the INSPIRE dataset.


A website for viewing the current state of the housing market in England in Wales with aggreations executed on the fly. You can read a more detailed post about House Stats on my website [here](https://morganthomas.uk/posts/housestats). 


### [1. Data Ingest](https://github.com/House-Stats/sales-ingest)
- A Golang + Python pipeline downloads the latest Price Paid Data from the Land Registry every month.
- It validates, parses, and uploads millions of transactions to a PostgreSQL database using a Kafka message queue and 
asynchronous workers.
- Updates are applied automatically (insert, amend, delete) to keep the dataset current with minimal downtime.
- Parses postcodes into their constituent parts for grouping and searching


### [2. Data API, Storage, and Caching](https://github.com/House-Stats/web-api)
- Data is normalised into the third normal form to eliminate duplication and improve query efficiency.
- PostgreSQL handles relational integrity while Redis provides an in-memory cache for frequent queries.
- Full-text indexes and partial postcode searching enable sub-second lookups across millions of records.
- This is all served using a Python API written in Flask, which communicates with the data-processors using RabbitMQ

### [3. Data Processing](https://github.com/House-Stats/data-processor)
- Analytical workloads (standard deviation, outlier removal, and percentage change analysis) are executed using [PolaRS](https://pola.rs/) 
a Rust drop-in for Pandas
- The system automatically scales to use multiple workers, allowing for batch processing of many areas simultaneously
- Results are cached in a MongoDB instance to allow for instant fetching of large areas that can take a few minutes to 
process

### [4. Web Interface](https://github.com/House-Stats/website)
- A Flask API serves data to a Svelte + Chart.js front-end for highly interactive visualisations.
- Users can search by town, postcode, or county; view price trends over time; and compare regions side-by-side.
- Uses write-ahead search capability to help users find the area they are looking for
- Change time period interval to look at 1mo, 3mo, 6mo, 1yr
