# Frush Admin System - Simplified Schema & Flow Documentation

## Table of Contents

1. [Database Schema](#database-schema)
2. [API Routes](#api-routes)
3. [Simplified Order Flow](#simplified-order-flow)
4. [Authentication Flow](#authentication-flow)
5. [Firebase Storage Structure](#firebase-storage-structure)

## Database Schema

### New Collections

#### 1. kitchens

```javascript
{
  id: "auto_generated_kitchen_id", // Firebase auto-generated
  name: "Kitchen Name",
  details: {
    contactNumber: "+919876543210",
    email: "kitchen@example.com", 
    address: "Complete kitchen address",
    city: "Mumbai",
    state: "Maharashtra", 
    pincode: "400001",
    latitude: 19.0760,
    longitude: 72.8777,
    capacity: 100,
    isActive: true,
    operatingHours: {
      open: "06:00",
      close: "23:00"
    }
  },
  settings: {
    autoAcceptOrders: true,
    autoAcceptTimeoutMinutes: 15,
    enableNotifications: true,
    deliveryRadius: 10 // km
  },
  createdAt: "2025-08-29T00:00:00.000Z",
  updatedAt: "2025-08-29T00:00:00.000Z", 
  createdBy: "superadmin_user_id"
}
```

#### 2. kitchen_admins

```javascript
{
  id: "auto_generated_admin_id",
  email: "admin@gmail.com",
  kitchenIds: ["kitchen_id_1", "kitchen_id_2"], // Array for multiple kitchens
  role: "kitchen_admin",
  permissions: {
    manageOrders: true,
    manageProducts: true,
    manageCoupons: true,
    viewStatistics: true
  },
  isActive: true,
  assignedAt: "2025-08-29T00:00:00.000Z",
  assignedBy: "superadmin_user_id",
  lastLoginAt: "2025-08-29T00:00:00.000Z"
}
```

#### 3. superadmin_users

```javascript
{
  id: "auto_generated_superadmin_id",
  email: "superadmin@frush.com",
  name: "Super Admin Name", 
  role: "superadmin",
  permissions: {
    manageKitchens: true,
    manageAdmins: true,
    viewAllStats: true,
    systemSettings: true
  },
  isActive: true,
  createdAt: "2025-08-29T00:00:00.000Z"
}
```

### Enhanced Existing Collections

#### 1. products (Enhanced)

```javascript
{
  id: "auto_generated_product_id",
  kitchenId: "kitchen_unique_id", // NEW FIELD - Links to specific kitchen
  name: "Product Name",
  description: "Product description", 
  price: 199,
  originalPrice: 249,
  discount: 20,
  category: "Eggstasy",
  image: "https://storage.url/kitchenId/images/product.jpg",
  isVeg: true,
  availableTime: "All day",
  stockQuantity: 50,
  showInMenu: true,
  rating: 4.5,
  variants: [
    {
      id: "variant_id",
      name: "Large",
      description: "Large size", 
      price: 249,
      image: "variant_image_url",
      active: true
    }
  ],
  addOns: [
    {
      id: "addon_id",
      name: "Extra Cheese",
      price: 30,
      active: true
    }
  ],
  isActive: true,
  createdAt: "2025-08-29T00:00:00.000Z",
  updatedAt: "2025-08-29T00:00:00.000Z"
}
```

#### 2. orders (Simplified - Product References Only)

```javascript
{
  id: "auto_generated_order_id",
  kitchenId: "kitchen_unique_id",
  userId: "user_id",
  status: "pending", // pending, confirmed, preparing, out_for_delivery, delivered, cancelled, rejected
  
  // SIMPLIFIED: Store product references instead of full details
  items: [
    {
      productId: "product_id", // Reference to products collection
      quantity: 2,
      selectedVariantId: "variant_id", // Optional - if variant selected
      selectedAddOnIds: ["addon_id_1", "addon_id_2"], // Optional - selected add-ons
      unitPrice: 199, // Price at time of order (for historical accuracy)
      totalPrice: 398 // quantity * unitPrice (+ variant/addon prices)
    }
  ],
  
  pricing: {
    subtotal: 398,
    deliveryFee: 30,
    taxes: 38,
    couponDiscount: 20,
    couponCode: "WELCOME20", // Optional - if coupon applied
    finalTotal: 446
  },
  
  paymentMethod: "upi", // upi, card, wallet, cod
  paymentStatus: "completed", // pending, completed, failed, refunded
  
  address: {
    fullAddress: "Delivery address",
    latitude: 19.0760,
    longitude: 72.8777
  },
  
  customerInfo: {
    name: "Customer Name",
    phone: "+919876543210", 
    email: "customer@example.com"
  },
  
  delivery: {
    estimatedTimeMinutes: 45,
    actualDeliveryTime: null, // Set when delivered
    deliveryPersonId: null // Optional - for future delivery tracking
  },
  
  timestamps: {
    orderPlaced: "2025-08-29T00:00:00.000Z",
    paymentCompleted: "2025-08-29T00:00:30.000Z",
    orderConfirmed: null,
    preparationStarted: null,
    outForDelivery: null,
    delivered: null
  },
  
  notes: {
    customerNotes: "Extra spicy", // Optional
    kitchenNotes: "Customer called - deliver by 8 PM", // Optional
    adminNotes: "VIP customer" // Optional
  },
  
  createdAt: "2025-08-29T00:00:00.000Z",
  updatedAt: "2025-08-29T00:00:00.000Z"
}
```

#### 3. coupons (Enhanced)

```javascript
{
  id: "auto_generated_coupon_id",
  kitchenId: "kitchen_unique_id", // Kitchen-specific coupons
  code: "WELCOME20",
  description: "20% off on first order",
  discountType: "percentage", // percentage, fixed
  discount: 20,
  minOrderValue: 200,
  maxUses: 100,
  userMaxUses: 1,
  useCount: 5,
  active: true,
  
  // Track which users have used this coupon
  userUses: {
    "user_id_1": 1,
    "user_id_2": 1
  },
  
  expiryDate: "2025-12-31T23:59:59.000Z",
  createdAt: "2025-08-29T00:00:00.000Z",
  updatedAt: "2025-08-29T00:00:00.000Z"
}
```

#### 4. orderStatuses (Simplified)

```javascript
{
  id: "auto_generated_status_id", 
  orderId: "order_id", // Reference to orders collection
  kitchenId: "kitchen_unique_id",
  currentStatus: "pending", // Current status
  
  statusHistory: [
    {
      status: "pending",
      statusText: "Order Placed", 
      timestamp: "2025-08-29T00:00:00.000Z",
      updatedBy: "system" // system, admin_email, auto
    },
    {
      status: "confirmed",
      statusText: "Order Confirmed", 
      timestamp: "2025-08-29T00:05:00.000Z",
      updatedBy: "admin@kitchen.com"
    }
  ],
  
  createdAt: "2025-08-29T00:00:00.000Z",
  lastUpdated: "2025-08-29T00:05:00.000Z"
}
```

#### 5. payments (Simplified)

```javascript
{
  id: "auto_generated_payment_id",
  orderId: "order_id", // Reference to orders collection
  kitchenId: "kitchen_unique_id",
  userId: "user_id",
  
  amount: 44600, // in paise (₹446.00)
  currency: "INR",
  method: "upi", // upi, netbanking, card, wallet
  status: "captured", // pending, captured, failed, refunded
  
  // Payment gateway details
  paymentGatewayId: "razorpay_payment_id",
  paymentGateway: "razorpay", // razorpay, payu, stripe, etc.
  gatewayResponse: {
    signature: "payment_signature",
    bank: "HDFC",
    vpa: "user@paytm",
    fee: 710, // gateway fee in paise
    tax: 108  // tax on fee in paise
  },
  
  timestamps: {
    initiated: "2025-08-29T00:00:00.000Z",
    completed: "2025-08-29T00:00:30.000Z" // null if failed/pending
  },
  
  createdAt: "2025-08-29T00:00:00.000Z"
}
```

### Existing Collections (No changes needed)

1. **users** - Customer profiles (unchanged)
2. **mail** - Email notifications (unchanged)
3. **service_notifications** - Service alerts (unchanged)
4. **unavailable_items** - Out of stock items (unchanged)

## API Routes

### Superadmin Routes

```javascript
// Authentication
POST /api/superadmin/auth/login
POST /api/superadmin/auth/logout

// Kitchen Management
GET  /api/superadmin/kitchens
POST /api/superadmin/kitchens
PUT  /api/superadmin/kitchens/:id
DELETE /api/superadmin/kitchens/:id

// Admin Management  
GET  /api/superadmin/kitchen-admins
POST /api/superadmin/kitchen-admins
PUT  /api/superadmin/kitchen-admins/:id
DELETE /api/superadmin/kitchen-admins/:id

// Analytics
GET  /api/superadmin/statistics/overview
GET  /api/superadmin/statistics/kitchen/:kitchenId
```

### Kitchen Admin Routes

```javascript
// Authentication
POST /api/kitchen-admin/auth/login  
GET  /api/kitchen-admin/auth/profile

// Orders with product details populated
GET  /api/kitchen-admin/orders/:kitchenId
GET  /api/kitchen-admin/orders/:kitchenId/:orderId/details // Populate product details
PUT  /api/kitchen-admin/orders/:kitchenId/:orderId/status
GET  /api/kitchen-admin/orders/:kitchenId/pending
GET  /api/kitchen-admin/orders/:kitchenId/today

// Products
GET  /api/kitchen-admin/products/:kitchenId
POST /api/kitchen-admin/products/:kitchenId
PUT  /api/kitchen-admin/products/:kitchenId/:productId
DELETE /api/kitchen-admin/products/:kitchenId/:productId

// Coupons
GET  /api/kitchen-admin/coupons/:kitchenId
POST /api/kitchen-admin/coupons/:kitchenId
PUT  /api/kitchen-admin/coupons/:kitchenId/:couponId
DELETE /api/kitchen-admin/coupons/:kitchenId/:couponId

// Statistics
GET  /api/kitchen-admin/statistics/:kitchenId

// Unavailable Items
GET  /api/kitchen-admin/unavailable-items/:kitchenId
POST /api/kitchen-admin/unavailable-items/:kitchenId
PUT  /api/kitchen-admin/unavailable-items/:kitchenId/:itemId
DELETE /api/kitchen-admin/unavailable-items/:kitchenId/:itemId
```

## Simplified Order Flow

### Phase 1: Order Creation (Mobile App)

1. User browses products (filtered by kitchenId based on location)
2. User adds items to cart
3. User clicks "Place Order"
   ↓
4. User completes payment via gateway
   ↓
5. **On PAYMENT SUCCESS:**
   - CREATE entry in orders collection with productId references
   - CREATE entry in payments collection  
   - CREATE entry in orderStatuses collection
   - UPDATE product stock quantities
   - UPDATE coupon usage (if coupon applied)
   ↓
6. **On PAYMENT FAILURE:**
   - Show error to user, allow retry
   - No database entries created

### Phase 2: Order Management (Kitchen Admin)

7. Order appears in kitchen admin dashboard
   ↓
8. Admin can:
   - View order details (API populates product details from productId references)
   - Accept/Confirm order → UPDATE order status to "confirmed"
   - Reject order → UPDATE status to "rejected" + initiate refund
   - Update status through fulfillment: preparing → out_for_delivery → delivered
   ↓
9. **Each status update:**
   - UPDATES orders.status and orders.timestamps
   - ADDS entry to orderStatuses.statusHistory array
   - SENDS notification to user (mobile app)

## Data Relationships & Queries

### Getting Complete Order Details

```javascript
// When admin views order details, populate product information:
async function getOrderWithProductDetails(orderId) {
  const order = await getOrder(orderId);
  
  // Populate each item with product details
  const itemsWithDetails = await Promise.all(
    order.items.map(async (item) => {
      const product = await getProduct(item.productId);
      
      // Get variant details if selected
      const variant = item.selectedVariantId ? 
        product.variants.find(v => v.id === item.selectedVariantId) : null;
      
      // Get add-on details if selected
      const addOns = item.selectedAddOnIds ? 
        product.addOns.filter(ao => item.selectedAddOnIds.includes(ao.id)) : [];
      
      return {
        ...item,
        productDetails: {
          name: product.name,
          description: product.description,
          image: product.image,
          isVeg: product.isVeg,
          category: product.category
        },
        variantDetails: variant,
        addOnDetails: addOns
      };
    })
  );
  
  return {
    ...order,
    items: itemsWithDetails
  };
}
```

### Kitchen-scoped Queries

```javascript
// All kitchen admin operations are scoped by kitchenId
const getKitchenOrders = (kitchenId) => 
  query(orders, where('kitchenId', '==', kitchenId));

const getKitchenProducts = (kitchenId) => 
  query(products, where('kitchenId', '==', kitchenId));

const getKitchenCoupons = (kitchenId) => 
  query(coupons, where('kitchenId', '==', kitchenId));
```

### Statistics Queries

```javascript
// Kitchen performance metrics
async function getKitchenStats(kitchenId, dateRange) {
  const ordersQuery = query(
    orders, 
    where('kitchenId', '==', kitchenId),
    where('createdAt', '>=', dateRange.start),
    where('createdAt', '<=', dateRange.end)
  );
  
  const orders = await getDocs(ordersQuery);
  
  return {
    totalOrders: orders.size,
    totalRevenue: orders.docs.reduce((sum, doc) => sum + doc.data().pricing.finalTotal, 0),
    averageOrderValue: totalRevenue / totalOrders,
    statusBreakdown: {
      pending: orders.docs.filter(doc => doc.data().status === 'pending').length,
      confirmed: orders.docs.filter(doc => doc.data().status === 'confirmed').length,
      delivered: orders.docs.filter(doc => doc.data().status === 'delivered').length,
    }
  };
}
```

## Authentication Flow

### Superadmin Authentication

1. User visits `/superadmin/login`
2. Gmail OAuth login
3. Check email in `superadmin_users` collection
4. If authorized → redirect to `/superadmin/dashboard`
5. If not authorized → show "Access Denied"

### Kitchen Admin Authentication

1. User visits `/kitchen-admin/login`
2. Gmail OAuth login 
3. Query `kitchen_admins` collection by email
4. If found:
   - Get `kitchenIds` array
   - If single kitchen → redirect to `/kitchen-admin/dashboard/:kitchenId`
   - If multiple kitchens → show kitchen selection page
5. If not found → show "Access Denied"

## Firebase Storage Structure

```
/storage/
├── products/
│   ├── kitchen_id_1/
│   │   ├── images/
│   │   │   ├── 1693123456-product1.jpg
│   │   │   └── variants/
│   │   │       └── 1693124000-variant1.jpg
│   │   └── documents/
│   ├── kitchen_id_2/
│   └── ...
└── system/
    ├── logos/
    └── banners/
```

## Key Benefits of Simplified Schema

### 1. Data Normalization
- Product details stored once in products collection
- Orders reference products by ID, avoiding data duplication
- Changes to product info don't affect historical orders (prices preserved)

### 2. Simplified Flow
- No complex queue system
- Direct payment → order creation
- Cleaner status management

### 3. Better Performance
- Smaller order documents
- Efficient queries with proper indexing
- Easy to populate product details when needed

### 4. Maintainable Relationships
- `orders.items[].productId` → `products.id`
- `orders.kitchenId` → `kitchens.id`  
- `orders.userId` → `users.id`
- `payments.orderId` → `orders.id`
- `orderStatuses.orderId` → `orders.id`
- `kitchen_admins.kitchenIds[]` → `kitchens.id`

### 5. Flexible Pricing
- Historical price accuracy (unitPrice stored in order)
- Support for dynamic pricing, variants, and add-ons
- Coupon tracking without data duplication

---

**Note:** This simplified schema removes the complexity of order queues and failed order tracking while maintaining all essential functionality and relationships. The product reference system keeps data normalized while preserving historical accuracy for pricing and order details.