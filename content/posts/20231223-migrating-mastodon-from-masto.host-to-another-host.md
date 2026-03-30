---
title: Migrate Mastodon from Masto.Host to Another Host via Cloudron
description: High level steps taken to migrate my personal single user mastodon instance from Masto.host to my own server.
date: 2023-12-23T12:32:38.123Z
preview: ""
draft: false
tags:
    - Masto.Host
    - Mastodon
    - Social
    - Fediverse
    - Cloudron
categories: []
---


## Intro

Wow, it's been quite some time since I posted on my blog. Some stuff happened, and I'll try to find time to write about it in the coming days. But right now, in the spirit of end-of-the-year cleaning (is that a thing?), I decided to spend some time to reduce some of my subscriptions. I won't go into the details of the other services I've axed, but this post is focused on my personal Mastodon instance.

I've been hosting [cdrum.social](https://cdrum.social) on [Masto.Host](https://masto.host) for a while - I have nothing but respect for the service and the folks who run it. But, I need to cut some costs. I also have a cloud instance (VPS) where I host [cdrum.photos](https://cdrum.photos) (and frankly, I'm not sure about the future of that, since I've been using Instagram a bit more lately), and that instance is definitely underutilized. So I figured I'd migrate my Mastodon service over to that VPS.

This blog post details the process I took.

## Migrating out of Masto.Host

Masto.Host makes it relatively easy to migrate out. Kudos to them!

From the console, navigate to **Download Backup** and scroll to the **Generate a backup from live data** section. You need to stop the server and request a new backup to make sure that you get everything, and that no data is lost from federation (if the server was still running). This means, downtime!.

![](/2023-12-23_07-40-34.png)

Wait for it to shut down...

![](/2023-12-23_07-45-42.png)

Now go back to the **Backup** section, and I opted to download only the media files I uploaded and not the cached files. I figured the cached files would be pulled later if I access those posts. I hope this was the right decision! 🤣

![](/2023-12-23_07-46-45.png)

Luckily, this is a small backup in my case. When it's done, you'll get an email to download it.

Once it's downloaded, now you can move onto the next step.


## Migrating onto my Server - via Cloudron

I use [Cloudron](https://www.cloudron.io) to manage the services on my dedicated vps. 

On the Cloudron console, install Mastodon.

![](/2023-12-23_07-53-49.png)

Select the correct domain you wish to host it on (make sure you add the domain to Cloudron - see Cloudron's documentation) - and, make sure you update your domain's DNS settings to point to the new host for the domain you chose!

![](/2023-12-23_07-55-07.png)

Wait for it to install...

![](/2023-12-23_08-00-21.png)

Then, connecting to the terminal of the Mastodon Application,

![](/2023-12-23_08-12-53.png)

You can then uploaded the backup `tar` you just downloaded from **Masto.Host** to the Mastodon container on the VPS managed by Cloudron into the `/tmp` folder.

![](/2023-12-23_08-20-32.png)

I untared the files in a new folder in `/tmp` for easy access:

```shell
cd /tmp
mkdir migration
cd migration
tar -xf ../cdrumsocial_2023.....tar
```

I then followed, largely, the instructions on Migrating Mastodon instances from Mastodon themselves: [https://docs.joinmastodon.org/admin/migrating/](https://docs.joinmastodon.org/admin/migrating/).

Put the Cloudron Mastodon app in Repair mode to ensure the DB isn't being written to.

![](/2023-12-23_08-39-46.png)

### Postgres DB

See [https://docs.cloudron.io/guides/import-postgresql/](https://docs.cloudron.io/guides/import-postgresql/) for Postgres-specific import instructions.

From the Cloudron Mastodon app Terminal (which we connected to above), do the following:

Clear out existing DB. (it was created for a fresh Mastodon installation):
```shell
PGPASSWORD=${CLOUDRON_POSTGRESQL_PASSWORD} psql -h ${CLOUDRON_POSTGRESQL_HOST} -p ${CLOUDRON_POSTGRESQL_PORT} -U ${CLOUDRON_POSTGRESQL_USERNAME} -d ${CLOUDRON_POSTGRESQL_DATABASE} -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public"
```

Then *restore* the backup into the DB:

```shell
pg_restore -Fc -h ${CLOUDRON_POSTGRESQL_HOST} -p ${CLOUDRON_POSTGRESQL_PORT} -U ${CLOUDRON_POSTGRESQL_USERNAME} -n public --no-owner --role=${CLOUDRON_POSTGRESQL_USERNAME} -d ${CLOUDRON_POSTGRESQL_DATABASE} /tmp/migration/pg_dump.custom
```

### Secrets

Secrets from the old host are in the file `secrets.txt`, specifically `SECRET_KEY_BASE`, `OTP_SECRET`, `VAPID_PRIVATE_KEY`, and `VAPID_PUBLIC_KEY`.

Copy all 4 into the `/app/data/env.production` - I commented out the existing values in case.

![](/2023-12-23_09-09-10.png)

### Media Files

Media files from the backup are in the `media/` folder. Move the `media/*` folders to `/app/data/system`, so now you have `/app/data/system/media_attachments/` and `/app/data/system/accounts/`.

## Restart Mastodon

Back on Cloudron, disable recovery mode.

![](/2023-12-23_09-15-23.png)

Access your Mastodon instance, and see if everything is working. I had lots of cached images broken (understandable considering I didn't migrate the cache over). I refreshed with: `/app/code/bin/tootctl account refresh --all`. This will take some time. You may need to adjust your ram settings, otherwise it may get killed. 

Hope this helps some folks wanting to do what I just did!
