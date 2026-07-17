# Resume Screener

Upload a zip of resumes (or individual PDFs), give it a job description, and it:
1. Extracts text from each resume
2. Sends it to an AI model along with your job description
3. Gets back a summary, an Accept/Reject decision, a match score, and reasoning
4. Lets you download a zip re-sorted into `accepted/` and `rejected/` folders
5. Lets you download an Excel sheet with every result

## API key setup (do this before deploying)

The app never asks for an API key in the UI — it reads it from a server
environment variable, so the key never touches the browser or gets
committed to source.

**Local development:**
```
cp .env.local.example .env.local
```
Then edit `.env.local` and paste in your key(s):
```
GEMINI_API_KEY=your-real-key
GROQ_API_KEY=your-real-key   # optional, only needed if you use the Groq toggle
```

**On Vercel:**
Project → Settings → Environment Variables → add `GEMINI_API_KEY` (and/or
`GROQ_API_KEY`) → redeploy. That's it — the running app will pick it up
automatically. Never paste a real key directly into any `.ts`/`.tsx` file.

## Run locally
```
npm install
npm run dev
```
Visit http://localhost:3000

## Deploy to Vercel
```
npm install -g vercel   # if you don't have it
vercel
```
Or: push this folder to a GitHub repo and import it at vercel.com/new.
Set the environment variables (above) before or right after the first deploy,
then redeploy once to pick them up.

## Notes on rate limits
Gemini's free tier throttles hard under any real volume (HTTP 429). The
screener processes resumes one at a time (not in parallel) and retries each
failed call with exponential backoff (up to 5 retries). If you're screening
a large batch and still hitting limits, switch the toggle to Groq — it's
generally more forgiving on free tier throughput.

## Vercel execution time
Screening runs inside a single API call, one resume after another. On
Vercel's Hobby plan, serverless functions are capped around 10s by default
— for anything beyond a handful of resumes, you'll likely need a paid plan
(higher function duration limit) or to screen in smaller batches.

## Notes on scanned/image PDFs
Text extraction only works on real (selectable-text) PDFs. A scanned resume
with no text layer will come back as "Unreadable" in the results — OCR isn't
included here.
