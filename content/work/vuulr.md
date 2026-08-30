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

Vuulr is a B2B marketplace for TV and film content licensing. It connects sellers (studios, distributors, production companies) directly with buyers (broadcasters, streamers, platforms) so licensing deals close in days instead of months. The industry had always been slow and relationship-driven, with a lot of paper. Vuulr digitized the transaction layer.

I co-founded the company in late 2017. By the time I left, Vuulr had become the #1 global platform in the film and TV content licensing space.

## What it does

Sellers list content with rights availability across territories, platforms, languages, and windows. Buyers search and filter that catalog, open deals with RFPs and offers, negotiate in the platform, and close with a digital deal memo. Discovery through to signed deal, all in one place.

Beyond that: private screening rooms for content previews, a shopping cart and selections system for managing deal pipelines, a messaging center, and a REST API that let third-party content providers push metadata in directly.

## Stack

PHP/Laravel backend, Vue.js frontend, MySQL with read/write replication for query scaling, Redis for caching and job queues. Search ran on Algolia across multiple content indices. Assets on AWS S3 behind CloudFront. Background jobs via Laravel Horizon. Video integrations with Vimeo, Brightcove, and Vzaar.

## Architecture

A layered monolith built on domain-driven design principles, with bounded contexts for the core marketplace domains (cart, screening rooms, selections, deal workflows, messaging) sitting alongside a traditional service/repository layer. A separate REST API namespace handled third-party integrations and content ingestion. Read replicas took the reporting and search-adjacent queries so they never slowed down transactional writes.

## My role

Co-Founder. I led product and engineering for all five years. I started with a small part-time team of remote engineers and grew it to 14 engineers and product managers.

On the engineering side: architecture design, technical direction, code reviews, deployments, and hands-on development. On the product side: roadmap ownership, feature scoping, stakeholder management, and building a data-led product culture. As a founder those lines blur constantly, so I wore whatever hat the company needed.
