---
title: Experimenting with a Single User Mastodon Instance - cdrum.social
description: ""
date: 2023-06-10T01:04:00.481Z
preview: ""
draft: false
tags:
  - FediAdmin
  - MastoAdmin
  - Mastodon
  - MastoHost
  - GitHub Actions
categories:
  - Social
keywords:
  - FediAdmin
  - MastoAdmin
  - Mastodon
  - MastoHost
  - GitHub Actions
---


## Intro

I've been using Mastodon maybe two weeks now, and I am liking what I've been seeing so far. Sure, the first few days were very quiet, but as I followed more users (and trimed as needed), and followed some hashtags, it started to become a useful service.

I was on the `mastodon.social` instance. But, with Mastodon being a federated service, I wanted to run my own instance. Maybe to control my own data? My own identity? I'm not really sure to be honest 

A lot of this config is based on [@michael@mstdn.thms.uk](https://mstdn.thms.uk/@michael)'s excellent [blog post](https://blog.thms.uk/2023/01/setting-up-mastodon). 

## Set Up

I was toying with the idea of "rolling my own" instance using Digital Ocean, or AWS, or some other similar service. I ended up going with [Masto.host](https://masto.host) as it seemed like a no brainer solution to get me up and running. I chose the $9/month plan. (4 Processing Threads, 5 GB Database, 50 GB Media Storage). I may downgrade or upgrade depending on my needs over time.

Once I got the instance up and running, which really was super simple on [Masto.host](https://masto.host), I proceeded to activate the account migraition through the Mastodon interface.

![](/2023-06-10_09-09-56.png)

I got my 80+ followed accounts in (after a bit of time), and... it was pretty quiet! Following hashtags didn't really do anything. The reason? Well, I wasn't getting any federated posts outside of the scope of the accounts I was following. So, enter some 3rd party tools/services/scrips (whatever you want to call them!).

## Configurations & Useful Tools/Services

### Relays

I am not (yet) using any traditional [relays](https://relaylist.com). (but, see FakeRelay below!) And, since my instance is hosted via [Masto.host](https://masto.host), I have limited configuration options beyond the stock Mastodon administrative configuration. And I think this is OK, for now. I really don't want too much headaches in managing this.

There are, however, some key tools I am employing to make the experience better/usable (and these are outside of Masto.host).

### FediFetcher

[FediFetcher](https://github.com/nanos/FediFetcher) (read more [here](https://blog.thms.uk/fedifetcher)) seems to be the missing link of getting threads and profiles that are relevant to you, "filled out". I just started running it last night, utilizing GitHub Actions, and I will see how it goes over the next several days. It's a bit intensive (especially as you add more follows), and still learning my way around it. Set up was relatively simple (for GitHub Actions, fork it, confiogure it, and let the pipelines take over). The first run took many hours, and each subsequent run took less and less time. Right now, at the time of writing, the most recent run took about 30 minutes.

But, even though the scheduled cron is set at `cron: '3,13,23,33,43,53 * * * *'`, I don't see it actually running every 10 minutes. Given the importance of this serivce, I think I may need to put this on a host where I can run an old school cron, and not rely on a free pipeline service like GitHub Actions. 

### GetMoarFediverse & FakeRelay

First of all, [FakeRelay](https://github.com/g3rv4/FakeRelay) was a no brainer. The author/admin of it, [Gervasio (@g3rv4@mastodonte.tech)](https://mastodonte.tech/@g3rv4), was nice enough to allow me to utilize his hosted relay. See the [readme](https://github.com/g3rv4/FakeRelay/) for instructions!

For [GetMoarFediverse](https://github.com/g3rv4/GetMoarFediverse), I forked the [GMFActionDemo](https://github.com/g3rv4/GMFActionDemo) repo and configured it to my liking (my config [here](https://github.com/cdrum/GMFActionDemo/blob/main/config.json)). Using GitHub Actions, it is really easy to get up and running. Then, adding it to my instance's relays config, simple!

![](/2023-06-10_09-33-17.png)

However, I'm not sure GitHub Actions (for a free user like myself) is the best long term... We shall see.

See [this Youtube video](https://www.youtube.com/watch?v=XOBD8OsdjGY) to get GetMoarFediverse+FakeRelay working. 

### FediBuzz

There's another service called [FediBuzz](https://relay.fedi.buzz) that allows you to create sort-of relays that are based on hashtags. I used it for a bit, but disabled them, in favor of the FakeRelay/GetMoarFediverse solution. I may go back. Let's see!

## Early Learnings

I'm not yet sure if I'm getting all the relevant content I want. With a combination of FakeRelay/GetMoarFediverse and FediFetcher, I think I'm getting what I want, but time will tell. THere seems to be some quarks, such as delays of geting in federated posts (and this could be simply due to config tuning that I need to do on the above services). 

And, as others have pointed out, the Explore and Trends feature on my timeline is not useful. But maybe that's ok? (I use [@MonaApp@mastodon.social](https://mastodon.social/@MonaApp) on both my Mac and iPhone, so it has some nice functionality to get around this limitation).

~~And, I'm not seeing how to get my website verified on my profile! (Yes, I changed the desitination of the Mastodon url on my website to my new mastodon instance!). In the screenshot below, the left shows my old mastodon.social account profile (with the cdrum.com verified), and on the right is my new cdrum.social account profile, the url is not. I'll investigate.~~

*Scratch this, it's working now!* 🤷‍♂️

[![](/2023-06-10_08-56-10.png)](https://cdrum.social/@cdrum/110517204024966330)

I'll report back on other learnings or changes I make to my set up.

## Conclusions

For now, [cdrum.social](https://cdrum.social) is up and running, and you'll see me "tooting" as [@cdrum@cdrum.social](https://cdrum.social/@cdrum). I hope this experiement goes well as I am sure moving *back* to [mastodon.social](https://mastodon.social) will probaby be more disruptive than I'd like.

## More Reading

- [@michael@mstdn.thms.uk](https://mstdn.thms.uk/@michael)'s excellent [blog post](https://blog.thms.uk/2023/01/setting-up-mastodon) giving tips for those of us crazy enough to run single user instances
- [@mull@mas.to](https://mas.to/@mull)'s [blog post](https://mull.net/mastodon) highlighting the dangers of running a single user instance. Yes, I clearly decided to ignore it, to my own peril! 🤷‍♂️ Actually, to be honest, with some of the tools I wrote about in this post, some of @mull's concerns are ... mitigated.
