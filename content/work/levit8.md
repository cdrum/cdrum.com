---
title: "Levit8"
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

Levit8 is a B2B SaaS platform in the HR Tech space, built for recruitment organizations that need to screen large volumes of candidates without scaling their team proportionally. It automated the screening pipeline — from resume evaluation through to conducted interviews — using LLMs throughout.

The company was born out of Antler's entrepreneurship residency program and raised USD 125k in seed funding.

## What it does

Recruiters define a job, upload a candidate pool, and Levit8 handles the rest. Resumes are parsed and scored against the job description by an LLM. Candidates who pass the first cut receive a screening interview — either text-based or real-time voice — conducted entirely by the AI. Post-interview, the system produces structured scores across skills, culture fit, and soft skills, which flow back into the recruiter dashboard for review.

The platform also included a Chrome extension for pulling job applications directly from LinkedIn, and a webhook API for syncing results back into ATS platforms.

## Stack

Python/FastAPI backend, Next.js/React frontend (separate apps for recruiter dashboard and candidate portal), MongoDB Atlas for data. Voice interviews ran on LiveKit with a custom agent pipeline. LLM workloads used OpenAI (GPT-4, GPT-4o-mini) with LangChain for prompt orchestration and Jinja2-templated prompts. Deployed on Google Cloud Run.

## Architecture

A single interview engine drove both text (WebSocket) and voice (LiveKit) modes from the same underlying logic. Interview state persisted in MongoDB, allowing sessions to pause and resume across connections. Voice interviews ran on a custom real-time agent pipeline — speech detection, transcription, LLM, and synthesis — with multiple provider options for latency tuning. LLM calls were tracked per evaluation stage for cost visibility.

## My role

Co-Founder and CTO. I defined the initial technical strategy, designed the architecture, made the infrastructure decisions, and did the majority of the early coding — particularly the backend, the LLM integration layer, and the voice agent pipeline. I managed a team of two engineers and took the product from zero to a working platform with paying pilots in under six months. I have since left to pursue other opportunities.