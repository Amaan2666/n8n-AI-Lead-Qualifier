# AI Lead Qualifier (n8n)

An n8n workflow with an AI agent:

1. **Lead Qualifier Agent** — reads customer call transcripts and automatically classifies each lead as a "Good Lead" (bulk/wholesale opportunity) or not, based on signals like quantity, logos/customization, team gifts, and events.

## How it works

**Lead Qualifier Agent**
- Pulls lead data (name, transcript) from a connected data source (Google Sheets in this setup)
- Passes each transcript to an LLM with a system prompt trained to spot bulk-order signals
- Uses a Structured Output Parser to force the response into consistent JSON: `Name`, `Is Good Lead`, `Reasoning`
- Formats the result into clean fields for use in a CRM or spreadsheet

## Nodes used

- **Chat Trigger / Manual Trigger** — starts each workflow
- **Google Sheets** — pulls lead transcripts as test/live data for the qualifier
- **AI Agent (LangChain)** — the core reasoning step for both agents
- **Anthropic Chat Model (Claude)** — the LLM powering both agents
- **Structured Output Parser** — enforces consistent JSON output for the lead qualifier
- **Simple Memory (Buffer Window)** — gives the chatbot short-term conversation context
- **Set / Edit Fields** — reshapes AI output into clean, usable fields
- **If** — routes the chatbot based on whether an order was confirmed
- **Code (No-Op)** — placeholder actions for order confirmation / no action

## Setup instructions

1. **Import the workflow**
   - In n8n: Workflows → Add Workflow → Import from File → select `workflow.json`

2. **Connect your Anthropic credential**
   - Click the **Anthropic Chat Model** node
   - Under Credentials, select "Create New" and add your Anthropic API key
   - Repeat for both AI Agent branches (Lead Qualifier and Chatbot) if they use separate credentials

3. **Connect Google Sheets (for the Lead Qualifier)**
   - Click the **Google Sheets** node
   - Create/connect a Google OAuth credential
   - Point it to your own lead data spreadsheet (must be a native Google Sheet, not a raw .xlsx)
   - Make sure your sheet has a `Customer Name` and `Transcript` (or similarly named) column, and update the field references in the AI Agent's prompt to match your column names

4. **Test the Lead Qualifier**
   - Click "Test workflow" from the manual trigger
   - Check the output of the Edit Fields node — you should see `Name`, `Good Lead`, and `Reasoning` for each row

## Notes

- Bulk discount tiers and product pricing are hardcoded in the chatbot's system prompt — update these to match your actual store
