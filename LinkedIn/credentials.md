# Credentials

Workflow JSON in this folder stores credential **names and ids** only, so an import can re-link a credential that already exists on the same n8n instance. The files do not contain API keys, OAuth tokens, client secrets, or passwords. Create each credential in the target n8n instance and attach it on the nodes listed below.

Example names match the source instance. You can use those names or your own. After import, open each node and select the credential; a new instance will not have the source ids.

## Google Gemini (AI Studio)

| | |
| --- | --- |
| n8n credential type | `googlePalmApi` |
| Example name | `Gemini account` |
| Source credential id | `YOUR_GEMINI_CREDENTIAL_ID` |
| Model | Gemini 3.5 Flash Lite (`models/gemini-3.5-flash-lite`) |

Create an API key in Google AI Studio and store it in this n8n credential.

| Workflow | Nodes |
| --- | --- |
| [1.1-LinkedIn - Draft and Schedule by Chat](workflows/1.1-LinkedIn-Draft-and-Schedule-by-Chat.json) | `Google Gemini Chat Model - Writer`, `Google Gemini Chat Model - Scheduler`, `Google Gemini Chat Model - Admin` |
| [08-LinkedIn - Web UI](workflows/08-LinkedIn-Web-UI.json) | `Gemini 3.5 Flash Lite` (draft generation for the page) |

## LinkedIn OAuth2

| | |
| --- | --- |
| n8n credential type | `linkedInOAuth2Api` |
| Example name | `LinkedIn account` |
| Source credential id | `YOUR_LINKEDIN_CREDENTIAL_ID` |

| Workflow | Nodes |
| --- | --- |
| [03-LinkedIn - Publish Due Posts](workflows/03-LinkedIn-Publish-Due-Posts.json) | `Create LinkedIn post` |

The source node posts as LinkedIn person `YOUR_LINKEDIN_PERSON_ID`. After you connect your own LinkedIn account, set **Person** on that node to the member you want to post as.

Workflow 03 is the only workflow that creates LinkedIn posts. Leave it unpublished until the credential, person, and data table are ready.

## Gmail OAuth2

| | |
| --- | --- |
| n8n credential type | `gmailOAuth2` |
| Example name | `Gmail account` |
| Source credential id | `YOUR_GMAIL_CREDENTIAL_ID` |

| Workflow | Nodes |
| --- | --- |
| [06-LinkedIn - Error Alerts](workflows/06-LinkedIn-Error-Alerts.json) | `Send error email` |
| [07-LinkedIn - Overdue Post Watchdog](workflows/07-LinkedIn-Overdue-Post-Watchdog.json) | `Send digest email` |

Both nodes send to the placeholder `you@example.com`. Put your own alert email in **Send To** on both nodes before publishing 06 and 07.

Error and digest links inside 06 and 07 use the placeholder host `https://n8n.example.com`. Replace that host with your n8n base URL. The web UI in workflow 08 posts its forms to `https://n8n.example.com/webhook/linkedin-posts`; put your own host there too.

## What does not use a credential

The `linkedin_posts` Data Table is referenced by name. Workflows 02, 04, and 05 are sub-workflows and use no external credential. See [schema/linkedin_posts.md](schema/linkedin_posts.md).
