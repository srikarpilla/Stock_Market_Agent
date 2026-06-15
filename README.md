# AI Stock Market Monitor (n8n Workflow)

An automated n8n workflow that runs twice on every trading day to deliver an AI-generated stock market briefing by email — covering price action for 7 major tech stocks, relevant market news, AI-driven sentiment/ratings (via Cohere), and a styled HTML report.

## What It Does

- Runs automatically at **market open (9:30 AM EST)** and **market close (4:05 PM EST)**, Monday–Friday.
- Pulls live price data for **AAPL, META, GOOGL, MSFT, TSLA, NVDA, AMZN** from Yahoo Finance.
- Pulls the latest finance/tech news headlines from **NewsAPI**.
- Sends both the price data and news to **Cohere AI** (`command-r-plus-08-2024`) for analysis — market sentiment, top mover, risk, opportunity, and Buy/Hold/Sell ratings per stock.
- Builds a dark-themed HTML report combining prices, ratings, AI commentary, and news links.
- Emails the report via SMTP.
- Includes a crash-alert banner if 4+ stocks drop ≥3% in a day, and a fallback mock-data generator if Yahoo Finance fails.

## Workflow at a Glance

```
Schedule Trigger (9:30/16:05 EST, Mon-Fri)
        │
Detect Morning/Evening (Code)
        │
        ├── Fetch Stock Prices (Yahoo Finance) → Parse Stock Data ──┐
        ├── Fetch Market News (NewsAPI) ─────────────────────────────┤
        └────────────────────────────────────────────────────────────┤
                                                                       │
                                              Merge All Inputs (3-way)
                                                       │
                                              Build Cohere Prompt
                                                       │
                                              Cohere AI Analysis
                                                       │
                                              Build Email HTML
                                                       │
                                              Send Report Email (SMTP)
```

## Tech Stack

| Component        | Service / Tool                          |
|-------------------|------------------------------------------|
| Orchestration     | n8n (workflow automation)                |
| Stock prices      | Yahoo Finance public spark API           |
| News              | NewsAPI.org                              |
| AI analysis       | Cohere `v2/chat` (command-r-plus-08-2024)|
| Email delivery    | SMTP (n8n Email Send node)               |

## Setup Requirements

1. **NewsAPI key** – replace the `apiKey` query param in the "📰 Fetch Market News" node with your own key from newsapi.org.
2. **Cohere API key** – replace the `Authorization: Bearer ...` header in the "🤖 Cohere AI Analysis" node with your own key from dashboard.cohere.com.
3. **SMTP credentials** – configure an SMTP credential in n8n for the "📨 Send Report Email" node, and update `fromEmail` / `toEmail`.
4. Activate the workflow (`active: true`) to enable the schedule trigger.

## ⚠️ Security Note

This exported workflow currently contains a **live Cohere API key and a NewsAPI key hardcoded in plain text**, plus hardcoded sender/recipient email addresses. Before sharing or committing this file anywhere, **rotate/revoke both keys** and move them into n8n credentials or environment variables.

## Disclaimer

This tool is for informational purposes only. The AI-generated ratings, sentiment, and outlook are **not financial advice**.
