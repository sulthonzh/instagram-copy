# Dokumentasi Aplikasi Mirip Instagram

Dokumentasi ini menyediakan rancangan skema data dan panduan implementasi untuk aplikasi yang mirip dengan Instagram, mencakup skema untuk MongoDB dan PostgreSQL yang selaras satu sama lain untuk mendukung fitur kunci seperti media dengan atribut, tagging, lokasi, audiens, pengingat, detail produk, dan musik.

## Skema Data

### MongoDB

#### Koleksi Pengguna (`Users`)

```json
{
  "_id": ObjectId,
  "username": String,
  "email": String,
  "bio": String,
  "passwordHash": String,
  "posts": [{ type: ObjectId, ref: 'Posts' }],
  "followers": [{ type: ObjectId, ref: 'Users' }],
  "following": [{ type: ObjectId, ref: 'Users' }]
}
```

#### Koleksi Postingan (Posts)
```json
{
  "_id": ObjectId,
  "user": ObjectId,
  "caption": String,
  "createdAt": Date,
  "mediaUrls": [
    {
      "url": String,
      "type": String,
      "description": String,
      "createdAt": Date
    }
  ],
  "likes": [ObjectId],
  "tags": [String],
  "taggedUsers": [ObjectId],
  "location": String,
  "audience": String,
  "reminder": {
    "eventName": String,
    "startTime": Date
  },
  "productDetails": {
    "title": String,
    "price": Number
  },
  "music": ObjectId
}
```

#### Koleksi Komentar (Comments)
```json
{
  "_id": ObjectId,
  "post_id": ObjectId,
  "user_id": ObjectId,
  "body": String,
  "createdAt": Date
}
```

### PostgreSQL
```sql
-- Tabel Pengguna
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    bio TEXT,
    password_hash TEXT NOT NULL
);

-- Tabel Postingan
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    caption TEXT,
    location TEXT,
    audience VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabel Media
CREATE TABLE media (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES posts(id),
    url TEXT NOT NULL,
    type VARCHAR(255),
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabel Komentar
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES posts(id),
    user_id INTEGER REFERENCES users(id),
    body TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabel Tag dan Tagged Users
CREATE TABLE post_tags (
    post_id INTEGER REFERENCES posts(id),
    tag TEXT,
    PRIMARY KEY (post_id, tag)
);
CREATE TABLE post_tagged_users (
    post_id INTEGER REFERENCES posts(id),
    user_id INTEGER REFERENCES users(id),
    PRIMARY KEY (post_id, user_id)
);

-- Tabel Pengingat
CREATE TABLE reminders (
    post_id INTEGER REFERENCES posts(id),
    event_name TEXT NOT NULL,
    start_time TIMESTAMP NOT NULL,
    PRIMARY KEY (post_id)
);

-- Tabel Detail Produk
CREATE TABLE product_details (
    post_id INTEGER REFERENCES posts(id),
    title TEXT NOT NULL,
    price DECIMAL NOT NULL,
    PRIMARY KEY (post_id)
);

-- Tabel Musik
CREATE TABLE music (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    artist TEXT,
    album TEXT
);
CREATE TABLE post_music (
    post_id INTEGER REFERENCES posts(id),
    music_id INTEGER REFERENCES music(id),
    PRIMARY KEY (post_id, music_id)
);

```