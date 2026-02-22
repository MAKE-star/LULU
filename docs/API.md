# LULU API Documentation

## Base URL
```
https://api.lulu-platform.io/v1
```

## Authentication
All API requests require a Bearer token in the Authorization header:
```
Authorization: Bearer <your_token>
```

## Endpoints

### Workers

#### GET /workers
List all available workers with filtering.

**Query Parameters:**
- `category` (string): Filter by service category
- `location` (string): Filter by city/area
- `min_rating` (float): Minimum rating (0-5)
- `available_now` (boolean): Only show currently available workers

**Response:**
```json
{
  "status": "success",
  "data": [
    {
      "id": "worker_123",
      "name": "Chef Adebayo",
      "category": "chef",
      "rating": 4.9,
      "jobs_completed": 127,
      "hourly_rate": 20000,
      "currency": "NGN",
      "location": {
        "city": "Lagos",
        "area": "Lekki"
      },
      "verification_status": {
        "identity": true,
        "skills": true,
        "background_check": true
      },
      "services": ["Nigerian Cuisine", "Continental", "Events"]
    }
  ]
}
```

### Bookings

#### POST /bookings
Create a new booking.

**Request Body:**
```json
{
  "worker_id": "worker_123",
  "service_type": "chef",
  "scheduled_time": "2026-03-01T15:00:00Z",
  "duration_hours": 4,
  "address": "123 Lekki Phase 1, Lagos",
  "notes": "Birthday dinner for 10 guests",
  "payment_method": "wallet"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "booking_id": "book_456",
    "handshake_code": "8821",
    "status": "pending",
    "total_amount": 80000,
    "currency": "NGN",
    "escrow_status": "held"
  }
}
```

### Handshake

#### POST /handshake/verify
Verify the 4-digit handshake code.

**Request Body:**
```json
{
  "booking_id": "book_456",
  "code": "8821",
  "role": "hirer"  // or "worker"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "verified": true,
    "both_parties_confirmed": true,
    "job_status": "started",
    "started_at": "2026-03-01T15:05:00Z"
  }
}
```

### Payments

#### POST /payments/release
Release payment from escrow after job completion.

**Request Body:**
```json
{
  "booking_id": "book_456",
  "rating": 5,
  "review": "Excellent service!"
}
```

## Webhooks

Subscribe to real-time events:

- `booking.created`
- `handshake.confirmed`
- `job.started`
- `job.completed`
- `payment.released`

## Rate Limits

- 100 requests per minute for authenticated users
- 20 requests per minute for unauthenticated users

## Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Resource Not Found |
| 409 | Conflict (e.g., worker already booked) |
| 422 | Validation Error |
| 500 | Internal Server Error |
