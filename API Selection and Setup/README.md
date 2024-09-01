# 1. API Selection: Coinbase API

## 1.1 Overview
The Coinbase API provides access to cryptocurrency data, such as real-time prices, exchange rates, and historical data. This makes it an excellent choice for building a data pipeline that ingests and processes financial data.

## 1.2 API Setup

The Coinbase API provides two main ways to authenticate requests: API Key authentication and OAuth 2.0. Since we’re primarily fetching public data (e.g., cryptocurrency prices) and don’t need to act on behalf of a user in this project, we will implement API key authentication for now.<br>
Please also note that Webhooks are also available as an option for sending real-time data to another application. We'll use the regular REST API for now and later, we will circle back and update our codes to use webhooks. Below is an example of how to make an API request using the API key: <br>

```python
import requests

url = "https://api.coinbase.com/v2/prices/spot"
headers = {
    'Authorization': f'Bearer {your_api_key}'
}
params = {
    'currency': 'USD'
}
response = requests.get(url, headers=headers, params=params)

if response.status_code == 200:
    data = response.json()
    print(data)
else:
    print("Failed to fetch data")

```

Please note that this IS NOT the secure way to make an API request. In the next section, [AWS Setup](../AWS%20Setup/README.md), we'll use AWS Secrets Manager to store our API key. Below is an example of how you would retrieve the API key from Secrets Manager using AWS SDK: <br>

```python
import boto3
import json

def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=secret_name)
    secret = json.loads(response['SecretString'])
    return secret['COINBASE_API_KEY']
```
