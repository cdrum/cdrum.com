---
title: "Vuulr"
date: 2018-01-01T00:00:00+08:00
draft: false
description: "Global B2B marketplace for TV and film content licensing."
tags: ["php", "laravel", "vue.js", "mysql", "redis", "algolia", "aws"]
featured: true
status: "ended"
dateStart: "January 2018"
dateEnd: "December 2022"
image: "/images/vuulr.png"
links:
  github: ""
  live: ""
externalURL: "https://www.vuulr.com"
---

Vuulr is a B2B marketplace for TV and film content licensing — connecting content sellers (studios, distributors, production companies) directly with buyers (broadcasters, streamers, platforms) to complete licensing deals in days rather than months. The industry was historically slow, relationship-driven, and paper-heavy. Vuulr digitised the transaction layer.

I co-founded the company in late 2017. By the time I left, Vuulr had become the #1 global platform in the film and TV content licensing space.

## What it does

Sellers list content with rights availability across territories, platforms, languages, and windows. Buyers discover and filter content through a search-driven marketplace, initiate deals via RFPs and offers, negotiate through the platform, and close with a digital deal memo. The platform handles the full transaction lifecycle — from discovery through to signed deal.

Additional features included private screening rooms for content previews, a shopping cart and selections system for managing deal pipelines, a messaging centre, and a REST API for third-party content providers to ingest metadata directly.

## Stack

PHP/Laravel backend, Vue.js frontend, MySQL with read/write replication for query scaling, Redis for caching and job queues. Search was handled by Algolia with multiple content indices. Assets on AWS S3 with CloudFront CDN. Background jobs via Laravel Horizon. Video integrations with Vimeo, Brightcove, and Vzaar.

## Architecture

A layered monolith structured with domain-driven design principles — bounded contexts for the core marketplace domains (cart, screening rooms, selections, deal workflows, messaging) alongside a traditional service/repository layer. A separate REST API namespace handled third-party integrations and content ingestion. Read replicas handled reporting and search-adjacent queries without impacting transactional writes.

## My role

Co-Founder. I led product and engineering for the full five years. I started with a small part-time team of remote engineers and built it to a team of 14 engineers and product managers.

On the engineering side: architecture design, technical direction, code reviews, deployments, and hands-on development. On the product side: roadmap ownership, feature scoping, stakeholder management, and building a data-led product culture. As a founder, those lines blurred constantly — I wore whatever hat the company needed.
