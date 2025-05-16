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
