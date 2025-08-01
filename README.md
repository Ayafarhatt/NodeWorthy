# 🤖 Team Project: AI Reminder Bot (Telegram + Google Sheets + Google Calendar)

## 📌 Overview

This project is a smart **AI-powered reminder bot** that runs on **n8n** and integrates with:

* **Telegram** for receiving user messages and sending reminders.
* **Google Sheets** for logging tasks.
* **Google Calendar** for daily event summaries.
* **AI model (Mistral + LangChain Agent)** to parse natural language and extract task details.

The bot allows users to send natural-language commands like:

> “Remind me to submit the report at 4pm”
> and handles the rest — intelligently setting the reminder, storing the data, and delivering alerts.

---

## 🔧 Main Workflows

### 1. Reminder Bot (Triggered by Telegram)

#### 🎯 Trigger:

* Activated when a user sends a message to the Telegram bot.

#### 🧠 Process:

1. **Get current date/time** using `Date & Time` node.
2. **Send message to AI agent** using Mistral via LangChain to extract:

   * `Task` – the action the user wants to remember.
   * `ReminderDateTime` – a full ISO 8601 datetime string (with timezone).
3. **Parse AI output** using a `Code` node.

   * Adjust reminder time if it's already passed today.
   * Format confirmation and reminder messages.
4. **Send confirmation** back to the user via Telegram.
5. **Log task details** to **Google Sheets**:

   * Task description
   * Reminder datetime
   * Timestamp of creation
6. **Wait** until the scheduled reminder time.
7. **Send reminder message** via Telegram.

#### 📄 Example:

> **Input:** "Remind me to call mom at 7pm"
> **AI Output:**
>
> ```
> Task: call mom  
> ReminderDateTime: 2025-08-01T19:00:00+03:00
> ```

---

### 2. Daily Calendar Reminder (Triggered by Cron)

> 💡 **Developed by:** *Aya Farhat* (Google Calendar Integration)

#### ⏰ Trigger:

* Runs automatically at **9:00 AM** daily.

#### 🔍 Process:

1. **Fetch events** from **Google Calendar** for the current day (Timezone: Asia/Beirut).
2. **Format each event** with:

   * Title
   * Start & End times
   * Location (optional)
3. **Check** if any events exist for the day.

#### 📤 Output:

* If events exist, format a **summary message** and send it to a **fixed Telegram chat ID**.
* If no events are found, a default message is sent.

---

## 📥 Data Storage

All reminders are logged in **Google Sheets** with the following columns:

| Task           | ReminderDateTime | CreatedAt        |
| -------------- | ---------------- | ---------------- |
| Submit project | 2025-08-01 18:00 | 2025-08-01 10:30 |

---

## ⚠️ Notes

* You must **manually set your Telegram chat ID** in the `Send Telegram Reminder` node for the daily summary to work.
* Dynamic chat ID mapping from Telegram triggers is not yet supported for cron-based flows.

---

## 💡 Technologies Used

* [n8n](https://n8n.io/) – Automation platform
* [Telegram Bot API](https://core.telegram.org/bots/api)
* [Google Sheets API](https://developers.google.com/sheets/api)
* [Google Calendar API](https://developers.google.com/calendar)
* [LangChain](https://www.langchain.com/) + [Mistral AI](https://mistral.ai/) – For natural language understanding

