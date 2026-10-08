# Deploy Skill Sync on Render (about 10 minutes)

## 1. MongoDB Atlas (free)
1. Create a free M0 cluster at cloud.mongodb.com.
2. Database Access: add a user + password.
3. Network Access: add `0.0.0.0/0` (Render IPs change).
4. Connect > Drivers > copy the string and add the DB name:
   `mongodb+srv://USER:PASSWORD@cluster0.xxxxx.mongodb.net/wie_ds?retryWrites=true&w=majority`

## 2. Push to GitHub
Push the `Skill_Sync` folder as the repo root (so `render.yaml` is at the top).

## 3. Render
New > Blueprint > select the repo. Render creates two services and asks you for:

| Service  | Variable          | Value                                                             |
|----------|-------------------|-------------------------------------------------------------------|
| backend  | `MONGODB_URI`     | the Atlas string from step 1                                      |
| backend  | `CORS_ORIGIN`     | frontend URL (fill after step 4; no trailing slash)               |
| backend  | `GEMINI_API_KEY`  | optional (chatbot + custom-skill assessments)                     |
| frontend | `VITE_API_URL`    | backend URL + `/api`, e.g. `https://skillsync-backend.onrender.com/api` |

JWT secrets are generated automatically.

## 4. Order of operations
1. Deploy once with placeholders. Note both URLs.
2. Set `CORS_ORIGIN` on backend = frontend URL, and `VITE_API_URL` on frontend = backend URL + `/api`.
3. Redeploy both (the frontend must be rebuilt to pick up `VITE_API_URL`).
4. Open the frontend URL. Demo logins (password `Demo@12345`): riya@demo.com, aryan@demo.com, recruiter@demo.com, agency@demo.com.

## Notes
- Free Render backend sleeps after inactivity; first request takes ~50 s.
- Frontend and backend are on different domains, so `COOKIE_SECURE=true` and `COOKIE_SAMESITE=None` are required (already set in render.yaml). Both are needed for login refresh to work.
- Set `SEED_DEMO=false` once you have real users.
- Vercel/Netlify for the frontend also work: `vercel.json` and `public/_redirects` are included. Set `VITE_API_URL` there.
