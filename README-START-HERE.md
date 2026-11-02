# BAND III BATTLE ARENA 3.0 — Neon Edition

A single Node.js multiplayer web app for 11th-grade Module C vocabulary and OREO practice. It serves three consistent neon screens: student phones, projector/teacher controls, and the lobby.

## What changed in version 3.0
- Shared premium neon design on phones, teacher controls, and projector leaderboard.
- ALL PLAY: every student answers on their own device in the same round.
- Spotlight, double-point duels, and point stealing. Students can request a duel or steal from their phone; the teacher approves or declines the request.
- Real-time leaderboard (the client polls every 0.9 seconds), timers, progress, CSV exports.
- Earned Shield Challenge: exactly ONE four-sentence OREO submission per player per game; rubric grades Content & Organization /10, Vocabulary /8, Language Use /8, Mechanics /4 (30 total), with **NO length deduction**. **23/30 or more** awards one shield. Shield automatically blocks the first otherwise-successful steal. No retries and no free shields.
- Optional automatic AI grading; manual teacher review if the grading API is unavailable. The teacher may grade pending submissions against the same rubric. Scores are formative estimates, not an official Ministry assessment.
- 85 question bank entries copied from the earlier version (60 vocabulary, 25 OREO), not an official Ministry frequency ranking.

## Deploy to your existing Render Web Service

**Important:** This is not a static Netlify Drop site. Node.js is required for live multiplayer. To keep the existing `https://module-c-band-iii-arena.onrender.com` address, update its EXISTING GitHub repository instead of creating a new Render web service.

1. Open your existing Render service dashboard. In **Settings**, find the connected repository (Source / Repository). Click the repository link to go to GitHub.
2. BACK UP the working repository (download or create a branch).
3. Upload **the contents** of this package, including `server.js`, `questions.json`, `package.json`, `public/` folder, `render.yaml`, and README to the ROOT of the **same repository**, replacing the previous versions. `state.json` should NOT be uploaded. Do not upload only the ZIP.
4. Render usually automatically deploys changes to the connected branch. If auto-deploy is disabled, use **Manual Deploy → Deploy latest commit** in Render. Build command: `npm install`; Start command: `npm start`; root directory blank.
5. Configure AI grading in Render **Environment** → **Add Environment Variable**:
   - Key: `OPENAI_API_KEY`
   - Value: your *own* OpenAI API key (do not paste it into GitHub or the chat).
   - Optional: `OPENAI_MODEL` = `gpt-4.1-mini` (or another compatible model accessible to your API account).
   - Without `OPENAI_API_KEY`, the shield essay is NOT automatically graded: it appears for the teacher to score using the rubric. When an API call fails, teacher review is also available.
6. Redeploy. Open your service URL and confirm `/api/health` reports `"version":"3.0"`. Check the Render logs for your PRIVATE teacher link. Do not send the teacher link to students.
7. Run a short test with two devices before using it in class.

## Game operation

**Teacher:** Open the private teacher link from logs. Project this screen. Copy the room code shown. Let students join with first name and last initial.

**Students:** Open the public Render link, enter the room code, enter a short name. The room code alone never grants teacher access.

**ALL PLAY:** all students answer simultaneously; the correct choice is revealed once everyone answers or time expires (30 seconds for vocabulary, 45 seconds for OREO). Teacher can reveal early.

**CHALLENGE A FRIEND:** on their phone, students choose a friend, mode (duel/steal), and vocabulary or OREO category. The teacher approves the pending request; it starts the relevant round. Each student can have only one pending request at a time.

**SPOTLIGHT:** selected student answers. **DUEL:** selected friend answers for double points; if wrong or time runs out, challenger gets the double points. **STEAL:** selected attacker answers correctly to transfer up to 20 points from target; an active earned shield blocks one successful steal, then disappears. Failed steal gives target +10.

**SHIELD:** teacher selects "Open Shield Challenge". Each student submits exactly four complete English sentences in OREO order. Upon successful submission the attempt is permanently locked for that game, whether pass or fail. AI (if configured) scores meaning/organization, vocabulary, language and mechanics; there are no word-count penalties. At least 23/30 earns one protection. The challenge can be opened/closed by teacher; previous submission remains locked.

**Export:** use the three CSV downloads for results, answers and the writing feedback. Do this before lesson ends.

## Critical limits

- If deployed on Render's ephemeral filesystem, scores can be lost on restart or redeploy. Export CSV reports. For permanent storage, attach a database in a future version.
- This is a formative classroom activity, not secure examination or an official Ministry grading tool. AI grading is an estimate and can be imperfect; the teacher should review borderline decisions.
- API use may incur costs and sends only student writing and its assignment prompt to the configured AI service; do not ask students to include names, ID numbers or personal information in the paragraph.
- Teacher URL includes an administrator secret. It should remain private; reset it if disclosed.
- Some AI accounts/models might require adjusting `OPENAI_MODEL` or `OPENAI_BASE_URL`.
- All scoring and shield decisions happen on the server; student clients never receive the correct answer before reveal.

## Sanity check

`npm start` then visit `http://localhost:3000/api/health` to see the version and question count. Teacher link prints in the server log. Tested with two concurrent students, hidden answers, duplicate-answer protection, 27/30 shield grading, one attempt enforcement, and blocked point steals.
