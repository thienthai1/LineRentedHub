Line LIFF Rented Hub

Quick start

1. Copy `.env.example` to `.env` and set your LIFF channel ID (the app's LIFF ID):

```bash
cp .env.example .env
# then edit .env and put your real VITE_CHANNEL_ID value
```

2. Install and run:

```bash
npm install
npm run dev
```

3. Open the URL printed by Vite (usually http://localhost:5173). The app will load the LIFF SDK and initialize it using the `VITE_CHANNEL_ID` value. If not logged in, click "Login with LINE" to sign in; after signing in the page will show:

Welcome to rented hub

Notes

- Keep your real LIFF channel ID secret (don't commit `.env`).
- The LIFF SDK is loaded from the official CDN in `index.html`.
# LineRentedHub
