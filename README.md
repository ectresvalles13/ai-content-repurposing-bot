# AI Content Repurposing Bot (n8n + Gemini)

Takes any blog post URL, extracts the article content, and uses an LLM to 
generate a summary plus ready-to-post social media captions for Twitter/X 
and LinkedIn — delivered instantly via Email and Slack.

## Demo
[Link to your Loom video here]

## How it works
1. **Webhook** receives a URL to an article
2. **HTTP Request** fetches the raw page HTML
3. **HTML Extract** pulls readable text content from the page
4. **Code node** builds a structured prompt using `JSON.stringify()` to 
   safely handle messy real-world text (quotes, special characters, etc.)
5. **HTTP Request (Gemini)** generates a summary, Twitter/X caption, and 
   LinkedIn caption, instructed to return strict JSON
6. **Code node** parses Gemini's response, with a defensive fallback that 
   adapts to the actual data shape rather than assuming a fixed field name
7. **Gmail** and **Slack** each deliver the finished content in parallel, 
   formatted natively for each platform (HTML for email, mrkdwn for Slack)

## Tech used
- n8n (self-hosted via Docker)
- Google Gemini API (via HTTP Request)
- Gmail API (HTML formatting)
- Slack API (mrkdwn formatting, Block Kit-ready)
- HTML Extract node for content scraping

## Key techniques
- **Safe JSON construction**: building the LLM request body in JavaScript 
  with `JSON.stringify()` instead of hand-typed JSON, to correctly escape 
  quotes and special characters in real article text — a hand-typed 
  expression silently breaks on messy scraped content
- **Defensive field access**: the parsing step doesn't assume a fixed 
  output field name; it falls back gracefully if an upstream node's 
  output shape is unexpected — critical for scraped/external data that 
  isn't always consistent
- **Platform-native formatting**: HTML (`<br>`, `<p>`) for email vs. 
  Slack's `mrkdwn` (`*bold*`, real newlines) — same content, correctly 
  adapted per channel

## Workflow file
See `content-repurposing-bot-workflow.json` — importable directly into 
any n8n instance. Requires a Gemini API key (free tier available) and 
Gmail/Slack credentials.

## Notes
Built as part of a self-directed learning path transitioning into 
AI Automation / VA work. This project involved real debugging of 
inconsistent scraped-data field naming — a common real-world challenge 
when automating around external websites.