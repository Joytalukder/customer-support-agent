# AI Customer Support Email Automation with n8n

An AI-powered customer support workflow built with **n8n**. It monitors incoming Gmail messages, uses an AI Agent with a retrieval-based knowledge source to draft a relevant response, and replies to the original email.

## Overview

The goal of this project is to automate first-line email support while reducing unsupported answers. The AI Agent is instructed to use information retrieved from the company Knowledge Base only. If the required information is unavailable, it should politely direct the customer to the support team.

## Key Features

- **Gmail monitoring:** Checks for incoming email on a scheduled polling interval.
- **AI-generated replies:** Uses an n8n AI Agent to produce concise, professional customer-support responses.
- **Knowledge retrieval (RAG):** Connects a Supabase Vector Store as a retrieval tool for company information.
- **Embeddings:** Uses Mistral Cloud embeddings to represent documents for semantic retrieval.
- **Chat model:** Uses the Groq Chat Model (`openai/gpt-oss-120b`) for AI responses.
- **Conversation memory:** Includes PostgreSQL Chat Memory.
- **Email response:** Sends the AI Agent's output as a reply to the triggering Gmail message.
- **Knowledge-base setup branch:** Includes a manual trigger, Google Drive folder creation, and a Supabase Vector Store insert node.

## Workflow Architecture

### Customer-support path

```text
Gmail Trigger
    ↓
AI Agent  ← Groq Chat Model
    ↑       ← PostgreSQL Chat Memory
    ↑
Supabase Vector Store (retrieval tool)
    ↑
Mistral Cloud Embeddings
    ↓
Reply to a message (Gmail)
```

### Knowledge-base setup branch

```text
Manual Trigger
    ↓
Create Google Drive folder
    ↓
Supabase Vector Store (insert mode) ← Mistral Cloud Embeddings
    ↑
Default Data Loader
```

> **Important:** The supplied workflow JSON includes the nodes above, but it does not show a Google Drive file-reading node connected to the Default Data Loader. Configure your document ingestion path and confirm that your documents are inserted into Supabase before relying on knowledge retrieval.

## Technology Stack

- [n8n](https://n8n.io/) — workflow automation
- Gmail / Google OAuth2 — email trigger and replies
- [Supabase](https://supabase.com/) — vector database
- Mistral Cloud — text embeddings
- [Groq](https://groq.com/) — chat model inference
- PostgreSQL — chat memory

## Requirements

Before running the workflow, prepare:

- An n8n instance (cloud or self-hosted)
- A Google account with Gmail and Google Drive access
- A Supabase project and a vector table named `documents`, configured for the embedding dimensions and schema required by your selected embedding model
- API credentials for Mistral Cloud and Groq
- A PostgreSQL database accessible to n8n, if using the included chat-memory node

Node options and credential requirements may vary by n8n version.

## Installation and Setup

1. **Download the workflow JSON** from this repository.
2. Open n8n and select **Import from File** (or the equivalent workflow import option).
3. Select the downloaded `.json` file.
4. Open each integration node and create or select your own credentials:
   - Gmail Trigger and Reply to a message
   - Google Drive
   - Supabase
   - Mistral Cloud
   - Groq
   - PostgreSQL
5. In Supabase, ensure the `documents` table exists and is configured correctly for the Supabase Vector Store node.
6. Configure a document-ingestion workflow to load your company FAQs, policies, product details, and other approved support documents into Supabase.
7. Test retrieval using sample questions whose answers are present in the knowledge base, then test questions whose answers are missing.
8. Send a test email to the connected Gmail account and verify the generated reply before enabling the workflow for real customer traffic.
9. Review the Gmail trigger polling settings and workflow activation settings to match your needs.

## AI Agent Behaviour

The configured agent prompt instructs the assistant to:

- Read the customer's email carefully.
- Use only information retrieved from the Knowledge Base.
- Avoid inventing or guessing answers.
- Tell the customer when the answer cannot be found and suggest contacting the support team.
- Write a professional, polite, concise email with a greeting and closing.

**Note:** Prompt instructions reduce the risk of unsupported answers but do not guarantee perfect retrieval or prevent every hallucination. Test the workflow and add appropriate review or escalation controls before production use.

## Security Notes

- **Never commit API keys, OAuth tokens, passwords, or private credentials** to GitHub.
- Configure credentials securely through n8n's credential manager.
- Use a dedicated test mailbox and non-sensitive sample data while developing.
- Apply appropriate access controls and retention policies to customer emails, vector data, and chat memory.
- Consider adding human approval before automatically sending replies to customers.

## Testing Checklist

- [ ] Gmail Trigger detects a new test email.
- [ ] The knowledge base contains the relevant answer.
- [ ] Supabase retrieval returns relevant information.
- [ ] The AI Agent responds accurately and professionally.
- [ ] Unknown questions receive a safe fallback response.
- [ ] Gmail replies to the correct original message.
- [ ] Credentials and private customer data are not exposed in the repository.

## Limitations and Future Improvements

- Add and validate a complete Google Drive/document ingestion path.
- Add human handoff for unresolved, sensitive, or low-confidence questions.
- Add error handling, retries, and failure notifications.
- Add logging and monitoring without storing unnecessary personal data.
- Add evaluation tests for retrieval quality and response accuracy.
- Consider filtering which emails trigger an automated reply to avoid replying to spam, automated messages, or unsupported email types.

## Repository Contents

```text
.
├── README.md
└── assinment06.json
```

Rename the exported workflow file to `assinment06.json` or update the filename above to match the file you commit.

## Author

**Joy Talukder**  
AI Automation | n8n | RAG | Workflow Integration

- GitHub: [Joytalukder](https://github.com/Joytalukder)
- LinkedIn: [Joy Talukder](https://www.linkedin.com/in/joy-talukder-3b08b036a/)

## License

No license is specified in the supplied workflow. Add a `LICENSE` file if you want to make the repository's reuse permissions explicit.
