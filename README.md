# Real-Estate-AI-Automation-N8N-AgencyTemplate
MVP and Model Context Protocol for an AI-powered real estate automation agency using n8n, OpenAI, Airtable, and Google Calendar.
# Real Estate AI Automation Agency

This project contains an MVP implementation and context protocol for an AI-powered front desk and automation solution for real estate businesses.

## Features
- Model Context Protocol schema and prompt templates
- n8n workflow JSONs for automation
- Setup guides and outreach messaging


# Real Estate AI Automation Agency

This project contains an MVP implementation and context protocol for an AI-powered front desk and automation solution for real estate businesses.

## Features
- Model Context Protocol schema and prompt templates
- n8n workflow JSONs for automation
- Setup guides and outreach messaging
context-protocol/schema-example.json

Copy{
  "client": {
    "name": "Jane Smith",
    "relationship": "Lead",
    "contact_history": [
      {"date": "2024-05-01", "summary": "Asked about 123 Main St."}
    ],
    "preferred_properties": [
      {"address": "123 Main St.", "status": "Available"}
    ]
  },
  "agent": {
    "name": "Alex Johnson",
    "availability": [
      {"date": "2024-05-17", "time": "3:00 PM - 5:00 PM"}
    ]
  },
  "business_info": {
    "business_hours": "Mon-Fri 9am-6pm",
    "office_address": "100 Real Estate Ave"
  },
  "current_interaction": {
    "channel": "phone",
    "timestamp": "2024-05-16T14:22:00Z",
    "intent": "schedule_showing",
    "property_in_question": "123 Main St."
  }
}
context-protocol/prompt-template.md

CopyYou are an AI front desk assistant for a real estate agency.
Here is the current context (as JSON):

{context_json}

The client just said: "{user_message}"

Based on the context, answer helpfully and proactively.
If the client wants to book an appointment and the agent is available, confirm the time.
If the client asks a question, answer using the context.
If the issue is urgent, recommend escalation.




Certainly, Tee! Here’s a full set of **ready-to-copy Markdown and JSON files** for your real estate AI automation agency project. Just create the folders/files as suggested and paste the content below.

---

**1. README.md**

```markdown
# Real Estate AI Automation Agency

An MVP and model context protocol for an AI-powered front desk and automation solution for real estate businesses.

## Features

- AI Front Desk for call/chat/appointment handling
- Model Context Protocol schema and prompt templates
- n8n workflow JSONs for automation
- Setup guides and outreach messaging

## Structure

- `context-protocol/` — Schema and prompt template for context-aware AI
- `n8n-workflows/` — n8n exportable JSON workflows
- `docs/` — Setup and business collateral
- `assets/` — Images and diagrams

## Quick Start

1. Import the workflow from `n8n-workflows/ai-frontdesk-starter.json` into your n8n instance.
2. Connect your Airtable, Google Calendar, and OpenAI accounts.
3. Adapt the model context schema in `context-protocol/`.
4. Use the outreach messaging and setup guide in `docs/`.

---
```

---

**2. context-protocol/schema-example.json**

```json
{
  "client": {
    "name": "Jane Smith",
    "relationship": "Lead",
    "contact_history": [
      {
        "date": "2024-05-01",
        "summary": "Asked about 123 Main St."
      }
    ],
    "preferred_properties": [
      {
        "address": "123 Main St.",
        "status": "Available"
      }
    ]
  },
  "agent": {
    "name": "Alex Johnson",
    "availability": [
      {
        "date": "2024-05-17",
        "time": "3:00 PM - 5:00 PM"
      }
    ]
  },
  "business_info": {
    "business_hours": "Mon-Fri 9am-6pm",
    "office_address": "100 Real Estate Ave"
  },
  "current_interaction": {
    "channel": "phone",
    "timestamp": "2024-05-16T14:22:00Z",
    "intent": "schedule_showing",
    "property_in_question": "123 Main St."
  }
}
```

---

**3. context-protocol/prompt-template.md**

```markdown
You are an AI front desk assistant for a real estate agency.

Here is the current context (as JSON):

{context_json}

The client just said: "{user_message}"

Based on the context, answer helpfully and proactively.
- If the client wants to book an appointment and the agent is available, confirm the time.
- If the client asks a question, answer using the context.
- If the issue is urgent, recommend escalation.
```

---

**4. n8n-workflows/ai-frontdesk-starter.json**

*Copy this into your `n8n-workflows/ai-frontdesk-starter.json` file. (You may need to adapt resource IDs or credentials in your n8n environment.)*

```json
{
  "nodes": [
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "ai-frontdesk"
      },
      "name": "Webhook Trigger",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 1,
      "position": [100, 200]
    },
    {
      "parameters": {
        "operation": "list",
        "table": "Contacts",
        "baseId": "YOUR_AIRTABLE_BASE_ID",
        "filterByFormula": "({Phone} = '{{ $json[\"phone\"] }}')"
      },
      "name": "Airtable Lookup",
      "type": "n8n-nodes-base.airtable",
      "typeVersion": 1,
      "position": [300, 200]
    },
    {
      "parameters": {
        "calendar": "YOUR_GOOGLE_CALENDAR_ID",
        "operation": "getAll",
        "options": {
          "timeMin": "={{$now}}"
        }
      },
      "name": "Google Calendar Availability",
      "type": "n8n-nodes-base.googleCalendar",
      "typeVersion": 1,
      "position": [500, 200]
    },
    {
      "parameters": {
        "functionCode": "// Assemble model context\nreturn [{\n  json: {\n    client: $items(\"Airtable Lookup\")[0].json,\n    agent: {name: \"Alex Johnson\", availability: $items(\"Google Calendar Availability\")},\n    business_info: { business_hours: \"Mon-Fri 9am-6pm\", office_address: \"100 Real Estate Ave\" },\n    current_interaction: $json\n  }\n}];"
      },
      "name": "Assemble Context",
      "type": "n8n-nodes-base.function",
      "typeVersion": 1,
      "position": [700, 200]
    },
    {
      "parameters": {
        "requestMethod": "POST",
        "url": "https://api.openai.com/v1/chat/completions",
        "jsonParameters": true,
        "options": {},
        "bodyParametersJson": "{\n  \"model\": \"gpt-4o\",\n  \"messages\": [\n    {\"role\": \"system\", \"content\": \"You are an AI front desk assistant for a real estate agency.\"},\n    {\"role\": \"user\", \"content\": \"Here is the current context: {{$json}}\\nThe client just said: '{{$json.current_interaction.message}}'\"}\n  ],\n  \"max_tokens\": 300\n}"
      },
      "name": "OpenAI GPT-4o",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 1,
      "position": [900, 200]
    }
  ],
  "connections": {
    "Webhook Trigger": {
      "main": [
        [
          {
            "node": "Airtable Lookup",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Airtable Lookup": {
      "main": [
        [
          {
            "node": "Google Calendar Availability",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Google Calendar Availability": {
      "main": [
        [
          {
            "node": "Assemble Context",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Assemble Context": {
      "main": [
        [
          {
            "node": "OpenAI GPT-4o",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

---

**5. docs/setup-guide.md**

```markdown
# Setup Guide: Real Estate AI Automation Agency

## Prerequisites

- n8n instance (cloud or self-hosted)
- Airtable account (for CRM/lead storage)
- Google Calendar (for agent scheduling)
- OpenAI API key (for GPT-4o model access)

## Steps

1. Clone this repository and import the workflow from `n8n-workflows/ai-frontdesk-starter.json` into your n8n dashboard.
2. Set up Airtable with a `Contacts` table. Add sample fields: `Name`, `Phone`, `Email`, `Contact History`, `Preferred Properties`.
3. Connect your Google Calendar and OpenAI accounts in n8n (under Credentials).
4. Edit the workflow nodes to match your table and calendar IDs.
5. Test the webhook by sending a POST request with sample data:
    ```json
    {
      "phone": "+1234567890",
      "channel": "phone",
      "intent": "schedule_showing",
      "property_address": "123 Main St.",
      "message": "I'd like to book a showing for tomorrow."
    }
    ```
6. Review the AI response and adjust the context schema or prompt as needed.

---

## Troubleshooting

- Ensure all credentials are set up in n8n.
- If the OpenAI node fails, check your API key and prompt formatting.
- For questions, contact [your support contact].

---
```

---

**6. docs/outreach-messaging.md**

```markdown
# Outreach Messaging for Real Estate AI Automation Agency

## Email/DM Template

Subject: Free Workflow Audit for Real Estate Pros—Save 10+ Hours a Week

Hi [Agent’s Name],

I specialize in helping real estate professionals like you automate repetitive tasks—like lead follow-up, appointment scheduling, and document handling—so you can spend more time closing deals.

I’d love to offer you a **free 15-minute workflow audit** where we’ll:
- Identify your biggest time-wasters
- Show you 2–3 quick automations that can save you 10+ hours a week
- Answer any questions about what’s possible with the latest AI tools

No sales pitch—just actionable ideas tailored to your business.
If you’re interested, you can book a time here: [Calendly Link]
Or just reply and let me know what works for you.

Looking forward to helping you work smarter,  
[Your Name]  
[Your Agency Name]  
[Your Phone/WhatsApp, if appropriate]

---

## Landing Page Copy

Automate Your Real Estate Business. Get Your Time Back.

We help agents and property managers save 10+ hours a week with AI-powered automations for lead follow-up, scheduling, and paperwork.

[Book My Free Workflow Audit]

---
```

---

