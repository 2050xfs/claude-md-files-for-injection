# Marketing Automation Stack

**Loaded from**: `~/.claude/stacks/marketing-automation.md`
**When to use**: Blog creation, lead generation, email campaigns, business intelligence

---

## Tech Stack

**Automation Platforms:**
- Make.com (formerly Integromat) — visual workflow builder
- n8n — self-hosted workflow automation
- Zapier — simple integrations

**CRM & Pipeline:**
- Go High Level (GHL) — all-in-one CRM, automations, SMS, email
- Supabase — database + webhooks for custom workflows

**Lead Generation:**
- Apify — web scraping (Google Maps, LinkedIn, etc.)
- AnyMailFinder — email discovery
- Google Maps API — business data

**AI & Content:**
- OpenAI API / Claude API — content generation
- Google Gemini — article writing, summaries
- Lovable AI Gateway — unified AI access

**Email & Communication:**
- SMTP (Gmail, SendGrid, Mailgun)
- Twilio — SMS automation
- Slack — notifications and alerts

**Data Storage:**
- Google Sheets — simple data management
- Airtable — relational data + forms
- CSV files — local knowledge bases

---

## Common Use Cases

### 1. Multi-Agent Blog Creation
**Pipeline:**
Research Agent → Writing Agent → QC Agent → Final Draft Agent

**File Structure:**
```
00_knowledge_base/
├── long-tail-keyword-phrases.csv
└── published_articles_db.csv

01_research_output/
└── [topic_slug]_research.md

02_writing_output/
└── [topic_slug]_draft.html

03_qc_output/
└── [topic_slug]_review.md

04_final_output/
└── [topic_slug]_final.html
```

**Key Pattern:**
- CSV knowledge base (not traditional database)
- File-based workflow with state tracking
- Google Web Search API for research
- Manual confirmation between stages

---

### 2. Business Intelligence Scraper
**Pipeline:**
Web Form → Webhook → Make.com → Apify (Google Maps) → Data Enrichment → Google Sheets → Email

**Components:**
- Dynamic research requests via web form
- Automated business discovery and scoring
- Contact information enrichment
- Digital presence analysis
- Opportunity scoring algorithm

**Data Flow:**
```
1. User submits search query
2. Webhook triggers Make.com scenario
3. Apify scrapes Google Maps for businesses
4. Make.com enriches data:
   - Email discovery (AnyMailFinder)
   - Website check
   - Social presence analysis
   - Reviews and ratings
5. Score opportunities
6. Store in Google Sheets
7. Email results to user
```

---

### 3. Lead Generation & Nurture
**GHL Workflow:**
```
Form Submit → GHL Pipeline → Tag Assignment → Automation Trigger
→ Email Sequence / SMS Campaign → Task Creation → Follow-up
```

**Key GHL Concepts:**
- **Pipelines**: Visual sales stages
- **Custom Fields**: Store lead data
- **Tags**: Trigger automations
- **Webhooks**: Connect to external services
- **A2P Compliance**: SMS registration requirements
- **Sub-accounts**: Client-specific setups

**Speed-to-Lead:**
Trigger instant response within 60 seconds of form submit (critical for conversion).

---

## Make.com Patterns

**Webhook Trigger:**
```
Webhook (receive data) → Router (conditional logic) → Multiple Actions
```

**Common Modules:**
- HTTP Request — API calls
- Google Sheets — read/write data
- OpenAI / Claude — AI processing
- Email — send notifications
- Data Store — temporary storage
- Iterator — loop through arrays

**Error Handling:**
- Add error handler routes to all critical modules
- Use fallback actions (e.g., notify admin on failure)
- Set up retry logic for API calls

**Scheduling:**
Use Schedule module to run workflows on intervals (e.g., daily cleanup jobs).

---

## n8n Patterns

**Workflow Structure:**
```
Trigger Node → Data Processing → AI Node → Storage → Notification
```

**Common Nodes:**
- Webhook — receive data
- HTTP Request — API calls
- Code (JS) — custom logic
- OpenAI / Claude — AI processing
- Google Sheets — data storage
- Email / Slack — notifications

**Self-Hosted Benefits:**
- No execution limits
- Full control over data
- Custom nodes
- Local file access

---

## Go High Level (GHL)

**Core Features:**
- CRM with custom fields and pipelines
- Email marketing with templates
- SMS campaigns (requires A2P registration)
- Landing pages and funnels
- Calendar and booking system
- Workflow automations
- Webhooks for external integrations

**Automation Triggers:**
- Form submission
- Tag added/removed
- Pipeline stage change
- Custom field updated
- Appointment booked/cancelled
- Email opened/clicked
- SMS replied

**Webhook Integration:**
```
GHL Trigger → Webhook URL → External Service (Make.com/n8n) → Process Data
→ Webhook Response → GHL Action (update contact, add tag, etc.)
```

**Field Mapping:**
Use custom fields to store enriched data from external sources.

**A2P Compliance:**
Register phone numbers for SMS campaigns (requirement for US).

---

## AI Content Generation

**Prompt Engineering:**
- Be specific about tone, format, and length
- Include examples when possible
- Use structured output (JSON, markdown)
- Chain prompts for complex tasks (research → outline → draft → polish)

**Common Patterns:**
```
1. Research Phase
   - Query web search API
   - Extract key points
   - Summarize findings

2. Writing Phase
   - Generate outline
   - Write sections
   - Add examples and data

3. QC Phase
   - Check for accuracy
   - Review tone and style
   - Flag issues

4. Final Phase
   - Apply feedback
   - Format for publication
   - Update knowledge base
```

**Token Management:**
- Use context windows efficiently
- Chunk long documents
- Cache frequently used prompts

---

## Data & Storage

**CSV Files:**
- Simple, portable, version-controllable
- Good for keyword lists, published articles tracking
- Use pandas/Python for processing

**Google Sheets:**
- Collaborative, real-time updates
- Easy integration with Make.com/n8n
- Good for lead lists, form responses
- Use Apps Script for custom functions

**Supabase:**
- PostgreSQL database with realtime subscriptions
- Row Level Security (RLS) for data access
- Edge Functions for serverless logic
- Storage for files and images

---

## Common Workflows

### Email Drip Campaign
```
New Contact Added → Wait 1 Day → Send Email 1 → Wait 3 Days
→ Send Email 2 → Wait 5 Days → Send Email 3 → Tag as "Nurtured"
```

### Lead Scoring
```
Form Submit → Calculate Score (based on fields) → Assign Tag
→ Route to Pipeline Stage → Notify Sales Rep (if hot lead)
```

### Content Scheduling
```
Schedule (daily 9am) → Read CSV → Pick Random Keyword
→ Generate Article → QC Check → Post to Blog → Update CSV
```

### Review Monitoring
```
Schedule (every 6 hours) → Fetch Google Reviews
→ Check for New Reviews → If Negative: Alert Team + Create Task
→ If Positive: Send Thank You Email
```

---

## API Integration

**Apify (Web Scraping):**
```javascript
const apifyClient = new ApifyClient({ token: 'YOUR_TOKEN' })
const run = await apifyClient.actor('apify/google-maps-scraper').call({
  searchStringsArray: ['coffee shops in San Francisco'],
  maxCrawledPlaces: 50
})
const { items } = await apifyClient.dataset(run.defaultDatasetId).listItems()
```

**OpenAI (Content Generation):**
```javascript
const response = await openai.chat.completions.create({
  model: 'gpt-4',
  messages: [
    { role: 'system', content: 'You are a blog writer.' },
    { role: 'user', content: 'Write about coffee trends.' }
  ]
})
```

**GHL Webhooks:**
```
POST https://services.leadconnectorhq.com/hooks/...
Headers: { 'Authorization': 'Bearer YOUR_TOKEN' }
Body: { contact: { email, name, tags } }
```

---

## Error Handling & Monitoring

**Logging:**
- Log all workflow executions (timestamp, input, output, errors)
- Store logs in Google Sheets or database
- Set up alerts for critical failures

**Monitoring:**
- Check webhook delivery status
- Monitor API rate limits
- Track success/failure rates
- Set up Slack notifications for errors

**Retry Logic:**
- Retry failed HTTP requests (3 attempts with exponential backoff)
- Queue failed tasks for manual review
- Log all retry attempts

---

## Best Practices

**Workflow Design:**
- Keep workflows modular (single responsibility)
- Use clear naming conventions
- Document complex logic
- Test with sample data before production

**Data Management:**
- Validate input data at entry points
- Sanitize data before storage
- Use consistent field names across systems
- Archive old data regularly

**Performance:**
- Batch API calls when possible
- Use pagination for large datasets
- Cache frequently accessed data
- Optimize webhook response times

**Security:**
- Store API keys in environment variables
- Use webhook authentication
- Limit data exposure in logs
- Implement rate limiting on public webhooks

---

## Common Issues

**Webhook Timeouts:**
- Respond immediately (200 OK), process asynchronously
- Use queue systems for long-running tasks

**Rate Limits:**
- Implement exponential backoff
- Batch requests when API supports it
- Monitor usage and upgrade plans if needed

**Data Sync Issues:**
- Use unique identifiers (email, ID) for deduplication
- Implement idempotency in webhooks
- Add timestamp fields to track updates

---

**This stack is optimized for high-volume lead acquisition and content automation.**
