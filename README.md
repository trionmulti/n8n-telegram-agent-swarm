# n8n AI Agent Swarm (via Telegram)

This n8n workflow implements an "agent swarm" architecture, often called a "router agent" or "dispatcher." A central AI agent analyzes incoming requests from Telegram (text or voice) and delegates the task to a specialized sub-agent (e.g., Email Agent, Calendar Agent) to perform the action.

## ✨ Features

* **Telegram Interface:** Interact with your agent swarm via text or voice.
* **Voice Transcription:** Uses `OpenAI` (Whisper) to transcribe voice notes.
* **Router Agent:** A main `AI Agent` (OpenRouter) acts as a dispatcher. It analyzes the user's request and routes it to the correct specialist agent.
* **Specialist Agents:**
    * **Email Agent:** Uses `GmailTool` to read, send, reply to, and manage Gmail.
    * **Calendar Agent:** Uses `GoogleCalendarTool` to create, find, update, and delete Google Calendar events.
    * **Contact Agent:** Uses `AirtableTool` to get or update contact information from an Airtable base.
    * **Web Agent:** Uses `TavilyTool` (quick search), `PerplexityTool` (deep research), and `OpenWeatherMapTool` (weather).
    * **YouTube Agent:** Uses `GoogleSheetsTool` to manage a video idea list and an `HTTPRequestTool` (Apify) to find top-performing videos.
* **Stateful Memory:** Uses `Simple Memory` to remember the conversation history for a single Telegram chat session.
* **Action Logging:** Logs every request, response, agent action, and token count to a Google Sheet for monitoring and debugging.

## ⚙️ Workflow Breakdown

1.  **Input:** A `Telegram Trigger` receives a text or voice message. Voice is transcribed by `Transcribe Audio`.
2.  **Router Agent:** The main `AI Agent` receives the input and consults its `Simple Memory`. Based on the prompt (e.g., "Send an email to...") it calls the appropriate tool, which is a *sub-agent* (e.g., `Email Agent`).
3.  **Specialist Agent:** The chosen sub-agent (e.g., `Email Agent`) receives the task (e.g., "send an email to nate..."). It then uses *its* specific tools (e.g., `GmailTool - Send Email`) to execute the request.
4.  **Execution:** The tool (e.g., Gmail) performs the real-world action.
5.  **Logging:** All agent steps and token usage are parsed by the `Clean Up` node and logged to a `Google Sheet`.
6.  **Response:** The final answer or a confirmation message is sent back to the user via the `Response` (Telegram) node.

## 🚀 Setup Guide

1.  **Import:** Download `Agent Swarm.json` and import it into your n8n canvas.
2.  **Connect Credentials:** This workflow requires many credentials.
    * **Telegram:** `telegramApi` (for `Telegram Trigger`, `Download Voice File`, `Response`, `Error Message`).
    * **OpenAI:** `openAiApi` (for the `Transcribe Audio` node).
    * **OpenRouter:** `openRouterApi` (for all the "GPT 4.1-mini" nodes that power the agents).
    * **Gmail:** `gmailOAuth2` (for the `GmailTool` nodes used by the Email Agent).
    * **Google Calendar:** `googleCalendarOAuth2Api` (for the `GoogleCalendarTool` nodes).
    * **Airtable:** `airtableTokenApi` (for the `AirtableTool` nodes).
    * **Google Sheets:** `googleSheetsOAuth2Api` (for the `GoogleSheetsTool` nodes *and* the `Append row in sheet` logging node).
    * **Tavily:** `tavilyApi` (for the `Tavily` tool).
    * **Perplexity:** `perplexityApi` (for the `Perplexity` tool).
    * **OpenWeatherMap:** `openWeatherMapApi` (for the `OpenWeatherMap` tool).
3.  **Configure Nodes:**
    * **Apify (YouTube Videos):** Open the `YouTube Videos` (`HTTPRequestTool`) node. In the `Query Parameters`, replace `YOUR APIFY API KEY` with your actual **Apify API Key**.
    * **Airtable (Contact Agent):** In the `Get Contacts` and `Add or Update Contact` nodes, select your Airtable `Base` and `Table`.
    * **Google Sheets (Logging):** In the `Append row in sheet` node, set your `Document ID` and `Sheet Name`. You can use the [Agent Action Log Sheet template](https://docs.google.com/spreadsheets/d/1PlRVi9Iv2n11SPYdshjhPGWccRCpKLHn3PjUkJEjgAQ/edit?usp=sharing).
    * **Google Sheets (YouTube Agent):** In the `Get Ideas` and `Add Idea` nodes, set your `Document ID` and `Sheet Name`. You can use the [YouTube Ideas Sheet template](https://docs.google.com/spreadsheets/d/1Jazczp5HtPwcJvu6bmJQrjkqt31S7adVqIFVCqXZwGk/edit?usp=sharing).
4.  **Activate:** Save and activate the workflow. You can now send messages to your bot on Telegram.

## 📄 License

MIT
