# inventory API

A Python REST API for managing products, stock and orders.

Built with FastAPI, SQLAlchemy and PostgreSQL.

## Python files:

- **main.py**: endpoints for products, stock and orders.
- **schemas.py**: fields accepted in request JSON.
- **database.py**: connection, get_db(), rollback on SQL errors and session cleanup.


## Setup on Windows
Install the database team's PostgreSQL schema first.

Open a terminal inside the API folder and run:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `.env` with your database connection settings and set:

```dotenv
LOCAL_DEMO=true
```

Use a database login with the `inventory_app` role.

For local order testing, `main.py` uses:

```python
temp_user_id = 1
```

This ID must belong to an existing active `PRODUCT_MANAGER`. Change it if your test employee has a different ID.

Do not upload `.env` or `.venv` to GitHub.

## Run the API

```powershell
.\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

Open http://127.0.0.1:8000/docs.

Select an endpoint, click **Try it out**, enter the values and click **Execute**.

## Test the endpoints

Replace the example IDs with the IDs returned by your API.

### Create a product

POST `/api/products`

```json
{
  "name": "Wireless Mouse",
  "sku": "MOUSE-001",
  "description": "USB mouse",
  "price": "12.50",
  "reorder_level": 5
}
```

Returns the new product ID. Stock starts at zero.

### Add stock

POST `/api/stock-transactions`

```json
{
  "product_id": 1,
  "transaction_type": "IN",
  "quantity": 10,
  "notes": "Initial stock"
}
```

- `IN`: adds units.
- `OUT`: removes units.
- `ADJUSTMENT`: sets the stock to the specified quantity.

### View products

GET `/api/products`

Lists up to 100 products.

GET `/api/products/1`

Shows one product and its current stock.

### Update a product

PUT `/api/products/1`

```json
{
  "name": "Wireless Mouse",
  "description": "Updated description",
  "price": "15.00",
  "reorder_level": 5
}
```

Updates product details. It does not change stock or SKU.

### Record a sale

POST `/api/orders`

```json
{
  "items": [
    {
      "product_id": 1,
      "quantity": 3
    }
  ]
}
```

### View an order

GET `/api/orders/1`

Shows the order, its status, total and items.

## Error responses

- `404`: product or order not found.
- `409`: database rejected the operation, such as insufficient stock.
- `422`: invalid request fields.
- `503`: database connection unavailable or local demo disabled.

## Current status

This version is for local testing. Java authentication and employee permission checks are not connected yet.
Orders use a temporary employee ID and do not process payments or support refunds.
