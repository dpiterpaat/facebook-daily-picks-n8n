# 🏠 Facebook Daily Picks — n8n Workflow

An automated daily pipeline that scrapes Facebook Marketplace for student-friendly property rentals in Lund, Sweden (3,000–8,000 SEK/month), analyzes them with an LLM, and sends the best pick of the day to subscribers via Telegram.

> ⚠️ **Work in Progress** — This project is actively being developed. Planned future features include deep reasoning of the picks, such as explaining *why* a listing suits a student's needs beyond basic filtering.

> 🔒 **Personal Use Only** — This project is built for personal use. Commercial use is not condoned. That said, feel free to use this as a template and adapt it to any Facebook Marketplace category that suits your needs.

---

## 📬 How to Get the Daily Picks

Send a message to the Telegram bot to start receiving daily picks:

| Command | Action |
|---|---|
| `[YOUR_SUBSCRIBE_COMMAND]` | Subscribe to daily picks |
| `[YOUR_UNSUBSCRIBE_COMMAND]` | Remove yourself from the list |

👉 **Bot link:** [Your Bot Link Here]

Once subscribed, you will receive one curated property listing per day, sent as a photo with details.

---

## 📋 How It Works

This repository contains two n8n workflows that work together:

### 1. `Facebook Property Marketplace.json` — Main Workflow
Runs once daily and does the following:

```
Schedule Trigger
  → Scrape Facebook Marketplace (Apify actor)
    → Remove duplicate listings
      → Store all listings to Google Sheets
        → Feed listings to LLM (Google Gemini)
          → AI picks the best listing for a student
            → Compare against previous day's pick (avoid re-sending)
              → Send the top pick as a photo + message to all subscribers via Telegram
```

- Listings are filtered to property rentals in **Lund, Sweden** priced between **3,000–8,000 SEK/month**
- The LLM evaluates listings based on student suitability (price, location, practicality)
- All listings are logged to Google Sheets for record-keeping
- Only subscribers registered via the sub-workflow receive the daily pick
- You can adjust the send time in the **Schedule Trigger** node to match your preferred time and timezone

### 2. `Get Chat ID Telegram (FDP).json` — Subscription Sub-Workflow
Handles user subscription management via a Telegram bot:

```
Telegram Trigger (listens for messages)
  → If message is a valid command
    → /subscribe  → Check if already subscribed
                    → If not: add to Google Sheets user list → Confirm subscription
                    → If yes: notify already subscribed
    → /unsubscribe → Remove from Google Sheets user list → Confirm removal
    → Other: Send instruction message
```

Subscribers are stored in a Google Sheet that the main workflow reads daily before sending picks.

---

## 🛠️ Prerequisites

Before importing and running these workflows, make sure you have the following:

| Requirement | Purpose |
|---|---|
| [n8n](https://n8n.io/) instance (self-hosted or cloud) | Running the workflows |
| [Apify](https://apify.com/) account + API key | Scraping Facebook Marketplace listings |
| Google account + Google Sheets API credentials | Storing listings and subscriber list |
| [Google Gemini](https://ai.google.dev/) API key | LLM analysis of listings |
| Telegram Bot Token (via [@BotFather](https://t.me/botfather)) | Sending messages and receiving commands |

---

## 🚀 Setup & Import

1. Clone or download this repository
2. Open your n8n instance
3. Go to **Workflows → Import from file**
4. Import `Get Chat ID Telegram (FDP).json` first (sub-workflow)
5. Import `Facebook Property Marketplace.json` (main workflow)
6. Configure credentials for each node:
   - Apify API key
   - Google Sheets OAuth2
   - Google Gemini API key
   - Telegram Bot Token
7. Update the Google Sheet IDs and Telegram webhook URLs to match your own setup
8. Activate both workflows

> 💡 Tip: Run the sub-workflow first and subscribe yourself via Telegram before activating the main workflow, so you receive the first pick.

---

## 🔧 Customizing for Other Use Cases

This template is built around property rentals in Lund, Sweden, but the pattern is reusable for any Facebook Marketplace category:

- Change the **Apify actor input** to target a different category, location, or price range
- Update the **LLM system prompt** in the AI Agent node to reflect different evaluation criteria
- Adjust the **Google Sheets schema** to match the fields relevant to your items

Examples of what you could adapt this for: electronics, furniture, cars, bicycles — anything on Facebook Marketplace.


