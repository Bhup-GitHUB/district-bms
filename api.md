# API Documentation - District Backend

Complete API reference with request/response examples for the Movie Ticket Booking Platform.

## Base URL
```
Development: http://localhost:8080/api/v1
Production: https://api.district.com/api/v1
```

## Authentication
Most endpoints require authentication via JWT token in the Authorization header:
```
Authorization: Bearer <your_jwt_token>
```

---

## 1. Authentication Endpoints

### 1.1 User Registration
**POST** `/auth/signup`

**Request Body:**
```json
{
  "email": "john@example.com",
  "phone": "+919876543210",
  "password": "SecurePass123!",
  "first_name": "John",
  "last_name": "Doe"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "john@example.com",
    "phone": "+919876543210",
    "first_name": "John",
    "last_name": "Doe",
    "created_at": "2025-11-01T10:30:00Z"
  }
}
```

### 1.2 User Login
**POST** `/auth/login`

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "john@example.com",
      "first_name": "John",
      "last_name": "Doe"
    },
    "token": {
      "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "expires_in": 86400
    }
  }
}
```

### 1.3 Refresh Token
**POST** `/auth/refresh-token`

**Request Body:**
```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400
  }
}
```

---

## 2. Movie Endpoints

### 2.1 Get All Movies
**GET** `/movies?city_id={city_id}&language={lang}&genre={genre}&page={page}&limit={limit}`

**Query Parameters:**
- `city_id` (optional): Filter by city
- `language` (optional): Filter by language (English, Hindi, etc.)
- `genre` (optional): Filter by genre (Action, Comedy, etc.)
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "movies": [
      {
        "id": "660e8400-e29b-41d4-a716-446655440000",
        "title": "Inception",
        "description": "A mind-bending thriller",
        "duration_minutes": 148,
        "language": "English",
        "genres": ["Action", "Sci-Fi", "Thriller"],
        "rating": 8.8,
        "certificate": "PG-13",
        "release_date": "2024-07-16",
        "poster_url": "https://cdn.district.com/posters/inception.jpg",
        "trailer_url": "https://youtube.com/watch?v=...",
        "cast": [
          {
            "name": "Leonardo DiCaprio",
            "role": "Cobb"
          }
        ],
        "available_theaters_count": 25
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 150,
      "total_pages": 8
    }
  }
}
```

### 2.2 Get Movie Details
**GET** `/movies/{movie_id}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "660e8400-e29b-41d4-a716-446655440000",
    "title": "Inception",
    "synopsis": "A detailed synopsis of the movie...",
    "duration_minutes": 148,
    "language": "English",
    "genres": ["Action", "Sci-Fi", "Thriller"],
    "rating": 8.8,
    "certificate": "PG-13",
    "release_date": "2024-07-16",
    "poster_url": "https://cdn.district.com/posters/inception.jpg",
    "trailer_url": "https://youtube.com/watch?v=...",
    "cast": [
      {
        "name": "Leonardo DiCaprio",
        "role": "Cobb",
        "image_url": "https://cdn.district.com/cast/leo.jpg"
      }
    ],
    "crew": [
      {
        "name": "Christopher Nolan",
        "role": "Director"
      }
    ],
    "reviews_count": 1250,
    "average_rating": 4.5
  }
}
```

### 2.3 Search Movies
**GET** `/movies/search?q={query}&city_id={city_id}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "movies": [
      {
        "id": "660e8400-e29b-41d4-a716-446655440000",
        "title": "Inception",
        "poster_url": "https://cdn.district.com/posters/inception.jpg",
        "language": "English",
        "rating": 8.8
      }
    ]
  }
}
```

---

## 3. Theater & Show Endpoints

### 3.1 Get Cities
**GET** `/cities`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "cities": [
      {
        "id": "770e8400-e29b-41d4-a716-446655440000",
        "name": "Mumbai",
        "state": "Maharashtra",
        "country": "India",
        "theater_count": 85
      }
    ]
  }
}
```

### 3.2 Get Theaters by City
**GET** `/theaters?city_id={city_id}&movie_id={movie_id}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "theaters": [
      {
        "id": "880e8400-e29b-41d4-a716-446655440000",
        "name": "PVR Phoenix",
        "address": "High Street Phoenix, Lower Parel, Mumbai",
        "latitude": 19.0176,
        "longitude": 72.8562,
        "facilities": ["Parking", "Food Court", "3D", "IMAX"],
        "distance_km": 2.5,
        "screens": [
          {
            "screen_id": "990e8400-e29b-41d4-a716-446655440000",
            "screen_name": "Audi 1",
            "screen_type": "IMAX",
            "total_seats": 250
          }
        ]
      }
    ]
  }
}
```

### 3.3 Get Shows for Movie
**GET** `/shows?movie_id={movie_id}&city_id={city_id}&date={YYYY-MM-DD}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "shows": [
      {
        "id": "aa0e8400-e29b-41d4-a716-446655440000",
        "movie_id": "660e8400-e29b-41d4-a716-446655440000",
        "theater_name": "PVR Phoenix",
        "screen_name": "Audi 1",
        "show_time": "2025-11-01T14:30:00Z",
        "show_date": "2025-11-01",
        "screen_type": "IMAX",
        "available_seats": 180,
        "total_seats": 250,
        "base_price": 350.00,
        "status": "AVAILABLE"
      }
    ]
  }
}
```

### 3.4 Get Seat Layout
**GET** `/shows/{show_id}/seats`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "show_id": "aa0e8400-e29b-41d4-a716-446655440000",
    "screen_name": "Audi 1",
    "total_seats": 250,
    "available_seats": 180,
    "seat_layout": {
      "rows": [
        {
          "row_name": "A",
          "seats": [
            {
              "seat_id": "bb0e8400-e29b-41d4-a716-446655440001",
              "seat_number": "A1",
              "seat_type": "GOLD",
              "price": 450.00,
              "status": "AVAILABLE"
            },
            {
              "seat_id": "bb0e8400-e29b-41d4-a716-446655440002",
              "seat_number": "A2",
              "seat_type": "GOLD",
              "price": 450.00,
              "status": "BOOKED"
            }
          ]
        }
      ]
    },
    "pricing": [
      {
        "seat_type": "GOLD",
        "price": 450.00
      },
      {
        "seat_type": "SILVER",
        "price": 350.00
      },
      {
        "seat_type": "REGULAR",
        "price": 250.00
      }
    ]
  }
}
```

---

## 4. Booking Endpoints

### 4.1 Hold Seats (Temporary Booking)
**POST** `/bookings/hold`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "show_id": "aa0e8400-e29b-41d4-a716-446655440000",
  "seat_ids": [
    "bb0e8400-e29b-41d4-a716-446655440001",
    "bb0e8400-e29b-41d4-a716-446655440002"
  ]
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Seats held successfully for 10 minutes",
  "data": {
    "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
    "booking_reference": "BK20251101001",
    "show_id": "aa0e8400-e29b-41d4-a716-446655440000",
    "seats": [
      {
        "seat_number": "A1",
        "seat_type": "GOLD",
        "price": 450.00
      },
      {
        "seat_number": "A2",
        "seat_type": "GOLD",
        "price": 450.00
      }
    ],
    "subtotal": 900.00,
    "convenience_fee": 50.00,
    "gst": 142.50,
    "total_amount": 1092.50,
    "hold_expires_at": "2025-11-01T10:40:00Z",
    "status": "PENDING"
  }
}
```

### 4.2 Get Booking Details
**GET** `/bookings/{booking_id}`

**Headers:**
```
Authorization: Bearer <token>
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
    "booking_reference": "BK20251101001",
    "status": "CONFIRMED",
    "movie": {
      "title": "Inception",
      "poster_url": "https://cdn.district.com/posters/inception.jpg"
    },
    "theater": {
      "name": "PVR Phoenix",
      "address": "High Street Phoenix, Lower Parel, Mumbai"
    },
    "screen": "Audi 1",
    "show_time": "2025-11-01T14:30:00Z",
    "seats": [
      {
        "seat_number": "A1",
        "seat_type": "GOLD"
      }
    ],
    "total_amount": 1092.50,
    "booking_date": "2025-11-01T10:30:00Z",
    "qr_code": "https://api.district.com/bookings/cc0e8400.../qr"
  }
}
```

### 4.3 Cancel Booking
**DELETE** `/bookings/{booking_id}`

**Headers:**
```
Authorization: Bearer <token>
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Booking cancelled successfully",
  "data": {
    "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
    "refund_amount": 1000.00,
    "refund_status": "INITIATED",
    "refund_eta": "3-5 business days"
  }
}
```

---

## 5. Payment Endpoints

### 5.1 Initiate Payment
**POST** `/payments/initiate`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
  "payment_method": "CARD"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "payment_id": "dd0e8400-e29b-41d4-a716-446655440000",
    "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
    "amount": 1092.50,
    "currency": "INR",
    "payment_gateway": "RAZORPAY",
    "gateway_order_id": "order_NEjabcdefghij",
    "redirect_url": "https://checkout.razorpay.com/...",
    "callback_url": "https://api.district.com/api/v1/payments/callback"
  }
}
```

### 5.2 Payment Callback (Webhook)
**POST** `/payments/webhook`

This endpoint is called by the payment gateway. User should not call this directly.

### 5.3 Verify Payment Status
**GET** `/payments/{payment_id}`

**Headers:**
```
Authorization: Bearer <token>
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "payment_id": "dd0e8400-e29b-41d4-a716-446655440000",
    "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
    "status": "SUCCESS",
    "amount": 1092.50,
    "payment_method": "CARD",
    "transaction_id": "txn_NEjabcdefghij",
    "completed_at": "2025-11-01T10:35:00Z"
  }
}
```

---

## 6. User Profile Endpoints

### 6.1 Get User Profile
**GET** `/users/profile`

**Headers:**
```
Authorization: Bearer <token>
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "john@example.com",
    "phone": "+919876543210",
    "first_name": "John",
    "last_name": "Doe",
    "email_verified": true,
    "phone_verified": true,
    "preferences": {
      "preferred_location": "Mumbai",
      "preferred_languages": ["English", "Hindi"],
      "preferred_genres": ["Action", "Thriller"],
      "email_notifications": true,
      "sms_notifications": true
    },
    "created_at": "2025-01-15T10:30:00Z"
  }
}
```

### 6.2 Update User Profile
**PUT** `/users/profile`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "preferences": {
    "preferred_location": "Mumbai",
    "preferred_languages": ["English", "Hindi"],
    "email_notifications": true
  }
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "first_name": "John",
    "last_name": "Doe"
  }
}
```

### 6.3 Get User Bookings
**GET** `/users/bookings?status={status}&page={page}&limit={limit}`

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `status` (optional): Filter by status (CONFIRMED, CANCELLED, EXPIRED)
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "bookings": [
      {
        "booking_id": "cc0e8400-e29b-41d4-a716-446655440000",
        "booking_reference": "BK20251101001",
        "movie_title": "Inception",
        "theater_name": "PVR Phoenix",
        "show_time": "2025-11-01T14:30:00Z",
        "total_amount": 1092.50,
        "status": "CONFIRMED",
        "booked_at": "2025-11-01T10:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 25
    }
  }
}
```

---

## 7. Review Endpoints

### 7.1 Add Movie Review
**POST** `/movies/{movie_id}/reviews`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "rating": 5,
  "review_text": "Absolutely mind-blowing! A masterpiece of modern cinema."
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Review added successfully",
  "data": {
    "review_id": "ee0e8400-e29b-41d4-a716-446655440000",
    "movie_id": "660e8400-e29b-41d4-a716-446655440000",
    "rating": 5,
    "review_text": "Absolutely mind-blowing! A masterpiece of modern cinema.",
    "created_at": "2025-11-01T10:30:00Z"
  }
}
```

---

## Error Responses

### Standard Error Format
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": {}
  }
}
```

### Common Error Codes
- `400` - Bad Request (Validation errors)
- `401` - Unauthorized (Invalid/expired token)
- `403` - Forbidden (Insufficient permissions)
- `404` - Not Found
- `409` - Conflict (e.g., seats already booked)
- `422` - Unprocessable Entity
- `429` - Too Many Requests (Rate limit exceeded)
- `500` - Internal Server Error

### Example Error Response
```json
{
  "success": false,
  "error": {
    "code": "SEATS_UNAVAILABLE",
    "message": "Selected seats are no longer available",
    "details": {
      "unavailable_seats": ["A1", "A2"]
    }
  }
}
```

---

## Rate Limiting

- **Anonymous requests**: 100 requests per hour
- **Authenticated requests**: 1000 requests per hour
- **Booking endpoints**: 10 requests per minute per user

Rate limit headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1698825600
```

---

## Pagination

All list endpoints support pagination:
```
?page=1&limit=20
```

Response includes pagination metadata:
```json
{
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "total_pages": 8,
    "has_next": true,
    "has_prev": false
  }
}
```

---

## Webhooks

### Payment Gateway Webhook
Your payment gateway will call this endpoint:
```
POST /api/v1/payments/webhook
```

Ensure your server can receive POST requests from:
- Stripe: `*.stripe.com`
- Razorpay: `*.razorpay.com`

---

## Testing

### Postman Collection
Import the Postman collection from `/docs/postman/district-api.json`

### Test Credentials
```
Email: test@district.com
Password: Test@123
```

---

For more information, visit our [GitHub Repository](https://github.com/yourusername/district-backend)
