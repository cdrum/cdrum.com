---
title: Moving GetMoarFediverse over to my own Infrastructure - Mastodon
description: ""
date: 2023-06-13T06:00:27.548Z
preview: ""
draft: false
tags:
  - DSM
  - GetMoarFediverse
  - MastoAdmin
  - Mastodon
  - Synology
  - SingleUserInstance
categories:
  - Social
keywords:
  - DSM
  - GetMoarFediverse
  - MastoAdmin
  - Mastodon
  - Synology
  - SingleUserInstance
---


## Intro

I wrote two days ago about [moving FediFetcher over to my own infrastructure]() (away from GitHub Actions and over to my Synology NAS at home). 

Since it has been going well, I decided to do the same with [GetMoarFediverse](https://github.com/g3rv4/GetMoarFediverse). (I'll still rely on the remote FakeRelay service as [detailed in this post](), at least for the time being.)

## Set Up

Getting this onto my Synology is actually pretty darn easy,

From my [FediFetcher post](), I will be using the `~/Mastodon` folder to hold everything.

First, I need to download the executable from the project's GitHub repo. I don't have much patience to compile it myself, and I trust the developer.

- Repo: [https://github.com/g3rv4/GetMoarFediverse](https://github.com/g3rv4/GetMoarFediverse)
- Executibles: [https://github.com/g3rv4/GetMoarFediverse/releases](https://github.com/g3rv4/GetMoarFediverse/releases)

At the time of writing, I downloaded [version 1.0.54](https://github.com/g3rv4/GetMoarFediverse/releases/download/v1.0.54/GetMoarFediverse_linux-x64.tgz), the latest version.

```sh
cd ~/Mastodon
wget https://github.com/g3rv4/GetMoarFediverse/releases/download/v1.0.54/GetMoarFediverse_linux-x64.tgz
tar -xvzf GetMoarFediverse_linux-x64.tgz 
```

This places the executable `GetMoarFediverse` in the folder `~/Mastodon/GetMoarFediverse-v1.0.54`.

I do a simple simlink so I don't need to worry about paths in the future when I upgrade.

```sh
cd ~/Mastodon
ln -s GetMoarFediverse-v1.0.54/ GetMoarFediverse
```

This allows me to run the executable from the path `/var/services/homes/cdrum/Mastodon/GetMoarFediverse`  (Your paths may be different depending on how your Synology DSM is set up, and the username, etc.)

Next, the confguraton. I took the config I had from my GitHub Action repo (that was cloned from [GMFActionDemo](https://github.com/g3rv4/GMFActionDemo)):

```json
{
    "FakeRelayUrl": "https://fakerelay.gervas.io",
    "Tags": [ "china", "singapore", "asean",
             "mastoadmin", "fediadmin", "mastohost", "singleuserinstance", "fedifetcher",
             "apple", "mackbook",
             "pilot", "ppl"
             
            ],
    "Instances": [ "hachyderm.io", "mastodon.social", "infosec.exchange", 
                  "mstdn.social", "mas.to", "mastodon.online", "mastodon.world", 
                  "fosstodon.org", "techhub.social", "indieweb.social"
                 ]
}
```

And I pasted the contents into a new json file I put in `/var/services/homes/cdrum/Mastodon` that I called `getmoarfediverse-config.json` (a bit long, I know).

I then need to add another entry `"FakeRelayApiKey":` and use the FakeRelay api key I got earlier, so the file now looks like:

```json
{
    "FakeRelayUrl": "https://fakerelay.gervas.io",
    "FakeRelayApiKey": "blahblahblah",
    "Tags": [ "china", "singapore", "asean",
             "mastoadmin", "fediadmin", "mastohost", "singleuserinstance", "fedifetcher",
             "apple", "mackbook",
             "pilot", "ppl"
             
            ],
    "Instances": [ "hachyderm.io", "mastodon.social", "infosec.exchange", 
                  "mstdn.social", "mas.to", "mastodon.online", "mastodon.world", 
                  "fosstodon.org", "techhub.social", "indieweb.social"
                 ]
}
```

*(replace "blahblahblah" with your key!)*

Then, I can simply run it one time manually by running the following:

`/var/services/homes/cdrum/Mastodon/GetMoarFediverse/GetMoarFediverse /var/services/homes/cdrum/Mastodon/getmoarfediverse-config.json`

If all goes well, you'll see a lot of output pulling in toots that match your settings. Example:

```sh
...
Fetching tag #china from mastodon.social
Fetching tag #china from infosec.exchange
Fetching tag #china from mstdn.social
Retrieved 0 new statuses from mastodon.online with hashtag #apple
Fetching tag #singapore from mas.to
Retrieved 1 new statuses from mas.to with hashtag #singapore
Fetching tag #singleuserinstance from indieweb.social
Retrieved 1 new statuses from fosstodon.org with hashtag #singapore
...
```

## Setting up the Cron

Going back to the Synology DSM Interface, go to the **Task Scheduler**

![](/2023-06-12_12-06-15.png)

Click **Create**

![](/2023-06-12_12-06-27.png)

Then fill out the various pages. give it a name:

![](/2023-06-13_14-17-24.png)

Configure the schedule (I chose every 5 minutes)

![](/2023-06-13_14-17-44.png)

And finally configure the taks settings it self. Paste in the following string in the **User-defined script** field:

`/var/services/homes/cdrum/Mastodon/GetMoarFediverse/GetMoarFediverse /var/services/homes/cdrum/Mastodon/getmoarfediverse-config.json >> /var/services/homes/cdrum/Mastodon/getmoarfediverse-cron.log 2>&1`

Notice I added the `>> /path_to_log` so I can check the log from time to time to make sure it runs. *(Note: do check on the file size from time to time. It will grow quite large.)*

![](/2023-06-13_14-22-17.png)

Click **OK** and let's see if it works!

You sholuld be able to tail the log file to make sure all is working correctly.

`tail -f getmoarfediverse-cron.log `

Then as you want to make changes to your hashtags and/or mastodon instances, simply edit the ..config.json file, and the next cron run should pick up the changes.

Hope this helps!