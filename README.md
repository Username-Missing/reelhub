# reelhub
🚀 AI Reels Automation Pipeline

End-to-End Multi-Agent System for Scriptwriting, Video Generation & Instagram Auto-Posting

This project is a fully automated pipeline that generates Instagram Reels using AI agents and publishes them to Instagram — without human intervention.
The workflow is orchestrated using n8n, with LLM processing from Groq, video generation via Google Veo, and social media posting through the Meta Graph API.

⸻

⭐ Project Overview

This system handles the entire short-form content lifecycle:
	1.	Input Prompt → (keyword, niche, style)
	2.	Script Generation (Groq LLM)
	3.	Script Cleaning & Formatting
	4.	Title + Hashtags Generation (Groq LLM)
	5.	Combine Script + Metadata
	6.	Build Video Prompt for Veo
	7.	Generate Video via Google Veo API
	8.	Poll Veo Job Until Ready (asynchronous)
	9.	Extract Final Video URL
	10.	(Upcoming) Auto-post to Instagram via Meta Graph API

This project functions as a multi-agent automated content generation system.

⸻

📌 Current Status: What’s Working Now

✅ 1. Data Input & Preprocessing
	•	User inputs: keyword, niche, style
	•	n8n “Edit Fields” node stores inputs for downstream agents

⸻

✅ 2. Script Generation Agent (Groq LLM)
	•	Using Groq model: llama-3.1-8b-instant
	•	Output: 20–25 sec script with hook, bullet points, CTA
	•	Script is extracted from Groq response and cleaned for next steps

⸻

✅ 3. Title + Hashtags Agent
	•	Uses Groq again to generate:
	•	Title
	•	6–10 hashtags (SEO + niche relevant)
	•	Output parsed into structured JSON:
  {
  "title": "Optimize Your Reels for Higher Engagement",
  "hashtags": "#InstagramReels #SocialMediaMarketing #ContentStrategy"
}
✅ 4. Combine All Agent Outputs

A custom node merges:
	•	keyword
	•	niche
	•	script
	•	title
	•	hashtags

This creates a clean object for video generation.

⸻

✅ 5. Video Generation (Google Veo API)
	•	Body created dynamically using user input + LLM outputs
	•	Makes API request to Veo using:
  https://generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predict

  Video jobs are asynchronous → returns operation ID

✅ 6. Veo Long-Running Job Polling System

Implemented a clean polling loop:
	1.	Send request to:
  /operations/{operation_id}

	2.	If "done": false → Wait X sec → Poll again
	3.	If "done": true → extract final video URL
⚙️ 7. Final Output Assembly

The final JSON object now includes:
	•	Fully generated video URL
	•	Final caption (title + hashtags)
	•	Ready for upload to Instagram
🧩 Architecture Diagram

User Input → Script Agent (Groq)
          → Clean Script
          → Title & Hashtags Agent (Groq)
          → Combine Metadata
          → Build Veo Request Body
          → Veo Generate
          → Veo Check Job (Loop Until Done)
          → Extract Video URL + Caption
          → (Next) Instagram Posting Agent

📚 Tech Stack

Automation & Orchestration
	•	n8n

LLM & Natural Language Generation
	•	Groq LLMs
	•	llama-3.1-8b-instant

AI Video Generation
	•	Google Veo
	•	veo-3.1-generate-preview

Social Media Publishing (Coming Soon)
	•	Meta Graph API
	•	Instagram Content Publishing
	•	Video Upload
	•	Publishing via /media_publish
🛠️ Project Setup (High-Level)

1. Clone repository
git clone https://github.com/username-missing/reelhub
cd reelhub
 
2. Install n8n (local Docker recommended)
   docker-compose up -d
3. Add required API keys in n8n credentials
	•	Groq API Key
	•	Google Generative Language API Key
	•	Meta App Access Token (coming soon)

🚧 Next Steps (Planned)

🔜 Instagram Auto-Posting Agent

Will include:
	•	Upload video to IG container
	•	Attach caption
	•	Publish reel
	•	Handle rate limits & errors

🔜 Cron Scheduler for Daily Auto-Content

Generate + post 1–3 reels/day automatically.

🔜 Analytics Agent

Track:
	•	reach
	•	engagement
	•	best performing topics
and regenerate optimized content.

LAYOUT 


ai-reels-automation/
├── README.md
├── .gitignore
├── workflows/
│   ├── video-agent.json
│   ├── veo-agent.json
│   ├── meta-posting-agent.json
│   └── orchestrator.json          # (later)
├── docs/
│   ├── architecture.md
│   ├── veo-notes.md
│   └── meta-setup.md
├── scripts/
│   └── examples/                  # later: python helpers, test scripts
├── env.example
└── assets/
    ├── screenshots/
    │   ├── n8n-video-agent.png
    │   ├── n8n-veo-loop.png
    │   └── n8n-meta-agent.png
    └── diagrams/
        └── architecture.png
