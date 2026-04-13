# StreamNet — CN Video Streaming Server

> **Computer Networks Project** | Client-Server Video Streaming with HTTP Range Requests, Cache Management & JWT Auth

---

## 🎯 Project Overview

This project implements a **real-time video streaming server** demonstrating core Computer Networks concepts:

| CN Concept                     | Implementation                                                             |
| ------------------------------ | -------------------------------------------------------------------------- |
| **HTTP Range Requests**        | Server sends `206 Partial Content` responses for video chunks              |
| **Client-Server Architecture** | Express server on `0.0.0.0:3000` accessible from all LAN devices           |
| **Cache-Control Headers**      | `public, max-age=3600` for static videos; `no-store` for dynamic user data |
| **Stateless Auth (JWT)**       | HMAC-SHA256 signed tokens in HTTP cookies                                  |
| **Chunked Transfer**           | Browser requests ~1MB chunks via `Range: bytes=start-end`                  |
| **MIME Types**                 | Correct `Content-Type: video/mp4` headers for browser player               |

---

## 📁 Project Structure

```
CN_project/
├── server.js                    # Express server (streaming + auth + cache API)
├── package.json
├── generate_sample_videos.py    # Generate demo MP4s with ffmpeg
├── videos/                      # Place your .mp4/.webm files here
└── public/
    ├── index.html               # Full SPA frontend
    ├── style.css                # Premium dark glassmorphic UI
    └── app.js                   # Frontend logic
```

---

## 🚀 Quick Start

### 1. Install dependencies

```bash
npm install
```

### 2. Add videos

Place `.mp4` or `.webm` files into the `videos/` folder.  
**Or** generate sample videos automatically:

```bash
# Install ffmpeg first
sudo apt install ffmpeg

# Generate 3 sample demo videos
python3 generate_sample_videos.py
```

### 3. Start the server

```bash
npm start
# or for auto-reload during development:
npx nodemon server.js
```

### 4. Open in browser

```
http://localhost:3000
```

### 5. Multi-device demo (hotspot)

```
http://<your-server-IP>:3000
```

Find your IP: `ip addr show` or `hostname -I`

---

## 🌐 API Endpoints

| Method   | Endpoint             | Description                            |
| -------- | -------------------- | -------------------------------------- |
| `POST`   | `/api/auth/register` | Register new user                      |
| `POST`   | `/api/auth/login`    | Login (sets JWT cookie)                |
| `POST`   | `/api/auth/logout`   | Logout                                 |
| `GET`    | `/api/auth/me`       | Current user info                      |
| `GET`    | `/api/videos`        | List all videos                        |
| `GET`    | `/stream/:videoId`   | **Stream video** (HTTP Range Requests) |
| `GET`    | `/api/cache`         | Get user's recently viewed list        |
| `POST`   | `/api/cache`         | Update viewing cache                   |
| `DELETE` | `/api/cache`         | Clear cache                            |
| `GET`    | `/api/stats`         | Network analytics data                 |

---

## 🔬 CN Concepts Explained

### HTTP Range Requests (RFC 7233)

The core of video streaming. The browser's `<video>` element automatically sends partial content requests:

```
CLIENT → SERVER:
GET /stream/abc123 HTTP/1.1
Range: bytes=0-1048575

SERVER → CLIENT:
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1048575/15728640
Content-Length: 1048576
Content-Type: video/mp4
Accept-Ranges: bytes

[1MB of video data]
```

### Cache Management

- **Server-side**: User watch history + recently viewed stored in server memory per user
- **Client-side**: Browser caches static video chunks via `Cache-Control: public, max-age=3600`
- **Dynamic data** (user cache list): `Cache-Control: no-store` prevents stale user-specific data

### JWT Authentication (Stateless)

```
POST /api/auth/login
→ JWT signed with HMAC-SHA256
→ Stored in httpOnly cookie
→ Validated on every request (stateless — no session store needed)
```

---

## 📊 Network Analysis Dashboard

Access the **Network Stats** tab in the app to view:

- Per-video view counts and total bytes streamed
- Average bytes per view
- Live HTTP Range Request flow diagram
- Buffer visualization (played vs buffered)
- Client-server connection info

---

## 🎬 Demo Guide (Presentation)

1. **Start server** → show it binding to `0.0.0.0:3000`
2. **Register two devices** on the same hotspot
3. **Stream simultaneously** from both → show CN concept of concurrent clients
4. **Seek in video** → observe `206 Partial Content` in browser DevTools → Network tab
5. Show **Recently Viewed** page → explain server-side cache management
6. Show **Network Stats** dashboard → explain performance metrics

---

## 🛠️ Requirements

- Node.js ≥ 16
- npm ≥ 9
- ffmpeg (optional, for sample video generation)
