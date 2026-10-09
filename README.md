# University Admission WhatsApp Chatbot

An n8n-based WhatsApp assistant that guides prospective students through an admission enquiry, gathers the information needed for an initial eligibility review, and keeps applicant details organized in a student record sheet.

## Overview

Admission teams often receive repetitive questions and have to collect the same applicant details across separate conversations. This project uses WhatsApp as the entry point so a prospective student can share information conversationally instead of relying only on manual back-and-forth. The AI agent manages the conversation, while a separate record-management workflow handles the storage and updating of student details.

The goal is to make initial enquiry handling more consistent and make information easier for the admissions team to review. Any eligibility result is preliminary; the institution remains responsible for confirming eligibility and admission.

## What the workflow does

- Receives incoming messages through a WhatsApp Trigger.
- Uses an AI Agent powered by an OpenRouter chat model to interpret messages and continue the conversation.
- Uses Simple Memory to retain conversational context during the interaction.
- Calls a separate **University Admission System – Student Record Manager** workflow when student information needs to be saved or updated.
- Reads existing rows in Google Sheets, merges existing and incoming student data, and uses an append-or-update operation to maintain the record.
- Sends the response back through WhatsApp.

## Workflow architecture

### 1. WhatsApp conversation

```text
WhatsApp Trigger
      ↓
   AI Agent ───────── OpenRouter Chat Model
      │  └─────────── Simple Memory
      │  └─────────── Call “University Admission System – Student Record Manager”
      ↓
 Send WhatsApp Message
```

The AI Agent is the conversational layer. It can use the connected record-management workflow as a tool, rather than mixing all spreadsheet operations into the main conversation flow.

### 2. Student record management

```text
When Executed by Another Workflow
      ↓
Prepare Incoming Student Data
      ↓
Get Existing Row(s) in Google Sheets
      ↓
Merge Existing and Incoming Data
      ↓
Append or Update Row in Google Sheets
```

This separation keeps the chatbot focused on the conversation and the sub-workflow focused on preparing and persisting student data. The merge and append-or-update steps are intended to preserve existing information while applying new details; the matching key and merge rules should be verified against the actual sheet columns.

## Technology

- **n8n** — orchestrates the chatbot and record-management workflows.
- **WhatsApp integration** — receives student messages and sends replies.
- **AI Agent + OpenRouter Chat Model** — handles natural-language interaction.
- **Simple Memory** — provides conversation context.
- **Google Sheets** — stores student enquiry records.
- **n8n sub-workflow tool** — lets the agent invoke the separate student record manager.

## Intended use

This prototype is suited to early-stage admissions enquiries: collecting applicant details, helping students understand the next step, and organizing their information for counsellor follow-up. Any programme options, questions, and preliminary eligibility rules depend on the configuration supplied to the workflow.

## Setup

1. Import or recreate both n8n workflows.
2. Configure the WhatsApp credentials, webhook, and send-message settings.
3. Configure the OpenRouter chat model and the Simple Memory session key so conversation state is separated by sender.
4. Connect the Google Sheets document and confirm the column names used by **Prepare Incoming Student Data**, the lookup, and the merge/upsert steps.
5. Verify how an existing student is matched to an incoming message and how blank or missing fields are handled.
6. Test with dummy applicants and multiple messages from the same sender before using real enquiries.

Keep credentials in n8n's credential manager. Do not commit access tokens, API keys, real applicant contact details, or private records to the repository.

## Scope and limitations

- This is an automation prototype, not an official university admission portal.
- A preliminary eligibility response must not be presented as a final admission decision.
- Replies and saved records depend on the workflow prompts, configured rules, Google Sheets structure, and connected account permissions.
- Production use requires end-to-end testing, appropriate access controls for student data, and confirmation that the WhatsApp webhook and messaging permissions are configured correctly.
