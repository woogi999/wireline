# Wireline

A two-person room: chat, camera, mic, screen share, and file transfer, all straight between two browsers. One person starts a room and gets a code. The other person joins with that code. Everything after that goes directly between the two browsers over WebRTC.

Nothing is uploaded anywhere. This site only helps two browsers find each other (via PeerJS's free public signaling broker) — it never touches, stores, or relays your chat, video, or files.

## What it does

- **Chat** — text messages over a WebRTC data channel.
- **Camera and mic** — toggle each on or off; the other person sees/hears you live.
- **Screen share** — swaps your outgoing video for your screen; toggle off to go back to your camera.
- **File transfer** — send a file straight in the chat; it streams in chunks and shows up as a download link on the other side.

## Deploy it yourself

1. Create a new GitHub repo and push these files to the `main` branch.
2. In the repo, go to **Settings → Pages → Build and deployment → Source**, and set it to **GitHub Actions**.
3. Push to `main` (or run the workflow manually from the **Actions** tab). The included workflow (`.github/workflows/deploy.yml`) builds nothing — it just publishes the static `index.html` — and deploys it to GitHub Pages.
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/`.

## How it works

- **Signaling**: uses PeerJS's free public broker (`0.peerjs.com`) just to let two browsers exchange connection info and agree on a room code. No chat, video, or file data passes through it.
- **Chat and files**: sent over a WebRTC `RTCDataChannel` directly between the two browsers. Files stream in 16 KB chunks.
- **Camera, mic, screen**: sent over a WebRTC media connection (`RTCPeerConnection`) directly between the two browsers.
- **Room codes**: randomly generated word-based codes (e.g. `amber-7-fox-quartz`), namespaced so they don't collide with unrelated traffic on the public broker.

## Limitations to know about

- **Two people at a time.** This is built for a single room with one other person, not a group call.
- **Both browsers need to be open at the same time.** There's no offline delivery — leave the room and the connection ends.
- **The browser will ask for camera/mic/screen permission.** This only happens when you turn one of those on, and it's your browser asking, not this site collecting anything.
- **Strict NATs/firewalls (e.g. some corporate networks) can block the direct connection.** WebRTC uses STUN for NAT traversal, but doesn't include a TURN relay by default, so a small fraction of connections may fail. If you need this to work reliably behind restrictive networks, add a TURN server to the `Peer` config in `index.html`.
- **The public PeerJS broker is a shared, free service.** It's fine for personal or small-scale use; for anything higher-stakes, consider running your own PeerServer (open source, self-hostable) and swapping the endpoint in `index.html`.
- **Large files**: work well up to at least a few hundred MB; very large files depend on both tabs staying open and the connection holding for the full duration.

## Customizing

Everything is in the single `index.html` file — styling, room-code generation, chat, media, and file-transfer logic. No build step, no dependencies to install.
