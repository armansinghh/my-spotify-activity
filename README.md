# Live Spotify Activity Widget 🎵

## 🚀 Live Demo
- [View Live Demo](https://my-spotify-activity.vercel.app) - Deployed on Vercel

Displays your Spotify currently playing track in real-time with a dynamic blurred album art background, progress bar, and clickable track link.

## Preview
![Preview](./preview/desktop.png)

## Features
- 🎵 Real-time currently playing track display (polls every 3 seconds)
- 🖼️ Dynamic background based on album art
- ⏳ Smooth progress bar showing song position
- 🔒 Secure token handling via serverless function
- 🌐 Public `/api/status` endpoint for embedding in portfolio sites
- 📱 Responsive design

## Project Structure
```
├── api
│   ├── status.js         # Public read-only endpoint — returns current track as JSON (for portfolio embeds)
│   └── token.js          # Serverless function for secure Spotify token refresh
├── src
│   ├── app
│   │   └── index.js      # Frontend JavaScript
│   ├── css
│   │   └── style.css     # Styles
│   └── img
│       └── ico.png       # Favicon
└── index.html            # Main HTML file
```

### About `api/status.js`
This endpoint lets you embed a "now playing" badge on an **external** site (e.g. your portfolio) without exposing credentials. It returns a simple JSON object:
```json
{
  "playing": true,
  "text": "Song Name – Artist",
  "imageUrl": "https://...",
  "spotifyUrl": "https://open.spotify.com/track/..."
}
```
It uses `BASE_URL` internally to call your own `/api/token` endpoint, so make sure that env var is set (see Setup below).

---

## Setup Instructions

### 1. Spotify API Setup
* Create a Spotify Application through the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
* Click **Edit Settings**
* Note down your:
  * `Client ID`
  * `Client Secret`
* Under **Redirect URIs**, add: `http://localhost/callback/`

### 2. Get Your Refresh Token
Navigate to the following URL (replace `{SPOTIFY_CLIENT_ID}` with yours):
```
https://accounts.spotify.com/authorize?client_id={SPOTIFY_CLIENT_ID}&response_type=code&scope=user-read-currently-playing&redirect_uri=http://localhost/callback/
```
* After logging in, copy the `{CODE}` from the redirect URL: `http://localhost/callback/?code={CODE}`
* Create a Base64 string from `{SPOTIFY_CLIENT_ID}:{SPOTIFY_CLIENT_SECRET}`
* Exchange the code for a refresh token:
```sh
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Authorization: Basic {BASE64}" \
  -d "grant_type=authorization_code&redirect_uri=http://localhost/callback/&code={CODE}" \
  https://accounts.spotify.com/api/token
```
Save the `refresh_token` value from the response.

Need help? Watch this [video tutorial](https://www.youtube.com/watch?v=yAXoOolPvjU) by API-University.

### 3. Local Development
1. Clone and install:
```bash
git clone https://github.com/ArmanSingh24/my-spotify-activity.git
cd my-spotify-activity
npm install
```

2. Create a `.env` file in the project root:
```env
CLIENT_ID=your_spotify_client_id
CLIENT_SECRET=your_spotify_client_secret
REFRESH_TOKEN=your_refresh_token
BASE_URL=http://localhost:3000
```
> `BASE_URL` is required by `api/status.js` to call your local token endpoint. Set it to whatever port `vercel dev` runs on (default is `3000`).

3. Run locally:
```bash
vercel dev
```

---

## Deployment

### Option A: Vercel (Recommended)
1. Install Vercel CLI:
```bash
npm i -g vercel
```

2. Deploy:
```bash
vercel
```

3. Add the following environment variables in your Vercel project settings:
   - `CLIENT_ID`
   - `CLIENT_SECRET`
   - `REFRESH_TOKEN`
   - `BASE_URL` — set this to your deployed Vercel URL, e.g. `https://my-spotify-activity.vercel.app`

### Option B: GitHub Pages
> ⚠️ **Not recommended.** The serverless functions (`api/token.js`, `api/status.js`) will not work on GitHub Pages since it only serves static files. Any workaround that moves token refresh to the client side would **expose your `CLIENT_SECRET` and `REFRESH_TOKEN` publicly** — avoid this. Use Vercel or another serverless-capable host instead.

---

## Security Notes
- Credentials are stored as environment variables and never exposed to the client
- The `/api/token` endpoint uses short-lived access tokens (~1 hour) and caches them server-side
- The `/api/status` endpoint is intentionally public (read-only, no credentials exposed)
- Never commit your `.env` file — it's already in `.gitignore`

---

## Contributing
Feel free to open issues and pull requests!

## Credits
- Built with the [Spotify Web API](https://developer.spotify.com/documentation/web-api)
- Deployed on [Vercel](https://vercel.com)

---
Made with ❤️ by Arman Singh