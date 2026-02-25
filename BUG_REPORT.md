# Ecommerce API - 5 Real Issues Workshop


## Issue #1: No Stock Validation

**File:** `controllers/orderController.js` → `createOrder` function

**Problem:**
```javascript
exports.createOrder = async (req, res) => {
  try {
    const { userId, items, shippingAddress } = req.body;

    let totalAmount = 0;
    const orderItems = [];

    for (const item of items) {
      const product = await Product.findById(item.productId);
      totalAmount += product.price * item.quantity;
      // NO CHECK IF PRODUCT HAS ENOUGH STOCK!
      orderItems.push({
        productId: item.productId,
        quantity: item.quantity,
        price: product.price
      });
    }
```

**What breaks:**
- Orders created for out-of-stock items
- Inventory oversold
- Fulfillment team can't complete orders
- Customer frustration and refunds
- Business loses money on impossible orders

**How to verify the bug:**
```bash
# Create product with only 3 in stock
POST /api/products
{
  "name": "iPhone 15",
  "price": 999,
  "stock": 3
}

# Try to order 100 units
POST /api/orders
{
  "userId": "user123",
  "items": [
    {
      "productId": "{productId}",
      "quantity": 100
    }
  ],
  "shippingAddress": "123 Main St"
}

# Succeeds despite insufficient stock!
```

---

## Issue #2: Create Order Errors

**File:** `controllers/orderController.js` → `createOrder` function

**Problem:**
```javascript
exports.createOrder = async (req, res) => {
  try {
    const { userId, items, shippingAddress } = req.body;
    // NO VALIDATION FOR REQUIRED FIELDS!

    let totalAmount = 0;
    const orderItems = [];

    for (const item of items) {
      const product = await Product.findById(item.productId);
      totalAmount += product.price * item.quantity;
      orderItems.push({
        productId: item.productId,
        quantity: item.quantity,
        price: product.price
      });
    }

    const order = new Order({
      userId: userId,
      items: orderItems,
      totalAmount: totalAmount,
      shippingAddress: shippingAddress
    });

    await order.save();
    res.status(201).json(order);
  } catch (error) {
    // GENERIC ERROR MESSAGE - UNHELPFUL!
    res.status(500).json({ message: 'Order creation failed' });
  }
};
```

**What breaks:**
- Missing userId not validated
- Invalid product IDs don't cause clear errors
- Generic error messages prevent debugging
- Customer-facing app has no idea what failed
- Support team can't help customers

**How to verify the bug:**
```bash
# Missing userId - should fail validation
POST /api/orders
{
  "items": [
    {
      "productId": "{productId}",
      "quantity": 1
    }
  ],
  "shippingAddress": "123 Main St"
}
# Saves successfully! Should reject.

# Invalid product ID - vague error
POST /api/orders
{
  "userId": "user123",
  "items": [
    {
      "productId": "not_a_valid_id",
      "quantity": 1
    }
  ],
  "shippingAddress": "123 Main St"
}
# Returns "Order creation failed" - no details about why
```

---

## Issue #3: Get Orders Returns All Orders

**File:** `controllers/orderController.js` → `getUserOrders` function

**Problem:**
```javascript
exports.getUserOrders = async (req, res) => {
  try {
    const userId = req.params.userId;
    const orders = await Order.find();  // NO FILTER BY USER!
    res.json(orders);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};
```

**What breaks:**
- CRITICAL SECURITY BREACH: User sees ALL orders from ALL customers
- Customer can view private orders of others
- Personal information exposed
- Payment details visible to unauthorized users
- Violates data privacy (GDPR, etc.)
- Legal liability

**How to verify the bug:**
```bash
# Create orders for different users
POST /api/orders (user_alice)
POST /api/orders (user_bob)
POST /api/orders (user_charlie)

# Now fetch alice's orders
GET /api/orders/user/user_alice

# Response includes ALL orders from ALL users!
# user_bob and user_charlie's orders exposed to user_alice
```

---

## Issue #4: Update Order Clears Fields

**File:** `controllers/orderController.js` → `updateOrder` function

**Problem:**
```javascript
exports.updateOrder = async (req, res) => {
  try {
    const orderId = req.params.id;
    
    const order = await Order.findByIdAndUpdate(
      orderId,
      {
        shippingAddress: req.body.shippingAddress,
        status: req.body.status
        // MISSING: items, totalAmount, userId all become undefined!
      },
      { new: true }
    );

    if (!order) {
      return res.status(404).json({ message: 'Order not found' });
    }
    
    res.json(order);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};
```

**What breaks:**
- Order items disappear when updating address
- Total amount is cleared
- Complete order data corrupted
- Refund/replacement impossible without data
- Customer service can't know what was ordered
- Business loses order history

**How to verify the bug:**
```bash
# Create order with items and total
POST /api/orders
{
  "userId": "user123",
  "items": [
    {
      "productId": "{productId}",
      "quantity": 2
    }
  ],
  "shippingAddress": "123 Old St",
  "totalAmount": 1998
}

# Check order - has items and totalAmount
GET /api/orders/{orderId}

# Update only address
PUT /api/orders/{orderId}
{
  "shippingAddress": "456 New St"
}

# Check again - items array is now empty!
# totalAmount is now undefined!
GET /api/orders/{orderId}
```

---

## Issue #5: Update Status Missing Enum Validation

**File:** `controllers/orderController.js` → `updateOrderStatus` function

**Problem:**
```javascript
exports.updateOrderStatus = async (req, res) => {
  try {
    const orderId = req.params.id;
    const newStatus = req.body.status;
    // VALID STATUSES: ['pending', 'confirmed', 'shipped', 'delivered', 'cancelled']
    // BUT NO VALIDATION THAT STATUS IS IN ENUM!

    const order = await Order.findByIdAndUpdate(
      orderId,
      { status: newStatus },  // Accepts ANY value!
      { new: true }
    );

    if (!order) {
      return res.status(404).json({ message: 'Order not found' });
    }

    res.json(order);
  } catch (error) {
    // GENERIC ERROR - doesn't explain enum constraint
    res.status(400).json({ message: 'Failed to update status' });
  }
};
```

**What breaks:**
- Invalid status values saved (UPPERCASE, typos, etc.)
- Downstream systems expect specific values and break
- Tracking pages show meaningless status
- Inventory management confused
- Reports and analytics broken
- Status history unreliable

**How to verify the bug:**
```bash
# Try uppercase status (should fail)
PATCH /api/orders/{orderId}/status
{
  "status": "CONFIRMED"
}
# Saves! Should only accept lowercase 'confirmed'

# Try non-existent status
PATCH /api/orders/{orderId}/status
{
  "status": "in_transit"
}
# Saves! Not in valid enum list

# Try another invalid status
PATCH /api/orders/{orderId}/status
{
  "status": "completed"
}
# Saves! Should be 'delivered' instead
```

---

## Summary

| Issue | Type | Severity | Fix Difficulty |
|-------|------|----------|-----------------|
| #1 Stock Validation | Business Logic | Critical | Medium |
| #2 Order Creation Errors | Input Validation | High | Easy |
| #3 Privacy Leak | Security | Critical | Easy |
| #4 Field Cleared | Data Integrity | Critical | Medium |
| #5 Enum Validation | Data Quality | High | Easy |

---

## Quick Test Checklist

- [ ] Try ordering more items than stock available
- [ ] Create order without userId and check error message
- [ ] Create orders for 2 users, fetch one user - see if other's orders leak
- [ ] Update order address only - check if items still exist
- [ ] Update order status to lowercase/UPPERCASE/invalid - see what happens

---

## Setup

```bash
npm install
cp .env.example .env
npm run dev
```

API runs on `http://localhost:5000`

**All 5 issues are live and ready to fix!**
