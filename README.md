## Confession Page Automation (Make.com)

This repo contains a Make.com scenario blueprint (`temp auto.blueprint.json`) that turns Google Form confessions into Instagram posts. It watches a responses sheet, generates a slide image from a template with AI-refined text, posts to Instagram, and cleans up the temporary slide.

What it does
- Watches Google Forms responses in a Google Sheet (tab: "Form Responses 1").
- Uses AI to refine/format the confession for the slide and to generate the Instagram caption.
- Creates a new slide from a Google Slides template by injecting the confession text.
- Fetches the created slide’s thumbnail URL.
- Posts the image to Instagram Business with the AI-generated caption.
- Deletes the temporary slide.

Is this a confession-page automation?
Yes. The source column is labeled `confession (B)`, and the flow’s purpose is to publish confessions as posts.

Flow overview (module IDs from the blueprint)
1) 3 — google-forms:watchRows
  - Reads new rows from the Google Sheet (columns: A Timestamp, B confession, etc.).
2) 27 — ai-agent:RunAnAIAgent
  - Input: `{{3.`1`}}` (column B: confession)
  - Output: `{{27.response}}` used for slide text.
3) 21 — google-slides:createASlideFromATemplate
  - Template slide object: `pageObject` set to the selected template slide.
  - Maps `{ confession: {{27.response}} }` into a placeholder named `confession` on the template.
4) 22 — google-slides:getPage
  - Gets the created slide page and exposes `contentUrl` (thumbnail).
5) 28 — ai-agent:RunAnAIAgent
  - Input: `{{3.`1`}}` (same confession)
  - Output: `{{28.response}}` used as the Instagram caption.
6) 24 — instagram-business:CreatePostPhoto
  - `image_url`: `{{22.contentUrl}}`
  - `caption`: `{{28.response}}`
7) 25 — google-slides:addDeleteASlide
  - Deletes the slide created in step 3 (uses `{{21.objectId}}`).

Prerequisites
- Make.com account with access to: Google (Sheets/Slides), Facebook/Instagram Business, and AI Agents.
- Google Sheet with a Forms-linked tab named "Form Responses 1" and a column B titled "confession".
- A Google Slides presentation with a template slide containing a text placeholder named `confession`.
- Instagram Business account connected through Facebook with publish permissions.

Setup (import and configure)
1. In Make.com, create a new Scenario and import the blueprint JSON (paste/upload `temp auto.blueprint.json`).
2. Reconnect modules to your own connections:
  - Google (Sheets/Slides)
  - Facebook/Instagram (Instagram Business)
  - AI Agents (both modules)
3. Update module parameters where needed:
  - google-forms:watchRows — select your Spreadsheet and `sheetId` ("Form Responses 1").
  - google-slides:createASlideFromATemplate — choose your Presentation and the template `pageObject` (template slide).
  - instagram-business:CreatePostPhoto — choose your Instagram Business page/account.
4. Run once in manual mode to validate mappings; then schedule the scenario.

Data mappings (verified from the JSON)
- Confession source: `{{3.`1`}}` (column B)
- Slide placeholder mapping: `requests.confession = {{27.response}}`
- Slide page ID to fetch: `{{21.objectId}}`; Presentation ID: `{{21.presentationId}}`
- Instagram image URL: `{{22.contentUrl}}`
- Instagram caption: `{{28.response}}`

Moderation and privacy
- Confessions can include sensitive content. Add a review step (manual or automated) before posting.
- Ensure consent and comply with platform policies and local laws.

Troubleshooting
- No image posted: Confirm `getPage` returns a valid `contentUrl` and that Instagram allows the URL.
- Permission errors: Re-authorize Google/Instagram connections and verify access to the Sheet/Slides/Page.
- Empty AI output: Check AI agent availability/IDs and timeouts.
- Wrong slide text: Verify the template has a placeholder named exactly `confession`.

Limits and notes
- Make runs/polling are subject to your plan’s operation limits.
- Google Slides/Drive thumbnails may take a moment; transient 404s can occur—retry helps.
- Instagram Graph API requires a Business account connected to a Facebook Page with publish permissions.

Repository contents
- `temp auto.blueprint.json` — exported scenario.
- `README.md` — this document.

License
No license is provided. Add one if you plan to distribute.

