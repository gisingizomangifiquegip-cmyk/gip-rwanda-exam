# Wix Integration Guide
## Automatically Save Exam Results to Wix CMS

---

## STEP 1 — Create a Wix CMS Collection called "ExamResults"

Add these fields to the collection:
- `studentName` (Text)
- `studentId` (Text)  
- `organisation` (Text)
- `scorePercentage` (Number)
- `result` (Text) — "PASS" or "FAIL"
- `grade` (Text)
- `rwandaReadiness` (Text)
- `strengths` (Long Text)
- `weaknesses` (Long Text)
- `recommendation` (Text)
- `tabFlags` (Number)
- `examDate` (Date)
- `transcriptUrl` (URL) — optional, link to stored transcript

---

## STEP 2 — Add a Wix HTTP Function (backend)

In your Wix project, create: `backend/http-functions.js`

```javascript
import { ok, badRequest } from 'wix-http-functions';
import wixData from 'wix-data';

// POST https://www.yoursite.com/_functions/saveExamResult
export async function post_saveExamResult(request) {
  const body = await request.body.json();
  
  const { examResult, studentInfo, tabFlags } = body;
  
  // Only save if exam is COMPLETED
  if (examResult.exam_status !== 'COMPLETED') {
    return badRequest({ body: 'Exam not completed' });
  }

  const record = {
    studentName:     examResult.candidate_name || studentInfo.name,
    studentId:       studentInfo.id,
    organisation:    studentInfo.org,
    scorePercentage: examResult.score_percentage,
    result:          examResult.result,
    grade:           examResult.grade,
    rwandaReadiness: examResult.rwanda_readiness_level,
    strengths:       (examResult.strengths || []).join(', '),
    weaknesses:      (examResult.weaknesses || []).join(', '),
    recommendation:  examResult.recommendation,
    tabFlags:        tabFlags || 0,
    examDate:        new Date(),
  };

  try {
    await wixData.insert('ExamResults', record);
    
    // Trigger certificate if passed
    if (examResult.result === 'PASS') {
      // Add certificate logic here
      // e.g., send congratulations email via Wix Triggered Emails
    }
    
    return ok({ body: JSON.stringify({ saved: true }) });
  } catch (err) {
    return badRequest({ body: err.message });
  }
}
```

---

## STEP 3 — Update api/examiner.js to POST to Wix

Add this to the bottom of your Vercel `api/examiner.js` handler, after getting the Claude response:

```javascript
// After getting the Claude response and detecting JSON...
const jsonMatch = text.match(/```json([\s\S]*?)```/);
if (jsonMatch && process.env.WIX_SAVE_URL) {
  try {
    const examJSON = JSON.parse(jsonMatch[1].trim());
    if (examJSON.exam_status === 'COMPLETED') {
      // Fire-and-forget save to Wix
      fetch(process.env.WIX_SAVE_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          examResult: examJSON,
          studentInfo: req.body.studentInfo || {},
          tabFlags: req.body.tabFlags || 0,
        }),
      }).catch(() => {}); // Don't block the response if Wix save fails
    }
  } catch(_) {}
}
```

Add `WIX_SAVE_URL` to your Vercel environment variables:
- Name: `WIX_SAVE_URL`
- Value: `https://www.yourwixsite.com/_functions/saveExamResult`

---

## STEP 4 — Also pass studentInfo from the frontend

In `public/index.html`, update the `ask()` function body to include studentInfo:

```javascript
body: JSON.stringify({ 
  messages: ST.history,
  studentInfo: ST.student,  // ← add this line
  tabFlags: ST.tabFlags,    // ← and this
}),
```

---

## STEP 5 — Leaderboard in Wix

In your Wix page (Velo):
```javascript
import wixData from 'wix-data';

$w.onReady(async () => {
  const results = await wixData
    .query('ExamResults')
    .descending('scorePercentage')  // Highest score first
    .eq('result', 'PASS')           // Only passed students
    .limit(50)
    .find();
    
  $w('#leaderboardRepeater').data = results.items;
});
```

In your repeater item:
```javascript
$w('#leaderboardRepeater').onItemReady(($item, itemData) => {
  $item('#nameText').text     = itemData.studentName;
  $item('#scoreText').text    = `${itemData.scorePercentage}%`;
  $item('#gradeText').text    = itemData.grade;
  $item('#orgText').text      = itemData.organisation;
  $item('#dateText').text     = new Date(itemData.examDate).toLocaleDateString();
  $item('#readinessText').text = `🇷🇼 ${itemData.rwandaReadiness}`;
});
```

---

## STEP 6 — Digital Certificate (optional)

When result is "PASS", trigger a Wix Triggered Email:

```javascript
import triggeredEmails from 'wix-crm-backend';

if (examResult.result === 'PASS') {
  await triggeredEmails.emailMember('certificateEmail', memberId, {
    variables: {
      studentName: examResult.candidate_name,
      score: String(examResult.score_percentage),
      grade: examResult.grade,
      date: new Date().toLocaleDateString(),
    }
  });
}
```

---

That's the complete Wix integration.
The Vercel exam app runs independently and pushes results to your Wix database automatically.
