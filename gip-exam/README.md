# GiP Rwanda — AI Architect Certification Exam
## Deploy to Vercel in 5 Minutes (Free)

---

### WHAT YOU GET
- Fully functional AI-proctored certification exam
- Live Claude AI Examiner with Rwanda-specific scenarios
- 14 questions (13 modules + Capstone)
- Secure API key handling (key never reaches student browser)
- Timer, proctor flagging, results + JSON output
- Downloadable exam report for every student

---

## STEP-BY-STEP DEPLOYMENT

### STEP 1 — Create a free GitHub account (if you don't have one)
Go to: https://github.com
Click "Sign up" — it's free.

---

### STEP 2 — Create a new GitHub repository
1. Click the green "New" button on GitHub
2. Name it: `gip-rwanda-exam`
3. Set it to **Public** (required for free Vercel)
4. Click "Create repository"

---

### STEP 3 — Upload these files to GitHub
Upload the following files in this exact folder structure:

```
gip-rwanda-exam/
├── vercel.json
├── package.json
├── api/
│   └── examiner.js
└── public/
    └── index.html
```

To upload:
1. Open your new GitHub repository
2. Click "uploading an existing file"
3. Drag and drop all files (keeping the folder structure)
4. Click "Commit changes"

---

### STEP 4 — Create a free Vercel account
Go to: https://vercel.com
Click "Sign Up" → choose "Continue with GitHub"
Authorize Vercel to access your GitHub.

---

### STEP 5 — Import your project to Vercel
1. On your Vercel dashboard, click **"Add New Project"**
2. Find `gip-rwanda-exam` and click **"Import"**
3. Leave all settings as default
4. Click **"Deploy"**

Vercel will build and deploy in about 60 seconds.

---

### STEP 6 — Add your Claude API Key (CRITICAL)
Your API key must be added as an environment variable — this keeps it secure on the server.

1. Go to your project in Vercel dashboard
2. Click **"Settings"** → **"Environment Variables"**
3. Click **"Add New"**
4. Set:
   - **Name:** `CLAUDE_API_KEY`
   - **Value:** `sk-ant-api03-...` (your full Anthropic API key)
   - **Environment:** Production, Preview, Development (check all three)
5. Click **"Save"**
6. Go to **"Deployments"** → click the three dots on your latest deployment → **"Redeploy"**

---

### STEP 7 — Your exam is live!
Vercel gives you a free URL like:
`https://gip-rwanda-exam.vercel.app`

Share this URL with your students.

---

## GETTING YOUR CLAUDE API KEY

1. Go to: https://console.anthropic.com
2. Click "API Keys" in the left sidebar
3. Click "Create Key"
4. Copy the key (starts with `sk-ant-api03-...`)
5. Add it to Vercel as described in Step 6

**Cost:** Claude API charges per token used.
- Each exam uses approximately 15,000–25,000 tokens total
- At current pricing (~$3 per 1M tokens for Sonnet), each exam costs approximately **$0.05–$0.08**
- For 100 students: approximately **$5–$8 total**

---

## SHARING WITH STUDENTS

Simply send them your Vercel URL:
`https://YOUR-PROJECT-NAME.vercel.app`

Students:
1. Enter their name, ID, and organisation
2. Click "Begin Certification"
3. The AI Examiner starts automatically
4. 14 Rwanda-context questions over 2 hours
5. Results + JSON displayed at the end
6. Can download their full transcript

---

## ADDING A CUSTOM DOMAIN (optional)
1. In Vercel → Settings → Domains
2. Add your domain (e.g., `exam.giprwanda.com`)
3. Update your DNS records as instructed
4. Free SSL certificate applied automatically

---

## COLLECTING RESULTS (for Wix leaderboard)

The JSON output at the end of each exam contains:
```json
{
  "exam_status": "COMPLETED",
  "candidate_name": "...",
  "score_percentage": 87,
  "result": "PASS",
  "grade": "Distinction",
  "rwanda_readiness_level": "High",
  ...
}
```

To collect this automatically, modify `api/examiner.js` to also write to:
- A Google Sheet (using Google Sheets API)
- Airtable (free tier supports this)
- Your Wix database (via Wix HTTP Functions)

See the WIXINTEGRATION.md file for Wix-specific code.

---

## TROUBLESHOOTING

**"API error 401"** → Your CLAUDE_API_KEY environment variable is wrong or missing. Check Step 6.

**"Cannot reach /api/examiner"** → You're opening index.html directly from your computer, not from Vercel. The app only works when hosted on Vercel.

**"API error 429"** → You've hit Anthropic's rate limit. Wait a few minutes or upgrade your Anthropic plan.

**Blank screen** → Open browser developer tools (F12) → Console tab → look for errors.

---

## SUPPORT
Global Institute of People LLC · Kentucky, USA
Rwanda National AI Mandate Programme
