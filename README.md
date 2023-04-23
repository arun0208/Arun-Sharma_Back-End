
# Steeleye Backend Assignment using FastAPI

Hey this is readme file / instructions for the rest api which I have created using FastAPI.


## FastAPI

FastAPI is a modern, fast (high-performance), web framework for building APIs with Python 3.7+ based on standard Python type hints.


## Run at your server

Dowload main.py from this repository or clone this project and start installing modules as mentioned


## Installation

To start using this rest api, you need to install some modules as given below

```bash
  pip install fastapi
  pip install uvicorn
  pip install fastapi_pagination
```
    
## Importing modules



```bash
from fastapi import FastAPI
import datetime as dt
from typing import Optional
from pydantic import BaseModel, Field
from fastapi_pagination import Page, add_pagination, paginate
```


## Features

- Get List Of All Trades
    -
- Get Trade By Id
    -
- Search Across Various Properties
    -
- Advanced Filtering
    -
- Pagination
    -
- Post Data
    -
- Delete Data
    -
## Home page ("/")
Creating app component to run FastAPI
```bash
    app = FastAPI()
    add_pagination(app)
```
This data will show when you start your server
```bash
    @app.get("/")
    def home():
        return {"data":"success"}
```
use this command to start your localhost server server.
```bash
uvicorn main:app --reload
```
open your localhost by entering
```bash
localhost:8000
```
Output:
```bash
{
  "data": "success"
}
```

## Trade List ("/get-trade")

Go to this URL to see the list of Trades.
```bash
    localhost:8000/get-trade
```
Output: You will see JSON format data like this
```bash
{
  "trades": [
    {
      "asset_class": "Bond",
      "counterparty": "buyer",
      "instrument_id": "TSLA",
      "instrument_name": "smartphone",
      "trade_date_time": "2023-04-23T12:38:50.747985",
      "trade_details": {
        "buySellIndicator": "Sell",
        "price": 29.99,
        "quantity": 10
      },
      "trade_id": 1,
      "trader": "Arun"
    },
}
```

## Pagination
On trade list page I have implemented pagination so that we can Paginate through pages. I have shown 10 data per page and have 2 pages in all. These values can also be changed by user through URL.
```bash
@app.get("/get-trade/")
def get_trades(limit: int=10, offset: int = 0):
    end_index = offset + limit
    trades = list(inventory.values())[offset:end_index]
    total_pages = len(inventory) // limit + (len(inventory) % limit != 0)
    previous_url = None
    if offset > 0:
        previous_offset = max(offset - limit, 0)
        previous_url = f"/get-trade/?limit={limit}&offset={previous_offset}"
    next_url = None
    if end_index < len(inventory):
        next_offset = end_index
        next_url = f"/get-trade/?limit={limit}&offset={next_offset}"
    result = {
        "trades": trades,
        "pagination": {
            "total_pages": total_pages,
            "previous": previous_url,
            "next": next_url
        }
    }
    
    return result
```
Output:
```bash
"pagination": {
    "total_pages": 2,
    "previous": null,
    "next": "/get-trade/?limit=10&offset=10"
  }
```
## Get By Id ("/get-trade/id/{item_id}")
We can search items by using id of that item in this API.

```bash
@app.get("/get-trade/id/{item_id}")
def getbyid(item_id:int):
    return inventory[item_id]
```
URL:
```bash
localhost/get-trade/id/1
```
Output: The data which have id 1 will be shown the value of id can be changed by user.
```bash
{
  "asset_class": "Bond",
  "counterparty": "buyer",
  "instrument_id": "TSLA",
  "instrument_name": "smartphone",
  "trade_date_time": "2023-04-23T12:38:50.747985",
  "trade_details": {
    "buySellIndicator": "Sell",
    "price": 29.99,
    "quantity": 10
  },
  "trade_id": 1,
  "trader": "Arun"
}
```
## Searching ("/get-trade/search")
This API allows user to search accross the api from the fields like 
```bash
Counterparty
InstrumentId
InstrumentName
Trader
```
Code:
```bash
@app.get("/get-trade/search")
def getbysearch(name:str):
    data ={}
    for item_id in inventory:
        if inventory[item_id]["counterparty"] == name:
            data[item_id] = inventory[item_id]
        if inventory[item_id]["instrument_id"] == name:
            data[item_id] = inventory[item_id]
        if inventory[item_id]["instrument_name"] == name:
            data[item_id] = inventory[item_id]
        if inventory[item_id]["trader"] == name:
            data[item_id] = inventory[item_id]

    return data
```
URL:
```bash
localhost:8000/get-trade/search?name=Arun
```
Output:
```bash
{
  "1": {
    "asset_class": "Bond",
    "counterparty": "buyer",
    "instrument_id": "TSLA",
    "instrument_name": "smartphone",
    "trade_date_time": "2023-04-23T12:38:50.747985",
    "trade_details": {
      "buySellIndicator": "Sell",
      "price": 29.99,
      "quantity": 10
    },
    "trade_id": 1,
    "trader": "Arun"
  }
}
```
## Advanced Filtering
This API allows user to Filter data based on
```bash
assetclass
minprice and maxprice
starttime and endtime
tradetype
```
Code:
```bash
@app.get("/get-trade/assetclass")
def asset(name:str):
    data = {}
    for item_id in inventory:
        if inventory[item_id]["asset_class"] == name:
            data[item_id]=inventory[item_id]

    return data
```
FILTER BY ASSET CLASS
URL:
```bash
loaclhost:8000/get-trade/assetclass?name=Equity
```
This Will return all the data which have assetclass Equity like this.

Output:
```bash
{
  "2": {
    "asset_class": "Equity",
    "counterparty": "seller",
    "instrument_id": "AMZN",
    "instrument_name": "laptop",
    "trade_date_time": "2023-04-23T12:38:50.747985",
    "trade_details": {
      "buySellIndicator": "Buy",
      "price": 34.99,
      "quantity": 20
    },
    "trade_id": 2,
    "trader": "Roshan"
  }
}
```
FILTER BY PRICE URL:
```bash
loaclhost:8000/get-trade/price?minprice=20.00&maxprice=80.00
```
Code:
```bash
@app.get("/get-trade/price")
def price(minprice:float,maxprice:float):
    data = {}
    for item_id in inventory:
        if inventory[item_id]["trade_details"]["price"] >= minprice and inventory[item_id]["trade_details"]["price"] <= maxprice:
            data[item_id] = inventory[item_id]

    return data
```
This will return all the data which has price range of
```bash
20.00 <= data <= 80.00
```
Output:
```bash
{
  "1": {
    "asset_class": "Bond",
    "counterparty": "buyer",
    "instrument_id": "TSLA",
    "instrument_name": "smartphone",
    "trade_date_time": "2023-04-23T12:38:50.747985",
    "trade_details": {
      "buySellIndicator": "Sell",
      "price": 29.99,
      "quantity": 10
    },
    "trade_id": 1,
    "trader": "Arun"
  }
} 
```

FILTER BY TRADE TYPE URL:
```bash
loaclhost:8000/get-trade/tradetype?name=Sell
```
Code:
```bash
@app.get("/get-trade/tradetype")
def tradetype(name:str):
    data = {}
    for item_id in inventory:
        if inventory[item_id]["trade_details"]["buySellIndicator"] == name:
            data[item_id]=inventory[item_id]

    return data
```
This will return all the data which tradetype value of sell like this

```bash
{
  "1": {
    "asset_class": "Bond",
    "counterparty": "buyer",
    "instrument_id": "TSLA",
    "instrument_name": "smartphone",
    "trade_date_time": "2023-04-23T12:38:50.747985",
    "trade_details": {
      "buySellIndicator": "Sell",
      "price": 29.99,
      "quantity": 10
    },
    "trade_id": 1,
    "trader": "Arun"
  }
}
```
## Post Item ("/create-item/{item_id}")

This API allows user to create and post data of their own based on pydantic class model. It will check whether the data at item id is present or not if not it will create the item.

Code:
```bash
@app.post("/create-item/{item_id}")
def create_item(item_id:int,item:Trade):
    if item_id in inventory:
        return{"Error":"Item already exists"}
    inventory[item_id]=item
    return inventory[item_id]
```

URL:
```bash
localhost:8000/docs
```
Go to docs page to check this feature. There you will find a POST Method.

## Delete Item (/delete-item)
This API also allows User to delete existing data. If the item id given by the user is present than it will delete the entry

Code:
```bash
@app.delete("/delete-item")
def delete_item(item_id:int):
    if item_id not in inventory:
        return{"Error":"Item not Found"}
    del inventory[item_id]
    return{"Success":"Item Deleted"}
```

URL:
```bash
localhost:8000/docs
```
Go to docs there you will find the delete method.
## Authors
This REST API is made completely by Arun Sharma.
All rights are reserved.

- [@Arun Sharma](https://github.com/arun0208)

