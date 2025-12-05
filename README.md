# ElevenLabs Real Estate AI Agent Suite (n8n Workflows)

This repository contains the blueprints for a fully automated, voice-enabled Real Estate AI Agent system.  
The architecture is designed to handle outbound calling campaigns, real-time booking, and post-call lead qualification using **ElevenLabs**, **n8n**, and **Google Gemini**.

---

## 📂 Repository Contents

The system is composed of three distinct **n8n workflows** that work together.

| Workflow File | Type | Function |
|---------------|-------|----------|
| `elevenlabs shutterxz real estate outbound agent.json` | **Orchestrator** | The “Caller” workflow. Triggers outbound calls to leads via the ElevenLabs API. |
| `Elevenlabs real estate Booking agent tool.json` | **Server-Side Tool** | The “Action” tool. Used during calls to check availability & book appointments. |
| `elevenlabs real estate lead qualification tool.json` | **Analysis Tool** | The “Analyst” tool. Processes transcripts and updates CRM with qualification data. |

---

## 🏗️ System Architecture

### 1. Outbound Orchestrator (`...outbound agent.json`)

Responsible for initiating calls. Acts as the **Dialer**.

**Trigger:** Scheduled or manual.  
**Logic:**
1. Fetch a batch of “Not Called” leads from Google Sheets.  
2. Loop through each lead.  
3. Send a `POST` request to:  
   `v1/convai/twilio/outbound-call` (ElevenLabs).  
4. Update lead status to **Called**.

---

### 2. Booking Agent Tool (`...Booking agent tool.json`)

A **Custom ElevenLabs Tool** invoked during the call when a user requests a viewing.

**Input:**  
Conversation summary or extracted date/time entities.

**AI Logic:**  
A Google Gemini agent determines whether the user wants availability info or wants to book directly.

**Integrations:**
- **Google Calendar**  
  - Read availability  
  - Create events

**Output:**  
Natural language response returned to the voice agent  
(e.g., “Your viewing is confirmed for 2 PM.”)

---

### 3. Lead Qualification Tool (`...lead qualification tool.json`)

The **Brain** of the system. Converts transcripts into CRM-ready data.

**Trigger:**  
Called by ElevenLabs at call end, or via webhook.

**AI Analysis:**  
A Google Gemini agent evaluates the transcript using a strict rubric:
- **Need** – Is the user buying/selling?  
- **Budget** – Can they afford the property?  
- **Timeline** – When are they moving?

**Output:**  
A structured JSON object, plus an update to Google Sheets with:
- Qualification status  
- Summary of evidence

---

## 🚀 Setup Instructions

### Prerequisites

- **n8n** — self-hosted or cloud  
- **ElevenLabs** — Conversational AI enabled  
- **Twilio** — telephony backbone  
- **Google Cloud Console** — Sheets, Calendar, Gemini APIs enabled  

---

### Step 1: Import Workflows

Import all three `.json` workflow files into n8n.

---

### Step 2: Configure Credentials

Update credentials in each workflow:
- **ElevenLabs API Key** — outbound dialing  
- **Google Gemini** — Booking & Qualification AI logic  
- **Google Sheets & Calendar** — lead management & scheduling  

---

### Step 3: Connect to ElevenLabs

1. Go to **ElevenLabs Agent Settings**  
2. **Add Booking Tool:**  
   - Create *Client/Webhook* tool  
   - Paste Production URL from Booking workflow  
3. **Add Qualification Tool:**  
   - Same process  
4. **Copy Agent ID:**  
   - Paste into the outbound agent workflow’s HTTP Request node  

---

## 📄 License

**MIT**

