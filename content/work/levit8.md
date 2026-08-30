---
title: "Levit8 AI"
date: 2024-01-01T00:00:00+08:00
draft: false
description: "AI-powered candidate screening platform for recruitment teams."
tags: ["python", "fastapi", "next.js", "openai", "livekit", "mongodb", "gcp"]
featured: true
status: "ended"
dateStart: "January 2024"
dateEnd: "December 2024"
image: "/images/levit8.png"
links:
  github: ""
  live: "https://www.levit8.ai"
externalURL: 
---

Levit8 is a B2B SaaS platform in HR tech, built for recruitment organizations that need to screen large volumes of candidates without growing their team at the same rate. It automated the screening pipeline end to end, from resume evaluation through to the interview itself, using LLMs at every stage.

The company came out of Antler's entrepreneurship residency program and raised USD 125k in seed funding.

## What it does

Recruiters define a job, upload a candidate pool, and Levit8 handles the rest. An LLM parses and scores each resume against the job description. Candidates who pass the first cut get a screening interview, text-based or real-time voice, conducted entirely by the AI. Afterward the system produces structured scores across skills, culture fit, and soft skills, and those flow back into the recruiter dashboard for review.

There was also a Chrome extension for pulling job applications straight from LinkedIn, and a webhook API for syncing results back into ATS platforms.

## Stack

Python/FastAPI backend, Next.js/React frontend (separate apps for the recruiter dashboard and the candidate portal), MongoDB Atlas for data. Voice interviews ran on LiveKit with a custom agent pipeline. LLM workloads used OpenAI (GPT-4, GPT-4o-mini) with LangChain for prompt orchestration and Jinja2-templated prompts. Deployed on Google Cloud Run.

## Architecture

One interview engine drove both modes, text over WebSocket and voice over LiveKit, from the same underlying logic. Interview state lived in MongoDB, so sessions could pause and resume across connections. The voice side ran on a custom real-time agent pipeline (speech detection, transcription, LLM, synthesis) with several provider options for tuning latency. LLM calls were tracked per evaluation stage so we could see what each one cost.

## My role

Co-Founder and CTO. I set the initial technical strategy, designed the architecture, made the infrastructure calls, and wrote most of the early code, especially the backend, the LLM integration layer, and the voice agent pipeline. I managed two engineers and took the product from nothing to a working platform with paying pilots in under six months. I've since moved on.
