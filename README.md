# Accredian Backend - Refer & Earn API

This is the backend service for the Accredian "Refer & Earn" platform. It provides a RESTful API to manage course referrals, stores data in a MySQL database, and sends automated email notifications using Google OAuth2.

## Project Overview

The Accredian Backend serves as the core logic provider for the referral system. When a user submits a referral form on the frontend, this service:
1. Validates the incoming data.
2. Stores the referral information in a MySQL database.
3. Sends a notification email to the referred friend (referee) using Gmail's SMTP with OAuth2 authentication.

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | TypeScript |
| **Runtime** | Node.js |
| **Framework** | Express.js |
| **ORM** | Prisma |
| **Database** | MySQL |
| **Validation** | Zod |
| **Email Service** | Nodemailer + Google OAuth2 |

## Architecture

The project follows a layered architecture to maintain a clean separation of concerns:

- **Routes (`src/routes/`):** Define the API endpoints and route them to the appropriate controllers.
- **Controllers (`src/controllers/`):** Handle incoming HTTP requests, perform input validation, and send responses.
- **Services (`src/services/`):** Contain the core business logic and orchestrate database operations and external service calls.
- **Validations (`src/validations/`):** Define Zod schemas for strict request body validation.
- **Utils (`src/utils/`):** Helper functions, including the email sending utility.
- **Config (`src/config/`):** Configuration singletons like the Prisma client.
- **Middlewares (`src/middlewares/`):** Global error handling and request processing.

## API Documentation

### `POST /api/referrals`

Creates a new referral and sends an email notification.

**Request Body:**

```json
{
  "referrerName": "John Doe",
  "referrerEmail": "john@example.com",
  "refereeName": "Jane Smith",
  "refereeEmail": "jane@example.com",
  "course": "Data Science Program"
}
```

**Success Response:**
- **Code:** `201 Created`
- **Content:**
  ```json
  {
    "message": "Referral created successfully",
    "referral": {
      "id": 1,
      "referrerName": "John Doe",
      "referrerEmail": "john@example.com",
      "refereeName": "Jane Smith",
      "refereeEmail": "jane@example.com",
      "course": "Data Science Program",
      "createdAt": "2024-07-20T10:00:00.000Z"
    }
  }
  ```

**Error Responses:**
- **Code:** `400 Bad Request` (Validation error)
- **Code:** `500 Internal Server Error` (Server-side failure)

## Database Schema

The system uses Prisma with MySQL. The primary model is `Referral`:

```prisma
model Referral {
  id            Int      @id @default(autoincrement())
  referrerName  String
  referrerEmail String
  refereeName   String
  refereeEmail  String
  course        String
  createdAt     DateTime @default(now())
}
```

### Running Migrations
To apply the schema to your database:
```bash
npx prisma migrate dev --name init
```

## Email Service Configuration

The system uses Gmail with OAuth2 for secure email delivery.

### Prerequisites
1. Create a project in the [Google Cloud Console](https://console.cloud.google.com/).
2. Enable the **Gmail API**.
3. Create **OAuth 2.0 Client IDs** (Web application).
4. Set the Authorized Redirect URI to `https://developers.google.com/oauthplayground`.

### Obtaining Refresh Token
1. Go to [OAuth2 Playground](https://developers.google.com/oauthplayground).
2. Click the gear icon and check "Use your own OAuth credentials". Enter your Client ID and Client Secret.
3. Select the `https://mail.google.com/` scope.
4. Authorize and exchange the authorization code for tokens.
5. Copy the `Refresh Token`.

## Setup & Installation

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd accredian-backend-task
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables:**
   Create a `.env` file from the template:
   ```bash
   cp .env.sampl .env
   ```
   Fill in the following variables:
   - `DATABASE_URL`: Your MySQL connection string.
   - `EMAIL_USER`: Your Gmail address.
   - `GOOGLE_CLIENT_ID`: From Google Cloud Console.
   - `GOOGLE_CLIENT_SECRET`: From Google Cloud Console.
   - `REFRESH_TOKEN`: From OAuth2 Playground.

4. **Initialize Database:**
   ```bash
   npx prisma migrate dev
   npx prisma generate
   ```

5. **Run the Server:**
   ```bash
   npm run dev
   ```
   The server will start on `http://localhost:3000` (by default).
