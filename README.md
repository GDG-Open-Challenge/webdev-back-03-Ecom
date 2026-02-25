# Ecommerce API

A RESTful ecommerce API built with **Express.js** and **MongoDB**.

## Setup Instructions

### Prerequisites
- Node.js (v14+)
- MongoDB

### Installation

1. Install dependencies:
```bash
npm install
```

2. Configure `.env`:
```
MONGODB_URI=mongodb://localhost:27017/ecommerce
PORT=5000
NODE_ENV=development
```

3. Start MongoDB:
```bash
docker run -d -p 27017:27017 --name mongodb mongo
```

4. Run the server:
```bash
npm start
# or for development with auto-reload
npm run dev
```

Available at `http://localhost:5000`

---

## API Endpoints

### Products
- `GET /api/products`
- `GET /api/products/:id`
- `POST /api/products`
- `PUT /api/products/:id`
- `DELETE /api/products/:id`

### Orders
- `POST /api/orders`
- `GET /api/orders/user/:userId`
- `GET /api/orders/:id`
- `PUT /api/orders/:id`
- `PATCH /api/orders/:id/status`
- `DELETE /api/orders/:id`

---

## Project Structure

```
ecommerce-api/
├── models/
│   ├── User.js
│   ├── Product.js
│   └── Order.js
├── controllers/
│   ├── productController.js
│   └── orderController.js
├── routes/
│   ├── productRoutes.js
│   └── orderRoutes.js
├── config/
│   └── database.js
├── server.js
├── .env
└── package.json
```

---

## License

MIT
