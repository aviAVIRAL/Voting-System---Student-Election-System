
# Secure Online Voting System

A secure online voting system built using the MERN stack (MongoDB, Express.js, React.js, and Node.js). The application provides user authentication, role-based access control, candidate management, and a voting mechanism that prevents users from voting more than once.

## Features

- **User Authentication:** User signup and login using email and password.
- **Password Security:** Passwords are hashed using `bcryptjs`.
- **JWT Authentication:** JSON Web Tokens are used to authenticate users.
- **Role-Based Access Control:** Separate permissions for administrators and regular users.
- **Candidate Management:** Administrators can add, update, and delete candidates.
- **Candidate Listing:** Authenticated users can view available candidates.
- **Voting System:** Users can cast a vote for their preferred candidate.
- **Duplicate Voting Prevention:** Each user is allowed to vote only once.
- **Live Results:** Administrators can view candidate vote counts.
- **MongoDB Database:** MongoDB stores user accounts, candidate information, and voting data.

## Tech Stack

| Technology  | Purpose                                      |
| ----------- | -------------------------------------------- |
| Node.js     | JavaScript runtime for the backend           |
| Express.js  | REST API and routing                         |
| MongoDB     | NoSQL database                               |
| Mongoose    | MongoDB object modeling                      |
| JWT         | Token-based authentication                   |
| bcryptjs    | Password hashing                             |
| dotenv      | Environment variable management              |
| body-parser | Parsing JSON request bodies                  |
| Nodemon     | Automatic server restarts during development |

**Note:** The supplied code contains the backend only. React.js frontend components have not been included yet.

## Project Structure

```
voting-system/
│
├── models/
│   ├── User.js
│   └── Candidate.js
│
├── routes/
│   ├── auth.js
│   ├── admin.js
│   └── user.js
│
├── .env
├── .gitignore
├── package.json
├── package-lock.json
├── server.js
└── README.md
```

## Prerequisites

Before running the project, ensure you have installed:

- Node.js and npm
- MongoDB (local installation or MongoDB Atlas)
- Postman or another API testing tool

## Installation and Setup

### 1. Clone the Repository

```
git clone <your-repository-url>
cd voting-system
```

Replace `<your-repository-url>` with your actual Git repository URL.

### 2. Install Dependencies

```
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory:

```
PORT=3000
MONGO_URI=mongodb://127.0.0.1:27017/voting-system
JWT_SECRET=replace_with_a_strong_random_secret
```

- `PORT`: Port on which the server runs.
- `MONGO_URI`: MongoDB connection string.
- `JWT_SECRET`: Secret key used to sign and verify JWTs.

For MongoDB Atlas, replace the local MongoDB URI with your Atlas connection string.

**Important:** Never commit your `.env` file or expose your JWT secret in a public repository.

### 4. Start MongoDB

If you are using a local MongoDB installation, make sure the MongoDB service is running.

If you are using MongoDB Atlas, ensure your database credentials and network access are configured correctly.

### 5. Start the Server

For development:

```
npm run dev
```

For normal execution:

```
npm start
```

If everything is configured correctly, the server will run on port `3000`, and a successful database connection will display a message in the terminal.

Base URL:

```
http://localhost:3000
```

## API Endpoints

### Authentication APIs

Base path: `/api/auth`

| Method | Endpoint  | Description             | Authentication |
| ------ | --------- | ----------------------- | -------------- |
| POST   | `/signup` | Register a new user     | Not required   |
| POST   | `/login`  | Log in an existing user | Not required   |

Example signup request:

```
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "StrongPassword123",
  "role": "user"
}
```

Example login request:

```
{
  "email": "john@example.com",
  "password": "StrongPassword123"
}
```

Successful signup or login returns a JWT token and basic user information.

### Admin APIs

Base path: `/api/admin`

All admin endpoints require a valid JWT belonging to an administrator.

| Method | Endpoint         | Description                |
| ------ | ---------------- | -------------------------- |
| POST   | `/candidate`     | Add a candidate            |
| PUT    | `/candidate/:id` | Update a candidate         |
| DELETE | `/candidate/:id` | Delete a candidate         |
| GET    | `/results`       | View candidate vote counts |

Example: Add a candidate

```
POST /api/admin/candidate
```

Request body:

```
{
  "name": "Candidate A"
}
```

Example: Update a candidate

```
PUT /api/admin/candidate/CANDIDATE_ID
```

Request body:

```
{
  "name": "Candidate B"
}
```

Example: Delete a candidate

```
DELETE /api/admin/candidate/CANDIDATE_ID
```

Example: Get voting results

```
GET /api/admin/results
```

Example response:

```
[
  {
    "_id": "candidate_id",
    "name": "Candidate A",
    "votes": 10
  }
]
```

The IDs and vote counts above are illustrative examples.

### User APIs

Base path: `/api/user`

All user endpoints require a valid JWT belonging to a regular user.

| Method | Endpoint      | Description             |
| ------ | ------------- | ----------------------- |
| GET    | `/candidates` | Retrieve all candidates |
| POST   | `/vote`       | Cast a vote             |

Example: Get all candidates

```
GET /api/user/candidates
```

Example response:

```
[
  {
    "_id": "candidate_id",
    "name": "Candidate A",
    "votes": 10
  },
  {
    "_id": "another_candidate_id",
    "name": "Candidate B",
    "votes": 5
  }
]
```

Example: Cast a vote

```
POST /api/user/vote
```

Request body:

```
{
  "candidateId": "CANDIDATE_ID"
}
```

A successful request returns:

```
{
  "message": "Vote cast successfully"
}
```

If the user has already voted, the current implementation returns an error message indicating that the user has already voted.

## Authentication

For protected API requests, include the JWT in the HTTP `Authorization` header:

```
Authorization: Bearer YOUR_JWT_TOKEN
```

Replace `YOUR_JWT_TOKEN` with the token returned by the signup or login endpoint.

The authentication middleware verifies the token and checks the user's role before allowing access to protected routes.

## Database Models

### User Model

The `User` model stores user account and voting information.

| Field       | Type    | Description                          |
| ----------- | ------- | ------------------------------------ |
| `name`      | String  | User's name                          |
| `email`     | String  | Unique email address                 |
| `password`  | String  | Hashed password                      |
| `role`      | String  | `user` or `admin`                    |
| `hasVoted`  | Boolean | Indicates whether the user has voted |
| `createdAt` | Date    | Record creation timestamp            |
| `updatedAt` | Date    | Last update timestamp                |

The `hasVoted` field defaults to `false`.

### Candidate Model

The `Candidate` model stores candidate details and vote counts.

| Field       | Type   | Description               |
| ----------- | ------ | ------------------------- |
| `name`      | String | Candidate's name          |
| `votes`     | Number | Total votes received      |
| `createdAt` | Date   | Record creation timestamp |
| `updatedAt` | Date   | Last update timestamp     |

The `votes` field defaults to `0`.

## Application Workflow

1. A user registers using the signup endpoint.
2. The password is hashed before being stored in MongoDB.
3. The server returns a JWT after successful registration or login.
4. The JWT is included in subsequent protected API requests.
5. Administrators manage candidates and view voting results.
6. Regular users view the candidate list.
7. A user selects a candidate and submits a vote.
8. The server checks whether the user has already voted.
9. If eligible, the candidate's vote count is incremented and the user's `hasVoted` field is set to `true`.
10. Administrators retrieve the updated vote counts through the results endpoint.

## Error Handling

The API returns HTTP status codes to indicate the outcome of a request.

| Status Code | Meaning                                     |
| ----------- | ------------------------------------------- |
| `200`       | Request completed successfully              |
| `201`       | Resource created successfully               |
| `400`       | Invalid request or duplicate voting attempt |
| `401`       | Authentication token missing                |
| `403`       | Invalid token or insufficient permissions   |
| `404`       | Requested user or candidate not found       |
| `500`       | Server-side error                           |

## Testing with Postman

Recommended testing sequence:

1. Register a regular user.
2. Log in and save the returned JWT.
3. Register or configure an administrator account.
4. Log in as the administrator.
5. Add candidates using the admin API.
6. Retrieve candidates using the user API.
7. Cast a vote as a regular user.
8. Attempt to vote again using the same user account.
9. Retrieve results using the admin API.
10. Verify that unauthorized requests are rejected.

Use separate tokens for administrator and regular-user requests.

## Security Considerations and Future Improvements

This project demonstrates basic authentication and voting logic. Before using it in production, consider implementing the following improvements:

- **Admin account protection:** Public signup currently accepts a `role` field. This allows a caller to request the `admin` role. The server should assign the regular-user role by default and create administrators through a trusted process.
- **Atomic voting:** The current vote flow updates the candidate and user in separate database operations. Use MongoDB transactions or another atomic design to prevent inconsistent vote counts and concurrent duplicate votes.
- **Input validation:** Validate names, emails, passwords, candidate IDs, and request bodies.
- **Database error handling:** Handle duplicate emails, invalid MongoDB IDs, and validation failures with appropriate status codes.
- **Vote integrity:** Prevent unauthorized changes to vote counts and restrict candidate updates to permitted fields.
- **Rate limiting:** Protect authentication endpoints against brute-force attempts.
- **JWT secret management:** Use a strong, private secret and rotate it when necessary.
- **Testing:** Add automated unit and integration tests.
- **Frontend:** Build a React.js interface for registration, login, candidate selection, and results visualization.

**Disclaimer:** This is an educational project. It is not a production-ready or independently audited election system. Real elections require additional security, privacy, auditability, and integrity safeguards.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details if one is included in the repository.

## Author

**Your Name**

GitHub: [Your GitHub Profile](https://github.com/)

Replace the placeholder author details with your own information.

## Important fixes before you publish

I noticed a few things in your supplied code that you should fix before showcasing the project on GitHub.

1. Prevent users from signing up as admins. Your current `/signup` route accepts the `role` from the request body, which is a security vulnerability.
2. Fix duplicate voting under concurrent requests. Checking `hasVoted` and updating the vote count separately can allow race conditions.
3. Add the missing Candidate model export. Your `Candidate.js` should end with:

```
module.exports = mongoose.model('Candidate', candidateSchema);
```
