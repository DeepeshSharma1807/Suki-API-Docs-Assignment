# Prompts And AI tools Used 
# Prompts And AI Tools Used 



## Tool 1: Google NotebookLLM
**Goal:** Understand how webhooks actually work. 

**Prompt:**
> *"I am a beginner technical writer. Can you explain the difference between 'API polling' and 'Webhooks' in simple English? Why do engineers prefer webhooks?"*

**What I kept and what I rejected:**
- **Kept:** I used its analogy (polling is like asking "are we there yet?", webhooks are like "I'll call you when we get there"). This helped me write the "Best Practices" section in my own words.
- **Rejected:** It generated a bunch of complicated backend code to show how to build a webhook server. I completely ignored that part because it was out of scope for our API guide.

## Tool 2: ChatGPT / Claude
**Goal:** Figure out a good structure for the doc and write a boilerplate code example.

**Prompts:**
> 1. *"What standard sections should be in REST API docs?"*
> 2. *"Take this JSON request body and write a JavaScript fetch example for it."*

**What I kept and what I rejected:**
- **Kept:** I used the suggested layout (Auth -> Endpoints -> Responses) because it felt organized and standard. I also kept the basic structure of the `fetch` request it wrote.
- **Rejected:** It suggested a massive "Error Codes" table at the very bottom of the page. I rejected that and moved the errors right under the responses where they are actually useful. 
- **Modified:** In the JS example, the AI hardcoded a fake token (`Bearer 12345`). I changed this to `<your_token_here>` and added a security warning in the doc, because hardcoding tokens is a bad practice I learned about.

## Tool 3: Grammarly
**Goal:** Check grammar and flow.

**What I did:** Ran my final draft through it to catch typos.

**What I kept and what I rejected:**
- **Kept:** Basic typo fixes.
- **Rejected:** It kept trying to make my sentences sound really formal and academic. I ignored those suggestions because I want the API doc to be conversational and easy for developers to scan.

---
**Summary:** 
Using AI was a great way to quickly learn what webhooks were and get a standard template going. This saved me time so I could focus on making the guide easy to read and highly focused on the Suki API.
