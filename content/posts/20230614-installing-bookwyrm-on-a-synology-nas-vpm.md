---
title: Installing Bookwyrm on Ubuntu 22.04 LTS Server on a Synology NAS VM (Virtual Machine Manager)
description: ""
date: 2023-06-15T12:43:31.744Z
preview: ""
draft: false
tags:
  - Bookwyrm
  - cloudflare
  - dsm
  - fediverse
  - synology
  - tunnels
  - ubuntu
  - virtual machines
  - Virtual Machine Manager
categories:
  - Social
keywords:
  - Bookwyrm
  - cloudflare
  - dsm
  - fediverse
  - synology
  - tunnels
  - ubuntu
  - virtual machines
  - virtual machine manager
---



## Intro

I keep telling myself I need to read more. I have a kindle with a long backlog of books I have yet to read. But like any serial procrastinator, I need the right tools to do something. And with my recent foray into the decentralized fediverse, I figured a [Bookwyrm](https://joinbookwyrm.com) account would be good to have! But... why create an account on bookwyrm.social when I can run my own instance?!

*"You must be mad!"* you may be thinking. Perhaps so. (and yes, this too me too much time to set up (troubleshooting the right settings for the tunnel), I could have made a serious dent in a book!)

In a [previous blog post](), I started to utilize my Synology NAS for some of my Fediverse experiments and services. Up to now, these have been focused completely on Mastodon. 

I figured that I should be able to run a Bookwyrm server on the NAS. Some challenges I had to face:

1. My Telco that provides my fiber access are a bunch of clowns 🤡 and they disabled the port forwarding functionality on the router they provided. And I don't really want to buy a new router.
2. I don't want to run Bookwyrm as a service on the main installation of the Synology NAS. I don't want there to be conflicts, nor do I want to expose my NAS to potential bad actors. So some sort of virtualized solution is needed.

Lucky for me, the solutions are within reach:

1. Synology offers VM capabilities in their **Virtual Machine Manager** app. You can easily run Linux VMs (and other OSs) that are nicely segregated from the rest of the Synology DMS OS.
2. [Cloudflare Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/) are free and will get around the port forwarding issue! *An added bonus, we don't need to mess around with Let's Encrypt certificates either!*

For the actual Bookwyrm installation and configuration, I followed the [official instructions at this page](https://docs.joinbookwyrm.com/install-prod.html) mostly to the letter, but in this post, I will explain where I deviated, especially when setting up Cloudflare Tunnels.

**Note:** At the time of this writing, **Bookwrym** is at version **v0.6.2**.

## Installing Ubuntu on a VPM

For some background reading, check out Lester Chan's write up on getting [Ubuntu Server up and running on the Synology DSM](https://lesterchan.net/blog/2021/04/05/installing-ubuntu-server-on-synology-nas/). Note, the screenshots are for an earlier version of Synology DSM - I'm on version 7.

You can, of course, run other Linux distros of your choice, but I personally am most comfortable with Debian/Ubuntu and decided to to install the server version of Ubuntu 22.04 LTS (we don't need any of the Gnome GUI stuff for this). I downloaded it directly onto the Synology from this url: [https://ubuntu.com/download/server](https://ubuntu.com/download/server).

Open **Virtual Machine Manager** and click **Create**.

![](/2023-06-14_21-00-10.png)

Select Linux.

![](/2023-06-14_21-00-20.png)

Configure the size of the VM - I chose 2 CPU and 2GB of memory (yes, the screenshot below shows 1 CPU). I'll see how this performs but I may need to increase this in the future. But for Bookwyrm? Should be ok, no? 🤞

Also, I had to switch the Video Card from **vmvga** to **vga**, otherwise it wouldn't boot. Not sure why! (So in the screenshot below, it's not the setting I ended up using. YMMV...)

![](/2023-06-14_21-00-45.png)

For Storage, I am giving it 200 GB, should be plenty. (Probably overkill, really... But if I end up hosting other services in this VM, It will be useful having extra storage).

![](/2023-06-14_21-01-03.png)

Select Default Networking. 

Then, in Other settings, select the boot up ISO that we downloaded.

![](/2023-06-14_21-01-34.png)

Then, assign power management permissions to your synology account. Save. Now, power it up. It should boot into the ISO. It may take a while!

Now, from the Virtual Machine Manager, click **Connect** to open a terminal (in a browser tab - we'll sort out SSH later) to the VM. Select **Try or Install Ubuntu**.

If you get to the screen that looks like the below screenshot, you're in good shape. Go through the process of installing Ubuntu, I won't cover the details here.

![](/2023-06-14_21-52-43.png)

Also, you may want to consider installing the minimized version since we're on a small virtual machine. No sense installing stuff we don't need.

![](/2023-06-14_21-56-21.png)

Grab a coffee ☕️. It will take a while.

Once it's done, it may be a good idea to take a snapshot of the VM's state, in case something goes wrong later, you won't have to go through the installation process again!

## Preparing for setting up Bookwyrm

We need to do a few things first, such as install a bunch of packages. Remember, we chose the minimal installation of Ubuntu Server, so not much bloat was installed.
Here are the packages (and other command line stuff I had to do) I had to explicitly install:

```shell
sudo apt install git
sudo apt install vim
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
sudo usermod -aG docker ${USER}
sudo apt install cron
```

Then, clone Bookwyrm in your path of choice. (See https://docs.joinbookwyrm.com/install-prod.html), specifically:

```shell
cd ~
git clone git@github.com:bookwyrm-social/bookwyrm.git
git checkout production
```

Stop there, let's get our tunnels going first.

## Configure Cloudflare Tunnels

Cloudflare Tunnels is a great free service from Cloudflare that allows you to create a secure tunnel into a machine/instance/VM deep within a network to allow remote access that otherwise wouldn't be allowed. mWe'll be using this for enabling SSH access to the VM, as well as HTTP/HTTPS access for the Bookwyrm service.

Setting up a tunnel is pretty straight forward and I'll only touch on some specifics that I think is important to cover.

There are two ways (that I know of) for setting up a tunnel. The first is via the CLI and doesn't require a Cloudflare account at all. The other does require a Cloudflare account as it utilizes the web console for set up and configuration. I opted for this method as I couldn't get the SSH tunnel working using the first method. It certainly could have been user error!

Also important to note is for you to use a custom domain for the tunnel, as we'll be doing, you need Cloudflare to host your domains DNS. Also free.

Some background reading: 

1. Instructions from [https://pkg.cloudflare.com/index.html](https://pkg.cloudflare.com/index.html).
2. Blog post that focuses on using the CLI to set up a simple tunnel: (Thanks[@michael@mstdn.thms.uk](https://mstdn.thms.uk/@michael)): https://blog.thms.uk/2023/05/use-cloudflare-tunnel-to-share-local-development-site
3. An article explaining how to get SSH working on the client and configuring the tunnel via the CLI: https://orth.uk/ssh-over-cloudflare/
4. Another blog post, focused on SSH access, also via the CLI: https://danishshakeel.me/creating-an-ssh-tunnel-using-cloudflare-argo-and-access/
5. Running the Cloudflare Tunnel within a Docker container: https://keestalkstech.com/2023/01/expose-docker-compose-app-with-a-secure-cloudflare-tunnel/

### Install and Configure the Cloudflared Tunnel for Web Access to Bookwyrm

First we need to install the `cloudflared` application. Luckily, there's a prebuilt binary for Ubuntu, we just need to add Cloudflare's repo to our system.

```shell
# Add cloudflare gpg key
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

# Add this repo to your apt repositories
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared jammy main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

# install cloudflared
sudo apt-get update && sudo apt-get install cloudflared
```

Now, remember, I chose to go the web console route for setting up and configuring my tunnels. I'll assume you have an account already and that you have your domain configured to use Cloudflare's DNS.

From the Cloudflare web console. access **Cloudflare Tunnel** from the your domain's control panel in Cloudflare.

![](/2023-06-15_08-31-33.png)

Then **Launch Zero Trust Dashboard**. I had to put in my credit card but it's free for a small team (up to 50 users) so should be fine.

Find **Tunnels** under **Access** then click **Create a tunnel**.

![](/2023-06-15_08-33-28.png)

Give the tunnel a name - I called it "bookwyrm".

![](/2023-06-15_08-35-18.png)

Install the connector by copying the command and pasting it into your terminal CLI. 

![](/2023-06-15_08-35-34.png)
(save the token for later)

Make sure the connector shows up at the bottom of the page (and shows "connected") before clicking Next.

![](/2023-06-15_08-40-50.png)

Next it will take you tot he public hostname configuration. This is where you add the HTTP service. The beautiful thing about Cloudfare Tunnels is it will handle the SSL stuff for us. So all we have to do here is add a single service for HTTP. The Service URL should be `nginx:80` - we'll address that later. Then click **Save tunnel**.

![](/2023-06-15_20-07-02.png)

You should now have single public hostname service that look something like this:

![](/2023-06-15_20-14-28.png)

This will automatically create the hostname in the dns and it point to the proxy. Looking at the DNS entries for the domain in Cloudflare, we see something like this:

![](/2023-06-15_14-33-07.png)

### Setting up a Cloudflare Tunnel Docker Container

Now, we won't be running the cloudflare client on the host VM - we'll be creating a new Docker Container to do so. See [this blog post](https://keestalkstech.com/2023/01/expose-docker-compose-app-with-a-secure-cloudflare-tunnel/) for more details (I encourage you to read that post now). 

Hopefully you have already cloned the bookwyrm repo and checked out the `production` branch. Some edits we need to make:

I made several changes to the `docker-compose.yaml` file. They are:

1. Insert the following above the `nginx` service - this is for the **Cloudfare Tunnel**:

```yaml
services:
  tunnel:
    image: cloudflare/cloudflared:latest
    command: tunnel --no-autoupdate run
    env_file: tunnel.env
    restart: always
    container_name: tunnel
    networks:
      - main
    depends_on:
      - web  
```

We want to force this container to start before the `web` container. 

2. Remove the ports from the `nginx` container. We don't need to expose ports at all!

```yaml
nginx:
  ...
  restart: unless-stopped
  ports:            ## Delete This
    - "80:80"       ## Delete This
    - "443:443"     ## Delete This
  depends_on:
    - web
```

3. Add the following to the `nginx` container definition a) Force the container to depend on the `tunnel` container to ensure it's loaded first, and b) force the container's name (remember we're referring to this in the Tunnel configuration above); I think this is needed for hostname resolution for the tunneling (? - it's a hunch!)

```yaml
nginx:
  ...
  depends_on:
    - web
    - tunnel                  ## Add This
  networks:
    - main
  container_name: nginx       ## Add This
  volumes:
    - ./nginx:/etc/nginx/conf.d
    ...
```

4. Remove *ALL* `certbot` references, namely the following lines:

```yaml
nginx:
  ...
  volumes:
    - ./certbot/conf:/etc/nginx/ssl       ## Delete This
    - ./certbot/data:/var/www/certbot     ## Delete This
    - static_volume:/app/static
    - media_volume:/app/images
certbot:                                  ## Delete This
  image: certbot/certbot:latest           ## Delete This
  command: certonly --webroot --webroot-path=/var/www/certbot --email ${EMAIL} --agree-tos --no-eff-email -d ${DOMAIN} -d www.${DOMAIN}   ## Delete This
  #command: renew --webroot --webroot-path /var/www/certbot   ## Delete This
  logging: *default-logging               ## Delete This
  volumes:                                ## Delete This
    - ./certbot/conf:/etc/letsencrypt     ## Delete This
    - ./certbot/logs:/var/log/letsencrypt ## Delete This
    - ./certbot/data:/var/www/certbot     ## Delete This
db:
  build: postgres-docker
  ...
```

Lastly, you will see that we referenced a `tunnel.env` file int he `tunnel` docker container above. Create the file `tunnel.env` int he root `bookwyrm` folder and add:

`TUNNEL_TOKEN=eyJhIjoi..........`

... where the token is from this page in the tunnel config:

![](/2023-06-15_08-35-34.png)

That's it, really. Pretty simple. We'll see this container fire up later when we start the real bookwyrm configuration.

### SSH Access

I created a 2nd tunnel specific for SSH access. See these two blog posts for great instructions [(1)](https://orth.uk/ssh-over-cloudflare/) and [(2)](https://danishshakeel.me/creating-an-ssh-tunnel-using-cloudflare-argo-and-access/).

I chose to create an obfuscated hostname for the tunnel. You can also chose a different port number too if you want extra security by obfuscation.

We cna see the two tunnels in the Cloudfare Tunnel console here:

![](/2023-06-15_20-16-56.png)

## Install Bookwyrm

OK, let's get back to setting up Bookwyrm!

Head on over to [official instructions at this page](https://docs.joinbookwyrm.com/install-prod.html) and follow them. I followed majority of the instructions as is. Obviously the instructions don't talk about tunnels or more complex situations. And, due to some of the complexity that Cloudfare handles for us, we can remove or omit some things that are no longer needed.

Do the relevant `.env` config file set up as per the instructions. Note that in the `.env` file we created, I had issues with the `REDIS_BROKER` password having special characters, so I removed them and kept it a simple alphanumeric password. For the email provider, I used *mailgun* - also free and easy to set up!

> The instructions ask you to copy the `nginx/production` config example into `nginx/default.conf`. We don't want to do that since it has all the Let's Encrypt SSL stuff in that. We don't need it! So, instead, copy the `nginx/development` config example into `nginx/default.conf`. And that's it!

Then we can run `./bw-dev migrate`. Grab another coffee. ☕️

Now, fire up the containers. Probably need another coffee ☕️!

`docker compose up --build`

Hopefully everything succeeds and you'll see something like the following (and a lot more of it, without any errors!)

![](/2023-06-15_20-25-38.png)

Do a `ctrl-c` to exit so we can complete the rest of the tasks.

We don't need to do any of the SSL or Let's Encrypt-specific tasks in the instructions.
- Set up HTTPS redirect - *Nope!*
- Set up a cron job to keep your certificates up to date - *Nope!*

Finally, I didn't bother with the external storage. They're books, right?

Next, we run `./bw-dev setup` to do the final set up stuff. Take note of the admin code, you'll need that.

Finally we re-run docker compose but this time with the daemon flag (and without the `--build` flag!)

`docker compose up -d`

Be sure to go to the site and complete the set up!

## Final Thoughts

We need to do some things like adding a service to start it up after a reboot, backups, etc. But otherwise, a pretty simple process. Let's see how this experiment goes. I should be able to kill my Goodreads account soon!

I welcome any constructive feedback. Find me on Mastodon at @cdrum@cdrum.social. 