# ZAX: Beyond the Script — Setup Instructions

## 1. Download assets (do this first, manually)

**ZAX character model:**
- Go to https://quaternius.com/packs/ultimateanimatednpcs.html
- Download the free pack, pick one humanoid GLB
- Rename it `zax.glb`
- Check its animations at https://gltf.report — confirm it has `Idle`, `Wave`, `Dance`, `Walk`, `Point`
- If the names differ, note the exact names — you'll edit `backend/index.js`'s prompt and `frontend/index.html` to match

**Buildings:**
- Go to https://kenney.nl/assets/city-kit-commercial
- Download the free kit, grab 2 building GLBs
- Rename them `building1.glb` and `building2.glb`
- If they're FBX, convert at https://products.aspose.app/3d/conversion/fbx-to-glb

Put all three GLB files in `frontend/`.

## 2. Install Node.js

https://nodejs.org (LTS version)

## 3. Get a free Gemini API key

- Go to https://aistudio.google.com/apikey
- Create a free API key (no card needed, has a generous free tier)

## 4. Set up the backend

```
cd backend
cp .env.example .env
```

Open `.env` and paste your Gemini key in place of `your_api_key_here`.

```
npm install
node index.js
```

Test it's working:

```
curl -X POST http://localhost:3000/chat \
	-H "Content-Type: application/json" \
	-d '{"message":"dance for me"}'
```

You should get back something like:
```
{ "reply": "Alright. Watch this.", "action": "Dance", "emotion": "happy" }
```

If you get a model-not-found error, Google renames these fast — go to
https://ai.google.dev/gemini-api/docs/models and swap the model name in
`backend/index.js` (`gemini-flash-lite-latest`) for whatever's current.

## 5. Set up multiplayer (NAF server)

```
git clone https://github.com/networked-aframe/naf-socketio-standalone-server
cd naf-socketio-standalone-server
npm install
node server.js
```

This runs locally on port 9000 by default. For students to join from their
own devices, this needs to be deployed (see step 7) — running it only on
your laptop means only your laptop can reach it.

## 6. Run the frontend locally to test

Open `frontend/index.html` in Chrome.

- WASD to move, mouse to look around
- Click ZAX to open the chat box, type, hit Enter, Escape closes it
- Open the same file in a second browser window — you should see a small
  cyan sphere (the other player) moving around when you move in the first

## 7. Deploy everything so students can join via QR code

**Backend (Gemini proxy) → Railway:**
1. Push `backend/` to a GitHub repo
2. railway.app → New Project → Deploy from GitHub
3. Add environment variable `GEMINI_API_KEY=your_key`
4. Copy the deployed URL (e.g. `https://zax-backend.up.railway.app`)
5. In `frontend/index.html`, update `const BACKEND = 'http://localhost:3000'`
   to that URL

**NAF server → Railway:**
1. Push `naf-socketio-standalone-server` to its own GitHub repo
2. Deploy the same way on Railway
3. Copy its URL
4. In `frontend/index.html`, update the `networked-scene="serverURL: ..."`
   attribute on `<a-scene>` to that URL

**Frontend → Vercel:**
1. Push `frontend/` to GitHub
2. vercel.com → Import project → select repo → Deploy
3. Copy the live URL — this is what students open

**QR code for students:**
Once you have the live Vercel URL, generate a QR code for it at
https://www.qr-code-generator.com (or any QR tool) and put it on your
slide. Students scan it, it opens in their phone/Quest browser, and they
land in the same shared scene as everyone else.

## Presentation demo lines that work well

- "dance for me" → ZAX dances
- "wave at me" → ZAX waves
- "are you real?" → philosophical reply, idle
- "I hate you" → annoyed, bubble turns red

## Final checklist before presentation day

- [ ] `zax.glb` loads and plays Idle in browser
- [ ] Player WASD works
- [ ] Click ZAX → chat UI opens
- [ ] Type → Gemini responds with valid JSON
- [ ] ZAX animation changes based on action
- [ ] 3D speech bubble shows above ZAX's head
- [ ] Color changes with emotion
- [ ] Two browser windows sync via NAF multiplayer
- [ ] Backend live URL updated in frontend/index.html
- [ ] NAF server live URL updated in frontend/index.html
- [ ] QR code generated and tested on an actual phone
