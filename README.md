# Data-Pipeline-for-Ingesting-API-Data-into-an-AWS-Data-Lake

## Project Overview
Designing and implementing a data pipeline that regularly ingests data from a public API into an AWS data lake. The data pipeline will ensure that the data is consistently updated and available for querying using AWS Athena. The project will showcase the use of various AWS services such as S3, Glue, and Athena, along with API integration and data pipeline design.

## Objectives
Implement a data pipeline to ingest data from an API into an AWS data lake.
Ensure that the ingested data is regularly updated and available for analysis.
Demonstrate the ability to query the ingested data using AWS Athena.
Showcase the design and management of a data lake architecture.

## Tools & Technologies
AWS S3: For storing raw and processed data.
AWS Glue: For ETL (Extract, Transform, Load) operations.
AWS Athena: For querying the data stored in S3.
Python/Java: For scripting and API integration.
Public API: Choose a public API that provides data in a structured format (e.g., JSON, CSV).


## 1. API Selection \& Setup
CoinGecko API (Cryptocurrency Data)
* Data Types: Cryptocurrency prices, market capitalization, trading volume, historical data, exchange information.
* Update Frequency: Cryptocurrency prices and market data are updated in real-time.
* Data Volume: You can access a wide range of data points across thousands of cryptocurrencies, providing substantial data for ingestion and analysis.

[CoinGecko API documentation](https://docs.coingecko.com/v3.0.1/reference/introduction)

Here’s a basic example to fetch the current market data for Bitcoin using the CoinGecko API:

```
import requests

url = "https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd"

response = requests.get(url)

if response.status_code == 200:
    data = response.json()
    print(data)
else:
    print(f"Failed to fetch data: {response.status_code}")
```
