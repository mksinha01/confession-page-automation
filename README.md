## Confession Page Automation (Make.com)

This repository contains an exported Make.com scenario (`temp auto.blueprint.json`) that automates taking confessions submitted via Google Forms and turning them into a slide image which is then posted to an Instagram Business page.

Summary
- Source trigger: Google Forms / Google Sheets ("Form Responses 1").
- AI processing: Two AI agent steps are present — one used to produce slide content and another used to produce the Instagram caption.
- Slide generation: A Google Slides template slide is populated with the confession text and a new slide is created in Drive.
- Thumbnail retrieval: The created slide page thumbnail is fetched.
- Posting: The thumbnail image is used to create an Instagram post (Instagram Business / Facebook connection).
- Cleanup: The temporary slide is deleted after posting.

Is this a "confession page" automation?
Yes. The JSON describes a scenario that watches a Google Forms responses sheet (column labeled `confession (B)`), takes the confession text, uses an AI agent to produce content for a slide and caption, generates a slide image from a Google Slides template, and posts that image to an Instagram Business account. The flow names and field labels clearly indicate the intent to publish confessions.

Flow (high level)
1. google-forms:watchRows — monitors a spreadsheet (`Form Responses 1`) for new rows.
2. ai-agent:RunAnAIAgent (id 27) — receives the form "confession" column and produces response text used for the slide.
3. google-slides:createASlideFromATemplate (id 21) — creates a new slide from a template, inserting the AI-generated confession into the slide placeholder.
4. google-slides:getPage (id 22) — retrieves the slide page (thumbnail / content URL) created in step 3.
5. ai-agent:RunAnAIAgent (id 28) — (second AI step) generates the Instagram caption (mapped to the confession column as input).
6. instagram-business:CreatePostPhoto (id 24) — posts the slide image to Instagram using the caption from step 5.
7. google-slides:addDeleteASlide (id 25) — deletes the temporary slide that was created.

Key configuration values to update before importing
- Google connection(s) — authorised account(s) with access to the spreadsheet and the presentation template.
- `spreadsheetId` and `sheetId` — set to your target Google Sheet (Form Responses tab).
- Presentation ID (`presentation`) and template `pageObject` — choose the Slides file and the template slide object to clone and populate.
- Instagram / Facebook connection (`accountId`) — Instagram Business page ID connected via Facebook.
- AI agent IDs — ensure the scenario's AI agent IDs correspond to agents you have access to in Make.com.

How to import into Make.com
1. Sign in to your Make.com account.
2. Create a new Scenario and choose the option to import or paste a JSON/blueprint (Make.com supports importing exported scenarios). If Make.com UI has changed, create modules in the order above and map the fields according to the JSON.
3. Attach or create the required connections (Google, Facebook/Instagram, AI agents).
4. Update the module parameters (spreadsheetId, presentation ID, page object, account ID, connections).
5. Run the scenario in manual mode and test with a sample form response.

Data mapping notes
- The JSON maps the confession text from the Google Forms sheet cell `{{3.`1`}}` to both AI agent inputs and to the slide template placeholder named `confession`.
- The Instagram post uses the slide's `contentUrl` from Google Slides as `image_url` and uses the response from the second AI agent as the `caption`.

Privacy & moderation
- Confessions are user-submitted content and can contain sensitive or personal information. Ensure you have consent to publish.
- Consider adding moderation steps (automated content filtering or manual approval) before posting public content.

Files in this repo
- `temp auto.blueprint.json` — exported Make.com scenario (source of truth for this README).

Assumptions
- The JSON was exported from Make.com and references connections (Google account, Facebook/Instagram account, AI agents) that must be re-authorized in your Make.com environment.
- The presentation referenced is a Slides file with a templated slide that contains a placeholder named `confession`.

Troubleshooting
- If the slide thumbnail is missing: verify the `getPage` module is pointed at the correct objectId and presentationId from the created slide module.
- If Instagram fails to post: confirm the Facebook connection has publish permissions and the accountId is the correct Instagram Business page ID.
- If AI outputs are empty: verify the AI agent IDs and any required credentials in Make.com.

License
This README and the exported JSON are provided without warranty. Adapt and review before publishing.

Contact / Next steps
- If you want, I can:
  - Produce a diagram of the module flow.
  - Generate a sanitized example Google Slides template (public) you can import.
  - Add a small moderation/manual-approval step to the scenario.

---
Requirements coverage
- Read and interpret `temp auto.blueprint.json`: Done
- Determine whether it is a "confession page" automation: Done — yes, described above
- Generate a GitHub-ready `README.md` describing the automation and setup: Done (this file)
