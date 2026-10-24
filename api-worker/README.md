# CareerMatch Voice Mock Interview Worker

This separate API Worker powers the interview feature on the static CareerMatch site. It follows this order: introduction, résumé project deep dive, DBMS/OOP questions, coding challenge, code review, complexity discussion, then final feedback. It supports recorded microphone answers (transcribed by OpenAI) and typed answers. Interviewer questions can be read aloud by the browser.

The Worker does not write interview text to storage or logs. Requests to the Responses API set `store: false`; audio is sent to the OpenAI transcription API and is not persisted by this Worker. Provider processing and abuse-monitoring controls still apply.

## Deploy

1. Install Node.js, then open a terminal in this `api-worker` directory.
2. Run `npx wrangler secret put OPENAI_API_KEY` and paste your OpenAI API key when prompted. Never put the key in `config.js` or `wrangler.jsonc`.
3. Run `npx wrangler deploy`.
4. Copy the deployed Worker URL (for example `https://careermatch-interview-api.<account-subdomain>.workers.dev`) into `interviewApiUrl` in the website's `config.js`.
5. Publish the website again.

`ALLOWED_ORIGIN` in `wrangler.jsonc` is restricted to the production website origin. Change it only if you use another site origin. `OPENAI_MODEL` defaults to `gpt-6-luna`; change it only to a model available to your API project that supports Structured Outputs.

## Before public launch

The API key is server-side, but the endpoint is public. Configure a Cloudflare rate-limiting rule for `/interview` and `/transcribe` and set usage/budget limits in your OpenAI project. CORS restricts browser requests to the website origin; it is not authentication and does not prevent non-browser requests.

The AI feature sends résumé text and the job description when the user starts. Microphone audio is recorded only after the user chooses Start recording, then uploaded to OpenAI for transcription. Typed or transcribed answers and submitted code go to OpenAI for interview feedback. CareerMatch does not save the session. Update the site's privacy notice if you change providers.

Code submissions are reviewed as text and are not executed by the website. The code textarea is a simple editor without syntax highlighting.
