---
title: "Alternatives.pe"
date: 2026-03-30T07:10:20+08:00
draft: false
description: "Private market data intelligence platform for Southeast Asia and Australia."
tags: ["python", "django", "next.js", "aws", "ecs", "postgresql"]
featured: true
status: "active"
dateStart: "July 2024"
dateEnd: "Present"
image: "/images/alternatives.png"
links:
  github: ""
  live: "https://www.alternatives.pe"
externalURL: ""
---

[Alternatives.pe](https://www.alternatives.pe) is a B2B data platform for private market intelligence across Southeast Asia and Australia: VC, PE, funds, deals, capital allocators, and the people behind them.

## What it does

Investment professionals get structured, searchable access to private market data. Funds, deals, investors, legal entities, market events. It's all reachable through filterable explorer views and detailed profile pages.

## Stack

Python/Django REST API, Next.js frontend, PostgreSQL, Redis, deployed on AWS ECS. Authentication and subscription management run on third-party services wired into both layers.

## Agent orchestration

AI agent workflows handle parts of the data pipeline, coordinating ingestion, enrichment, and validation.

## My role

I'm CTO, which means I own the architecture end to end: data model, infrastructure, deployment pipelines. I'm still in the codebase across both the Python/Django backend and the Next.js frontend. I'm not a CTO who stopped coding.

I run a small engineering team, so hiring, onboarding, code review, technical direction, and keeping delivery moving without piling on process all land with me. So do the build vs. buy calls, how the data layer is structured to hold up at scale, and whether the system stays maintainable as the team and the product grow.

We're also deliberately moving toward being an AI-native engineering org. That means AI tooling in how we work, not only AI features in what we ship.
