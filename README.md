# E-Commerce Backend API

A RESTful backend API for an e-commerce platform built with **Python, FastAPI, and SQLite**.

The API supports product management, user registration, secure login, and authenticated order creation.

## 1. Technologies Used

- Python 3.12
- FastAPI — REST API framework
- SQLite — Database
- Pydantic — Request validation
- Argon2 — Password hashing
- JWT — Authentication
- Uvicorn — ASGI server

## 2. Project Structure

```text
app/
├── main.py
├── database.py
├── routes/
│   ├── products.py
│   ├── users.py
│   └── orders.py
├── schemas/
│   ├── product.py
│   ├── user.py
│   ├── auth.py
│   └── order.py
└── security/
    ├── password.py
    ├── token.py
    └── auth.py

tests/
.env
.gitignore
requirements.txt
README.md
```

## 3. Installation

Clone the repository and enter the project directory.

Create a virtual environment:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

## 4. Environment Configuration

Generate a secure secret key:

```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

Create a `.env` file in the project root:

```env
SECRET_KEY=replace_with_your_generated_secret
```

Replace the placeholder with the generated value.

**Security note:** Never commit your `.env` file or expose your secret key publicly.

## 5. Running the API

Start the development server:

```bash
python -m uvicorn app.main:app --reload
```

The API will be available at:

```text
http://127.0.0.1:8000
```

Interactive API documentation:

```text
http://127.0.0.1:8000/docs
```

The SQLite database is automatically initialized when the application starts.

## 6. API Endpoints

### Products

| Method | Endpoint | Description |
|---|---|---|
| GET | `/products` | Retrieve all products |
| GET | `/products/{product_id}` | Retrieve a product by ID |
| POST | `/products` | Create a new product |

Example request — Create a product:

```json
{
  "name": "Laptop",
  "description": "High-performance laptop",
  "price": 450000,
  "in_stock": true
}
```

### Users

| Method | Endpoint | Description |
|---|---|---|
| POST | `/users` | Register a new user |
| POST | `/users/login` | Authenticate a user |

Example request — Register:

```json
{
  "username": "samson",
  "password": "securepass123"
}
```

Example response:

```json
{
  "id": 1,
  "username": "samson"
}
```

Passwords are securely hashed before being stored.

Example request — Login:

```json
{
  "username": "samson",
  "password": "securepass123"
}
```

Example response:

```json
{
  "access_token": "your_generated_jwt_token",
  "token_type": "bearer"
}
```

### Orders

| Method | Endpoint | Description |
|---|---|---|
| POST | `/orders` | Create an order for an authenticated user |

Example request:

```json
{
  "product_id": 1,
  "quantity": 2
}
```

This endpoint requires a valid JWT access token.

Include the token in the request header:

```text
Authorization: Bearer YOUR_ACCESS_TOKEN
```

Example response:

```json
{
  "id": 1,
  "user_id": 1,
  "product_id": 1,
  "quantity": 2,
  "status": "Pending"
}
```

## 7. Authentication Workflow

To create an authenticated order:

1. Register an account using `POST /users`.
2. Log in using `POST /users/login`.
3. Copy the returned `access_token`.
4. Open the API documentation at `/docs`.
5. Use the Authorize option to provide your bearer token.
6. Submit an order using `POST /orders`.

Without a valid token, the order endpoint returns HTTP `401 Unauthorized`.

## 8. Input Validation and Security

The API implements the following security measures:

- Passwords are hashed using Argon2 before storage.
- JWT tokens are used to authenticate protected requests.
- Tokens expire after 30 minutes.
- Pydantic validates incoming request data.
- Product names and descriptions have length restrictions.
- Product prices must be greater than zero and finite.
- Usernames and passwords have length restrictions.
- Order quantities must be between 1 and 1000.
- SQL queries use parameterized values to reduce SQL injection risks.
- Duplicate usernames are rejected.
- Foreign key constraints protect relationships between users, products, and orders.

## 9. HTTP Status Codes

| Status Code | Meaning |
|---|---|
| 200 | Request successful |
| 201 | Resource successfully created |
| 400 | Invalid operation, such as ordering an out-of-stock product |
| 401 | Authentication required or invalid credentials |
| 404 | Requested product not found |
| 409 | Username already exists |
| 422 | Request validation failed |

## 10. Database

The application uses SQLite.

The database file, `ecommerce.db`, is created automatically when the application starts.

The database contains three tables:

- `users` — Stores registered users and hashed passwords.
- `products` — Stores product information.
- `orders` — Stores orders associated with users and products.

Sample products are inserted automatically when the products table is empty.