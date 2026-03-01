# Spotify Backend API

Backend API for a Spotify-style app built with Node.js, Express, and MongoDB.

## Tech Stack
- Node.js
- Express
- MongoDB + Mongoose
- JWT (cookie-based auth)
- Multer (file upload)
- ImageKit (`@imagekit/nodejs`) for music file storage

## Features
- User registration and login
- Role-based access (`user` and `artist`)
- Artist-only music upload
- Artist-only album creation
- User-only music and album read endpoints

## Project Structure
```text
src/
  app.js
  controllers/
    auth.controller.js
    music.controller.js
  db/
    db.js
  middlewares/
    auth.middleware.js
  models/
    user.model.js
    music.model.js
    album.model.js
  routes/
    auth.routes.js
    music.routes.js
  services/
    storage.service.js
server.js
```

## Environment Variables
Create a `.env` file in the project root with:

```env
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
IMAGEKIT_PRIVATE_KEY=your_imagekit_private_key
```

## Installation and Run
```bash
npm install
npm start
```

Server runs on:

```text
http://localhost:3000
```

## Authentication
After successful register/login, a `token` cookie is set by the server.

- Protected routes use `req.cookies.token`
- Use Postman with cookie persistence enabled
- `authArtist` routes require `role: "artist"`
- `authUser` routes require `role: "user"`

## API Endpoints

### Auth Routes (`/api/auth`)

1. `POST /register`
- Body (JSON):
```json
{
  "username": "john",
  "email": "john@example.com",
  "password": "password123",
  "role": "user"
}
```
- `role` is optional (`user` by default, or `artist`)

2. `POST /login`
- Body (JSON):
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```
You can also log in using `username` + `password`.

3. `POST /logout`
- Clears the auth cookie.

### Music Routes (`/api/music`)

1. `POST /upload` (artist only)
- Content type: `multipart/form-data`
- Fields:
  - `music` (file) required
  - `title` (text) required

2. `POST /album` (artist only)
- Body (JSON):
```json
{
  "title": "My Album",
  "musics": ["<musicId1>", "<musicId2>"]
}
```

3. `GET /` (user only)
- Returns music list with artist details.

4. `GET /albums` (user only)
- Returns album list with artist details.

5. `GET /albums/:albumId` (user only)
- Returns a single album by id with artist details.

## Data Models

### User
- `username` (String, unique, required)
- `email` (String, unique, required)
- `password` (String, required, hashed)
- `role` (`user` | `artist`)

### Music
- `uri` (String, required)
- `title` (String, required)
- `artist` (ObjectId -> `user`)

### Album
- `title` (String, required)
- `musics` (ObjectId[] -> `music`)
- `artist` (ObjectId -> `user`)

## Notes
- This project currently enforces strict role separation in middleware:
  - Artists can create content.
  - Users can read content.
- If you want artists to also access read endpoints, update `authUser` middleware logic.
